---
tags:
  - 📥️
  - 🌱
publish: true
aliases: 
cssclasses: 
---

# [OAuth](OAuth.md)
---
OAuth (current version is 2.0) is a *standard* that apps can use to provide client applications with “secure delegated access”. OAuth works over HTTPS and authorizes devices, APIs, servers, and applications with *access tokens* rather than credentials.

Instead of users giving third party API's their sensitive information (e.g. username, password, payment information), the **third party API receives an access token** from a service's authorization server to receive resources pertaining to that user (assuming user logs in to the auth server and grants permission). 

![Pasted image 20220629200140.png](../images/Pasted%20image%2020220629200140.png)

## Authorization Code Flow
Entities:
- User is usually a person interacting with a Client Application via a web browser 
- Client Application is usually a 3rd party service that is requesting some protected resources from another application. This application is authorized by the User (via login) to access protected resources.
- Resource Application is the service or data store that the Client application would like access to.
- Authorization Server is typically an identity service provider, such as Auth0, that handles user login and granting of authorization codes and access tokens
![OAuth 2024-02-02 09.44.58.excalidraw.svg](../images/OAuth%202024-02-02%2009.44.58.excalidraw.svg)
[🖋 Edit in Excalidraw](../images/OAuth%202024-02-02%2009.44.58.svg)%%  %%  %%  %%

---
Reference: https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth
https://darutk.medium.com/the-simplest-guide-to-oauth-2-0-8c71bd9a15bb