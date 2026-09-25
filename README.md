
# Microsoft Entra ID Identity Lab

An identity lab built in my own Entra ID tenant. I set up a few accounts with different levels of access, then ran through the kind of tickets a help desk handles every day: a user locked out of their account, a password reset, and offboarding someone who left.

Everything else I've built has been in AWS, so this was my first real time working in Azure.

**Stack:** Microsoft Entra ID · Azure for Students · Security defaults (MFA) · Microsoft Authenticator · Sign-in logs

## Setup

Signed up for Azure for Students and verified with my school email. That gave me my own tenant where I'm the Global Administrator, instead of being a regular user in my school's directory.

![Azure for Students credit]<img width="3412" height="1884" alt="01-azure-for-students-credit" src="https://github.com/user-attachments/assets/9fd7fd33-fe43-4856-8675-71d191c74315" />

I set up four accounts instead of doing everything from one:

- **Jordan Drake (Owner):** the personal account that created the tenant, kept as a backup way in
- **Jordan Drake (Admin):** Global Administrator for normal admin work
- **Jordan Drake (Help Desk):** Helpdesk Administrator, can only reset passwords for regular users
- **Robert Williams:** a regular employee with no admin roles

![All users](screenshots/02-all-users.jpg)

![Global Administrator role](screenshots/03-admin-global-admin-role.jpg)

![Helpdesk Administrator role](screenshots/04-jordan-helpdesk-admin-role.jpg)

Security defaults are on, so every account has to set up MFA with Microsoft Authenticator the first time it signs in.

## Password reset ticket

Robert signs in with the wrong password a few times.

![User sign-in error](screenshots/05-user-signin-error.jpg)

The sign-in logs show his attempts failing with error code `50126`, which means a bad username or password. So the problem is the password, not MFA or the account itself.

![Sign-in logs](screenshots/06-signin-logs-failed-attempt.jpg)

From the help desk account, I reset his password and gave him the temporary one.

![Password reset](screenshots/07-helpdesk-password-reset.jpg)

He signs in, sets a new password, and he's back in. The "Don't have a subscription?" page is expected since he doesn't have access to any Azure resources.

![Access restored](screenshots/07b-user-access-restored.jpg)

## Least privilege

I tried resetting the Admin account's password from the help desk account, and it got blocked. The Helpdesk Administrator role can't touch other admins.

![Help desk blocked](screenshots/08-helpdesk-blocked-on-admin.jpg)

It also couldn't disable users. The "Account enabled" checkbox was grayed out until I switched over to the Admin account.

## Offboarding

For this one, Robert "left the company." First I revoked his sessions so he'd get signed out everywhere.

![Revoke sessions](screenshots/09-offboarding-revoke-sessions.jpg)

Then I disabled the account instead of deleting it, so it still exists for records but can't sign in.

![Account disabled](screenshots/10-offboarding-account-disabled.jpg)

Signing in as Robert now gets blocked:

![Sign-in blocked](screenshots/11-offboarding-signin-blocked.jpg)

And the logs show error code `50057`, meaning the account is disabled.

![Sign-in logs disabled](screenshots/11b-signin-logs-account-disabled.jpg)

## Things I ran into

- My personal account couldn't get into the Microsoft 365 admin center at all, since it doesn't allow consumer accounts. I had to use the Admin account I created in the tenant
- I tried starting the Entra ID P1 trial for Conditional Access and dynamic groups, but setting up billing kept failing on the new tenant, so I stuck with the free tier for now
- I got a "You don't have access" error partway through because I was still signed in as a test user in the same browser. After that I only used test accounts in incognito
- Disabling Robert's account didn't sign him out right away since he still had an active session. That's why revoking sessions matters
- New sign-ins took about 5 to 15 minutes to show up in the logs

Other error codes that showed up in the logs: `50055` (temporary password expired on first sign-in) and `50072` (user has to set up MFA).

## What's next

Get the P1 trial working so I can add Conditional Access, a dynamic group based on department, and self-service password reset. I also want to tie this into my ServiceNow lab so each scenario starts as a ticket and gets closed out with notes.

---

Built by [Jordan Drake](https://github.com/jdrakegit) · [LinkedIn](https://www.linkedin.com/in/jordan-drake-a95471397)
