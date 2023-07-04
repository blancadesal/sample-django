# Sample Django app for Toolforge build service

The app is based on the official [Writing your first Django app tutorial](https://docs.djangoproject.com/en/4.2/intro/tutorial01/).
You can see it [deployed on Toolforge](https://django-test.toolforge.org/polls/).

## How to Deploy on Toolforge

### Build

1. log in to Toolforge
2. `become django-test`
3. `toolforge build start https://github.com/blancadesal/sample-django`
4. Wait for the build to complete. You can check the status with `toolforge build show`. When finished, the status should show 'ok (Succeeded)'
5. If needed, check the build logs with `toolforge build logs`

### Deploy

When the build has finished successfully, it's time to deploy.

1. `toolforge webservice buildservice start`
2. Wait for the webservice to spin up, then check the logs to see if everything went ok: `toolforge webservice logs`