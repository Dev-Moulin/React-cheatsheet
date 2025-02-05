# React-cheatsheet

## Aide-mémoire

### JSX
JSX signifie JavaScript XML. Il s'agit simplement d'une extension de la syntaxe JavaScript. Elle nous permet d'écrire directement du HTML dans React (à l'intérieur du code JavaScript). Il est facile de créer un modèle en utilisant JSX dans React, mais ce n'est pas un simple langage de template ; il offre toute la puissance de JavaScript.

```javascript
import logo from "./logo.svg";
import "./App.css";
function App() {
  const element = <h1>Hello, world!</h1>;
  return <div>{element}</div>;
}
export default App;
```

Explication : JSX est une façon pratique d'écrire du HTML à l'intérieur de votre code JavaScript React. React utilise ensuite ce JSX pour créer l'interface utilisateur. Le code ci-dessus montre un exemple très basique d'un composant React qui utilise JSX pour afficher "Hello, world!".

### Props
Dans ReactJS, les "props" (abréviation de "properties") sont un type d'objet où la valeur des attributs d'une balise est stockée. Le mot "props" implique "propriétés", et sa fonctionnalité est très similaire aux attributs HTML. Fondamentalement, ces composants "props" sont des composants en lecture seule.

```javascript
function Introduction(props) {
  return <h1>Hello, I’m {props.name}!</h1>;
}
function Introduction({ name }) {
  return <h1>Hello, I’m {name}!</h1>;
}
const element = <Introduction name="The Doctor" />;
```

Explication : Les props sont une façon de transmettre des données d'un composant parent à un composant enfant. Elles sont comme des arguments que vous passez à une fonction. Le composant enfant ne peut pas modifier les props qu'il reçoit ; elles sont en "lecture seule". Le code ci-dessus montre deux façons d'accéder aux props : en utilisant l'objet props directement (props.name) ou en utilisant la déstructuration ({ name }).

### État (State)
#### Qu'est-ce que l'état (state) ?
React possède un autre objet intégré spécial appelé "state" (état), qui permet aux composants de créer et de gérer leurs propres données. Ainsi, contrairement aux "props", les composants ne peuvent pas transmettre de données avec "state", mais ils peuvent les créer et les gérer en interne.

Explication (ajoutée) : L'état, contrairement aux props, est une donnée interne à un composant qui peut être modifiée. Il représente l'état actuel du composant et contrôle son comportement et son affichage. Lorsqu'un état change, React redessine l'interface utilisateur du composant.

### De l'état des composants aux Hooks
Nous allons commencer avec un compteur super simple.

```javascript
class Counter extends Component {
  render() {
    return (
      <main className="Counter">
        <p className="count">0</p>
        <section className="controls">
          <button>Incrementer</button>
          <button>Décrémenter</button>
          <button>Réinitialiser</button>
        </section>
      </main>
    );
  }
}
```

#### Mise en place du composant de base
Nous allons commencer par une méthode constructor qui définit l'état du composant.

```javascript
constructor(props) {
  super(props);
  this.state = {
    count: 0,
  };
}
```

Nous utiliserons cet état dans le composant.

```javascript
render() {
  const { count } = this.state;
  return (
    <main className="Counter">
      <p className="count">{count}</p>
      <section className="controls">
        <button>Incrémenter</button>
        <button>Décrémenter</button>
        <button>Réinitialiser</button>
      </section>
    </main>
  );
}
```

Nous allons implémenter les méthodes pour incrémenter, décrémenter et réinitialiser le compteur.

```javascript
increment() {
  this.setState({ count: this.state.count + 1 });
}

decrement() {
  this.setState({ count: this.state.count - 1 });
}

reset() {
  this.setState({ count: 0 });
}
```

Nous ajouterons ces méthodes aux boutons.

```javascript
<button onClick={this.increment}>Incrémenter</button>
<button onClick={this.decrement}>Décrémenter</button>
<button onClick={this.reset}>Réinitialiser</button>
```

