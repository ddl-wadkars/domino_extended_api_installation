# Using the API

The API provides the following endpoints:

#### /api-extended/refresh_cache

Invoke this endpoint if you want to refresh all caches. To avoid having to read Mongo repeatedly,
the EnvironmentRevision and Project information from the Mongo collections are cached.

If you want to refresh the cache invoke this method

#### /api-extended/projects/beta/projects

This is an extension of the endpoint ` /api/projects/beta/projects`

The returned json contains an attribute `projects` which is a list of the projects the user is member of.

Each project json in the list contains two additional attributes as compared to the orignal from the `projects` 
Mongo collection

- `environment_id`
- `default_environment_revision_spec`


#### /api-extended/environments/beta/environments

This is an extension of the endpoint ` /api/environments/beta/environments`

The returned json contains an attribute `environments` which is a list of the environments the user has access to

Each environment instance in the list has two attributes , `latestRevision` and `selectedRevision`. Each of them
is enhanced by adding attributes

- `basedOnDockerImage` (This is the root docker image based on the env hierarchy that the environment revsion is based on)
- `basedOnDockerImageStatusMessage` (This will contain an error message or `Success` )

For brevity the attribute `availableTools` is replaced with `None` 

#### v4-extended/autoshutdownwksrules

The full endpoint inside the Domino workspace is (assuming `domino-platform` as the platform namespace)
```shell
http://domino-extendedapi-svc.domino-platform/v4-extended/autoshutdownwksrules
```

Type : POST

Headers and Body:
```
--header 'X-Domino-Api-Key: ADD YOUR API KEY HERE ' \
--header 'Content-Type: application/json' \
--data-raw '{
    "users": {
        "wadkars": 3600,
        "integration-test":  21600
    },
    "override_to_default" : false
}'
```
Or if using the bearer token obtained from `http://localhost:8899/access-token`

```shell
--header 'Authorization: Bearer <ADD YOUR TOKEN HERE>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "users": {
        "wadkars": 3600,
        "integration-test":  21600
    },
    "override_to_default" : false
}'
```

For each user you want to override the default value update the `users`
attribute above as:
`{domino-user-name}` : {auto_shutdown_duration_in_seconds}

The default auto-shutdown-duration is obtained from the central config parameter:
```shell
com.cerebro.domino.workspaceAutoShutdown.globalDefaultLifetimeInSeconds
```
The `override_to_default` attribute is used to determine if all users (not specificied)
in the `users` attribute tag as also update to have their default autoshutdown duration
set to default.

if `override_to_default` is set to `true` every user except the users mentioned in the 
`users` attribute will be configured for the default value of autoshutdown

The value of `com.cerebro.domino.workspaceAutoShutdown.globalDefaultLifetimeInSeconds`
is expected to be lower than `com.cerebro.domino.workspaceAutoShutdown.globalMaximumLifetimeInSeconds`

Likewise for the values provided for each user in the `users` attribute

If not, the auto shutdown duration is capped at the value of `com.cerebro.domino.workspaceAutoShutdown.globalMaximumLifetimeInSeconds`


An example Python client is provided in the file `client/extended_api_client.py`.
Copy its content to a workbook and try it out.

