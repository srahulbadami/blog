---
title: GSoC 2020 1st Phase Ends
cover-img: /assets/img/gsoc_ends.jpg
readtime: true
share-img: /assets/img/gsoc_ends.jpg
subtitle: Phase 1 Summary
tags: [GSoC 2020, gsoc, owasp, blt, Bugheist, Bug Hunt]
---

The phase 1 of this year GSoC has came to an end.

During this period my first task was to create a basic architecture/skeleton of webapp. So, as dicussed in the community bonding period I started with the DB Models for the app. 

# First Week

Firstly, I worked on a subscription model,i.e when a company wants to register to organise a hunt they must select a basic plan for the organisation. Earlier the subscriptions were hardcoded in the application itself. So we replaced it with a more dynamic version. 

| Variable | Data Type |
| -------- | -------- |
| name | CharField |
| charge_per_month | Float  |
| feature | Boolean |

The Subscription had 3 fields,i.e the name, charge_per_month and feature. 
* Charge per month defines the charge a domain has to pay to join the Service
* The Feature boolean variable was used to mark it so that it could be shown in the homepage of our website.

Next I added a new field in the existing domain model, by which an admin can be assigned to a particular domain, so they can have all the admin privileges.

Also, I created a model for the sub admins for the domain.

| Variable | Data Type |
| -------- | -------- |
| role | Select Field |
| user | Foreign(User)  |
| domain | Foreign(Domain)   |
| isActive | Boolean   |

The Domain admin had 4 fields,i.e the role, user, domain and isActive. 
* The role field was used to give the user permissions for a domain,i.e hunt admin/superadmin.
* User field to assign user to the table
* Domain field to assign respective domain
* isActive field to find if the user has been removed from the AdminPanel. For removing the user from admin, we could easily delete the particular entry itself, but we went with this flow as this would be good for the analytics point of view.

# Second Week

For the next week I started working on the dashboard designs, I went on the web and saw many security related websites, saw many dashboards and then created a basic mock design which then we agreed to.

So, now I had three dashboards to make.
* The First Dashboard for the Domain Admin Itself
* The Second for Hunters
* And, the third for the Super Admins,i.e the Website Admins.

So, I designed a basic dashboard template and made them common to the all the user types.
Now, the task was relatively easy. I just had to create some forms and then validate it and then perform the respective task

I create the forms and the basic designs for the templates but something was off about the whole process. So, we then had a discussion and then we found that the approach we used to take inputs from the Domain Dashboard was a liitle off. So, we searched some alernate ways to do that and then tried several Markdown Editors. This approach was better than the last. So, we tried to stick with this flow.

# Third Week

In the next week's meeting we discussed a major issue in our design. From the start we only focused on the Domains,i.e user submit issues for domains, domain register in the website and so on. But we forgot that a company can have multiple domains, So there should be a model above the Domain and that table should contain domains in it. So, then we created the Company Table.

This feature costed me a week of my project as I had to change a domain related functions to company related and then add domain related new functionalities to those company, at the end of the week we were successful and then tested the flow on or development server and this flow worked flawlessly.

So, now the flow for a company to join the service was -
1.  Comany wants to join Bugheist, so they fill up a form at the Bugheist website.
2.  Then the Bugheist admin would contact the Company, verify its status and then create a Compony from the Bugheist Admin Panel, Select a free subscription and then add a admin of that company
3.  Now the company can either get a paid subscription and get more features or can work with the free plan.

# Fourth Week
For this week I planned on working on the timezone issue which I was facing while creating a hunt, i.e when user creates a hunt in their timezone the db should save it in UTC and then convert the time zone as per the requested user's timezone. So, I read many articles related to this issue, and then created my own timezone converter,i.e as the user send a post request to save a particular hunt I would also get thier timezone from the browser and then convert it in the backend itself. And the same process worked if the user wanted to see/edit a hunt . But in this approach I found that this method would not work on daylight saving timezones,i,e at some places there would be 1 hour difference between the expected and the acutal time zone recieved. For this we are yet to have a discussion and them find a method to fix this issue.

# 1st Phase Ends
Over the past month, I worked on this project and got and unexpected result, Let's hope it gets completed in the next two months.