Nous devons lier ces écouteurs d'événements parce que tout est horrible. (Note : Ceci est une blague faisant référence à la complexité de la liaison des méthodes dans les composants de classe React.)

```js
constructor(props) {
  super(props);
  this.state = {
    count: 3,
  };

  this.increment = this.increment.bind(this);
  this.decrement = this.decrement.bind(this);
  this.reset = this.reset.bind(this);
}
```

**Explication** : Dans les composants de classe React, il est nécessaire de lier explicitement les méthodes de la classe au contexte `this` si vous voulez les utiliser comme gestionnaires d'événements. Sinon, `this` sera `undefined` à l'intérieur de la méthode. Cette exigence a été l'une des raisons pour lesquelles les hooks ont été introduits, car ils évitent ce problème. La note entre parenthèses est de l'humour, car la liaison (`bind`) est considérée comme une tâche fastidieuse par beaucoup de développeurs.

---

## Quiz sur l'état des composants

### Mises à jour asynchrones et mise en file d'attente

D'accord, disons que nous avons refactorisé `increment()` comme suit :

```js
increment() {
  this.setState({ count: this.state.count + 1 });
  this.setState({ count: this.state.count + 1 });
  this.setState({ count: this.state.count + 1 });

  console.log(this.state.count);
}
```

Deux questions :
1. Qu'est-ce qui sera enregistré dans la console ?
2. Quelle sera la nouvelle valeur ?

**Explication** : `this.setState` est une fonction asynchrone. Cela signifie que les mises à jour de l'état ne sont pas appliquées immédiatement. Au lieu de cela, React met en file d'attente les mises à jour et les applique en lot plus tard. Par conséquent, `console.log(this.state.count)` affichera la valeur avant que les mises à jour ne soient appliquées. La nouvelle valeur, après que les mises à jour en file d'attente ont été traitées, sera `this.state.count + 3`.

---

### Utilisation d'une fonction comme argument

`this.setState` accepte également une fonction. Cela signifie que nous pourrions refactoriser `increment()` comme suit.

```js
this.setState((state) => {
  return { count: state.count + 1 };
});
```

Si nous voulions nous montrer, nous pourrions utiliser la déstructuration pour rendre cela encore plus propre.

```js
increment() {
  this.setState(({ count }) => {
    return { count: count + 1 };
  });
}
```

Il y a des choses potentiellement cool que nous pourrions faire ici. Par exemple, nous pourrions ajouter une certaine logique à notre composant.

```js
this.setState((state, props) => {
  if (state.count >= 10) return;
  return { count: state.count + 1 };
});
```

Disons que nous voulions ajouter un nombre maximum comme "prop".

```js
render(<Counter max={10} />, document.getElementById("root"));
this.setState((state) => {
  if (state.count >= this.props.max) return;
  return { count: state.count + 1 };
});
```
Il s'avère que nous pouvons aussi avoir un deuxième argument : les props.

```js
this.setState((state, props) => {
  if (state.count >= props.max) return;
  return { count: state.count + 1 };
});
```

> **Explication** : Cette partie souligne que la fonction passée à `setState` peut accepter deux arguments : l'état actuel (`state`) et les props du composant (`props`). Cela vous donne la possibilité d'utiliser les props pour déterminer comment l'état doit être mis à jour. Dans cet exemple, la prop `max` est utilisée pour limiter la valeur maximale du compteur.

---

Oh attendez, et si nous faisions cela trois fois ?

```js
increment() {
  this.setState((state, props) => {
    if (state.count >= props.max) return;
    return { count: state.count + 1 };
  });
  this.setState((state, props) => {
    if (state.count >= props.max) return;
    return { count: state.count + 1 };
  });
  this.setState((state, props) => {
    if (state.count >= props.max) return;
    return { count: state.count + 1 };
  });
}
```

