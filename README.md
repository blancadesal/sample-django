# Sample Django app for Toolforge build service

The app is based on the official [Writing your first Django app tutorial](https://docs.djangoproject.com/en/4.2/intro/tutorial01/).
You can see it [deployed on Toolforge](https://django-test.toolforge.org/polls/).

For simplicity, this app uses a sqlite database.
In production, you would want to configure and use ToolsDB.
Rememeber to change the settings in mysite/settings.py

## Development environment

1. Fork and clone this repository, then cd into the project folder
2. Create and activate a virtual environment
3. Install the dependencies:
    `pip install -r requirements.txt`
4. Make sure these environment variables are available to the application via your preferred method:
    * [DJANGO_SECRET_KEY](https://docs.djangoproject.com/en/4.2/ref/settings/#std-setting-SECRET_KEY)
    * PROD: 0 for development, 1 for production

    If you save these variables to a file, make sure to add it to your `.gitignore`.
5. Run the migrations:
    `python manage.py migrate`
    This will create a `db.sqlite3` file in the project root

## How to Deploy on Toolforge

### Build

This assumes you have previously created a tool in Toolforge with the name `django-test`. Change this name to something else if you actually want to deploy your own copy, as a tool with this name already exists.

1. log in to Toolforge
2. `become django-test`
3. `toolforge build start https://github.com/blancadesal/sample-django`
4. Wait for the build to complete. You can check the status with `toolforge build show`. When finished, the status should show 'ok (Succeeded)'
5. If needed, check the build logs with `toolforge build logs`

### Deploy

When the build has finished successfully, it's time to deploy. We will first set up the environment variables, then start the webservice.

#### Set up environment variables

1. For each environment variable needed in production, run the following command:
    `toolforge envvars create <NAME> <VALUE>`
2. Check that the environment variables exist:
    `toolforge envvars list`

You can also change the variables once the webservice is running, but you will have to restart it manually for the changes to take effect.

#### Apply DB migrations

The sqlite database will be created in the tool's home directory (`/data/project/<tool-name>`).
The application has access to this dir at runtime.

To apply the migrations, run:
`toolforge jobs run --image tool-<tool-name>/tool-<tool-name>:latest --command "migrate" --no-filelog --wait migrate`

This uses the Toolforge jobs framework.
The 'migrate' after `--command` refers to the `migrate` process in the `Procfile` and runs `python manage.py migrate`.

#### Start the webservice

1. `toolforge webservice buildservice start`
2. Wait for the webservice to spin up, then check the logs to see if everything went ok: `toolforge webservice logs -f`
3. Navigate to <https://app-name.toolforge.org/polls/> to verify the application is working as expected.
You will see a page with the text "No polls are available".
This is expected, as the db starts out empty.
