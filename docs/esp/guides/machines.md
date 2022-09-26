# Máquinas

Una máquina de estado es un conjunto finito de estados que pueden transitar de  unos a otros de forma determinista como consecuencia de  eventos. Para conocer más, lea nuestra [introducción a statecharts](./introduction-to-state-machines-and-statecharts/index.md).


## Configuración

Las máquinas de estado y statecharts se definen usando la factory function `createMachine()`:


```js
import { createMachine } from 'xstate';

const lightMachine = createMachine({
  // Identificador de la máquina 
  id: 'light',

  // Initial state
  initial: 'green',

  // Contexto local para toda la máquina 
  context: {
    elapsed: 0,
    direction: 'east'
  },

  // Definiciones de estados 
  states: {
    green: {
      /* ... */
    },
    yellow: {
      /* ... */
    },
    red: {
      /* ... */
    }
  }
});
```

La configuración de la máquina es la misma que la [configuración del nodo de estado](./statenodes.md), con la adición de la propiedad context:

- `context` - representa el "estado extendido" para todos los estados anidados de la máquina. Consulte  [la documentación de context](./context.md) para más detalles.

## Options

Las implementaciones de  [actions](./actions.md), [delays](./delays.md), [guards](./guards.md), y [services](./communication.md) pueden ser identificadas en la configuración de la máquina usando un string, y luego ser especificadas como un objeto en el segundo argumento de  `createMachine()`: 


```js
const lightMachine = createMachine(
  {
    id: 'light',
    initial: 'green',
    states: {
      green: {
        // action identificación usando un  string
        entry: 'alertGreen'
      }
    }
  },
  {
    actions: {
      // action implementación
      alertGreen: (context, event) => {
        alert('Green!');
      }
    },
    delays: {
      /* ... */
    },
    guards: {
      /* ... */
    },
    services: {
      /* ... */
    }
  }
);
```

Este objecto tiene 5 propiedades opcionales:

- `actions` - el mapeo de los nombres de  action a su implementación 
- `delays`  - el mapeo de los nombres de  delay a su implementación 
- `guards`  - el mapeo de los nombres de  transition guard (`cond`) a su implementación 
- `services` - el mapeo de los nombres de invoked service (`src`) a su implementación
- `activities` (deprecated) - el mapeo de los nombres de activity a su implementación 



## Extendiendo  Máquinas

Las máquinas existentes se pueden extender usando `.withConfig()`, que toma la misma estructura del objeto anterior: 

```js
const lightMachine = // (igual que el ejemplo anterior)

const noAlertLightMachine = lightMachine.withConfig({
  actions: {
    alertGreen: (context, event) => {
      console.log('green');
    }
  }
});
```

## Context Inicial

Como se muestra en el primer ejemplo, el `context` es definido directamente en la propia configuración. Si quieres extender una máquina existente con un `context` inicial diferente, puedes usar `.withContext()` y pasar el `context` personalizado: 

```js
const lightMachine = // (igual que el ejemplo anterior)

const testLightMachine = lightMachine.withContext({
  elapsed: 1000,
  direction: 'north'
});
```

::: advertencia
Esto _no_ hará un shallow merge del `context` original, y en su lugar _reemplazará_ el `context` original con el `context` proporcionado a `.withContext(..)`. Puedes "fusionar" el `context` manualmente, haciendo referencia a `machine.context`:


```js
const testLightMachine = lightMachine.withContext({
  // fusionar con el context original
  ...lightMachine.context,
  elapsed: 1000
});
```

:::