> **Explication** : Cette section pose une question intéressante sur le comportement de `setState` lorsqu'il est appelé plusieurs fois de suite. Comme `setState` est asynchrone, React peut regrouper plusieurs appels à `setState` en une seule mise à jour pour améliorer les performances. Cependant, lorsque vous utilisez une fonction comme argument de `setState`, React garantit que chaque fonction reçoit la valeur la plus récente de l'état. Dans cet exemple, même si `setState` est appelé trois fois, chaque fonction recevra la valeur correcte de `state.count` et la mise à jour sera appliquée correctement (tant que `state.count` ne dépasse pas `props.max`). Le résultat est que le compteur ne sera incrémenté qu'une seule fois, car les appels suivants à `setState` ne feront rien une fois que `state.count` aura atteint `props.max`.

---

L'autre chose que nous pouvons faire est de sortir cette fonction du composant. Cela rend les tests unitaires beaucoup plus faciles.

```js
const increment = (state, props) => {
  if (state.count >= props.max) return;
  return { count: state.count + 1 };
};

increment() {
  this.setState(increment);
}
```

> **Explication** : Comme mentionné précédemment, sortir la fonction de mise à jour de l'état du composant améliore la modularité et la testabilité du code. La fonction `increment` peut maintenant être testée indépendamment du composant.

---

### En résumé :

- La fonction passée à `setState` peut accéder à la fois à l'état actuel et aux `props` du composant.
- React garantit que la fonction reçoit la valeur la plus récente de l'état, même si `setState` est appelé plusieurs fois de suite.
- Sortir la fonction de mise à jour de l'état du composant améliore la modularité et la testabilité du code.

---

## Callbacks

`this.setState` prend un deuxième argument en plus de l'objet ou de la fonction. Cette fonction est appelée après que le changement d'état a eu lieu.

```js
this.setState(increment, () => console.log("Callback"));
```

Nous pouvons aussi faire quelque chose comme :

```js
this.setState(increment, () => console.log(this.state));
```

Ou mettre à jour le titre du document :

```js
this.setState(increment, () => (document.title = `Count: ${this.state.count}`));
```

**Explication** : Le deuxième argument de `setState` est une fonction de callback qui est exécutée une fois que l'état a été mis à jour et que le composant a été redessiné. Ceci est utile pour effectuer des actions qui dépendent de la nouvelle valeur de l'état, comme mettre à jour le titre du document.

---

## Refactorisation vers les Hooks

Les Hooks sont un nouveau modèle qui nous permet d'écrire beaucoup moins de code. Préparez-vous à supprimer du code.

Commençons par supprimer tout sauf la méthode `render`.



```javascript
const Counter = ({ max }) => {
  const [count, setCount] = React.useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(0);

  return (
    <main className="Counter">
      <p className="count">{count}</p>
      <section className="controls">
        <button onClick={increment}>Incrémenter</button>
        <button onClick={decrement}>Décrémenter</button>
        <button onClick={reset}>Réinitialiser</button>
      </section>
    </main>
  );
};
```

Alors, qu'est-ce que nous n'avons pas à faire ici ?

Nous n'avons rien à bind.

Nous n'avons pas besoin d'une référence à this.

Nous n'avons pas besoin d'un constructor du tout.

Exécution de certaines de nos expériences précédentes
Que se passe-t-il si nous triplons à nouveau ?

```js
const increment = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
```

Il s'avère également que les setters de useState peuvent également prendre des fonctions.

```js
const increment = () => {
  setCount((c) => c + 1);
};
```

Contrairement à l'utilisation de valeurs, l'utilisation de fonctions fonctionne également de la même manière qu'avec this.setState.

```js
const increment = () => {
  setCount((c) => c + 1);
  setCount((c) => c + 1);
  setCount((c) => c + 1);
};
```

Il y a une différence importante. Vous n'obtenez que l'état dans ce cas. Il n'y a pas de deuxième argument avec les props. Cela dit, nous les avons dans la portée.

Ils ne prennent pas non plus en charge les fonctions de callback comme this.setState. Plus tard, nous utiliserons useEffect pour déclencher des effets secondaires basés sur les changements d'état.

