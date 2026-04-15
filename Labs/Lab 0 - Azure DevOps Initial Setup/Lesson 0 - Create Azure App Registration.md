# Lesson 0 - Create an Azure App Registration for Dataverse and Power Platform

## Objective

In this lesson you will create a Microsoft Entra app registration in the Azure portal that can be used by Power Platform ALM tooling, Azure DevOps pipelines, Dataverse, and Dynamics 365 CE environments.

By the end of this exercise you will have:

1. A single-tenant confidential client app registration.
2. A client secret for workshop use.
3. A Dataverse application user in your target environment.
4. The System Administrator security role assigned to that application user.

Screenshots in this lesson are based on current Microsoft Learn portal imagery. Your tenant branding and minor navigation labels may differ slightly.

> Important
> The app registration itself does not automatically have access to Dataverse. The effective privileges come from the Dataverse application user that you create in each environment and the security roles you assign to it.

## What You Are Building

For this workshop you are creating a server-to-server (S2S) identity for automation. This is the identity your pipelines and tools will use instead of a named interactive user.

For the workshop scenario, one app registration can be reused across multiple Dataverse environments in the same tenant. You must still create an application user in each environment and assign the required security role in each one.

## Before You Start

You need the following:

| Requirement | Why it is needed |
| --- | --- |
| Access to the correct Microsoft Entra tenant | The app registration must be created in the same tenant as the Dataverse environments you want to access. |
| Permission to create app registrations | A dependable minimum is Application Developer. Cloud Application Administrator, Application Administrator, or Global Administrator also work. |
| Permission to create application users in the target Power Platform environment | You need this to bind the app registration to Dataverse. |
| A target Dataverse environment | You will create the application user in this environment. |
| An account that can assign the System Administrator security role in the environment | This gives the app enough privileges for the workshop labs. |

## Values to Record

Capture these values as you go. You will need them again in later labs.

| Value | Example | Where you get it |
| --- | --- | --- |
| App Registration Name | `pp-devops-workshop-s2s` | Entered by you |
| Directory (tenant) ID | `aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee` | App registration Overview |
| Application (client) ID | `11111111-2222-3333-4444-555555555555` | App registration Overview |
| Client Secret Value | `xxxxxxxxxxxxxxxx` | Certificates & secrets page |
| Client Secret Expiry Date | `2027-04-09` | Certificates & secrets page |
| Dataverse Environment URL | `https://org12345.crm11.dynamics.com` | Power Platform admin center or maker portal |

## Step 1 - Create the App Registration in the Azure Portal

