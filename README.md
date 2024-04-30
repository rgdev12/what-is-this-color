# What Is This Color?

Description
---------------------------------------------
This project is a simple color picker from an image. It also has two other tools: you can choose a color and get its RGB adn HEX values, or you can enter a color and get a color palette from the entered color. 

## 🚀 Project Structure

Inside of this project, you'll see the following structure:

```text
/
├── public/
│   └── favicon.svg
├── src/
│   ├── assets/
│   │   └── icons/
│   │   └── img/
│   ├── components/
│   │   └── Card.astro
|   │   └── Header.astro
|   │   └── ...
│   ├── layouts/
│   │   └── Layout.astro
│   └── pages/
│       └── index.astro
└── package.json
```

Astro looks for `.astro` or `.md` files in the `src/pages/` directory. Each page is exposed as a route based on its file name.

There's nothing special about `src/components/`, but that's where we like to put any Astro/React/Vue/Svelte/Preact components.

Any static assets, like images, can be placed in the `public/` directory.

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |
