# SSCCE an issue with the `create-vite` template `react-ts` linting configuration

[I created an issue on the upstream repository to report this.](https://github.com/vitejs/vite/issues/17647)

## Reproduction


First, I create a new project with `react-ts` template:

```bash
npm create vite@latest . -- --template react-ts
npm install
npm run lint
```

This works fine.

Follow the instructions in the README:

> ## Expanding the ESLint configuration
>
> If you are developing a production application, we recommend updating the
> configuration to enable type aware lint rules:
> ...

At this point, `.eslintrc.cjs` looks like:

```cjs
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/strict-type-checked',
    'plugin:@typescript-eslint/stylistic-type-checked',
    'plugin:react-hooks/recommended',
    'plugin:react/recommended',
    'plugin:react/jsx-runtime',
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
    project: ['./tsconfig.json', './tsconfig.node.json'],
    tsconfigRootDir: __dirname,
  },
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
  },
}
```

Then run `npm run lint` again to reproduce the error. 1 error of 3 similar errors shown:

```
/home/robatt/Projects/_tmp/test-create-vite-react-ts/src/App.tsx
  0:0  error  Parsing error: ESLint was configured to run on `<tsconfigRootDir>/src/App.tsx` using `parserOptions.project`: 
- <tsconfigRootDir>/tsconfig.json
- <tsconfigRootDir>/tsconfig.node.json
However, none of those TSConfigs include this file. Either:
- Change ESLint's list of included files to not include this file
- Change one of those TSConfigs to include this file
- Create a new TSConfig that includes this file and include it in your parserOptions.project
See the typescript-eslint docs for more info: https://typescript-eslint.io/troubleshooting/typed-linting#i-get-errors-telling-me-eslint-was-configured-to-run--however-that-tsconfig-does-not--none-of-those-tsconfigs-include-this-file
```


---

# React + TypeScript + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend updating the configuration to enable type aware lint rules:

- Configure the top-level `parserOptions` property like this:

```js
export default {
  // other rules...
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
    project: ['./tsconfig.json', './tsconfig.node.json'],
    tsconfigRootDir: __dirname,
  },
}
```

- Replace `plugin:@typescript-eslint/recommended` to `plugin:@typescript-eslint/recommended-type-checked` or `plugin:@typescript-eslint/strict-type-checked`
- Optionally add `plugin:@typescript-eslint/stylistic-type-checked`
- Install [eslint-plugin-react](https://github.com/jsx-eslint/eslint-plugin-react) and add `plugin:react/recommended` & `plugin:react/jsx-runtime` to the `extends` list
