---
layout: post
title: Bloccit
thumbnail-path: "img/bloccit.png"
short-description: Bloccit is a Reddit replica that allows users to post, vote on, share and save links and comments.

---

{:.center}
![]({{ site.baseurl }}/img/bloccit.png)

## Explanation

Bloccit is the first project based on Rails that I have created during learning of Bloc curriculum. Its purpose is to help me understand all the basics of creating an application with rails. I completed this project by following the tutorials of the curriculum. After this project, I gained a much better understanding of the key concepts of Restful web service and Rails development. Such as MVC and routing, as well as url redirecting between different views, also the integration of html, css and ruby code. I also learned how to involve TDD development approach into the process.

## Problem

Bloccit is a platform for people to create and publish content. It also allows people to view other people's publication and leave comments.

A key feature Bloccit should have is authentication. People needs to register as member of Bloccit to post. They need to login before they can post and manage their previous post and comments.

Another feature of Bloccit is authorization. Everything on Bloc has to be maintained, Administrators can create, update or destroy any topic and post. While Bloccit members can create, update or destroy only their own post. And Guest who is not registered, can read anything on the site, but can't post until sign up to become a member.

Users are also able to vote on posts. This feature can help users to distinguish the good posts from the bad. It allows users to up or down vote posts. User are also able to save a post as favorite, so they can receive emails as notifications once there is any update on the post or its comments.

Users have their own personal information page. They can view and update their profiles as well as viewing other user's profile.
## Solution
Post and Comment
To create Bloc, the first step is to create models needed for this project. Such as post, comment, topic, user etc. Then assign appropriate associations to finish the object relational mapping. After that, add controllers, views and routes to finally complete each model's resource which is able to interact with by CRUD actions. And so far these will ensure Bloccit to have the frame work for users to create, view, update and delete their posts and comments.


SignUp and SignIn
Instead of using ready to use systems from third party gems, I need to build a authentication system of my own. Use BCrypt gem and Ruby's has_secure_password to secure user's password. BCrypt takes a plain text password and turns it into an unrecognizable string of characters using a hashing algorithm such as MD5. And has_secure_password adds methods to set and authenticate against a BCrypt password. This function abstracts away much of the complexity of obfuscating user passwords using hashing algorithms which we would otherwise be inclined to write to securely save passwords. It also adds an authenticate method automatically to user model. This method ,which will be used for designing the sign in function, determines if a given password is valid for a particular user by computing its digest and comparing the result to password_digest in the database.

Use Ruby's session object to make the sign in function work. A session is just a place to store data during one request that you can read during later requests. The sign in system works like this: when user hits sign in button after filling user email and password, the session controller will look for the same email in the database, then it will call the authenticate method to verify the password that user entered, if user enter the correct password, a new session object will be created and store the entered email and password in it. In the case of no match of email can be found or the password does not match, the session controller will return an error message of invalid email/password combination.

Authorization
Lets recall the authorization rules of Bloccit: Administrator, can create, update or delete any topic or post. Member, can create, update or delete only their own post. Guest, can read anything on the site, but can't post until sign up to become a member. Enum is used to represent different rules in Bloccit. It is a special attribute type whose values map to integers, but can be referenced by name. For example, enum role: [:member, :admin] will use a column in the database named role, but allows us to reference and assign the role using member or admin. This allows us to restrict the roles to only those we've specified (member and admin) while still being easy to work with.

Authorization design can be as easy as adding methods to verify if the user is of a specific rule, and then run the method in the before_action callback in the controller. For example, a authorize_user method is added to the topic controller, it will check if the current user's role is administrator, if not, it will redirect the page to the topic list. Then add the authorize_user method to the callback like this:  before_action :authorize_user, except: [:index, :show]. This means that except index and show action, all other action including new, update and destroy, should go through the authorize_user method before action.This will make sure that only administrators can create, update and delete topics. Things goes similar in post controller and comment controllers.

Voting
A vote model is added to the database and post being the foreign key. the value attribute is either 1 or -1 which represents upvote and downvote. A partial is created show the point of vote of each post.

Emails notifications
We user Rails' action mailer to format emails notifications. We also use the email sending service of SenGrid to send the email. Also the Figaro gem is used to set up environment variables to keep our SendGrid username and password safe.

## Results

The Test-Driven Development method is used for this project. It is the process of writing tests before writing production code. I used to be confused how I can write test for something yet not created. But as my learning process goes on, I get much more comfortable with this method and find that it has a lot of advantages. It allows me to divide problems into small and testable steps, which result in a leaner and more efficient codebase and the ability of catching problems early before they gets bigger. All these helped me to systematize my though and have a better idea of what the application should behave.

We use Rpec for testing Bloccit. It is one of the most popular testing frameworks for developing web applications with Rails. The hard part of Rspec is that it is a huge framework that requires a lot of learning. But I managed to understand most frequently used methods for testing Bloccit during the process.

Another tools that I used for testing is the developer tool provided by google chrome. with developer tool I can get a better view of how my application behaves. It is extremely useful when working with the front end design because it is able to show the result of change dynamically. I don't need to keep altering the code over and over before I fix the problem. Just keep trying different solutions then change the actual code finally after finding  what is the right move.

## Conclusion

I gained substantial knowledge of Rails development after this project. From a basic Rails project to a application with a complete set of functionalities. From setting up minimum UI to custom more complicated Views. It is not just what I've learned to make Bloccit work, but also the thoughts behind the development process, Including the structure of a Rails app, and the glimpse of best practice of Rails development, which will assist me in future application developments.
