---
track: "React-Fundamentals"
title: "Project Three"
type: "Project Prompt"
topics: "Unit Projects"
---

# Project Three: A MERN Stack Application:

<br>
<br>
<br>


## Overview

**You’ve come a long way, and it's time to show it.** This will be your most advanced project to date.

**Before you start working** on the planning for your project, be sure to review your idea with an instructional team to ensure that it both:

- **Meets the minimum requirements**, and
- **Is reasonably scoped**

<br>
<br>
<br>


## Necessary Deliverables

#### 1) Project Planning

A project consists of more than just code.

This project requires **planning** organized within a **Trello board** with the following **lists**:

- **Icebox**: Holds user stories that have yet to be moved to the _Current/MVP_ list. All user stories are originally put into the _Icebox_, including both MVP and wish list stories. 

- **Current/MVP**: Holds user stories that must be completed to meet the minimum project requirements (MVP). Once the MVP has been met, additional user stories may be moved from the _Icebox_.

- **Completed**: Hold completed user stories. 

- **Wireframes**: Sketches of each screens's user interface for the major functionality of the application.

- **Entity-Relationship-Diagram (ERD)**: A diagram of the app's models (one per data entity) and the relationships between them.

> User stories need to be formed properly using this template:<br>`As a <role>, I want <feature> so that <reason>`. The _reason_ is optional if it's patently obvious.

<br>
<br>
<br>


#### 2) Project Source Control & README

The project's source code must be hosted on a personal **GitHub repository**.

The repo is to contain **frequent commits** dated from the beginning of the project through its completion.

The project must include a **`README.md`** file with the following sections:

- **Introduction**: A paragraph used to introduce interested parties to the project and needs to include one or more screenshots.

- **Technologies Used**: A list of all technologies, libraries, APIs, etc. used in the project.

- **Getting Started**: Links to the project's planning (Trello board)  and the **deployed app** on Heroku.

- **Unsolved Problems**: List any unsolved issues.

- **Future Enhancements**: Identify future features and enhancements planned for the project.

<br>
<br>
<br>


#### 3) Application Technical Requirements/Deliverables

- A **working** full-stack, single-page application hosted on Heroku.

- Incorporate the technologies of the **MERN-stack**:
	- MongoDB/Mongoose
	- Express
	- React
	- Node

- **Have a well-styled interactive front-end** that communicates with the **Express** backend via AJAX.

- Implement token-based **authentication**.  Including the ability of a user to sign-up, log in & log out.

- Implement **authorization** by restricting CUD data functionality to authenticated users. Also, navigation should respond to the login status of the user.

- **Have a comprehensive feature-set**. For example, **if the app does not include full-CRUD** data operations, **ensure the addition** of one or more of the following:
	
	- Consume a third-party API.
	- Include _admin_ features.
	- Utilize multi-user, real-time communications (difficult and time consuming)


<br>
<br>
<br>


#### 4) Project Presentation

You will have 10 minutes to present and demonstrate the following:

1. Introduce your project by paraphrasing its README.

2. Click the link in the README to open the deployed app on Heroku.

3. Demonstrate the application's authentication features by signing up a new user, logging out that user, then logging in with your preferred user.

4. Demonstrate your app's main features.

5. Share/discuss the following code (not line-by-line):

	- The main/central Mongoose model
	- Your "favorite" Express controller method
	- Your "favorite" React component
	- The client-side routing

6. Share the experience by answering the following:

	- What was your biggest challenge?
	- What are your key learnings/takeaways?

Following your presentation, there will be a brief Q & A period and optional instructor feedback.

<br>
<br>
<br>



## Project Assistance

- At this stage of SEIR, being able to find the the answers to your development issues is of paramount importance. 

- Feel free to use all resources available and collaborate with others.

- If you do seek assistance in Slack, explain the issue, include screenshots, and explain what you've done to solve the issue on your own.

<br>
<br>
<br>




## Suggestions to Get Started

- Don’t get too caught up in too many awesome features – simple is better. Favor fewer features that look/feel impressive over numerous clunky/sloppy features.

- Implement the `User` model and authentication first. Then implement the "As a visitor, when I browse to the app, I want..." user story.

- Prioritize user stories and code them accordingly.

- When implementing a feature, think through the steps that it takes in plain language first. If necessary, write and/or diagram the steps to help guide your coding.

- Follow the steps we've done in class to implement features, beginning with the user's interaction, identifying the proper route, etc. 

- Read the docs for whatever technologies / frameworks / API’s you use.

<br>
<br>
<br>




## Best Practices

-  **Write DRY code.**

- In a SPA, communication with the backend is via AJAX.  Build **RESTful APIs to CRUD your data entities (resources)** and perform other functionality via AJAX.  In a React app, those AJAX calls should be made from "service" modules, not components.

- **Be consistent** with your code style.

- **Clearly name variables and functions** - remember, variables are usually named as **nouns** and functions as **verbs**.

- **Write well-formatted HTML/CSS/JS.** Properly formatting your code makes it more readable. Improperly formatted code infers sloppiness.

- **Comment your code where it makes sense**. Most code is self-documenting, however, comments help explain complicated code.

- If you have any questions regarding these requirements, please feel free to contact your instructional team!