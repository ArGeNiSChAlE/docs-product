---
summary: Troubleshooting, errors, warnings, and known issues.  
tags:
---

# Troubleshooting

This document provides more details about errors, warnings, and known issues in the technical preview Use **SEO-friendly URLs for Reactive Web Apps**,

## Errors and warnings

A list of errors and warnings, with tips on how to fix them.

### Invalid page name { #error-30290 }

Service Studio shows **Invalid Page Name Error**:

* Page Name in [screen name] screen contains invalid characters. Please use only digits, letters, or any of these special characters: '-', '~', '/'.

Some characters in the URLs have special uses and you can't use those characters in the **Page Name** property. To fix the error, remove all characters different from letters, digits, dash (`-`), tilde (`~`), or forward slash (`/`).

### Page name contains invalid characters { #error-30296 }
  
Service Studio shows **Invalid Format** errors:

* Invalid Format | Page name can't start or end with special characters. Delete any special characters in the beginning or end of the page name and try again.
* Invalid Format | Page name can't include double slashes "//". Delete any double slashes in the page name and try again.

The **Page Name** property contains double forward slashes (`//`) or starts or ends with one the reserved special characters, and Service Center can't create the URL.

Check the examples of correct and incorrect **Page Names**:

| Correct   | Incorrect                   |
| --------- | --------------------------- |
| `Buy`     | `Buy!`                      |
| `About`   | `\\About`                   |
| `Product` | ` Product` (space at start) |

### Invalid parameter order { #error-30295 }

Service Studio shows the **Invalid Parameter Order** error:

* Invalid Parameter Order | Mandatory parameters must come before optional ones when parameters are set to Path. Reorder mandatory input parameters by placing them above optional parameters.

When you have a Screen with custom URLs, put the mandatory Input Parameters first. You can place optional Input Parameters only after the mandatory ones. You can fix the error by:

* Reordering the Input Parameters. Drag the Input Parameters under the Screen where the error shows, and reorder them by putting the mandatory parameters on the top of the parameter list.
* Changing the **Is Mandatory** property of Input Parameters. Check the properties of the Input Parameters under the Screen that reports the error. If there's a Parameter that should be mandatory instead of optional, set **Is Mandatory** to **Yes**.   

### Duplicated page name

Service Studio shows the **Duplicated Page Name** error:

* Duplicated Page Name | (screen name) has name Page Name clashing with (element name). Please use unique values.

To fix the error, change the Page Names so it's different from the names of the following **elements in the module**:

* Screens, including the name of the Screen where you set Page Name
* Blocks
* UI Flows
* The module itself

#### Avoiding duplicated redirects

Avoid duplicate page names in your modules when adding custom Screen URLs and Site Rules. When choosing a page name for a Screen, consider the following:

* Does the module have Screens with Page Names?
* Have you set Page Names to an app name or module name in the environment? 
* Is there a Site Rule in the environment that redirects to that module?

If yes, there's a risk of creating duplicate redirects pointing to different places.

Consider the following example.

* In the module **MyApp**, there's a Screen **Screen1** with the Page Name set to `Users`.
* In the environment, there's the **Users** system app to manage users at `/Users`.
* In the environment, the Site Rule redirects `www.example.com` to the module **MyApp**.

When users navigate to the `www.example.com/Users` URL, there are two possibilities for the platform to route the URL:

* Navigate to `Screen1` of the **MyApp** module.
* Navigate to the **Users** system app.

Depending on **where the platform registers the request** to access `www.example.com/Users`, the routing is as follows:

* If the request comes from **within** the browser running MyApp, **Screen1** of the module **MyApp** loads.
* If the request comes **outside** the browser running MyApp, the system app **Users** loads.

## Reserved page name

Error:

* The Page Name in [name_of_screen] screen contains a string that is not supported: "[reserved_string]". Use a different Page Name.


## Known issues

The following sections cover more technical details related to the known issues.

### Redirects don't work after adding rules

In a small number of OutSystems Cloud environments the redirect rules may not work initially. This may happen in environments that satisfy all [prerequisites for the technical preview](intro.md#prerequisites) **SEO-friendly URLs for Reactive Web Apps**. To make your redirects work, first check what version of React your OutSystems apps are running.

Do the following in Google Chrome to get the React version your apps are using:

1. Visit a page that belongs to your OutSystems app.
1. Press **F12** to open the browser console.
1. Paste the following code into the console: `console.log("React Version " + require("react").version);`
1. Press **Enter** to confirm. The console returns the React version.

If your React version is **lower than 16**, please contact OutSystems Support and inform them that your OutSystems Cloud environment isn't running React 16. There may be a valid technical reason why your OutSystems Cloud is still using React 15. 


### Empty string in a required parameter causes an error

When the app runtime passes an **empty string** as a value for a **required parameter** of the Text data type, by design the screen can't load. The users of the app see the following error:

* Error building URL for (screen name)/:(parameter name). Parameter (parameter name) is missing or has an empty value.

As a fix, try configuring the Screen without parameters on the path.

### Users can't access page when Screen has External URL in SecurityException

If you have:

* An active site rule for a domain
* An **External URL** node in the **SecurityException** logic

The following happens and prevents users from accessing a login page or a home page:

* A blank page loads in the browser
* Service Center logs show an error with the message "Content Security Policy blocked `<url>`"

To fix the issue, use a **JavaScript** note instead of the **External URL**, and add the following code:


```javascript
var url = window.location.origin + $parameters.url;
console.log(url);
setTimeout(function() {
    window.location.href = url
}, 0);
```
Here is a screenshot of the fix:

![JS workaround for redirect](images/workaround-js-redirect-url-ss.png?width=900)