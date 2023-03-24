---
description: server rest api documentation
layout: editorial
---

# Documentation

### Account Services

{% hint style="warning" %}
The `/connect` endpoint can only be accessed from roblox servers, any other attempts at contact or use will be faced with denied access.
{% endhint %}

{% swagger method="get" path="/connect" baseUrl="https://server.wedimagineering.com" summary="Fetches player data (limited) on game initialization" %}
{% swagger-description %}
Used for receiving API data on users, including rank, clearance values, authorization, admin level, and more.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" required="true" type="String" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="place" type="Number" required="true" %}
place id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="Number" required="true" %}
roblox id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="training" type="Boolean" required="true" %}
is training server?
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "certifications": "char,tot,mtp,btmr,rnrc",
    "auth": true, // authorization (boolean)
    "clearance": 0 // clearance level (1 - cast member, 2 - head/lead member, 3 - manager+)
}
```
{% endswagger-response %}

{% swagger-response status="401: Unauthorized" description="error" %}
```json
{
    "status": "error",
    "message": "invalid key"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/profile" baseUrl="https://server.wedimagineering.com" summary="Fetches player data (full)" %}
{% swagger-description %}
Used for receiving API data on users, including rank, clearance values, authorization, admin level, and more.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="Number" required="false" %}
roblox id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" %}
roblox username
{% endswagger-parameter %}

{% swagger-parameter in="query" type="Number" name="discord" %}
discord id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success",
    "id": 1,
    "_id": "62f6b211c393da0ce38e5dac", // database container id
    "username": "Roblox",
    "points": 0,
    "rank": "Guest",
    "rankid": 0,
    "cast": false, // cast status (boolean)
    "certifications":["tot"]
}
```
{% endswagger-response %}

{% swagger-response status="404: Not Found" description="error" %}
```json
{
    "status": "error",
    "message": "couldn't find user",
    "discord": "We couldn't find the requested user, please ensure you typed it correctly and try again."
}
```
{% endswagger-response %}
{% endswagger %}

### Certification Services

{% swagger method="post" path="/certification/grant" baseUrl="https://server.wedimagineering.com" summary="Grant player certification" %}
{% swagger-description %}
Used for granting player certifications, allowing authorization to utilize certification required actions; proving training and knowledge.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="certification" type="String" required="true" %}
certification 

_(rnrc/tot/mtp/char/btmr)_
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" required="true" %}
roblox username
{% endswagger-parameter %}

{% swagger-parameter in="query" name="moderator" type="Number" required="true" %}
discord moderator id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success",
    "discord": "Successfully granted **Roblox** the **tot** certification."
}
```
{% endswagger-response %}

{% swagger-response status="403: Forbidden" description="error" %}
```json
{
    "status": "error",
    "discord": "The requested user does not meet the rank pre-requisites to receive a certification."
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="post" path="/certification/revoke" baseUrl="https://server.wedimagineering.com" summary="Revoke player certification" %}
{% swagger-description %}
Used for revoking player certifications, removing authorization to utilize certification required actions; proving necessity for training and knowledge.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" type="String" name="certification" required="true" %}
certification 

_(rnrc/tot/mtp/char/btmr)_
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" required="true" %}
roblox username
{% endswagger-parameter %}

{% swagger-parameter in="query" name="moderator" type="Number" required="true" %}
discord moderator id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success",
    "discord": "Successfully revoked **Roblox**'s **tot** certification."
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="error" %}
```json
{
    "status": "error",
    "discord": "**Roblox** doesn't have the **tot** certification."
}
```
{% endswagger-response %}
{% endswagger %}

### Application Services

{% swagger method="post" path="/application/approve" baseUrl="https://server.wedimagineering.com" summary="Approve player application" expanded="false" %}
{% swagger-description %}
Used for approving player applications, ranking the player in the group and approving entry into the university program.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="Number" required="true" %}
roblox id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="moderator" type="Number" required="true" %}
moderator id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success",
    "message": "successfully ranked user"
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="error" %}
```json
{
    "status": "error",
    "message": "user is already trainee/cast"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="post" path="/application/upload" baseUrl="https://server.wedimagineering.com" summary="Upload player application" %}
{% swagger-description %}
Used for uploading player applications to a fileserver and returning a pastebin raw file url for accessing the application externally. (expires after 1 month)
{% endswagger-description %}

{% swagger-parameter in="query" type="String" required="true" name="key" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" type="Number" name="id" required="true" %}
roblox id
{% endswagger-parameter %}

{% swagger-parameter in="body" name="q1" type="String" required="true" %}
question contents
{% endswagger-parameter %}

{% swagger-parameter in="body" name="q2" type="String" required="true" %}
question contents
{% endswagger-parameter %}

{% swagger-parameter in="body" name="q3" type="String" required="true" %}
question contents
{% endswagger-parameter %}

{% swagger-parameter in="body" type="String" name="q4" required="true" %}
question contents
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "succness",
    "url": "https://pastebin.com/xxxxxxx"
}
```
{% endswagger-response %}
{% endswagger %}

### Shift Services

{% hint style="warning" %}
The `/log` endpoint can only be accessed from roblox servers, any other attempts at contact or use will be faced with denied access.
{% endhint %}

{% swagger method="post" path="/log" baseUrl="https://server.wedimagineering.com" summary="Log player shift" %}
{% swagger-description %}
Used for logging player shifts, calculating earned points and automatically assigning them to the user's profile.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" type="Number" name="id" required="true" %}
roblox id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="place" type="Number" required="true" %}
place id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="duration" type="Number" required="true" %}
shift duration (in seconds)
{% endswagger-parameter %}

{% swagger-parameter in="query" name="training" type="Boolean" required="true" %}
is training server?
{% endswagger-parameter %}

{% swagger-parameter in="query" name="server" type="String" required="true" %}
roblox jobid
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success"
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="error" %}
```json
{
    "status": "error",
    "message": "invalid certification"
}
```
{% endswagger-response %}
{% endswagger %}