1. Open the Azure portal at [https://portal.azure.com](https://portal.azure.com) in a private browser session.

![Open the Azure portal](./Media/Lesson%200/Step%201/OpenAzurePortal.png)

*Screenshot: Opening the Azure portal.*

2. Sign in with an account in the same tenant as your Power Platform environments.

![Sign In](./Media/Lesson%200/Step%201/SignIn.png)

*Screenshot: Signing in.*

![Enter password](./Media/Lesson%200/Step%201/EnterPassword.png)

*Screenshot: Entering password.*

![Proceed through MFA](./Media/Lesson%200/Step%201/ProceedThroughMFA.png)

*Screenshot: Proceeding through MFA.*

![Choose whether to remain signed in](./Media/Lesson%200/Step%201/ChooseWhetherToRemainSignedIn.png)

*Screenshot: Choosing whether to remain signed in.*

3. In the portal search bar, search for `Microsoft Entra ID` and open it.

![Search for and open Microsoft Entra ID](./Media/Lesson%200/Step%201/SearchForAndOpenMicrosoftEntraID.png)

*Screenshot: Searching for and opening Microsoft Entra ID.*

4. In the left navigation, select `App registrations` under the `Manage` section.

![Select App Registrations](./Media/Lesson%200/Step%201/SelectAppRegistrations.png)

*Screenshot: Select App Registrations.*

5. Select `+ New registration`.

![Select New registration](./Media/Lesson%200/Step%201/SelectNewRegistration.png)

*Screenshot: Select + New registration.*

6. Complete the registration form with the following values:

	| Setting | Value for the workshop |
	| --- | --- |
	| Name | Use a meaningful name such as `pp-devops-workshop-s2s` |
	| Supported account types | `Single tenant only - <Your Tenant Name>` |
	| Redirect URI | Leave blank for this workshop scenario |

![Create App Registration](./Media/Lesson%200/Step%201/CreateAppRegistration.png)

*Screenshot: Create App Registration.*

7. Select `Register`.
8. On the `Overview` page, copy and save these two values:
	- `Application (client) ID`
	- `Directory (tenant) ID`

![Copy App Registration Details](./Media/Lesson%200/Step%201/CopyAppRegistrationDetails.png)

*Screenshot: Copy App Registration Details.*

### Notes for the Workshop

- Use `single tenant` unless you have a specific reason to support multiple tenants.
- Do not configure a redirect URI for this confidential client workshop scenario.
- Do not enable public client flows for this lesson.
- Do not add Microsoft Graph or other high-privilege API permissions just to make Dataverse automation work.

## Step 2 - Create a Client Secret

1. In your app registration, go to `Certificates & secrets`.
2. Select the `Client secrets` tab.
3. Select `+ New client secret`.
4. Enter a description such as `Power Platform DevOps Bootcamp`.
5. Choose an expiry that matches your organization policy.
	- Microsoft recommends short-lived secrets and regular rotation.
	- For a workshop or lab, choose a duration that will not expire before you complete the exercises.
6. Select `Add`.
7. Immediately copy the `Value` column for the new secret and store it safely.

> Important
> Copy the secret `Value`, not the `Secret ID`. You will not be able to see the secret value again after you leave or refresh the page.

![Client secret in Certificates and secrets](https://learn.microsoft.com/en-us/entra/identity-platform/media/quickstart-register-app/add-client-secret.png)

*Screenshot: Creating and recording a client secret in the Certificates & secrets pane.*

## Step 3 - Decide Whether You Need API Permissions

For this workshop scenario, the answer is normally `no`.

For a confidential client used with Dataverse server-to-server authentication:

- The critical configuration is the Dataverse application user.
- The critical authorization is the Dataverse security role.
- You do not need to add delegated `user_impersonation` permissions for normal pipeline-based Dataverse access.

That delegated permission is commonly used in interactive user sign-in scenarios, not this workshop's S2S automation pattern.

## Step 4 - Create the Dataverse Application User

Now bind the app registration to the Dataverse environment.

1. Open the Power Platform admin center at [https://admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com).
2. Sign in with an account that can manage the target environment.
3. Select `Manage`.
4. Select `Environments`.
5. Open the environment you want this app to access.
6. Select `Settings`.
7. Go to `Users + permissions`.
8. Select `Application users`.
9. Select `+ New app user`.
10. Select `+ Add an app`.
11. Search for the app registration you created earlier.
12. Select the app and then select `Add`.
13. Set the `Business Unit`.
	 - In most workshop environments, the root business unit is fine.
14. Enter an email address.
	 - Use a sensible service-style address if your organization has a naming standard.
	 - For a lab, any appropriate valid-looking administrative address pattern is usually sufficient.
15. Assign the `System Administrator` security role.
16. Select `Create`.

> Note
> Some Microsoft documentation and older tenants show an `S2S` shortcut on the environment details page. In the current admin center experience, the supported route is through `Settings` > `Users + permissions` > `Application users`.

## Step 5 - Repeat for Every Environment Needed by the Workshop

If your bootcamp uses three environments such as Development, Test, and Production, repeat only the `application user` step for each environment.

You usually do not need three separate app registrations if:

- all environments are in the same tenant
- the same automation identity is acceptable for all three environments

You do need a separate application user in each environment.

## Step 6 - Verify the Configuration

Confirm the following before moving on:

| Check | Expected result |
| --- | --- |
| App registration exists in Microsoft Entra ID | Yes |
| Client secret was copied and stored | Yes |
| Tenant ID and Client ID were recorded | Yes |
| Application user exists in the Dataverse environment | Yes |
| Application user has `System Administrator` | Yes |

If you want to do a quick technical verification later with Power Platform CLI, the authentication pattern will look like this:

```powershell
pac auth create --url https://yourorg.crm.dynamics.com --applicationId <client-id> --clientSecret <client-secret> --tenant <tenant-id>
```

If the app user and security role are configured correctly, this authentication should succeed.

## Common Mistakes

### Mistake 1 - Creating the app registration in the wrong tenant

If the app is not in the same tenant as the Dataverse environment, you will not be able to bind it correctly as an application user.

### Mistake 2 - Copying the Secret ID instead of the Secret Value

Only the `Value` works as the client secret.

### Mistake 3 - Forgetting to create the Dataverse application user

This is the most common issue. The Azure app registration alone is not enough.

### Mistake 4 - Assigning too little privilege in Dataverse

For this workshop, use `System Administrator` to avoid permission-related noise during the labs.

### Mistake 5 - Adding unnecessary API permissions

For the workshop S2S pattern, extra delegated permissions do not replace the Dataverse application user and are usually not the missing step.

## Security Guidance

For the workshop, a client secret is acceptable. For production, prefer a certificate or managed identity where supported.

Minimum good practice:

1. Keep the secret in Azure Key Vault, Azure DevOps secret variables, or another approved secret store.
2. Do not paste the secret into source control.
3. Rotate the secret before it expires.
4. Use separate application users and role assignments per environment.
5. Review whether `System Administrator` is appropriate outside a workshop context.

## Summary

You now have:

1. A Microsoft Entra app registration.
2. A client ID, tenant ID, and client secret.
3. A Dataverse application user bound to that app registration.
4. System Administrator rights in the environment for workshop automation.

This is the identity you will use in later labs for Power Platform ALM and Azure DevOps pipeline authentication.

## Reference Links

- [Register an application in Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app)
- [Add and manage application credentials in Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/identity-platform/how-to-add-credentials?tabs=client-secret)
- [Register an app with Microsoft Entra ID for Dataverse](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/walkthrough-register-app-azure-active-directory#confidential-client-app-registration)
- [Manage application users in the Power Platform admin center](https://learn.microsoft.com/en-us/power-platform/admin/manage-application-users)
