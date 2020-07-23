---
layout: post
title:  Comparison between SQS / SNS / EventBridge / Kinesis
description: In this post I will explain the differences betweeen SQS, SNS, EventBridge and Kinesis
date:   2020-07-23
---
I will share my learnings about SQS, SNS, EventBridge and Kinesis from [this](https://www.youtube.com/watch?v=Id2IVs1f7S0) video. If you are more of a reader than a watcher, please continue reading, Alternatively, watch the [video](https://www.youtube.com/watch?v=Id2IVs1f7S0).

## What's Covered
*   AWS Simple Queue Service (SQS)
*   AWS Simple Notification Service (SNS)
*   AWS EventBridge
*   AWS Kinesis   

##  SQS
*   Oldest AWS service which means it has been there since 2006. As the name "Queue" implies, it's queue service which means that you put messages in from one service and it comes out to another

*   Used for decoupling

*   Pull service which means that which ever service is connected to SQS, it will only give the message to that service if that service "pulls" from it

*   Messages are kept in SQS for 14 days. After that, they expire

*   Ranked as number 1 for AWS Lambda trigger. The reason is because it decouples them. For instance, have as many Lambdas you want and place the queue in between them

*   Type of queues
    *   Standard
        *   Default queue
        *   Order of the messages isn't preserved
        *   Has high throughput
    
    *   First In First Out (FIFO)
        *   Order of messages is preserved
        *   Low throughput

## SNS
*   Pub / Sub sevice. That means you have to "sub"scribe to get "pub"lished topic

*   Classified as one-to-many relationship. So, A message that is published can be send to MANY subscribers

*   Push service which means that it's doesn't wait for anyone to "pull" the message, it recieves it and give it to its consumer straight away

*   Consumers (service that recieves message) can apply message filtering based on a criteria which reduces the amount of messages the sevice recieves

*   Has a high throughput

*   Can be used with SQS too

## EventBridge
*   Around a years old (launched in July 2019)

*   Messaging service

*   Allows to send mmessages from MANY providers to MANY consumers

*   How it work is it has an event bus which has a set of rules defined in it. When a message comes, it evaluates the message against each of the rules and check which rule is applied to it. When it finds applicable rule(s), it checks for the targets for each of the rules and sends the message to the assigned targets. There is a possibility of multiple rules and targets be applied to a single message

*   Warranties message delivery within 24 hours, So, it's also a push service

*   Not restricted to only AWS, 3rd party provider events can be used too.

## Kinesis
*   Ingest data from anywhere into your AWS Cloud in real time.

*   Has very high throughput

*   Stores the data for maximum of 7 days

*   Takes in data from many services