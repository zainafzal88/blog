---
layout: post
title:  Basics & Building of First Alexa Skill
description: This post contains basic information and walkthrough of how to build your first Alexa skill
date:   2020-06-15
---
I have been intrigued of how we, users, can talk to technology devices and get information. One such device is Amazon Echo which is powered by a cloud based voice service called Alexa

## Basic Concept of Alexa Skills

So, how Alexa skills work is, it consists of three components

1.  Alexa Cloud
2.  Your Backend (normally lambda function)

I'll explain using a diagram:

<p align="center">
  <img src="/assets/images/2020-06-14/how-alexa-skills-work.png">
</p>

### Alexa Cloud
This can be called as the engine of Alexa skill. So, three main things happen in this "engine" when you are building an Alexa skill:

1.  **Speech Recognition**
    This is where your speech is converted to text which is done with some context and accurately too.

2.  **Natural Language Understanding (NLU)**
    This is where the computer understands what the user is saying. NLU is made up of _Utterance_. 
    
    _Utterence_ is _what the user is saying_ to the device for example, "What is the weather" or "Is it hot outside" or "Should I wear a jacket". Ofcourse we all have different ways of saying the same thing. A good skill should be programmed to understand all variations.
    
    What happens in the background is NLU breaks the utterences in two things, an _Intent_ and _Slot_.

    _Intent_ is what _action the user is trying to take_. For example:
    *   What's the weather
    *   Book a flight
    *   Have breakfast

    _Slot_ is _any sort of variable_ or anything that you think won't have a fixed value within the user's utterenace. For example:
    *   What's the weather is England
    *   What's the weather in Melbourne
    
    where England and Melbourne are the slots(variables). There can be multiple slot values for an utterance. For example:
    As there are two "Melbourne" around the world, one in US and the other in Australia, So we can say
    *   What's the weather in Melbourne, US

    where Melbourne and US are slot values

3.  **Speech Synthesis Markup Language (SSML)**
    I will explain this later to make much more sense of the flow.

### Your backend service
This is normally your lambda function however, not limited to that. It can be anything from API to database what ever you like. When you speak to the device (Echo in this case) it will trigger the intent which is then sent to your lambda function(or anything else). Lambda function will then send a text response to Alex Cloud.

Now, We need something to convert the text response into speech so that the Alexa can talk back to us. This will be done by SSML. It's a general technology that makes your voice sounds like a human's voice such as adjusting tone, speed etc of the response.

Now that we have the basics covered, let's build out first Alexa skill

## Building Alexa Skill

I will be building an extremely simple skill called **Cake Walk** which would capture users' birthday and prompt if the user hasn't input all the madatory values.


### Frontend

