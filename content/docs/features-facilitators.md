---
weight: 400
title: "Facilitator Features"
description: "Features available to facilitators of the OpenCourseMatch application"
icon: "tips_and_updates"
author: "OpenCourseMatch"
---

## User Management

Facilitators have access to user management features, allowing them to create, edit, and import user accounts.

The user management overview page can be accessed by navigating to `Participants and tutors` from the facilitator dashboard.
It contains a list of all user accounts, listed with their username, first name, last name and group.

### Create User Accounts

User accounts can be created by navigating to `Participants and tutors` > `Create user`.
In the form, the facilitator must enter the following information:

- **First name** and **Last name**

Furthermore, the user can be assigned to a specific group, which determines the courses they can choose from, given a password, and be assigned to a course as course leader.

After clicking `Save`, the user account is created and a PDF file containing the credentials, the link to the web application, and a QR code for easy access is downloaded.
It can be given to the user either in printed or digital form.

### Edit User Accounts

User accounts can be edited by navigating to `Participants and tutors` and then clicking on the row of the user account in the list.

This opens a form where the facilitator can edit the first name, last name, group, password, and led courses of the user.
After clicking `Save`, the changes are saved, and the PDF file with the credentials is downloaded again.

{{< alert context="info" text="Leaving the password field empty when editing the user will not reset their password. However, the downloaded PDF file does not contain the user's password in this case." />}}

### Edit Course Choices of a User

A user's course choices can be edited by navigating to `Participants and tutors` and then clicking on the row of the user account in the list.

Below the edit form, the current course choices of the user are displayed.
It can be edited by clicking on `Edit choice`, which opens the selection menu.

### Import User Accounts from CSV

Facilitators can import multiple user accounts from a CSV file at once by navigating to `Participants and tutors` > `Import users`.

The CSV file must contain the columns

- **First name**
- **Last name**

and the first line is not ignored.

Besides the CSV file, the facilitator can select a group for the users, which determines the courses they can choose from, and a password for all users.

### Group Actions

Facilitators can perform group actions on multiple user accounts by navigating to `Participants and tutors` > `Group actions`.

In the form, they have to select the affected group.
Next, they can choose to reset the password for all users in the group, move all users to another group, or delete all users in the group.
