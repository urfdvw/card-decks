## how to start a react project

ref: https://vite.dev/guide/#scaffolding-your-first-vite-project

```
(base) river@Hechuans-MBP bible-presenter % npm create vite@latest .
Need to install the following packages:
  create-vite@6.1.1
Ok to proceed? (y) y
✔ Current directory is not empty. Please choose how to proceed: › Ignore files and continue
✔ Select a framework: › React
✔ Select a variant: › JavaScript
```

then
- test run
    - `npm i`
    - `npm run dev` to verify
- clean up
    - remove content of `README.md`
    - `index.html`
        - `<link rel="icon" type="image/svg+xml" href="/vite.svg" />`
        - `<title>`
    - delete `public/vite.svg`
    - src
        - empty `src/assets`
        - remove content of `src/App.css`
        - remove content of `src/index.css`
        - App.jsx
            - remove
                ```js
                import reactLogo from './assets/react.svg'
                import viteLogo from '/vite.svg'
                ```
            - change return to only `<button onClick={() => setCount((count) => count + 1)}>count is {count}</button>`
    - autoformat all files

---

## Useful react libs

- `npm install <package-name>`
    - flexlayout-react
- `npm install <package-name> --save-dev`
    - vite-plugin-singlefile

---

## How to upgrade node

- `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash`
- `source ~/.nvm/nvm.sh`
- `nvm install node`
- `nvm use node`

---

## How to use `vite-plugin-singlefile`

ref: https://github.com/urfdvw/bible-presenter/commit/ad8e7a3d89aecbaf9d9b761891f729e8174587b4

- `npm install vite-plugin-singlefile --save-dev`
- in `vite.config.js`
    - `import { viteSingleFile } from "vite-plugin-singlefile";`
    - add `viteSingleFile()` to plug-in list

---

## How to set up flexlayout
ref
    - https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664
    - https://github.com/urfdvw/bible-presenter/commit/8dc60e32fb64c7356466938ff91c43ef7074d0c2

- layout folder
    - [layout.json]
    - [Factory.jsx]
    - css [from flexlayout](https://github.com/caplin/FlexLayout/blob/master/style/)
- [App.jsx](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-d274a54187c91ba0f532df2a9e194e27ab50e988f5e4c33f5a7893918320c661)
    - [import FlexLayout lib, layout and Factory](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-d274a54187c91ba0f532df2a9e194e27ab50e988f5e4c33f5a7893918320c661R6-R9)
    - create the component, line 12 and 23 in the link above
- css
    - [maximize app](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-60f5dcfc15327d5dd812d9df394c217efbedb4aa33dca782ed69d39dce811972)
    - [disable scroll](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-85da366246340e911d7a0eb9153e64137a1b31f0686c74d9aef9ae4f032cb09e)
    - [add layout style to index.html](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-0eb547304658805aad788d320f10bf1f292797b5e6d745a3bf617584da017051R8)
- [test with place holder tab](https://github.com/urfdvw/bible-presenter/commit/8dc60e32fb64c7356466938ff91c43ef7074d0c2)

---

## How to set up a context
ref
    - https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664
    - https://github.com/urfdvw/bible-presenter/commit/8dc60e32fb64c7356466938ff91c43ef7074d0c2

- [context file](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-5d16999e73e7106daf76777a60ce08c0d1bf46a99194077663870eff6c3b4e90)
- [provider](https://github.com/urfdvw/bible-presenter/commit/8bfa9e11bde057a5129a6da570066fd981b78664#diff-d274a54187c91ba0f532df2a9e194e27ab50e988f5e4c33f5a7893918320c661R17-R25)
- [use context](https://github.com/urfdvw/bible-presenter/commit/8dc60e32fb64c7356466938ff91c43ef7074d0c2#diff-da209dce2ed9d79e90fa3a46174258042533a32f095ab36f9038b173102913a9R5)

---

## How to solve the issue that npm is not loading the latest local package file because of the unchanged file name

- `npm cache clean --force`
- delete `package-lock.json`
- delete `node_modules`
- `npm i` again

---

## How to make a web app installable
> progressive, pwa

ref
- https://web.dev/articles/install-criteria
- https://web.dev/articles/add-manifest
- https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Tutorials/js13kGames