```js
setCount((c) => {
  if (c >= max) return;
  return c + 1;
});
```

Oh, ça explose après dix. Ceci est au cœur de la différence entre le fonctionnement de useState et de this.setState.

Avec this.setState, nous donnons au composant cet objet de valeurs qu'il doit mettre à jour. Avec useState, nous avons une fonction dédiée pour modifier un élément d'état particulier.

Comment pouvons-nous corriger cela ?

```js
setCount((c) => {
  if (c >= max) return c;
  return c + 1;
});
```

## Une brève introduction à useEffect
Nous allons approfondir useEffect, mais faisons d'abord un aperçu de haut niveau.

Use effect nous permet d'implémenter une sorte d'effet secondaire dans notre composant en dehors des changements d'état et de props déclenchant un nouveau rendu.

C'est utile pour une tonne de raisons :
- Stocker des choses dans localStorage.
- Faire des requêtes AJAX.

### Implémentation de localStorage
Mettons en place la configuration de base ici.

```js
const getStateFromLocalStorage = () => {
  const storage = localStorage.getItem("counterState");
  if (storage) return JSON.parse(storage);
  return { count: 0 };
};
```

Nous allons lire la propriété "count" depuis "localStorage".

```js
const [count, setCount] = React.useState(getStateFromLocalStorage().count);
```

Maintenant, nous allons enregistrer un effet secondaire.

```js
React.useEffect(() => {
  localStorage.setItem("counterState", JSON.stringify({ count }));
}, [count]);
```

## Événements
Les gestionnaires d'événements déterminent quelle action doit être entreprise lorsqu'un événement est déclenché.

```js
function ActionLink() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log("The link was clicked.");
  };
  return <button onClick={handleClick}>Click me</button>;
}
```

### Que sont les événements synthétiques dans React ?
React implémente un système d'événements synthétiques qui apporte cohérence et haute performance aux applications.

## Mise à jour de l'état dans un gestionnaire d'événements onClick

```js
import React, { useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Incrémenter</button>
      <button onClick={() => setCount(count - 1)}>Décrémenter</button>
    </div>
  );
};

export default App;
```

## useMemo
useMemo est un Hook React qui vous permet de mettre en cache le résultat d'un calcul entre les rendus.

### Exemple d'utilisation

```js
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  const [evenNum, setEvenNum] = useState(2);

  function evenNumDouble() {
    console.log("double");
    return evenNum * 2;
  }

  return (
    <div>
      <h2>Counter: {count}</h2>
      <h2>Even Numbers: {evenNum}</h2>
      <h2>even Number Double Value: {evenNumDouble()}</h2>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setEvenNum(evenNum + 2)}>Even Numbers</button>
    </div>
  );
}

export default Counter;
```

Explication

Lorsque nous cliquons sur le bouton Nombres pairs, la fonction evenNumDouble() est appelée car l'état de evenNum est modifié.

Cliquer sur le bouton Incrément affiche également la fonction evenNumDouble(), bien que l'état du compteur ne change pas.

Cela signifie que chaque fois que la fonction evenNumDouble() est rendue inutilement (sur la page), le code devient moins efficace. Nous allons corriger cela avec le hook useMemo ci-dessous.

```javascript
import React, { useState, useMemo } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  const [evenNum, setEvenNum] = useState(2);

  const memoHook = useMemo(
    function evenNumDouble() {
      console.log("double");
      return evenNum * 2;
    },
    [evenNum]
  );

  return (
    <div>
      <h2>Counter: {count}</h2>
      <h2>Even Numbers: {evenNum}</h2>
      <h2>even Number Double Value: {memoHook}</h2>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setEvenNum(evenNum + 2)}>Even Numbers</button>
    </div>
  );
}

export default Counter;
```

### Explication

Dans le code ci-dessus, nous avons défini la sortie de la fonction evenNumDouble() dans une constante memoHook.

Cela filtre la fonction via le hook useMemo pour vérifier uniquement si la variable spécifiée (evenNum dans ce cas) a été modifiée ; ce n'est qu'alors que cette fonction sera rendue.

