# Grand Cheese Race

## Preface
Thanks for applying to Web Development at Berkeley & for taking the time to work on this technical project. Projects are due Wednesday, September 7th by 11:59 PM.

This project is designed for **you** to gauge whether you want to apply to the **bootcamp** or **industry** branch. Completing all checkpoints for the branch you are applying to is highly preferred, but don't worry if you aren't able to finish everything! As an estimate, you shouldn't need to spend any longer than ~3-4 hours on this project -- we don't want this to be a huge burden on you.


## Clarifications
None yet!

## Submission Instructions

Welcome to WDB's backend project for development branch applicants — Fall 2022 👋

Make sure you read this ENTIRE DOCUMENT, especially these instructions, carefully before you start. If you have any questions
please reach out to [our email](webatberkeley@gmail.com).

To submit your project, please place your submission into a GitHub repo that is set to private. You
will be submitting your code on [Gradescope](https://www.gradescope.com/). If you do not have a
Gradescope account, please create one and if you are unable to create one, please email us
immediately. The Gradescope course code is `4V22DJ`. You will see two different assignments:
`Frontend Project` and `Backend Project`. _Please only submit to Backend Technical Project._ You can ignore Frontend Technical Project.

The technical project will be due by Wednesday, 9/7 at midnight. We will be unable to respond to clarification emails sent in after then. so if you have any questions about the project, please let us know before then (we will be hosting technical project office hours in our club recruitment Discord, which you can join [here](https://linktr.ee/webdevatberkeley)).

Also, this page may potentially keep changing if we get some frequently asked questions, so keep this repository bookmarked and check back on it every now and then! If there are major changes however, we'll make sure to email you about those.

## Introduction

The Grand Cheese Race has been the most elite sporting event for thousands of years. Every year, each country sends one honorable representative to compete for the Double Gloucester cheese. This year, for the first time in history, all living previous victors are coming to compete for the Golden Double Gloucester cheese. Our client is organizing the Grand Cheese Race and wants you to build a service to:

1. allow runners to register
2. get all runners
3. allow spectators to gift competitors of their choice (think Hunger Games-style)
4. allow competitors to use gifted items as speedups/delays
5. simulate *n* seconds taking place
6. get metadata on which items have been gifted (**Industry only**)

In particular, they want you to build an API capable of handling all of the functionality mentioned above. 

_**NOTE: THERE ARE SEPARATE TASKS FOR BOOTCAMP VS. INDUSTRY**_

# API Specification

The client has listed a comprehensive set of rules/business logic for the event (_make sure you read through this carefully_)


## Register Runner
A Runner can sign up for races at any point. When they sign up, they will begin at the start line with a user-specified base speed (in m/s).

Example request:

```
POST /runner

{
    "name": "Anjan",
    "speed": 5,  
}
```

## Get All Runners
This is a self-explanatory route — simply return a list of names of all Runners currently registered (any order is fine).

Example request:

```
GET /runner
```

Example response:

```
{
    "runners": [
        "Nico",
        "Vaikundh",
        "Danielle",
        "Anjan",
    ]
}
```

## Send Gift
A spectator can send a gift to any Runner, and for free too! However, if the name of the spectator has the same starting letter as the name of the gift, then the gift transaction will fail. They can choose from a predefined list of gifts, which you can access in one of two ways:
  - **Bootcamp Checkpoint**: you can directly hard-code the values from "db.json" (feel free to morph it into whichever data structure works best for you).
  - **Industry Checkpoint**: the predefined list is the same, but you should reference it at at http://localhost:3000/gifts (see the section "json-server" for more information). Note that we will be checking to make sure you do not hard-code the items in this predefined list — treat it as an API endpoint!

If the gifting fails (because of the first letter criteria), return some form of error message (you can choose an appropriate response code and message).

Example request:

```
POST /gift

{
    "name": "Mystery Pills",
    "spectator": "Emir",
    "runner": "Danielle",
}
```

## Use Gift
A Runner can use any gift in their inventory at any time. When they use the gift, their base velocity should be internally updated to reflect the boost from the gift (just add the boost value).
  - **Bootcamp Checkpoint**: the above functionality is enough
  - **Industry Checkpoint**: the same boost cannot be used 4 times in a row, globally. This means that if the last 3 uses of gifts (across all Runners) have been "Bread", for example, then the next gift to be used cannot be "Bread" again.

If there is any issue in using the boost, return some form of error message (you can choose an appropriate response code and message).

Example request:

```
POST /use

{
    "runner": "Anjan",
    "boost": "Bread",  
}
```

## Simulate Race Time
The client should be able to simulate passage of time. Based on the user-specified # of seconds, the race "fast-forwards" and each Runner's position should update accordingly based on their current stats.

A response should then be passed back with a list of names of all Runners, in sorted order based on their position (first place, second place, etc.)

Example request:

```
POST /sim

{
    "seconds": 10,  
}
```

Example response:

```

{
    "runners": [
        "Danielle",
        "Vaikundh",
        "Nico",
        "Anjan",
    ]  
}
```

## Get Gift Metadata
Note: This endpoint is only a requirement for the **Industry checkpoint**. If you are only looking to apply for the Bootcamp, you can skip this (although feel free to work on it if it sounds interesting)!

When metadata on gifts is requested, we will return an unordered mapping from each giftable item to how many times it has been **gifted** (not how many times it has been **used**, necessarily).

Example request:

```
GET /gift/metadata
```

Example response:

```

{
    "gifts": {
        "Bread": 1,
        "Water": 3,
        "Mystery Pills": 0,
        "Cookie": 5,
        "Cheese Touch": 1,
    }
}
```

## Note: json-server
For the industry checkpoint in "Send Gift", you will need to spin up a localhost server to provide the API endpoint with valid gifts. To do so, please follow the instructions below, or at [this link](https://github.com/typicode/json-server):

Install JSON Server 

```
npm install -g json-server
```

Start JSON Server

```bash
json-server --watch db.json
```

Now if you go to [http://localhost:3000/gifts](http://localhost:3000/gifts), you should see the gifts. 

Since this JSON server will be using up the :3000 port, make sure to use a different one for your backend server.

# More Examples

In case it helps, here's a couple of example scenarios of how the API might be used:

1. Register a few runners:

```
Request: POST /runner
{
    "name": "Anjan",
    "speed": 5,  
}
```
```
Request: POST /runner
{
    "name": "Vaikundh",
    "speed": 6,  
}
```
```
Request: POST /runner
{
    "name": "Danielle",
    "speed": 9,  
}
```
```
Request: POST /runner
{
    "name": "Nico",
    "speed": 7,  
}
```

2. Get all runners to make sure they show up

```
Request: GET /runner
```
```
Response:
{
    "runners": [
        "Nico",
        "Vaikundh",
        "Danielle",
        "Anjan",
    ]
}
```

3. Send some gifts, with some succeeding and some failing

**This request should succeed:**
```
Request: POST /gift

{
    "name": "Water",
    "spectator": "Emir",
    "runner": "Danielle",
}
```

**This request should succeed:**
```
Request: POST /gift

{
    "name": "Cheese Touch",
    "spectator": "Kate",
    "runner": "Nico",
}
```

**This request should fail (first-letter criteria):**
```
Request: POST /gift

{
    "name": "Water",
    "spectator": "William",
    "runner": "Vaikundh",
}
```
**This request should succeed:**
```
Request: POST /gift

{
    "name": "Water",
    "spectator": "Cindy",
    "runner": "Vaikundh",
}
```

**This request should succeed:**
```
Request: POST /gift

{
    "name": "Water",
    "spectator": "Abdul",
    "runner": "Anjan",
}
```

**This request should succeed:**
```
Request: POST /gift

{
    "name": "Water",
    "spectator": "Cindy",
    "runner": "Nico",
}
```


4. Query for gift metadata:

```
Request: GET /gift/metadata
```

```
Response: {
    "gifts": {
        "Bread": 0,
        "Water": 4,
        "Mystery Pills": 0,
        "Cookie": 0,
        "Cheese Touch": 1,
    }
}
```

5. Simulate passage of time

```
Request: POST /sim

{
    "seconds": 10,  
}
```

Example response:

```

{
    "runners": [
        "Danielle",
        "Nico",
        "Vaikundh",
        "Anjan",
    ]  
}
```

6. Some Runners will use gifts
```
POST /use

{
    "runner": "Anjan",
    "boost": "Water",  
}
```
Anjan's speed should now be updated internally.

7. Simulate passage of time (again)

```
Request: POST /sim

{
    "seconds": 30,  
}
```

Example response:

```

{
    "runners": [
        "Anjan",
        "Danielle",
        "Nico",
        "Vaikundh",
    ]  
}
```

8. Use remaining gifts (some will fail, if you did the Industry Checkpoint — if you did the Bootcamp Checkpoint, all will succeed).
```
POST /use

{
    "runner": "Anjan",
    "boost": "Water",  
}
```

**This request should succeed:**
```
Request: POST /use

{
    "runner": "Danielle",
    "boost": "Water",  
}
```

**This request should succeed:**
```
Request: POST /use

{
    "runner": "Vaikundh",
    "boost": "Water",  
}
```

**This request should fail (industry checkpoint), no 4-in-a-row rule:**
```
Request: POST /use

{
    "runner": "Nico",
    "boost": "Water",  
}
```

**This request should succeed:**
```
Request: POST /use

{
    "runner": "Nico",
    "boost": "Cheese Touch",  
}
```



9. Simulate passage of time one last time.

```
Request: POST /sim

{
    "seconds": 1000,  
}
```

Example response:

```

{
    "runners": [
        "Nico",
        "Danielle",
        "Vaikundh",
        "Anjan"
    ]  
}
```

## Some closing thoughts

Some of these routes are naturally harder than others. If you aren't able to finish all of the routes for the project, **don't worry**! It's supposed to be challenging, and we don't expect everyone to finish it. We have created separate checkpoints for the bootcamp vs. industry, so you don't necessarily need to complete the full project in order to move on in this round :)

Note: we **highly** recommend using MongoDB as your database for this project, although if you don't have experience with it, any NoSQL database is also a great alternative. If none of those work however, you are still welcome to use other alternatives.

It is also encouraged to use JavaScript/TypeScript with Node.js for your backend, but it is completely fine if you would rather use a different stack.

## Design Doc

In addition to building out this API, you will need to write up a short design doc (designdoc.md). We don't intend for this to take very much time, but we want to hear some of the choices you made and why. To be specific, here are some points you might want to talk about:

- why did you choose to organize your data schemas/models in this particular way?
- can talk a bit about the "harder" routes that you worked on — harder is completely subjective, so feel free to get creative here!
- how did you decide on certain response codes?

This should be at most a page, so feel free to be brief!

## Assumptions

There are many details that are left intentionally vague. Though you are very much welcome to
email us to ask for clarifications, we will most likely tell you to use your best judgement.
Because of this, feel free to create a `assumptions.md`, where you can type out and
voice any assumptions you made throughout this project. We also _highly_ encourage you to
write out your own documentation to this API and provide us a glimpse of your rationale
behind every design decision.