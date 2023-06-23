# youtrack-connector


## About usersyoutrack and users

The "usersyoutrack" and "users" datatypes are specialcases: The main youtrack api only gives a readonly view of 
YouTrack's profile of the user account (ref: https://www.jetbrains.com/help/youtrack/devportal/resource-api-users.html).
We collect these as the "usersyoutrack" datatype.

The "real" user data can be retrieved and modified via the "Hub" api (ref: https://www.jetbrains.com/help/youtrack/devportal/HUB-REST-API_Users.html).
We collect these as the "users" datatype.

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


## About groups and usergroups

The "groups" and "usergroups" datatypes are similar to the "usersyoutrack" and "users" datatypes.

The main youtrack api only gives a readonly view of the user groups (ref: https://www.jetbrains.com/help/youtrack/devportal/resource-api-groups.html).

The "real" user group data can be retrieved and modified via the "Hub" api (ref: https://www.jetbrains.com/help/youtrack/devportal/HUB-REST-API_User-Groups.html).

The "group" from the Youtrack api has two ids:

  * A "id" field that looks something like this: "1-6".
  * A "ringId" field that looks something like "f76f0560-7483-47cf-a82f-5dc1f46ed14c" and that refers to the "id" field
    of the "Hub" user group.

The reason we need to collect the "groups" is that it is this id that that the other datatypes in Youtrack refer to.
"groups" is a readonly datatype, so is has no share-pipe.

## About projects and hubprojects

The "projects" and "hubprojects" datatypes are similar to the "usersyoutrack" and "users" datatypes.

The main youtrack api lets you retrieve and update some of the project properties (ref: https://www.jetbrains.com/help/youtrack/devportal/resource-api-admin-projects.html).

The "Hub" api lets us get some additional information (for instance which organization a project belongs to (ref: https://www.jetbrains.com/help/youtrack/devportal/HUB-REST-API_Projects.html).

The "project" from the Youtrack api has two ids:

  * A "id" field that looks something like this: "1-6".
  * A "ringId" field that looks something like "f76f0560-7483-47cf-a82f-5dc1f46ed14c" and that refers to the "id" field
    of the corresponding "Hub" project.

We don't have a share-pipe for the 'hubproject' datatype, since it seems that we can update all the properties we need via the "project" main api.

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

By default, YouTrack returns only the $type and id attributes of the requested entities. To get more information you should provide a list of returned entity attributes in the fields parameter of the request. The $type and id attributes will appear in the response regardless of whether you specify it explicitly or not.