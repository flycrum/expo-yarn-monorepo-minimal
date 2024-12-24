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

6. Create a package.

   a. From within the root of the monorepo, create the package directory and initialize it with yarn.

   ```bash
   mkdir -p packages/cool-package
   cd packages/cool-package
   yarn init
   ```

   b. Add a simple `index.js` file to the package.

   ```bash
   echo "export const greeting = 'Hello!';" > index.js
   ```

7. Add the package to the app.

   a. Add `packages/cool-package` to `apps/cool-app/package.json` so we can use it within our Expo app.

   ```bash
   yarn add cool-package
   ```

   Note: Like standard packages, we need to add our cool-package as a dependency to our cool-app. The main difference between a standard package, and one from the monorepo, is you'll always want to use the "current state of the package" instead of a version. Let's add cool-package to our app by adding "cool-package": "\*" to our app package.json file.

   b. After adding the package as a dependency, run yarn install to install or link the dependency to your app.

   ```bash
   yarn install
   ```

   c. You should now be able to use the package inside your app! To test this, let's edit the `HomeScreen` component defined within `index.tsx` again:

   ```js
   import { greeting } from "cool-package";

   // ...

   <ThemedView style={styles.titleContainer}>
     <ThemedText type="title">{greeting}</ThemedText>
     <HelloWave />
   </ThemedView>;

   // ...
   ```

   d. When running the apps, you should now see `Hello!` displayed instead of `Welcome!`.

## Issues

<details>
  <summary>
    Error: <span style="color: red;">Unable to resolve "../../App" from "node_modules/expo/AppEntry.js"</span>
  </summary>
  &nbsp;

> Note: as seen when running `npx expo start`

<strong>Problem:</strong> you're likely trying to run the app from the wrong location -- like the root of the monorepo, but the app is located in the `apps/cool-app` directory.

<strong>Solution:</strong> run the command from the `apps/cool-app` directory instead.

</details>

Also:

- With monorepos comes added complexity and issues you'll need to solve along the way
- Expo SDK 50 and higher has improved support for more complete node_modules patterns, such as isolated modules. Unfortunately, React Native can still cause issues when installing multiple versions inside a single monorepo. Because of that, it's recommended to only use a single version of React Native. You can check if your monorepo has multiple React Native versions and why they are installed through the package manager you use.
  ```bash
  yarn why react-native
  ```

## Structure

We will assume some familiar names, but you can fully customize them. After this guide, our basic structure should look like this:

- `apps` - Contains multiple projects, including React Native apps.
- `packages` - Contains different packages used by our apps.
- `package.json` - Root package file, containing Yarn workspaces config.
