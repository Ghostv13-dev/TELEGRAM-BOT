
grammy.dev

Hosting: Deno Deploy | grammY
4 - 5 minutes

This guide tells you about the ways you can host your grammY bots on Deno Deploy.

Please note that this guide is only for Deno users, and you need to have a GitHub account for creating a Deno Deploy account.

Deno Deploy is ideal for most simple bots, and you should note that not all Deno features are available for apps running on Deno Deploy. For example, the platform only supports a limited set of the file system APIs available in Deno. It’s just like the other many serverless platforms, but dedicated for Deno apps.

The result of this tutorial can be seen in our example bots repository.
Preparing Your Code ​

    Remember that you need to run your bot on webhooks, so you should use webhookCallback and not call bot.start() in your code.

    Make sure that you have a file which exports your Bot object, so that you can import it later to run it.
    Create a file named main.ts or main.js, or actually any name you like (but you should be remembering and using this as the main file to deploy), with the following content:

ts

import { webhookCallback } from "npm:grammy";
// You might modify this to the correct way to import your `Bot` object.
import bot from "./bot.ts";

const handleUpdate = webhookCallback(bot, "std/http");

Deno.serve(async (req) => {
  if (req.method === "POST") {
    const url = new URL(req.url);
    if (url.pathname.slice(1) === bot.token) {
      try {
        return await handleUpdate(req);
      } catch (err) {
        console.error(err);
      }
    }
  }
  return new Response();
});

We advise you to have your handler on some secret path rather than the root (/). Here, we are using the bot token (/<bot token>).
Deploying ​
Method 1: With GitHub ​

    This is the recommended method, and the easiest one to go with. The main advantage of following this method is that Deno Deploy will watch for changes in your repository which includes your bot code, and it will deploy new versions automatically.

    Create a repository on GitHub, it can be either private or public.

    Push your code.

        It is recommended that you have a single stable branch and you do your testing stuff in other branches, so that you won’t get some unexpected things happen.

    Visit your Deno Deploy dashboard.

    Click on “New Project”.

    Install the GitHub app on your account or organization, and choose your repository.

    Select the branch you want to deploy.

    Select the entrypoint file main.ts, and click “Deploy Project” to deploy.

Method 2: With deployctl ​

    This is a method for more advanced users or if you don’t want to upload your code to GitHub. It allows you to deploy the project via the command line or GitHub Actions.

    Install deployctl.

    Create an access token from the “Access Tokens” section in account settings.

    Go to your project directory and run the following command:

    sh

    deployctl deploy --project=<project> --entrypoint=./main.ts --prod --token=<token>

    Setting environment variables

    Environment variables can be set by heading to your project’s settings after deploying.

    But this is possible from the command line, as well:
        You can assign environment variables from a dotenv file by adding the --env-file=<file> argument.
        You can also specify them individually by using the --env=<key=value> argument.

    To set up GitHub Actions, refer to this.

Check out the deployctl documentation for more information.
Note ​

After getting your app running, you should configure your bot’s webhook settings to point to your app. To do that, send a request to

sh

curl https://api.telegram.org/bot<token>/setWebhook?url=<url>

replacing <token> with your bot token, and <url> with the full URL of your app along with the path to the webhook handler.
