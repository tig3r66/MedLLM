# MedLLM

Everything you need to build a Svelte project, powered by [`create-svelte`](https://github.com/sveltejs/kit/tree/master/packages/create-svelte).

## Cloning the Repository

To clone the repository, type in your command line/terminal:

```
git clone https://github.com/tig3r66/MedLLM.git
cd MedLLM
```

## Creating a project

If you've cloned this repository, you can skip this step. Congrats!

```bash
# create a new project in the current directory
npm create svelte@latest

# create a new project in my-app
npm create svelte@latest my-app
```

## Adding Your API Key

Next, create a .env file and add your OpenAI API key as such (see .env.example for an example). You can get an API by following [these instructions](https://help.openai.com/en/articles/4936850-where-do-i-find-my-secret-api-key).

```
OPENAI_KEY="YOUR-KEY-HERE"
```

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://kit.svelte.dev/docs/adapters) for your target environment.
