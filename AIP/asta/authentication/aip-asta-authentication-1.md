---
aip: aip-asta-authentication-1
title: Authentcation of request on asta
description: Methods and prodedures for authenticating the incoming requests on asta.
author: Piyush Jaiswal (@JaisPiyush)
status: Draft
type: Standard
module: asta/authentication
created: 2022-08-22
---


## Abstract
The following details the proposal of authentication system for each request handled by `asta backend`. 
Almight backend is required to verify the request for providing better security to resources. The authentication
system aims to imploy multiple authentication techniques to address multiple use-creterions.

## Specification
Asta is aiming to provide security measures for utilizing resources on the server. The spec aims to authenticate and differentiate between the types of authorization requesting entities as:

1. **User :** - Almight platform users. 
2. **Project :** - Project is container of all resources and their configurations. Project is created by an authenticated `User` only.
3. **Profile :** - Profile is similar to `User` but for each project. The end-user (not `User`) authenticating for any particular `Project`.
4. **Service :** - External service (out of `asta` system) which is responsible for any task and requires to communicate with asta.

For e.g, `Uniswap` and other `dapps` will be `Project`, while their original developers will be the `User` on the platform. Any end-user must authenticate their wallet in order to use the Uniswap services, those authenticated entities will be the `Profile`. Services like external `storj node` or `ipfs node` working with asta will be the `Service`.

The authentication system will require:
* Authenticate `User` making requests like create project, edit project, billings, delete project, etc.
* Authenticate `Project` for making requests like authentication of end-user, storage service from client side, as well as superior uses from backend. For e.g, restrictions will allow `Profile` to be authenticated from client side, but request from project's backend will be allowed to access all the profiles loggedin so far.
* Authenticate `Profile` against each request it makes. For e.g adding new wallet or storing any file on the storage.
* Authenticate `Service` for the source of origin and permissions to use requested resources. For e.g non storage services should not be allowed to access any storage related data.



## Rationale
[JWT](https://jwt.io/introduction) based authentication should be used to authenticate the requests. It comes with benefits of least database trips.

simple jwt payload scheme
```ts
interface JWTPayload {
    // Issuer of the JWT
    iss?: string;
    // recipient of the JWT. If provided then authenticator must match the request origin
    aud?: string;
    // Unix timestamp of token creation date time
    iat: UnixTimestamp;
    // Unix timestamp after which token will be invalid
    expr: UnixTimestamp;
    // Unique id directly mapped to the entity in the database
    uid: string;
    type: TokenType
}
```

* `Token Type` indicating the type of entity token is associated with.
```ts
type TokenType = 'user' | 'profile' | 'project-public' | 'project-private' | 'service'
```

## Test Cases

```ts

// Facebook issuing tokens for google.com origin for an user entity with uid: user-id-1
const userJWT = JWT.sign({
    iss: "facebook.com",
    aud: "google.com",
    iat: 1516239022,
    expr: 1516439022 ,
    type: 'user',
    uid: 'user-id-1'
});

console.log("User JWT: ", userJWT);

// signDefault will be an abstract function that will fill all the default values such as iss and aud

const projectClientJWT = JWT.signDefault({
    iat: 1516239022,
    expr: 1516439022 ,
    type: "project-client",
    uid: "project-id-1"
});

console.log("Project JWT: ", projectClientJWT);


```

```bash
User JWT: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmYWNlYm9vay5jb20iLCJhdWQiOiJnb29nbGUuY29tIiwiaWF0IjoxNTE2MjM5MDIyLCJleHByIjoxNTE2NDM5MDIyLCJ0eXBlIjoidXNlciIsInVpZCI6InVzZXItaWQtMSJ9.C3pcCvDB8iRiPSRBFwlM6u5XxAK423ee_PSs4A3UiHs

Project Client JWT: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE1MTYyMzkwMjIsImV4cHIiOjE1MTY0MzkwMjIsInR5cGUiOiJwcm9qZWN0LWNsaWVudCIsInVpZCI6InByb2plY3QtaWQtMSJ9.C4oAhy1FyD9DShJMNAASz2c5F-ZYXsyP7jaFhO372Oo


```

## Security Considerations

JWT tokens are stateless and can only be invalid once expired. In a case scenerio when single profile has multiple JWTs across devices, after request for changing the password, except the device making request all other JWTs remains unaffected. In case of password stealing or unfair login hacks the hacker won't be denied access upon password change because all other issued JWT for that profile or user will remain untouched and will be allowed to work fine untill expired.

### Permissions
Each authenticated entity will have permission boundaries. Any entity should only uses permissioned resources and trial for crossing the boundry must result in error.


Another mechanism should be used along with the `Permission` management mechanism to control the use of such tokens.
