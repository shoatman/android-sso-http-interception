# Http Interception for SSO

## Overview 

Enterprise customers want to enable single sign on (SSO) whenever possible.  The goal being a user/employee only needs to authenticate once on a device and have that account be used by all applications on their device.  Each authentication performed by a user represents an impact on productivity of a user / employee.

The Android operating system currently provides support for SSO via account authenticators (AbstractAccountAuthenticator implementations).  A user adds their account to the Android device (operating system) and then an application can access the secrets associated with the added account (with user consent) from the android AccountManager.  The Android AccountManager provides an API from which application wishing to use the SSO state (secrets) associated with account can call.  

This document describes challenges with the current model and proposes changes to the current model to improve the usability and accessbility of SSO on Android.

### Challenges

There are a few inherent challenges with this model:

- Developers are required to know about and explicitly invoke the android account manager API
- Developers are required to know how to use the secrets / SSO state associated with a custom account type
- Custom account types should only be published by the identity provider (IDP) responsible for the custom account type
  - NOTE: Our observation is that developers currently abuse custom account types (For example: Microsoft publishes 130... diminishing the account management experience value)
- User consent experience (granting an application permission to use a custom account) is cumbersome and confusing.  See example consent experience for usage of custom account (contacts permission)
- Many identity providers support multiple authentication and authorization protocols; The android custom account authenticator model is not currently aware of protocol specifics

### Proposal

Via Android Enterprise, allow organizational admins to enable http request interception to append SSO state to outgoing requests to URLs associated with an identity provider.  Associate and publish the SSO interception code via the existing abstract account authenticator model.

#### Challenges addressed

- Developers are not required to make changes to apps using modern HTTP based authentication protocols (SAML 2, OAuth 2, OIDC, Kerberso) in order to benefit from SSO
- Enterprise users (employees) do not need to consent to apps using their account.  This is done on their behalf by organizational admins.
- Publishers of abstract account authenticators (IDPs) can make SSO available for all of the http based auth protocols that they support

#### High Level Design Summary:

- Extend AbstractAccountAuthenticator to:
  - Associate a custom account type to the identity provider that manages them via URLs owned by the identity provider
    - For Example: https://login.microsoftonline.com; https://login.windows.net; etc...
    - Extend the abstract account authenticator xml (android manifest)
    - NOTE: Some identity providers allow identity services to be rebranded.  These rebranded identity providers still typically own their own URLs.  (For example: Azure AD B2C)
  - Add abstract methods for appending SSO artifacts to outgoing HTTP requests to URLs owned by the identity provider
    - Assumption is that modern auth protocols operate over http: SAML 2, OIDC, oAuth 2.0, Kerberos
    - The abstract account authenticator interface continues to be unaware of protocol specifics, but the implementation of an abstract account authenticator will be aware of supported protocols and based on the contents of a received outgoing HTTP request will be able to alter the request to automatically include the necessary request elements to enable SSO
- Allow organization admin via Android Enterprise to enable use of HTTP interception for SSO for specific account types
  - These same admins can deploy the APKs hosting the abstract account authenticator(s)
- Extend HttpUrlConnection to support HTTP interception for SSO
- Extend Android Web View (WebViewProvider) (Chromium project) to support HTTP interception for SSO


## References

- Android create a custom account type: https://developer.android.com/training/id-auth/custom_auth
- AbstractAccountAuthenticator: https://developer.android.com/reference/android/accounts/AbstractAccountAuthenticator
- Android AccountManager: https://developer.android.com/reference/android/accounts/AccountManager
- Apple SSO Extensions: https://developer.apple.com/videos/play/tech-talks/301/
- Identity provider: https://en.wikipedia.org/wiki/Identity_provider#:~:text=An%20identity%20provider%20(abbreviated%20IdP,user%20authentication%20as%20a%20service.
- Example rebranding of identity: https://azure.microsoft.com/en-us/services/active-directory/external-identities/b2c/
- Android Enterprise: https://www.android.com/enterprise/
- Android Enterprise - Mobile Management Partners: https://androidenterprisepartners.withgoogle.com/emm/
- Chromium code search: https://cs.chromium.org
- Android code search: https://cs.android.com
- WebViewChromium (loadUrl): https://source.chromium.org/chromium/chromium/src/+/master:android_webview/glue/java/src/com/android/webview/chromium/WebViewChromium.java?originalUrl=https:%2F%2Fcs.chromium.org%2F
- HttpUrlConnectionImpl: https://cs.android.com/android/platform/superproject/+/master:external/okhttp/okhttp-urlconnection/src/main/java/com/squareup/okhttp/internal/huc/HttpURLConnectionImpl.java?q=httpurlconnection&ss=android
- WebView for System Integrators: https://chromium.googlesource.com/chromium/src/+/HEAD/android_webview/docs/aosp-system-integration.md
- Building chromium for Android: https://chromium.googlesource.com/chromium/src/+/HEAD/docs/android_build_instructions.md
- Replacing existing Webview with custom: https://www.chromium.org/developers/how-tos/build-instructions-android-webview
- Submitting Patches to Android: https://source.android.com/setup/contribute/submit-patches#start-a-repo-branch
- Android Upstream Projects: https://source.android.com/setup/contribute/submit-patches#upstream-projects
- Android Developer Codelab: https://source.android.com/setup/start
- AIDL-CPP: https://android.googlesource.com/platform/system/tools/aidl/+/brillo-m10-dev/docs/aidl-cpp.md


## Glossary

- Identity Provider, IDP, IdP: "a system entity that creates, maintains, and manages identity information for principals while providing authentication services to relying applications within a federation or distributed network" (wikipedia.org see reference above)
- Relying party: Software that depends on an identity provider for either authentication or authorization services
- 

## Requirements (REQUIRED)

### Scenario overview / Problem Statement

Customers want SSO and will resort to custom VPN solutions to get it if the operating system does not provide it.

### Data supporting for prioritization of this work

> NOTE: In some ways this is a general Android compete issue since Apple provides a solution for SSO based on their SSO Extensions model (See reference above)

### Expected outcomes and measurement

> How do we measure adoption of this feature?

### Functional requirements


#### Developers of abstract account authenticators



#### Admin requirements



#### Questions


### System quality requirements (non-functional requirements - OPTIONAL)

#### Performance

- Only http requests to IDP owned URLs should be intercepted.  Since we need to perform this lookup frequenly this needs to be a performant query/check.  Abstract account authentication implementations likewise need to be fast and constrained to TBD miliseconds.

#### Usability

- Admins should be able to specify easily which APKs hosting account authenticators are authorized to intercept/append to HTTP requests.

#### Security


## Detailed design

### Components

#### AbstractAccountAuthenticator

#### AuthenticatorDescription

#### WebViewChromium

#### HttpUrlConnectionImpl (okhttp)

#### HttpUrlConnectionImpl

#### 



### Threat model 

> TBD


### Sequence diagram

### User experience mockups

#### Android Enterprise Admin & Admin Consent Experience



## Test Cases (REQUIRED)


