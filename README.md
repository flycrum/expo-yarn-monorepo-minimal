# minimal-expo-yarn-monorepo

With first-class support, yarn is arguably the easiest way to manage a monorepo with the challenges that come with Expo dependencies (starting with Expo SDK 48).

## Docs

- [Expo: Work with monorepos](https://docs.expo.dev/guides/monorepos)
- [Expo: Upgrade Expo SDK](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough)

## Step-by-step approach

In this example, we will set up a monorepo using Yarn workspaces without the nohoist option.

1. Make sure you have yarn 1.x installed.

   ```bash
   yarn --version
   ```

   Install it if you don't have it.

   ```bash
   npm install --global yarn
   ```

2. Initialize yarn project. Run `yarn init`, or create the `package.json` manually. It should look something like this:

   > Note: All Yarn monorepos should have a "root" package.json file.

   ```json
   {
     "name": "monorepo",
     "version": "1.0.0"
   }
   ```

3. Set up yarn workspaces. Yarn and other tooling have a concept called "workspaces". Every package and app in our repository has its own workspace. Before we can use them, we have to instruct Yarn where to find these workspaces. We can do that by setting the workspaces property using glob patterns, in the package.json:

   > Note: Yarn workspaces require the root package.json to be private.

   ```json
   {
     "name": "monorepo",
     "version": "1.0.0",
     "workspaces": ["apps/*", "packages/*"]
   }
   ```

4. Create our first app.

   a. Create the app directory.

   ```bash
   mkdir apps/cool-app
   cd apps/cool-app
   ```

   b. Create the Expo app with its default `tabs` template using Expo Router.

   ```bash
   yarn create expo apps/cool-app
   ```

   c. Make sure to move the .gitignore file to the root of the monorepo.

   ```bash
   mv apps/cool-app/.gitignore ./
   ```

5. Test the app. When running the `npx expo start` command, use the `EXPO_USE_METRO_WORKSPACE_ROOT` environment variable. It enables the auto server root detection for Metro. This variable can also be defined inside a `.env` file.

   > Note: If you are not using Expo Router, you can [change the default entrypoint](https://docs.expo.dev/guides/monorepos/#change-default-entrypoint).

   a. When testing the app, you should see the following message:

   ```
   ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
   █ ▄▄▄▄▄ █▀▄█▀ ▄█ ▄█ ▄▄▄▄▄ █
   █ █   █ █▀▄▀   ▀▀▄ ▄▄ █▀█▄█
   █ █▄▄▄█ █▀▀▄  ▀ █  █▄  ▄█▄█
   █▄▄▄▄▄▄▄█▄▄▄▄▄█▄█▄███▄▄█▄▄█

   › Metro waiting on exp://192.168.1.19:8082
   › Scan the QR code above with Expo Go (Android) or the Camera app (iOS)

   › Web is waiting on http://localhost:8082

   › Using Expo Go
   › Press s │ switch to development build

   › Press a │ open Android
   › Press i │ open iOS simulator
   › Press w │ open web
   ```

   b. Make certain `live reload` is working as expected by testing both the web (w) and native (i | a) apps. You can do this by starting those apps and making changes to the `HomeScreen` component defined within `index.tsx`. Changes should be reflected immediately.

## Structure

We will assume some familiar names, but you can fully customize them. After this guide, our basic structure should look like this:

- `apps` - Contains multiple projects, including React Native apps.
- `packages` - Contains different packages used by our apps.
- `package.json` - Root package file, containing Yarn workspaces config.
