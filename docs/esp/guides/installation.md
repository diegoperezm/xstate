# Instalación

Puedes instalar XState usando NPM or Yarn, o  puedes embeber el `<script>` directamente usando un CDN. 

## Package Manager

```bash
npm install xstate@latest --save
# o:
yarn add xstate@latest --save
```

## CDN

Puedes incluir XState directamente usando [unpkg CDN](https://unpkg.com/xstate@4/dist/):

- XState core: [https://unpkg.com/xstate@4/dist/xstate.js](https://unpkg.com/xstate@4/dist/xstate.js)
- XState web: [https://unpkg.com/xstate@4/dist/xstate.web.js](https://unpkg.com/xstate@4/dist/xstate.web.js)
  - Browser-friendly, ES module build

```html
<script src="https://unpkg.com/xstate@4/dist/xstate.js"></script>
```

La variable `XState` estará disponible globalmente, lo cual te dará accesso a los top-level exports.

```js
const { createMachine, actions, interpret } = XState; // variable global: window.XState

const lightMachine = createMachine({
  // ...
});

const lightService = interpret(lightMachine);
```
