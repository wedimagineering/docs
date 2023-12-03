---
description: Server rest-api documentation for Magicband container
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: false
  outline:
    visible: false
  pagination:
    visible: false
---

# 📄 Documentation

{% hint style="danger" %}
The `server.wedimagineering.com` hostname can only be accessed from roblox servers, the status page workers, and the bot container. Any other attempted access or use will be faced with a blocked message.
{% endhint %}

***

### Player services

{% swagger method="post" path="/connect" baseUrl="https://server.wedimagineering.com" summary="Fetches player authentication data" %}
{% swagger-description %}
Requested data upon player entry to game, recognised as authentication initialization. Returns data to check and modualize player permissions across experiences.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" required="true" type="String" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="Number" required="true" %}
roblox user id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="training" type="Boolean" required="false" %}
is training server?
{% endswagger-parameter %}

{% swagger-parameter in="query" name="server" type="String" %}
roblox server jobid
{% endswagger-parameter %}

{% swagger-parameter in="header" name="roblox-id" type="Number" %}
roblox place id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success (user found)" %}
If the user could be found in the database, the response will contain `clearance` and `certifications` relative to other pretaining conditions.

```json
{
    "certifications": [ string ], // comma separated
    "clearance": [ number ] // 1 - cast member, 2 - senior cast member, 3 - manager+, 4 - lead imagineer+
}
```
{% endswagger-response %}

{% swagger-response status="200: OK" description="success (user banned)" %}
If the user could be found in the database and is banned, the response will contain `banned`, `reason`, and `moderator`.

```json
{
    "certifications": [string], // comma separated
    "clearance": [number], // 1 - cast member, 2 - senior cast member, 3 - manager+, 4 - lead imagineer+
    "banned": [boolean] , // true
    "reason": [string],
    "moderator": [number]
}
```
{% endswagger-response %}

{% swagger-response status="200: OK" description="success (user not found)" %}
If the user could not be found in the database, the response will contain `clearance` and `certifications`, returning fixed non-permissive variables.

```json
{
    "certifications": 'None', // comma separated
    "clearance": 0, // 1 - cast member, 2 - senior cast member, 3 - manager+, 4 - lead imagineer+
}
```
{% endswagger-response %}

{% swagger-response status="200: OK" description="success (training server)" %}
If the request contains the `training` query boolean and it equals `true`, the response will contain `certifications` and `clearance`. Every applicable certification is applied to the `certifications` field, while the `clearance` field is still variable and conditional.

```json
{
    "certifications": 'char,tot,mtp,btmr,rnrc,srn,barn', // comma separated
    "clearance": [ number ] // 1 - cast member, 2 - senior cast member, 3 - manager+, 4 - lead imagineer+
}
```
{% endswagger-response %}
{% endswagger %}



{% swagger method="get" path="/profile" baseUrl="https://server.wedimagineering.com" summary="Fetches player data" %}
{% swagger-description %}
Requested data upon query for player data, including but not limited to displaying internal database data stored or fetched for a player.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="id" type="Number" required="false" %}
roblox user id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" %}
roblox user username
{% endswagger-parameter %}

{% swagger-parameter in="query" type="Number" name="user" %}
discord user id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="data found / data not found" %}
```json
{
    "status": 'success',
    "message": [string],
    "data": {
        "_id": [string], // if data found
        "id": [number],
        "username": [string],
        "points": [number],
        "rank": [string],
        "rankid": [number],
        "cast": [boolean],
        "certifications": [array]
    }
}
```

If the user could be found in the database, the response `message` will contain `data found` and `_id` will exist, all other data is variable, cast is conditional. If the user could not be found in the database, the response `message` will contain `data not found` and `_id` will not exist, all other data is variable, cast is conditional.
{% endswagger-response %}

{% swagger-response status="404: Not Found" description="user not found" %}
If the user could not be found, the response will contain the `status` and `message`, displaying the error. The `data` array may be attached if applicable to display queried data.

<pre class="language-json"><code class="lang-json">{
    "status": 'error', 
    "message": [string], 
<strong>    "data": [array] // if applicable
</strong>}
</code></pre>
{% endswagger-response %}

{% swagger-response status="401: Unauthorized" description="invalid key" %}
If the request is missing or sending an incorrect key query, the response will contain the `status` and `message`, displaying the error.

