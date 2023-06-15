# youtrack-connector


## About users

The "users" datatype is a specialcase: The main youtrack api only gives a readonly view of 
YouTrack's profile of the user account (ref: https://www.jetbrains.com/help/youtrack/devportal/resource-api-users.html).

The "real" user data can be (retrieved and modified) via the "Hub" api (ref: https://www.jetbrains.com/help/youtrack/devportal/HUB-REST-API_Users.html).

The user profiles from the Youtrack api has two ids: 

  * A "Youtrack" id that looks something like this: "1-6".
  * A "Hub" id that looks something like this: "f76f0560-7483-47cf-a82f-5dc1f46ed14c"

The YouTrack user has a "ringId" field that contains the "Hub" user-id.

We collect the Youtrack users in the *-users-youtrack-collect" pipe. We really only need the "id" and "ringId" fields,
but we get the "login" field as well to make debuggin easier.  Example entity:
```
{
  "_id": "1-6",
  "$type": "User",
  "id": "1-6",
  "login": "knut.johannessen2",
  "ringId": "f76f0560-7483-47cf-a82f-5dc1f46ed14c"
}
```

We collect the Hub users in the *-users-all pipe. This is where we get all the user data. Example entity:
```
{
  "_id": "c6b6e9c6-0f42-42f3-ac96-2ea69ac758fd",
  "banned": false,
  "id": "c6b6e9c6-0f42-42f3-ac96-2ea69ac758fd",
  "login": "knut.johannessen",
  "name": "Knut Johannessen2",
  "profile": {
    "avatar": {
      "type": "defaultavatar",
      "url": "https://sesamtalkdev.youtrack.cloud/hub/api/rest/avatar/c6b6e9c6-0f42-42f3-ac96-2ea69ac758fd"
    },
    "email": {
      "email": "knut.johannessen@sesam.io",
      "type": "EmailJSON",
      "verified": true
    },
    "locale": {
      "language": "en",
      "name": "en"
    }
  },
  "type": "user"
}
```

And finally we connect the Youtrack and Hub users in the *-users-collect pipe. This pipe reads from the *-users-all
pipe and uses a hop to add the Youtrack user "id" value as "sesam_youtrack_user_id". We need this id, since it is the
one that the other datatypes in Youtrack refer to. Example entity:
```
{
  "_id": "c6b6e9c6-0f42-42f3-ac96-2ea69ac758fd",
  "_updated": 14,
  "_previous": 10,
  "_deleted": false,
  "_ts": 1686808707943601,
  "_hash": "066ca7b9a26232210993146204588dbe",
  "banned": false,
  "id": "c6b6e9c6-0f42-42f3-ac96-2ea69ac758fd",
  "login": "knut.johannessen",
  "name": "Knut Johannessen2",
  "profile": {
    "avatar": {
      "type": "defaultavatar",
      "url": "https://sesamtalkdev.youtrack.cloud/hub/api/rest/avatar/c6b6e9c6-0f42-42f3-ac96-2ea69ac758fd"
    },
    "email": {
      "email": "knut.johannessen@sesam.io",
      "type": "EmailJSON",
      "verified": true
    },
    "locale": {
      "language": "en",
      "name": "en"
    }
  },
  "sesam_youtrack_user_id": "1-3",
  "type": "user"
}
```

