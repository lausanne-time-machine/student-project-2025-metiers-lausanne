# Cartographie des revenus dans la ville de Lausanne
## Une étude de la cartographie des métiers à Lausanne durant le XXe siècle, par le groupe Neuchâtel Fun Machine

This is an [Observable Framework](https://observablehq.com/framework/) app. To install the required dependencies, run:

```
npm install
```

Then, to start the local preview server, run:

```
npm run dev
```

Then visit <http://localhost:3000> to preview your app.

For more, see <https://observablehq.com/framework/getting-started>.

## Project structure

A typical Framework project looks like this:

```ini
.
├─ src
│  ├─ data
│  │  ├─ images                # images for the website
│  │  └─ plot_data             # data for the plots on the website
│  ├─ a_propos.md              # a small about us page
│  ├─ methodo.md               # our methods
│  └─ index.md                 # the home page, where all the magic happens
├─ .gitignore
├─ observablehq.config.js      # the app config file
├─ package.json
└─ README.md
```

## Command reference

| Command           | Description                                              |
| ----------------- | -------------------------------------------------------- |
| `npm install`            | Install or reinstall dependencies                        |
| `npm run dev`        | Start local preview server                               |
| `npm run build`      | Build your static site, generating `./dist`              |
| `npm run deploy`     | Deploy your app to Observable                            |
| `npm run clean`      | Clear the local data loader cache                        |
| `npm run observable` | Run commands like `observable help`                      |
