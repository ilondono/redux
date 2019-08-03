# Introducción

Redux es una librería que nos permite manejar el estado de la aplicación.
No está construido para trabajar solo con React, por lo que esta sección solo se va a enfocar en Redux sin interactuar con React.

## Ciclo Redux:

Para administrar el estado de la aplicación utilizando Redux se deben crear varios elementos que permitan ejecutar el ciclo de vida:

* Action Creators
* Action
* Dispatch
* Reducers
* State

![alt text](https://raw.githubusercontent.com/ilondono/redux/master/ReduxCycleExplained.PNG "Ciclo Redux explicado.")

# Ejemplo:

Suponga que existe una compañía de seguros que cuenta con tres áreas de operación:

* Suscripción
* Reclamaciones
* Contabilidad

La compañía cuenta con un único repositorio centralizado de datos, en el que se encuentran registrados:

* Pólizas existentes (Para facilidad del ejercicio, solo se identifican por el nombre del tomador del seguro y además cuenta con un valor de prima mensual).
* Reclamaciones realizadas (Quién la hizo y por cuánto fue).
* Capital con el que cuenta la compañía (Reserva, en caso de tener que responder ante una reclamación).

Cuando un cliente quiere adquirir un seguro, se acerca a un auxiliar en una oficina y le entrega un **formulario de Solicitud**.
El auxiliar requiere sacar copias de dicho formulario y **pasarlo a cada área de operación** para que los interesados realicen las operaciones pertinentes y **actualicen el repositorio de información** (Cambios de estado de la aplicación).

> Este proceso descrito para un nuevo seguro aplica también en caso de que un cliente quiera hacer una reclamación o incluso cancelar su póliza.

La analogía con el ciclo de Redux aplicada a este ejemplo sería la siguiente:

![alt text](https://raw.githubusercontent.com/ilondono/redux/master/ReduxCycleVsCiaSeguros.PNG "Ciclo Redux Compañía de seguros")

## Implementación:

Para la implementación vamos a usar la herramienta [Codepen](https://codepen.io):

###### Creamos un nuevo espacio de trabajo a través del botón ***Create pen*

![alt text](https://raw.githubusercontent.com/ilondono/redux/master/Codepen_1.PNG "Create Pen")

###### Debemos agregar la librería de Redux. Para eson, en la opción **Settings**, seleccionamos la pestaña **Javascript** y buscamos redux. Seleccionamos el primer resultado:

![alt text](https://raw.githubusercontent.com/ilondono/redux/master/Codepen_2.PNG "Importar Redux")


Ahora utilizamos el área de trabajo de JS para proceder a crear todos los elementos necesarios para llevar a cabo el cambio de estado de la aplicación usando Redux:

#### Action Creators:

```javascript
const crearPoliza = (nombre, prima) => {  
  return {
    type: 'CREAR_POLIZA',
    payload: {
      nombre: nombre,
      prima: prima,
    },
  };   
}; 

const crearReclamacion = (nombre, montoDelReclamo) => {
  return {
    type: 'CREAR_RECLAMACION',
    payload: {
      nombre: nombre,
      montoDelReclamo: montoDelReclamo,
    },
  }; 
}; 

const cancelarPoliza = (nombre) => {
  return {
    type: 'CANCELAR_POLIZA',
    payload: {
      nombre: nombre,
    },
  }; 
};
```

#### Actions
Son los elementos que retornan los **Action creator** del paso anterior.

#### Dispatch
Como vamos a ver en el código, redux ya tiene la funcionalidad de **Dispatch** de forma nativa, por lo que no es necesario construirla.


#### Reducers:

```javascript
const historialReclamaciones = (historialReclamacionesAnterior = [], action) => {
  
  if (action.type === 'CREAR_RECLAMACION') {
    return [...historialReclamacionesAnterior, action.payload];
  }  
  return historialReclamacionesAnterior;  
};

const contabilidad = (reserva = 100, action) => {
  
  if (action.type === 'CREAR_POLIZA') {
    return reserva + action.payload.prima;
  } else if (action.type === 'CREAR_RECLAMACION') {
    return reserva - action.payload.montoDelReclamo;
  }  
  return reserva; 
}; 

const polizas = (polizasExistentes = [], action) => {  
  if (action.type === 'CREAR_POLIZA') {
      return [...polizasExistentes, action.payload.nombre]; 
  } else if (action.type === 'CANCELAR_POLIZA') {
    return polizasExistentes.filter(nombre => nombre !== action.payload.nombre);
  }
  return polizasExistentes; 
};
```

> En el Reducer **contabilidad** estamos iniciando al parámetro **reserva** en 100.

#### State

Para manejar el estado, ahora sí entra en acción la librería de Redux. Se requiere importar dos objetos:
* combineReducers: permite enlazar todos los reducers.
* createStore: permite crear el objecto **store** a partir de lo que nos entrega **combineReducers**. El **store** representa en sí a Redux en el proceso.

```javascript
const { createStore, combineReducers } = Redux;

const departamentos = combineReducers({
  contabilidad: contabilidad,
  historialReclamaciones: historialReclamaciones,
  polizas: polizas,
}); 

const store = createStore(departamentos);
```

> Como se mencionó más arriba, no se implementó **Dispatch** porque Redux ya la tiene nativa. Se debe utilizar a través del objeto **store**:

```javascript
console.log(store.getState());

const action = crearPoliza('Andres', 20);
store.dispatch(action);

// También se pueden usar los Action Creator dentro del parámetro enviado al dispatch:
store.dispatch(crearPoliza('Diego', 15));
store.dispatch(crearPoliza('Dairo', 35));

console.log(store.getState());

store.dispatch(crearReclamacion('Andres', 80));
console.log(store.getState());

store.dispatch(cancelarPoliza('Dairo'));
console.log(store.getState());
```

# Notas

Para más documentación al respecto, está disponible el sitio de Redux:

*	[Sitio oficial Redux](https://redux.js.org/)

En la siguiente sección vamos a ver el uso de Redux en React.




	