```json
{
    "status": 'error', 
    "message": [string]
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="invalid query / no query" %}
If the request is missing a required query, the response will contain the `status` and `message`, displaying the error.

```json
{
    "status": 'error', 
    "message": [string]
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
certification _(rnrc/tot/mtp/char/btmr)_
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" required="true" %}
roblox user username
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

{% swagger-response status="500: Internal Server Error" description="error" %}
```json
{
    "status": "error",
    "modifiedCount": 0,
    "upsertedCount": 0,
    "discord": "You've encountered a rare database error; if this is a reoccuring issue, please contact `JTmaveryk#0001`."
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
certification _(rnrc/tot/mtp/char/btmr)_
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

{% swagger-response status="500: Internal Server Error" description="error" %}
```json
{
    "status": "error",
    "modifiedCount": 0,
    "discord": "You've encountered a rare database error; if this is a reoccuring issue, please contact `JTmaveryk#0001`."
}
```
{% endswagger-response %}
{% endswagger %}

### Player Moderation

{% swagger method="post" path="/ban/add" baseUrl="https://server.wedimagineering.com" summary="Ban player" %}
{% swagger-description %}
Used for granting player certifications, allowing authorization to utilize certification required actions; proving training and knowledge.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="reason" type="String" required="true" %}
action reason
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" required="true" %}
roblox user username
{% endswagger-parameter %}

{% swagger-parameter in="query" name="moderator" type="Number" required="true" %}
discord moderator id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success",
    "discord": "Successfully banned **JTmaveryk** ([172051854](https://www.roblox.com/users/172051854/profile)) for **bad boy**."
}
```
{% endswagger-response %}

{% swagger-response status="500: Internal Server Error" description="error" %}
```json
{
    "status": "error",
    "modifiedCount": 0,
    "upsertedCount": 0,
    "discord": "You've encountered a rare database error; if this is a reoccuring issue, please contact `JTmaveryk#0001`."
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="post" path="/ban/remove" baseUrl="https://server.wedimagineering.com" summary="Unban player" %}
{% swagger-description %}
Used for granting player certifications, allowing authorization to utilize certification required actions; proving training and knowledge.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" name="reason" type="String" required="true" %}
action reason
{% endswagger-parameter %}

{% swagger-parameter in="query" name="username" type="String" required="true" %}
roblox user username
{% endswagger-parameter %}

{% swagger-parameter in="query" name="moderator" type="Number" required="true" %}
discord moderator id
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success",
    "discord": "Successfully unbanned **JTmaveryk** ([172051854](https://www.roblox.com/users/172051854/profile)) for **good boy**."
}
```
{% endswagger-response %}

{% swagger-response status="500: Internal Server Error" description="error" %}
```json
{
    "status": "error",
    "modifiedCount": 0,
    "discord": "You've encountered a rare database error; if this is a reoccuring issue, please contact `JTmaveryk#0001`."
}
```
{% endswagger-response %}
{% endswagger %}

### Application Services

{% swagger method="post" path="/application/approve" baseUrl="https://server.wedimagineering.com" summary="Approve player application" expanded="false" fullWidth="false" %}
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

{% swagger-response status="500: Internal Server Error" description="error" %}
```json
{
    "status": "error",
    "message": "couldn't rank user"
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
    "status": "success",
    "url": "https://pastebin.com/xxxxxxx"
}
```
{% endswagger-response %}
{% endswagger %}

### Shift Services

{% swagger method="post" path="/log" baseUrl="https://server.wedimagineering.com" summary="Log player shift" %}
{% swagger-description %}
Used for logging player shifts, calculating earned points and automatically assigning them to the user's profile.
{% endswagger-description %}

{% swagger-parameter in="query" name="key" type="String" required="true" %}
api authorization
{% endswagger-parameter %}

{% swagger-parameter in="query" type="Number" name="id" required="true" %}
roblox user id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="place" type="Number" required="true" %}
roblox place id
{% endswagger-parameter %}

{% swagger-parameter in="query" name="duration" type="Number" required="true" %}
shift duration (in seconds)
{% endswagger-parameter %}

{% swagger-parameter in="query" name="training" type="Boolean" required="true" %}
is training server?
{% endswagger-parameter %}

{% swagger-parameter in="query" name="server" type="String" required="true" %}
roblox place jobid
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="success" %}
```json
{
    "status": "success"
}
```
{% endswagger-response %}

{% swagger-response status="500: Internal Server Error" description="error" %}
```json
{
    "status": "error",
    "message": "error encountered",
    "modifiedCount": 0
}
```
{% endswagger-response %}
{% endswagger %}

