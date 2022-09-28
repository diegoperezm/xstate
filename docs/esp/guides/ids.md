# Identificando Nodos De Estado 

[:rocket: Quick Reference](#quick-reference)

Por defecto, el `id` de un nodo de estado es su ruta completa delimitada. Puedes usar este `id` por defecto para especificar un nodo de estado: 

```js
const lightMachine = createMachine({
  id: 'light',
  initial: 'green',
  states: {
    green: {
      // ID por defecto: 'light.green'
      on: {
        // Puedes apuntar a los nodos de estado usando su ID por defecto.
        // Esto es lo mismo que TIMER: 'yellow'
        TIMER: { target: '#light.yellow' }
      }
    },
    yellow: {
      on: {
        TIMER: { target: 'red' }
      }
    },
    red: {
      on: {
        TIMER: { target: 'green' }
      }
    }
  }
});
```

## Relative Targets

Los nodos de estado hijos pueden ser apuntados relativos a su padre especificando un punto (`'.'`) seguido por su clave: 


```js {10-12}
const optionsMachine = createMachine({
  id: 'options',
  initial: 'first',
  states: {
    first: {},
    second: {},
    third: {}
  },
  on: {
    SELECT_FIRST: { target: '.first' }, // se resuelve a 'options.first'
    SELECT_SECOND: { target: '.second' }, // 'options.second'
    SELECT_THIRD: { target: '.third' } // 'options.third'
  }
});
```
Por defecto, los relative targets son  [transiciones internas](./transitions.md#internal-transitions),
lo que significa que el estado padre _no_ saldrá y volverá a entrar. Puedes hacer que los relative targets sean transiciones externas especificando `internal: false`:


```js {4}
// ...
on: {
  SELECT_FIRST: {
    target: { target: '.first' },
    internal: false // transición externa, saldrá/entrará del nodo de estado padre 
  }
}
```

## IDs Personalizados

Los nodos de estados pueden ser apuntados usando identificadores únicos, en lugar de identificadores relativos. Esto puede simplificar la creación de statecharts complejos.

Para especificar un ID para un nodo de estado, proporciona un string identificador único como su propiedad  `id`, e.g., `id: 'greenLight'`.

Para apuntar a un nodo de estado usando su ID, anteponga el símbolo `'#'` al string ID, e.g., `TIMER: '#greenLight'`.



```js
const lightMachine = createMachine({
  id: 'light',
  initial: 'green',
  states: {
    green: {
      // identificador personalizado
      id: 'greenLight',
      on: {
        // apunte al  nodo de estado usando su ID 
        TIMER: { target: '#yellowLight' }
      }
    },
    yellow: {
      id: 'yellowLight',
      on: {
        TIMER: { target: '#redLight' }
      }
    },
    red: {
      id: 'redLight',
      on: {
        // targets relativos seguirán funcionando 
        TIMER: { target: 'green' }
      }
    }
  }
});
```

**Notas:**

- Siempre se recomienda usar IDs para el nodo de estado raíz.
- Asegúrese de que todos los IDs son únicos para evitar conflictos  de nombres. Esto es naturalmente impuesto por los IDs generados automáticamente.


::: advertencia
No mezcle identificadores personalizados con identificadores relativos. Por ejemplo, si el nodo de estado `red` tiene un identificador personalizado `"redLight"` y un nodo de estado hijo `walking`, e.g.:


```js
// ...
red: {
  id: 'redLight',
  initial: 'walking',
  states: {
    // el ID continúa resolviéndose como 'light.red.walking'
    walking: {/* ... */},
    // ...
  }
}
// ...
```
Entonces no puedes apuntar al estado `'walking'` usando `'#redLight.walking'`, porque su ID se resuelve a `'#light.red.walking'`. Un target que comienza con `'#'` siempre se referirá al _match exacto_  de `'#[state node ID]'`.
:::

## Quick Reference

**Por defecto, ID generado automáticamente:**

```js
const lightMachine = createMachine({
  id: 'light',
  initial: 'green',
  states: {
    // ID: "light.green"
    green: {
      /* ... */
    },
    // ID: "light.yellow"
    yellow: {
      /* ... */
    },
    // ID: "light.red"
    red: {
      /* ... */
    }
  }
});
```

**ID Personalizado**

```js
// ...
states: {
  active: {
    id: 'custom-active', // puede ser cualquier string único 
    // ...
  }
}
```

**Apuntando a un nodo de estado por su ID:**

```js
// ...
on: {
  EVENT: { target: '#light.yellow' }, // target ID por defecto 
  ANOTHER_EVENT: { target: '#custom-id' } // target  ID personalizado
}
```

