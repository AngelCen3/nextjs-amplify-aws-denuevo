# Crea Aplicacion en tiempo Record con Nextjs y AWS Amplify (Gen2)

## [Crea Aplicacion en tiempo Record con Nextjs y AWS Amplify (Gen2)](https://www.youtube.com/watch?v=EJjiK16Lw_8&t=1172s)

### Una de las primeras cosas que vamos hacer va ser crear una funcion para eliminar un item pero lo primero es mostrar el archivo sin modificar

app\page.tsx

```tsx
"use client";

import { useState, useEffect } from "react";
import { generateClient } from "aws-amplify/data";
import type { Schema } from "@/amplify/data/resource";
import "./../app/app.css";
import { Amplify } from "aws-amplify";
import outputs from "@/amplify_outputs.json";
import "@aws-amplify/ui-react/styles.css";

Amplify.configure(outputs);
/**********************************************BETO*****client es la coneccion con la base de datos */ 
const client = generateClient<Schema>();

export default function App() {
  const [todos, setTodos] = useState<Array<Schema["Todo"]["type"]>>([]);

  function listTodos() {
    client.models.Todo.observeQuery().subscribe({
      next: (data) => setTodos([...data.items]),
    });
  }

  useEffect(() => {
    listTodos();
  }, []);

  function createTodo() {
    client.models.Todo.create({
      content: window.prompt("Todo content"),
    });
  }

  return (
    <main>
      <h1>My todos</h1>
      <button onClick={createTodo}>+ new</button>
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>{todo.content}</li>
        ))}
      </ul>
      <div>
        🥳 App successfully hosted. Try creating a new todo.
        <br />
        <a href="https://docs.amplify.aws/nextjs/start/quickstart/nextjs-app-router-client-components/">
          Review next steps of this tutorial.
        </a>
      </div>
    </main>
  );
}
```

### Y este es el modificado

app\page.tsx

```tsx
"use client";

import { useState, useEffect } from "react";
import { generateClient } from "aws-amplify/data";
import type { Schema } from "@/amplify/data/resource";
import "./../app/app.css";
import { Amplify } from "aws-amplify";
import outputs from "@/amplify_outputs.json";
import "@aws-amplify/ui-react/styles.css";
/********************************************BETO*****Este es el archivo de configuracion que bajamos */
Amplify.configure(outputs);
/*****************************************BETO******client es la coneccion con la base de datos */ 
const client = generateClient<Schema>();

export default function App() {
  const [todos, setTodos] = useState<Array<Schema["Todo"]["type"]>>([]);
/**************************************BETO ****Y todo eso viene desde models ( Informacion) */
  function listTodos() {
    client.models.Todo.observeQuery().subscribe({     /**BETO****Sirve para refrescar los datos -> observeQuery()*/
      next: (data) => setTodos([...data.items]),
    });
  }
/******************************BETO *****Inicialmente cuando carga la aplicacion el carga las listas de tareas*/
  useEffect(() => {
    listTodos();
  }, []);

  function createTodo() {
    client.models.Todo.create({
      content: window.prompt("Todo content"),
    });
  }

  /////////ESTAS LINEAS DE CODIGO FUERON LAS QUE SE AGREGARON/////////BETO ************


  /*****Cada ves que se llame a la funcion deleteTodo resivira un id con el tipo string*/
  function deleteTodo( id: string) {
    /************Se puede poner tambien asi -> client.models.Todo.delete({id: id});*/
    client.models.Todo.delete({id});
  }


  /////////////////////////////////////////////////////

  return (
    /********************BETO *********************Desde main esta recorriendo todas las tareas */
    <main>
      <h1>My todos</h1>
      <button onClick={createTodo}>+ new</button>
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}
          /*************onClick={() => deleteTodo(todo.id)}*******BETO ****Esta linea se agrego */
            onClick={() => deleteTodo(todo.id)}
          >{todo.content}</li>
        ))}
      </ul>
      <div>
        🥳 App successfully hosted. Try creating a new todo.
        <br />
        <a href="https://docs.amplify.aws/nextjs/start/quickstart/nextjs-app-router-client-components/">
          Review next steps of this tutorial.
        </a>
      </div>
    </main>
  );
}
```

## Ahora vamos con la autenticasion

Para eso instalaremos un paquete para tener interfaces graficas
Esto es como tener chat100 o materrialIUX

```bash
npm add @aws-amplify/ui-react
```

He importamos para poder usar lo que instalamos

```tsx
"use client";

import { useState, useEffect } from "react";
import { generateClient } from "aws-amplify/data";
import type { Schema } from "@/amplify/data/resource";
import "./../app/app.css";
import { Amplify } from "aws-amplify";
import outputs from "@/amplify_outputs.json";
import "@aws-amplify/ui-react/styles.css";  



/********************************************Esto se una despues de instalar > *******npm add @aws-amplify/ui-react *********/
import { Authenticator } from "@aws-amplify/ui-react"; /****BETO **Este componente va englobar toda la pagina */
```

### Usamos Authenticator

Lo encerramos dentro de una funcion y hacemos esto por que puede regresar valores como
(singOut) o informacion del usuario (user)

```tsx
{({ signOut }) => (  <main> content </main>   )} 
ya que puede tener valores como 
```

```tsx
return (
    /********************BETO *********************Desde main esta recorriendo todas las tareas */
    /******Primero -->   npm add @aws-amplify/ui-react */
    /******Segundo ---> import { Authenticator } from "@aws-amplify/ui-react";*/
    /******Tercero --->  Con <Authenticator>  <main> content </main> </Authenticator> que englobe la aplicacion */
    /******Lo encerramos dentro de una funcion {({ signOut }) => (  <main> content </main>   )} */
    <Authenticator>
    <main>
      <h1>My todos</h1>
      <button onClick={createTodo}>+ new</button>
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}
          /*************onClick={() => deleteTodo(todo.id)}*******BETO ****Esta linea se agrego */
            onClick={() => deleteTodo(todo.id)}
          >{todo.content}</li>
        ))}
      </ul>
      <div>
        🥳 App successfully hosted. Try creating a new todo.
        <br />
        <a href="https://docs.amplify.aws/nextjs/start/quickstart/nextjs-app-router-client-components/">
          Review next steps of this tutorial.
        </a>
      </div>
    </main>
    </Authenticator>
  );
```

## Ahora nos falta es registranos

La implementacion de la autenticacion ya viene con logica
Y por el momento utilizaremos un servicion para generar imails

[10minutemail.com](https://10minutemail.com/)

## Hasta los cambios que se han hecho solo son locales

Para actualizar en la app en aws tenemos que hacer 

```bash

```
