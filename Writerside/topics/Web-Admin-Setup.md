# Web Admin Setup

### Registering an Account
To begin using FluxVend, you need to create an account. Follow the steps below to register:

Visit the FluxVend website at [www.fluxvend.com/registration](https://fluxvend.com/registration). You will be presented with the registration form to fill in your details.
Fill in your details, including your personal and business information, ensuring to use a valid email address. This email address will be used to verify your account.

```plantuml
@startuml
package RegistrationForm [
    Business Name
    ----
    Email Address
    ----
    Contact Number
    ----
    First Name
    ----
    Last Name
]
@enduml
```


Then click the "Register" button to submit your details. 
You will be prompted to verify your email address by clicking on the link sent to your email.


Check your email inbox for an email from FluxVend. Click on the activate button to verify your email address.
This will redirect you to the FluxVend website, where you will be prompted to enter your new username and password.
Once you have successfully activated your account, you will be redirected to the login page.

### Sequence Diagram

```plantuml
@startuml
title FluxVend Web Admin Setup
actor User as U
participant "FluxVend Website" as F
participant "Email Server" as E
participant "FluxVend Database" as D

U -> F: Visit FluxVend Website
F -> U: Registration Form
U -> F: Fill in Details
U -> F: Click Register
F -> E: Send Verification Email
E -> U: Email Verification Link
U -> F: Click on Link
F -> D: Verify Email
D -> F: Email Verified
F -> U: Set Username and Password
U -> F: Login
F -> U: Redirect to Admin Portal
@enduml

```

### Logging In
After you have successfully registered and activated your account, you can log in to the FluxVend Admin Portal using your newly created credentials.
