# youtrack-connector


## About users

The "users" datatype is a specialcase: The main youtrack api only gives a readonly view of 
YouTrack's profile of the user account (ref: https://www.jetbrains.com/help/youtrack/devportal/resource-api-users.html).

The "real" user data can be (retrieved and modified) via the "Hub" api (ref: https://www.jetbrains.com/help/youtrack/devportal/HUB-REST-API_Users.html).

The user profile from the Youtrack api has two ids:

  * A "id" field that looks something like this: "1-6".
  * A "ringId" field that looks something like "f76f0560-7483-47cf-a82f-5dc1f46ed14c" and that refers to the "id" field
    of the "Hub" user.

We collect the Youtrack users in the *-usersyoutrack-collect" pipe. We really only need the "id" and "ringId" fields,
but we get the "login" field as well to make debugging easier.  Example entity:
```
{
  "_id": "1-6",
  "$type": "User",
  "id": "1-6",
  "login": "knut.johannessen2",
  "ringId": "f76f0560-7483-47cf-a82f-5dc1f46ed14c"
}
```
The reason we need to collect the usersyoutrack is that it is this id that that the other datatypes in Youtrack refer to.
This is a readonly datatype, so there is no share-pipe for this.


We collect the Hub users in the *-users-collect pipe. This is where we get all the actual user data. Example entity:
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

### Testing APIs with Postman
Choose the authorization method to be the `Bearer Token` and use your Youtrack api key as for the token. 

URL template: https://`<domain>`.youtrack.cloud/

URL for Youtrack users: 
https://sesamtalkdev.youtrack.cloud/api/users

URL for projects:
https://sesamtalkdev.youtrack.cloud/api/admin/projects

URL for issues: 
https://sesamtalkdev.youtrack.cloud/api/issues

URL for Hub users: 
https://sesamtalkdev.youtrack.cloud/hub/rest/users

All of the APIs above accept a set of `fields` that will return a selective array of attributes that you choose. Feed the fields attributes as URL parameters. 

`$skip` and `$top` URL parameters are used for pagination. 

The API for the list of issues have an `updated` field which is in the format of long and can potentially be used for continuation support though we still have not figured out a solid way to do so. Youtrack seems not to have a since property to indicate changes easily and uses a `query` URL parameter to fetch records. More research needs to be done on this subject. 