# Comenzando

## Nuestra primera máquina

Supongamos que queremos modelar una [Promise](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Promise) como una máquina de estado. Primero, instala XState usando NPM o Yarn:

```bash
npm install xstate --save
```

> Si estás usando VSCode, deberás instalar nuestra [Extensión de VSCode](https://marketplace.visualstudio.com/items?itemName=statelyai.stately-vscode) que te permitirá  visualizar la máquina que estás construyendo a medida que avanzas.

Luego, en tu proyecto, importa `createMachine`, que es una función que crea una máquina de estado.

``js
import { createMachine } from 'xstate';

const promiseMachine = createMachine(/* ... */);
```
Pasaremos la [configuración de la máquina](./machines.md#configuration) a `createMachine`. Tendremos que proporcionar el:

- `id` - para identificar la máquina
- `initial` - para especificar el nodo de estado inicial en el que debe estar la máquina
- `states` - para definir cada uno de los estados hijos:

``js
import { createMachine } from 'xstate';

const promiseMachine = createMachine({
  id: 'promise',
  initial: 'pending',
  states: {
    pending: {},
    resolved: {},
    rejected: {}
  }
});
```

A continuación, tenemos que añadir [transiciones](./transitions.md) a los nodos de estado.

```js
import { createMachine } from 'xstate';

const promiseMachine = createMachine({
  id: 'promise',
  initial: 'pending',
  states: {
    pending: {
      on: {
        RESOLVE: { target:  'resolved' },
        REJECT:  { target:  'rejected' }
      }
    },
    resolved: {},
    rejected: {}
  }
});
```

También tendremos que marcar los nodos de estado `resolved` y `rejected` como [nodos de estado final](./final.md) ya que la máquina promise termina de ejecutarse una vez que alcanza esos estados:

``js
import { createMachine } from 'xstate';

const promiseMachine = createMachine({
  id: 'promise',
  initial: 'pending',
  states: {
    pending: {
      on: {
        RESOLVE:  { target: 'resolved' },
        REJECT:   { target: 'rejected' }
      }
    },
    resolved: {
      type: 'final'
    },
    rejected: {
      type: 'final'
    }
  }
});
```

Nuestra máquina está lista para ser visualizada. Puedes copiar/pegar el código anterior y [visualizarlo en Stately Viz](https://stately.ai/viz). Así es como se verá:

<iframe src="https://stately.ai/viz/embed/68548871-eecb-479b-b92a-b261e7d89671?mode=viz&panel=code&readOnly=1&showOriginalLink=1&controls=0&pan=0&zoom=0"
allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## Ejecución de nuestra máquina

La forma de ejecutar nuestra máquina depende de dónde pensemos utilizarla.

### En Node/Vanilla JS

Para [interpretar](./interpretation.md) la máquina y hacerla andar, necesitamos añadir un intérprete. Esto crea un servicio:

``js
import { createMachine, interpret } from 'xstate';

const promiseMachine = createMachine({
  /* ... */
});

const promiseService = interpret(promiseMachine).onTransition((state) =>
  console.log(state.value)
);

// Start the service 
promiseService.start();
// => 'pending'

promiseService.send({ tipo: 'RESOLVE' });
// => 'resolved'
```

### En React

Si quisiéramos utilizar nuestra máquina dentro de un componente React, podemos usar el hook [useMachine](../packages/xstate-react/index.md#api):

> Tendrás que instalar `@xstate/react`.

```tsx
import { useMachine } from '@xstate/react';

const Component = () => {
  const [state, send] = useMachine(promiseMachine);

  return (
    <div>
      {/** You can listen to what state the service is in */}
      {state.matches('pending') && <p>Loading...</p>}
      {state.matches('rejected') && <p>Promise Rejected</p>}
      {state.matches('resolved') && <p>Promise Resolved</p>}
      <div>
        {/** You can send events to the running service */}
        <button onClick={() => send('RESOLVE')}>Resolve</button>
        <button onClick={() => send('REJECT')}>Reject</button>
      </div>
    </div>
  );
};
```