1.  Go to [AmazonDeveloper](https://developer.amazon.com)

2.  Click **Alexa**

3.  Click **Create Alexa Skills**
    <p align="center">
        <img src="/assets/images/2020-06-14/create-alexa-skill.png">
    </p>

4. Click **Console**
    <p align="center">
        <img src="/assets/images/2020-06-14/click-console.png">
    </p>

5.  Click **Create Skill**
    <p align="center">
    <img src="/assets/images/2020-06-14/create-skill.png">
    </p>

6.  Enter skills name as **Cake Walk** and language as **English (US)**

    <p align="center">
    <img src="/assets/images/2020-06-14/skill-name.png">
    </p>

7. Leave the rest as default and click **Create Skill**

    This will take you to next page

8.  Choose template **Hello World** and click **Continue With Template** on the top right
    <p align="center">
        <img src="/assets/images/2020-06-14/template.png">
    </p>

    It will take couple of minutes to create an Alexa Hosted Skill and prepare the code editor. Your screen should look like this:
    <p align="center">
        <img src="/assets/images/2020-06-14/code-editor.png">
    </p>

The above is known as a **Dashboard**.

The red rectangule showing in the above image is what can be called as build phases:

*   Build    
    You build the frontend of the skill in this section.

*   Code   
    Backend coding of the skill take place.

*   Test   
    Combine the build and code together and test using simulator.

*   Distribution    
    Publishing of a skills takes place here like giving a logo, a company.

*   Certification   
    A process to make sure your the skill does what the owner says it does, fully functional and does error out.

*   Analytics   
    Examine how the skill is being used by users.

9.  Click on **Invocation name**    
    Invocation Name is _what you say to activate a skill_
    <p align="center">
    <img src="/assets/images/2020-06-14/enter-invocation-name.png">
    </p>
10.  Delete the current **HelloWorldIntent**
    <p align="center">
    <img src="/assets/images/2020-06-14/delete-hello-world-intent.png">
    </p>

11. To create a new intent, Click **Add Intent**

    <p align="center">
    <img src="/assets/images/2020-06-14/create-intent.png">
    </p>

12. Enter `CaptureBirthdayIntent` and click **Create Custom Intent**

    <p align="center">
    <img src="/assets/images/2020-06-14/capture-birthday-intent.png">
    </p>

Now we need to enter Sample Utterences. Like we discussed earlier, utterences are what the user says. To give birthdays to the computer we can say for example

*   My birthday is on 6 March 1998
*   I was born on 31 April 1988

There can be many variations but i'll leave it at this. One thing to note is there are be many dates, years, month for birthday and it will be cubersome to manually present these as options to the user. This is where slot values come in. 

The above utterences can be update with slots like:

*   My birthday is on {date} {month} {year}
*   I was born on {date} {month} {year}

where `{date}`, `{month}` and `{year}` are slot values (variables) which store what ever the user stores in theme depending on their data type.

13. Now enter some utterences and click the `+` to add.
    <p align="center">
    <img src="/assets/images/2020-06-14/add-utterence.png">
    </p>
Add another variation.
    <p align="center">
    <img src="/assets/images/2020-06-14/add-utterence-1.png">
    </p>

14. Add data types for the slots

*   Click on `year` and select `AMAZON.FOUR_DIGIT_NUMBER` from the **Slot Type** dropdown

    <p align="center">
    <img src="/assets/images/2020-06-14/slot-data-type-1.png">
    </p>

*   `{year}` as `AMAZON.FOUR_DIGIT_NUMBER`
*   `{date}` as `AMAZON.DATE`
*   `{month}` as `AMAZON.Ordinal`

15. Scroll down on the same page and turn on the **Slot Filling**. 
    Slot filling means that it is mandatory to fill this slot otherwise the intent won't complete.

    <p align="center">
    <img src="/assets/images/2020-06-14/slot-filling-date.png">
    </p>

16. Input `what date of the year were you born` in **Alexa speech prompts**
    
    <p align="center">
        <img src="/assets/images/2020-06-14/date-slot-filling.png">
    </p>

This means that if a user forgets to provide the date, the above message will be spoken. You can add many and Alexa will randomely pick one.

17. Input `i was born on {date}` and just `date` for **User utterences**

    <p align="center">
        <img src="/assets/images/2020-06-14/user-utterences.png">
    </p>

In the above, you can predict what user will say in response to you speech prompts.

18. Do the same for `{month}` and `{year}` (small exercise for you)

19. Go back to `CaptureBirthdayIntent`
    
    <p align="center">
        <img src="/assets/images/2020-06-14/go-back.png">
    </p>

20. Choose `enable auto delegation` from **Dialog Delegation Strategy** dropdown

    <p align="center">
        <img src="/assets/images/2020-06-14/enable-auto-delegation.png">
    </p>

21. Click **Save Model** to save and then **Build Model** to build
    
    <p align="center">
        <img src="/assets/images/2020-06-14/save-and-build.png">
    </p>

    You should see notfication that says `Full Build Successfully` like: 

    <p align="center">
        <img src="/assets/images/2020-06-14/full-build-successful.png">
    </p>

This concludes our frontend now, let's move to backend.

### Backend

1.  Go to **Code**

    <p align="center">
        <img src="/assets/images/2020-06-14/go-to-code.png">
    </p>

Every intent as its own handler at the backend as you can see `HelloWorldIntentHandler` which we removed from the frontend in the beginning. We will be modifying this to match our `CaptureBirthdayIntent`. But first, let's discuss how to backend works with the frontend.

The `LaunchRequesthandler` gets fired up when a user activates a skill saying the `invocation name` in our case it's `Cake Walk` which in turn calls the `LaunchRequest` (shown below). This means your skill has been requested to launch.
<p align="center">
    <img src="/assets/images/2020-06-14/launch-request.png">
</p>

2.  Now let's modify the handler like below:

    <p align="center">
        <img src="/assets/images/2020-06-14/modify-request-handler.png">
    </p>

When we speak our skill invocation name, it will check `canHandle(handlerInput)` to check if the request can be handled. If it can, then it will execute `handle(handlerInput)` which in turn speech prompts `Welcome to Cake Walk! When is your birthday?` If you don't want to keep the session open after Alexa has completed its job, simply remove `reprompt(speakOutput)`.

3.  Click **Save** and **Deploy**
    
    <p align="center">
        <img src="/assets/images/2020-06-14/save-and-deploy.png">
    </p>

If all went well, you should see a notification like below:
    <p align="center">
        <img src="/assets/images/2020-06-14/deployment-success.png">
    </p>

Finally we are done with frontend and backend development. hopefully everything went well for you

## Testing

To test it, you can use both, typing and voice. Howevr, it's highly recommended to use voice. For this test, I will just type but you can test with it with voice if you like. 

1.  Now go to **Test**

    <p align="center">
        <img src="/assets/images/2020-06-14/go-to-test.png">
    </p>

2.  From the **Skill Testing is enable in** dropdown, choose **Development**

    <p align="center">
        <img src="/assets/images/2020-06-14/skill-test-in-development.png">
    </p>

3.  Type your skill invocation name `Cake Walk` and it should display the response we gave in backend step 2.
    
    <p align="center">
        <img src="/assets/images/2020-06-14/testing-1.png">
    </p>

4. If you followed all along correctly, you will see:

    <p align="center">
        <img src="/assets/images/2020-06-14/testing-2.png">
    </p>

### Testing CaptureBirthdayIntent

1.  Go back to Backend (**Code** in the menu bar)
*   Replace `HelloWorldIntentHandler` with `CaptureBirthdayIntentHandler`
*   Replace `HelloWorldIntent` with `CaptureBirthdayIntent`
*   `const speakOut` = `'Thanks'`
    <p align="center">
        <img src="/assets/images/2020-06-14/testing-3.png">
    </p>

2.  Scroll down to `exports.handler = Alexa.SkillBuilders.custom()` and replace `HelloWorldIntentHandler` with `CaptureBirthdayIntentHandler`

    <p align="center">
        <img src="/assets/images/2020-06-14/testing-4.png">
    </p>

3. Click **Save** and **Deploy**

4. Now, test in the simulator

    <p align="center">
        <img src="/assets/images/2020-06-14/testing-intent.png">
    </p>

Well done for making your first Alexa skill. Hopefully you have a good understanding about Alexa now. If you faced any problem, feel free to get in touch and I'll help you out.