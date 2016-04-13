---
title: Handling SendGrid Callbacks with AWS API Gateway
author: Rob Di Marco
layout: post
categories:
  - aws
  - apigateway
  - lambda
  - rails
  - sqs
---

# TL; DR

Sending [SendGrid](https://sendgrid.com/) [event](https://sendgrid.com/docs/API_Reference/Webhooks/event.html) and [inbound mail](https://sendgrid.com/docs/API_Reference/Webhooks/parse.html) callbacks through [API Gateway](https://aws.amazon.com/api-gateway/) -> [AWS Lambda](https://aws.amazon.com/lambda/) -> [SQS](https://aws.amazon.com/sqs/) -> [Shoryuken](https://github.com/phstc/shoryuken) -> [Rails](http://rubyonrails.org/) is an effective way to manage callbacks without worrying about absorbing heavy load on your Rails server. It comes with some configuration challenges that I talk about here.

# Problem and Desired Solution

At [eLocal](https://www.elocal.com), we use Rails as our primary web framework. In addition, we use SendGrid for both sending mails as well as for receiving inbound email. When sending mail, SendGrid has a series of webhooks you can integrate with to alert you of things like delivery, bounces, opens. In addition, when parsing an inbound email, they will send you a HTTP callback with the parsed version of the message. These are very easy for us to integrate with our web applications, but an unfortunate side effect is that when we are sending a lot of mail, we can receive a surge in callbacks. For example, when we send out a marketing mailer, we may see 10x our normal volume of HTTP requests hitting our Rails servers.

What we were looking to do was to enqueue these requests in a Amazon SQS queue and work them off at a more leisurely rate. We wanted to do this with as minimal amount of server configuration

Our goal was to use Amazon API Gateway to take any inbound callback request and send the request to an AWS Lambda function. This Lambda function would be responsible for taking in the request parameters and putting the data on to an SQS queue. We then would have a worker listening on the queue from one of our Rails servers that would then process data from the queue. This way when there is a surge of callbacks, the requests are just enqueued and then leisurely worked off.

# Setting up the AWS Lambda Function

When setting up the AWS Lambda function, I used as a template the [AWS Labs Lambda ECS Worker Patter](https://github.com/awslabs/lambda-ecs-worker-pattern/blob/master/ecs-worker-launcher/ecs-worker-launcher.js). This template illustrated the basic pattern of taking in a message and putting it on a queue.

My code to do so was basically what you see below.

{% highlight javascript %}

var async = require('async');
var aws = require('aws-sdk');
var sqs = new aws.SQS({apiVersion: '2012-11-05'});

exports.handle = function(event, context) {
    async.waterfall([
            function (next) {
                var params = {
                    MessageBody: JSON.stringify(event),
                    QueueUrl: event.queue_url
                };
                sqsRes = sqs.sendMessage(params, function (err, data) {
                    if (err) {
                      console.warn('Error while sending message: ' + err);
                    }  else {
                      console.info('Message sent, ID: ' + data.MessageId);
                    }
                    next(err);
                });
            }
        ], function (err) {
            if (err) {
                context.fail('An error has occurred: ' + err);
            }
            else {
                context.succeed({ status: 'ok', message: 'success' });
            }
        }
    );
};

{% endhighlight %}

What is nice about this is it is super generic. It requires the inbound event to tell me the SQS Queue URL and just puts the whole event on to the queue. This way it is agnostic to the content, it simply serves as a router.

# Setting up the AWS API Gateway

When setting up the AWS API Gateway, I set up a resource for each type of callback. Each resource would have a POST request method set up to receive the callbacks. The goal of each API was to process the inbound HTTP request parameters off to be sent as event data to the Lambda process. In addition, I would also need to send the appropriate SQS Queue URL in the event to route the request properly.

Since I wanted to have multiple stages for testing vs. production, I defined a Stage Variable and put the Queue URL in there.

# Simple Case, an API call with JSON request data

For the webhooks, the data is sent in to the request as JSON. Configuring the body mapping template with content type ```application/json``` for the Integration Request was simple.

{% highlight javascript %}
{
  "queue_url": "$stageVariables.appropriate_queue_url",
  "body-json" : $input.json('$')
}
{% endhighlight %}

Here we are creating a JSON object with two keys. The body, as JSON, and the appropriate queue URL from the stage variables.

A couple of non-obvious notes.

1. When setting the queue URL, I need double quotes as the Velocity template will directly input an unquoted string.
2. When setting the "body-json", I **do not** need double quotes. The input is coming in as JSON, so we will substitute it directly in.

# More Complex Case, multipart/form data

The second hook I was trying to configure is the inbound parsed email. This is submitted with a content type of ```multipart/form-data```. Unfortunately, this was not as obvious in how to send it over to Lambda. I kept getting parsing errors when trying to set up my template. But after some googling and stack overflowing, I finally figured out what needed to be done. We need to set up a body mapping template of type ```multipart/form-data``` and then Base 64 encode the inbound request body.

{% highlight javascript %}
{
  "queue_url": "$stageVariables.appropriate_queue_url",
  "body-raw": "$util.base64Encode($input.body)",
  "http-headers": "$util.base64Encode($input.params().header)"
}
{% endhighlight %}

The key difference is in the use of the ```$util.base64Encode()``` method that will take the raw input body and encode it. By doing this, I can get the arbitray form data in to the message event without having API Gateway try to parse it.

# Running With Rails

For pulling the jobs off of the SQS queue, I wound up using [Shoryuken](https://github.com/phstc/shoryuken). It is a [Sidekiq](http://sidekiq.org/) inspired background job running library that easily integrates with Rails. All I had to do was set up workers to pull the jobs off the queues and we were good to go! I set up an upstart process for running this on our Rails boxes, tied the restart in to our Capistrano deploys, and we were off and running.

# Summary

Although some of the API Gateway configuration was, shall I say, non-obvious, overall the solution is working quite well and has helped stop the callback surge from impacting our primary Rails servers.

