# Microsoft Entra ID Identity & Access Lab

A hands-on identity lab built in my own Microsoft Entra ID tenant. Set up separate owner, admin, help desk, and employee accounts, then worked through the stuff a help desk actually deals with day to day: a user who can't sign in, reading the sign-in logs to find out why, resetting their password, and offboarding someone who "quit."

First time working in Azure after doing everything else in AWS. The goal was to learn identity the way it's handled in a real org, not just click through a tutorial.

**Stack:** Microsoft Entra ID · Azure for Students · Security defaults (MFA) · Microsoft Authenticator · Sign-in logs · Microsoft 365 admin center

## Setup

Signed up for Azure for Students with a personal account and verified student status through my school email. That created a tenant I fully control, so I'm the Global Administrator instead of a regular user in someone else's directory.

![Azure for Students credit](screenshots/01-azure-for-students-credit.jpg)

## Accounts

Split things up the way a real company would, instead of doing everything from one all-powerful account:

| Account | Role | Purpose |
|---|---|---|
| Jordan Drake (Owner) | Global Administrator | Personal account that created the tenant. Kept as the break-glass account |
| Jordan Drake (Admin) | Global Administrator | Day-to-day admin work, and the account used for the M365 admin center |
| Jordan Drake (Help Desk) | Helpdesk Administrator | Password resets for regular users, nothing more |
| Robert Williams | None | Regular employee, used as the "user" in every scenario |

![All users](screenshots/02-all-users.jpg)

![Global Administrator role](screenshots/03-admin-global-admin-role.jpg)

![Helpdesk Administrator role](screenshots/04-jordan-helpdesk-admin-role.jpg)

## Security Model

```
Owner / Admin   → full control of the tenant (Global Administrator)
Help Desk       → can reset passwords for non-admin users only
Robert          → can sign in, no admin roles, no access to Azure resources
```

Security defaults are on, so every account has to register MFA with Microsoft Authenticator on first sign-in.

## Scenario 1: "I can't log in" ticket

Robert tries to sign in with the wrong password a few times.

![User sign-in error](screenshots/05-user-signin-error.jpg)

Instead of guessing, I checked the sign-in logs. Robert's attempts show up as `Failure` with error code **50126** (invalid username or password), so the problem is the password and not the account, MFA, or a policy.

![Sign-in logs showing 50126](screenshots/06-signin-logs-failed-attempt.jpg)

Signed in as the help desk account and reset his password. Entra hands back a temporary password that forces a change on next sign-in.

![Help desk password reset](screenshots/07-helpdesk-password-reset.jpg)

Robert signs in with the temp password, sets a new one, and he's back in. The "Don't have a subscription?" page is expected, since he has no access to any Azure resources.

![Access restored](screenshots/07b-user-access-restored.jpg)

## Scenario 2: Testing least privilege

From the help desk account, tried resetting the Admin account's password. Blocked, because Helpdesk Administrators can't reset passwords for other admins.

![Help desk blocked on admin](screenshots/08-helpdesk-blocked-on-admin.jpg)

The help desk account also couldn't disable users. The "Account enabled" setting was grayed out until I switched to the Admin account. Same idea: the help desk role only gets what it needs for password tickets.

## Scenario 3: Offboarding

Pretended Robert left the company. Revoked his sessions first so he gets kicked out everywhere immediately.

![Revoke sessions](screenshots/09-offboarding-revoke-sessions.jpg)

Then disabled the account instead of deleting it, which keeps the account around for records while blocking any new sign-ins.

![Account disabled](screenshots/10-offboarding-account-disabled.jpg)

Trying to sign in as Robert now gets blocked:

![Sign-in blocked](screenshots/11-offboarding-signin-blocked.jpg)

And the sign-in logs confirm it with error code **50057** (user account is disabled):

![Sign-in logs showing 50057](screenshots/11b-signin-logs-account-disabled.jpg)

## Error codes I ran into

| Code | Meaning | Where it came from |
|---|---|---|
| 50055 | Password expired | First sign-in with a temporary password |
| 50072 | User must enroll in MFA | Security defaults forcing MFA setup |
| 50126 | Invalid username or password | The wrong-password attempts in Scenario 1 |
| 50057 | User account is disabled | Signing in after offboarding |
| 50140 | "Stay signed in?" prompt | Normal interruption, not an actual error |
| 50203 | User hasn't registered the Authenticator app | Admin account's first MFA setup |

## Notes from the build

- The personal account that owns the tenant couldn't use the Microsoft 365 admin center at all ("Login is not supported for consumer users without business presence"). Had to create a proper tenant account with Global Administrator to get in
- Licenses and trials moved out of the Azure portal and into the M365 admin center, so the Try/Buy button in Entra was grayed out
- Tried to start the Entra ID P1 trial for Conditional Access and dynamic groups, but billing profile creation kept failing on the brand-new tenant. Moved on with the free tier instead of retrying endlessly
- Got a 401 "You don't have access" partway through because I was still signed in as a test user in the same browser. After that, test users only ever went in incognito windows
- Disabling Robert's account didn't kick him out right away since he still had an active session. Disabling alone isn't enough, you have to revoke sessions too
- The first "blocked" test showed a passkey error instead of the disabled message, because the sign-in page tried a passkey in a private window. Had to choose "Use your password" to get the real result
- Sign-in logs took roughly 5 to 15 minutes to show new attempts, so an empty log right after a failed sign-in doesn't mean nothing happened

## What's next

Get the Entra ID P1 trial working so I can add Conditional Access policies, a dynamic group based on department, and self-service password reset. After that, I want to connect this to my ServiceNow lab so each scenario starts as a real ticket and closes with work notes.

---

Built by [Jordan Drake](https://github.com/jdrakegit) · [LinkedIn](https://www.linkedin.com/in/jordan-drake-a95471397)
