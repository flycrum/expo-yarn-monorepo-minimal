# minimal-expo-yarn-monorepo

With first-class support, yarn is arguably the easiest way to manage a monorepo with the challenges that come with Expo dependencies (starting with Expo SDK 48).

## Docs

- [Expo: Work with monorepos](https://docs.expo.dev/guides/monorepos)
- [Expo: Upgrade Expo SDK](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough)

## Step-by-step approach

> Note: In this example, we will set up a monorepo using Yarn workspaces without the nohoist option.

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

## Structure

We will assume some familiar names, but you can fully customize them. After this guide, our basic structure should look like this:

- `apps` - Contains multiple projects, including React Native apps.
- `packages` - Contains different packages used by our apps.
- `package.json` - Root package file, containing Yarn workspaces config.
