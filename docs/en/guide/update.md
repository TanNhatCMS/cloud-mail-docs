# Project Updates

## Action Deployment Updates
After syncing the code, GitHub Actions will update automatically.

## UI Deployment Updates
1. After syncing the code, the Worker will update automatically.
2. Rebind the `d1` and `kv` databases.
3. Visit `https://<worker-custom-domain>/api/init`, enter your `jwt_secret` in the form, and submit to update the database (this will only apply updates and will not overwrite existing data).