```javascript
import React, { memo, useState } from "react";
import { useContext } from "react";
import { GrudgeContext } from "./GrudgeContext";

const NewGrudge = memo(({ onSubmit }) => {
  const { addGrudge } = React.useContext(GrudgeContext);
  const [person, setPerson] = useState("");
  const [reason, setReason] = useState("");

  const handleChange = (event) => {
    event.preventDefault();
    addGrudge({ person, reason });
  };

  return (
    <form className="NewGrudge" onSubmit={handleChange}>
      <input
        className="NewGrudge-input"
        placeholder="Person"
        type="text"
        value={person}
        onChange={(event) => setPerson(event.target.value)}
      />
      <input
        className="NewGrudge-input"
        placeholder="Reason"
        type="text"
        value={reason}
        onChange={(event) => setReason(event.target.value)}
      />
      <input className="NewGrudge-submit button" type="submit" />
    </form>
  );
});

export default NewGrudge;
```

### useCallback

useCallback est un hook React qui vous permet de mettre en cache la définition d'une fonction entre les rendus.

```javascript
const [grudges, dispatch] = useReducer(reducer, initialState);
const addGrudge = useCallback(
  ({ person, reason }) => {
    dispatch({
      type: GRUDGES_ADD,
      payload: {
        person,
        reason,
      },
    });
  },
  [dispatch]
);
```

### useRef

useRef est un Hook React qui vous permet de référencer une valeur qui n'est pas nécessaire pour le rendu.

```javascript
import { useRef } from "react";
function MyComponent() {
  const reference = useRef(initialValue);
  const someHandler = () => {
    const value = reference.current;
    reference.current = newValue;
  };
}
```

```javascript
import { useRef } from "react";
function LogButtonClicks() {
  const countRef = useRef(0);

  const handle = () => {
    countRef.current++;
    console.log(`Clicked ${countRef.current} times`);
  };
  console.log("I rendered!");
  return <button onClick={handle}>Click me</button>;
}
```

### useLayoutEffect

```javascript
import React, { useLayoutEffect } from "react";
const APP = (props) => {
  useLayoutEffect(() => {
    return () => {
      // Cleanup
    };
  }, [dependencies]);
};
```

### Style de votre application React

#### Styles en ligne

```javascript
export default function App() {
  return (
    <section
      style={{
        fontFamily: "-apple-system",
        fontSize: "1rem",
        fontWeight: 1.5,
        lineHeight: 1.5,
        color: "#292b2c",
        backgroundColor: "#fff",
        padding: "0 2em",
      }}
    >
      <div
        style={{
          textAlign: "center",
          maxWidth: "950px",
          margin: "0 auto",
          border: "1px solid #e6e6e6",
          padding: "40px 25px",
          marginTop: "50px",
        }}
      >
        <img
          src="https://randomuser.me/api/portraits/women/48.jpg"
          alt="Tammy Stevens"
          style={{
            margin: "-90px auto 30px",
            width: "100px",
            borderRadius: "50%",
            objectFit: "cover",
            marginBottom: "0",
          }}
        />
        <div>
          <p
            style={{
              lineHeight: 1.5,
              fontWeight: 300,
              marginBottom: "25px",
              fontSize: "1.375rem",
            }}
          >
            This is one of the best developer blogs on the planet! I read it
            daily to improve my skills.
          </p>
        </div>
        <p
          style={{
            marginBottom: "0",
            fontWeight: 600,
            fontSize: "1rem",
          }}
        >
          Tammy Stevens
          <span style={{ fontWeight: 400 }}> · Front End Developer</span>
        </p>
      </div>
    </section>
  );
}
```

### CSS simple

```css
/* src/styles.css */

body {
  font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  margin: 0;
  font-size: 1rem;
  font-weight: 1.5;
  line-height: 1.5;
  color: #292b2c;
  background-color: #fff;
}
.testimonial {
  margin: 0 auto;
  padding: 0 2em;
}
```


