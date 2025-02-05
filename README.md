# React-cheatsheet

Aide-mémoire
JSX
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

Props
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

État (State)
Qu'est-ce que l'état (state) ?

React possède un autre objet intégré spécial appelé "state" (état), qui permet aux composants de créer et de gérer leurs propres données. Ainsi, contrairement aux "props", les composants ne peuvent pas transmettre de données avec "state", mais ils peuvent les créer et les gérer en interne.

Explication (ajoutée) : L'état, contrairement aux props, est une donnée interne à un composant qui peut être modifiée. Il représente l'état actuel du composant et contrôle son comportement et son affichage. Lorsqu'un état change, React redessine l'interface utilisateur du composant.

De l'état des composants aux Hooks

De l'état des composants aux Hooks
Nous allons commencer avec un compteur super simple.

Tel quel, il n'a pas grand-chose à offrir.

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

Mettons-le en place comme un exercice d'échauffement amusant.

Mise en place du composant de base
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

Bien, maintenant nous allons implémenter les méthodes pour incrémenter, décrémenter et réinitialiser le compteur.

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

```javascript
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

Quiz sur l'état des composants
Mises à jour asynchrones et mise en file d'attente
D'accord, disons que nous avons refactorisé increment() comme suit :

```javascript
increment() {
  this.setState({ count: this.state.count + 1 });
  this.setState({ count: this.state.count + 1 });
  this.setState({ count: this.state.count + 1 });

  console.log(this.state.count);
}
```

Deux questions :

Qu'est-ce qui sera enregistré dans la console ?

Quelle sera la nouvelle valeur ?

Note : La première question fait référence à un piège courant. this.setState est asynchrone, donc la valeur de this.state.count dans console.log ne sera pas encore mise à jour.

Utilisation d'une fonction comme argument
this.setState accepte également une fonction. Cela signifie que nous pourrions refactoriser increment() comme suit.

```javascript
this.setState((state) => {
  return { count: state.count + 1 };
});
```

Si nous voulions nous montrer, nous pourrions utiliser la déstructuration pour rendre cela encore plus propre.

```javascript
increment() {
  this.setState(({ count }) => {
    return { count: count + 1 };
  });
}
```

Il y a des choses potentiellement cool que nous pourrions faire ici. Par exemple, nous pourrions ajouter une certaine logique à notre composant.

(Le Live Coding commence ici)

this.setState((state, props) => {
  if (state.count >= 10) return;
  return { count: state.count + 1 };
});
Use code with caution.
Js
Disons que nous voulions ajouter un nombre maximum comme "prop".

render(<Counter max={10} />, document.getElementById("root"));
Use code with caution.
Js
this.setState((state) => {
  if (state.count >= this.props.max) return;
  return { count: state.count + 1 };
});
Use code with caution.
Js
Il s'avère que nous pouvons aussi avoir un deuxième argument : les props.

this.setState((state, props) => {
  if (state.count >= props.max) return;
  return { count: count + 1 };
});
Use code with caution.
Js
Oh attendez, et si nous faisions cela trois fois ?

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
Use code with caution.
Js
Oh, c'est intéressant.

L'autre chose que nous pouvons faire est de sortir cette fonction du composant. Cela rend les tests unitaires beaucoup plus faciles.

const increment = (state, props) => {
  if (state.count >= props.max) return;
  return { count: state.count + 1 };
};
Use code with caution.
Js
increment() {
  this.setState(increment);
}
Use code with caution.
Js
Callbacks
this.setState prend un deuxième argument en plus de l'objet ou de la fonction. Cette fonction est appelée après que le changement d'état a eu lieu.

Voici l'implémentation la plus simple possible.

this.setState(increment, () => console.log("Callback"));
Use code with caution.
Js
Nous pouvons aussi faire quelque chose comme :

this.setState(increment, () => console.log(this.state));
Use code with caution.
Js
Voici une chose simple que nous pourrions choisir de faire.

this.setState(increment, () => (document.title = `Count: ${this.state.count}`));
Use code with caution.
Js
Utilisation de LocalStorage dans un effet secondaire
Créons une petite fonction d'aide.

const getStateFromLocalStorage = () => {
  const storage = localStorage.getItem("counterState");
  if (storage) return JSON.parse(storage);
  return { count: 0 };
};
Use code with caution.
Js
Nous pouvons ensuite utiliser un callback pour définir localStorage lorsque l'état change.

this.setState(increment, () =>
  localStorage.setItem("counterState", JSON.stringify(this.state))
);
Use code with caution.
Js
Sortons cela avec "increment".

const storeStateInLocalStorage = () => {
  localStorage.setItem("counterState", JSON.stringify(this.state));
};
Use code with caution.
Js
increment() {
  this.setState(increment, storeStateInLocalStorage);
}
Use code with caution.
Js
Ça ne marche pas. C'est dommage. Il serait formidable que la fonction de callback reçoive une copie de l'état, mais ce n'est pas le cas. Nous pourrions l'envelopper dans une fonction, puis transmettre l'état.

Nous pourrions gérer cela de plusieurs façons.

Nous pourrions utiliser une fonction anonyme, puis la transmettre comme argument.

const storeStateInLocalStorage = (state) => {
  localStorage.setItem("counterState", JSON.stringify(state));
};
Use code with caution.
Js
increment() {
  this.setState(increment, () => storeStateInLocalStorage(this.state));
}
Use code with caution.
Js
Alternativement, si nous sommes prêts à renoncer aux fonctions fléchées, nous pouvons utiliser bind.

function storeStateInLocalStorage() {
  localStorage.setItem("counterState", JSON.stringify(this.state));
}
Use code with caution.
Js
increment() {
  this.setState(increment, storeStateInLocalStorage.bind(this));
}
Use code with caution.
Js
Enfin, nous pouvons simplement le mettre sur le composant de classe lui-même.

storeStateInLocalStorage() {
  localStorage.setItem('counterState', JSON.stringify(this.state));
}

increment() {
  this.setState(increment, this.storeStateInLocalStorage);
}
Use code with caution.
Js
C'est probablement votre meilleure option.

Refactorisation vers les Hooks
Les Hooks sont un nouveau modèle qui nous permet d'écrire beaucoup moins de code. Préparez-vous à supprimer du code.

Commençons par supprimer tout sauf la méthode "render".

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
Use code with caution.
Js
Alors, qu'est-ce que nous n'avons pas à faire ici ?

Nous n'avons rien à bind.

Nous n'avons pas besoin d'une référence à this.

Nous n'avons pas besoin d'un constructor du tout.

Exécution de certaines de nos expériences précédentes
Que se passe-t-il si nous triplons à nouveau ?

const increment = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
Use code with caution.
Js
D'accord, cela a du sens.

Il s'avère également que les setters de useState peuvent également prendre des fonctions.

const increment = () => {
  setCount((c) => c + 1);
};
Use code with caution.
Js
Contrairement à l'utilisation de valeurs, l'utilisation de fonctions fonctionne également de la même manière qu'avec this.setState.

const increment = () => {
  setCount((c) => c + 1);
  setCount((c) => c + 1);
  setCount((c) => c + 1);
};
Use code with caution.
Js
Il y a une différence importante. Vous n'obtenez que l'état dans ce cas. Il n'y a pas de deuxième argument avec les props. Cela dit, nous les avons dans la portée.

Ils ne prennent pas non plus en charge les fonctions de callback comme this.setState. Plus tard, nous utiliserons useEffect pour déclencher des effets secondaires basés sur les changements d'état.

Plus tôt avec this.setState, nous avons fini par renvoyer "undefined" si notre compteur avait atteint le maximum. Que se passerait-il si nous faisions quelque chose de similaire ici ?

setCount((c) => {
  if (c >= max) return;
  return c + 1;
});
Use code with caution.
Js
Oh, ça explose après dix. Ceci est au cœur de la différence entre le fonctionnement de useState et de this.setState.

Avec this.setState, nous donnons au composant cet objet de valeurs qu'il doit mettre à jour. Avec useState, nous avons une fonction dédiée pour modifier un élément d'état particulier.

Comment pouvons-nous corriger cela ?

setCount((c) => {
  if (c >= max) return c;
  return c + 1;
});
Use code with caution.
Js
Une brève introduction à useEffect
Nous allons approfondir useEffect, mais faisons d'abord un aperçu de haut niveau.

Use effect nous permet d'implémenter une sorte d'effet secondaire dans notre composant en dehors des changements d'état et de props déclenchant un nouveau rendu.

C'est utile pour une tonne de raisons :

Stocker des choses dans localStorage.

Faire des requêtes AJAX.

Implémentation de localStorage
Mettons en place la configuration de base ici.

Voici un rappel de cette fonction de récupération à partir de localStorage.

const getStateFromLocalStorage = () => {
  const storage = localStorage.getItem("counterState");
  if (storage) return JSON.parse(storage);
  return { count: 0 };
};
Use code with caution.
Js
Nous allons lire la propriété "count" depuis "localStorage".

const [count, setCount] = React.useState(getStateFromLocalStorage().count);
Use code with caution.
Js
Maintenant, nous allons enregistrer un effet secondaire.

React.useEffect(() => {
  localStorage.setItem("counterState", JSON.stringify({ count }));
}, [count]);
Use code with caution.
Js
Événements
Les gestionnaires d'événements déterminent quelle action doit être entreprise lorsqu'un événement est déclenché. Cela peut être un clic de bouton ou une modification dans un champ de texte.

Essentiellement, les gestionnaires d'événements permettent aux utilisateurs d'interagir avec votre application React. La gestion des événements avec les éléments React est similaire à la gestion des événements sur les éléments DOM, à quelques exceptions mineures près.

function ActionLink() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log("The link was clicked.");
  };

  return <button onClick={handleClick}>Click me</button>;
}
Use code with caution.
JavaScript
Que sont les événements synthétiques dans React ?
React implémente un système d'événements synthétiques qui apporte cohérence et haute performance aux applications et interfaces React. Il assure la cohérence en normalisant les événements afin qu'ils aient les mêmes propriétés sur différents navigateurs et plateformes.

Un événement synthétique est un wrapper multi-navigateur autour de l'événement natif du navigateur. Il a la même interface que l'événement natif du navigateur, y compris stopPropagation() et preventDefault(), sauf que les événements fonctionnent de manière identique sur tous les navigateurs.

Il offre des performances élevées en utilisant automatiquement la délégation d'événements. En réalité, React n'attache pas les gestionnaires d'événements aux nœuds eux-mêmes. Au lieu de cela, un seul écouteur d'événements est attaché à la racine du document. Lorsqu'un événement est déclenché, React le mappe à l'élément de composant approprié.

Appeler une fonction en ligne dans un gestionnaire d'événements onClick
Les fonctions en ligne vous permettent d'écrire du code pour la gestion des événements directement dans JSX. Voir l'exemple ci-dessous :

import React from "react";

const App = () => {
  return <button onClick={() => alert("Hello!")}>Say Hello</button>;
};

export default App;
Use code with caution.
JavaScript
Ceci est couramment utilisé pour éviter la déclaration de fonction supplémentaire en dehors de JSX, bien que cela puisse être moins lisible et plus difficile à maintenir si le contenu de la fonction en ligne est trop important.

Mettre à jour l'état à l'intérieur d'un gestionnaire d'événements onClick
Disons que votre application React vous oblige à mettre à jour l'état local dans un gestionnaire d'événements onClick. Voici comment faire :

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
Use code with caution.
JavaScript
Dans l'exemple ci-dessus, la valeur de useState est modifiée par les boutons "Incrémenter" et "Décrémenter", qui ont la fonction de mise à jour setCount à l'intérieur du gestionnaire d'événements onClick.

Appeler plusieurs fonctions dans un gestionnaire d'événements onClick
Le gestionnaire d'événements onClick vous permet également d'appeler plusieurs fonctions.

import React, { useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  const sayHello = () => {
    alert("Hello!");
  };

  return (
    <div>
      <p>{count}</p>
      <button
        onClick={() => {
          sayHello();
          setCount(count + 1);
        }}
      >
        Say Hello and Increment
      </button>
    </div>
  );
};

export default App;
Use code with caution.
JavaScript
Composants personnalisés et événements dans React
En ce qui concerne les événements dans React, seuls les éléments DOM sont autorisés à avoir des gestionnaires d'événements. Prenons l'exemple d'un composant appelé CustomButton avec un événement onClick. Ce bouton ne répondrait pas aux clics pour la raison ci-dessus.

import React from "react";

const CustomButton = ({ onPress }) => {
  return (
    <button type="button" onClick={onPress}>
      Click on me
    </button>
  );
};

const App = () => {
  const handleEvent = () => {
    alert("I was clicked");
  };
  return <CustomButton onPress={handleEvent} />;
};
export default App;
Use code with caution.
JavaScript
Explication (ajoutée): Dans React, les événements standards comme onClick sont conçus pour fonctionner directement sur les éléments HTML natifs (les éléments du DOM). Pour les composants personnalisés, vous devez créer vos propres "props" pour gérer les événements (ici, onPress). C'est une façon de simuler un événement sur un composant personnalisé.

Cycle de vie
Montage (Mounting)
Ces méthodes sont appelées dans l'ordre suivant lorsqu'une instance d'un composant est créée et insérée dans le DOM :

constructor()
static getDerivedStateFromProps()
render()
componentDidMount()

Mise à jour (Updating)
Une mise à jour peut être provoquée par des modifications des props ou de l'état. Ces méthodes sont appelées dans l'ordre suivant lorsqu'un composant est redessiné :

static getDerivedStateFromProps()
shouldComponentUpdate()
render()
getSnapshotBeforeUpdate()
componentDidUpdate()

Démontage (Unmounting)
Cette méthode est appelée lorsqu'un composant est supprimé du DOM :

componentWillUnmount()

Hooks;
useState
useEffect
Cela signifie que useEffect s'exécute à chaque rendu et que, par conséquent, les gestionnaires d'événements seront inutilement détachés et rattachés à chaque rendu.

window.React.useEffect(() => {
  console.log(`adding listener ${count}`);
  window.addEventListener("click", listener);
});
Use code with caution.
Js
Explication (ajoutée) : Cet exemple illustre un problème potentiel si vous ne gérez pas correctement les dépendances de useEffect. L'effet sera exécuté à chaque rendu, ce qui peut entraîner des problèmes de performance.

ComponentWillUnmount
Il s'agit du mécanisme de nettoyage facultatif pour les effets. Chaque effet peut renvoyer une fonction qui nettoie après lui. Cela nous permet de garder la logique pour ajouter et supprimer des abonnements à proximité les uns des autres. Ils font partie du même effet !
l'effet secondaire s'exécute après chaque rendu.

window.React.useEffect(() => {
  return () => {
    console.log(`removing listener ${count}`);
    window.removeEventListener("click", listener);
  };
});
Use code with caution.
Js
import { useEffect } from "react";
function RepeatMessage({ message }) {
  useEffect(() => {
    const id = setInterval(() => {
      console.log(message);
    }, 2000);
    return () => {
      clearInterval(id);
    };
  }, [message]);
  return <div>I'm logging to console "{message}"</div>;
}
Use code with caution.
Js
useEffect componentDidMount
l'effet secondaire s'exécute une fois après le rendu initial.

import { useEffect } from "react";
function Greet() {
  let name = "Hassan Habib Tahir";
  const message = `Hello, ${name}!`; // Calculates output
  useEffect(() => {
    // Good!
    document.title = `Greetings to ${name}`; // Side-effect!
  }, []);
  return <div>{message}</div>; // Calculates output
}
Use code with caution.
Js
callback est la fonction contenant la logique de l'effet secondaire. callback est exécuté juste après que les modifications ont été poussées vers le DOM.
dependencies est un tableau facultatif de dépendances. useEffect() exécute callback uniquement si les dépendances ont changé entre les rendus.

useEffect(callback[, dependencies]);
Use code with caution.
Js
Composant a fait la mise à jour
import { useEffect } from "react";
function MyComponent({ prop }) {
  const [state, setState] = useState();
  useEffect(() => {
    // Side-effect uses `prop` and `state`
  }, [prop, state]);
  return <div>....</div>;
}
Use code with caution.
Js
useMemo
useMemo est un Hook React qui vous permet de mettre en cache le résultat d'un calcul entre les rendus.

Le but principal du hook useMemo est lié au fait que nous essayons d'éviter le rendu inutile des composants et des props dans notre programme.

Exemple 1

Créons une application simple pour démontrer l'utilisation du hook useMemo.

Le programme ci-dessous possède les composants suivants :

Bouton Incrément : commence à partir de 0 et augmente le compteur de 1.

Bouton nombre pair : commence à partir de 2 et renvoie les nombres pairs qui suivent.

Une fonction evenNumDoouble() qui renvoie la valeur double du nombre pair.

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
Use code with caution.
Js
Explication

Lorsque nous cliquons sur le bouton Nombres pairs, la fonction evenNumDouble() est appelée car l'état de evenNum est modifié.

Cliquer sur le bouton Incrément affiche également la fonction evenNumDouble(), bien que l'état du compteur ne change pas.

Cela signifie que chaque fois que la fonction evenNumDouble() est rendue inutilement (sur la page), le code devient moins efficace. Nous allons corriger cela avec le hook useMemo ci-dessous.

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
Use code with caution.
Js
Explication

Dans le code ci-dessus, nous avons défini la sortie de la fonction evenNumDouble() dans une constante memoHook.

Cela filtre la fonction via le hook useMemo pour vérifier uniquement si la variable spécifiée (evenNum dans ce cas) a été modifiée ; ce n'est qu'alors que cette fonction sera rendue.

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
Use code with caution.
Js
useCallback
useCallback est un hook React qui vous permet de mettre en cache la définition d'une fonction entre les rendus.

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
Use code with caution.
Js
Explication (ajoutée): useCallback est souvent utilisé en combinaison avec useMemo et React.memo pour optimiser les performances. Il permet d'éviter de recréer des fonctions à chaque rendu, ce qui peut être coûteux si ces fonctions sont passées en props à des composants enfants.

useRef ;
useRef est un Hook React qui vous permet de référencer une valeur qui n'est pas nécessaire pour le rendu.

et créer une variable mutable qui ne redessinera pas les composants

useRef(initialValue) est un hook React intégré qui accepte un argument comme valeur initiale et renvoie une référence (alias ref). Une référence est un objet ayant une propriété spéciale "current".

import { useRef } from "react";
function MyComponent() {
  const reference = useRef(initialValue);
  const someHandler = () => {
    // Access reference value:
    const value = reference.current;
    // Update reference value:
    reference.current = newValue;
  };
}
Use code with caution.
Js
La mise à jour d'une référence ne déclenche pas un nouveau rendu de composant.

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
Use code with caution.
Js
Accéder aux éléments DOM
import { useRef, useEffect } from "react";
function AccessingElement() {
  const elementRef = useRef();
  useEffect(() => {
    const divElement = elementRef.current;
    console.log(divElement); // logs <div>I'm an element</div>
  }, []);
  return <div ref={elementRef}>I'm an element</div>;
}
Use code with caution.
Js
useLayoutEffect
Le hook useEffect sert de manière asynchrone, tandis que le hook useLayoutEffect fonctionne de manière synchrone ;

Ceci s'exécute de manière synchrone immédiatement après que React a effectué toutes les mutations DOM. Cela peut être utile si vous devez effectuer des mesures DOM (comme obtenir la position de défilement ou d'autres styles pour un élément), puis effectuer des mutations DOM ou déclencher un nouveau rendu synchrone en mettant à jour l'état.

En ce qui concerne la planification, cela fonctionne de la même manière que componentDidMount et componentDidUpdate. Votre code s'exécute immédiatement après la mise à jour du DOM, mais avant que le navigateur n'ait eu la chance de « peindre » ces modifications (l'utilisateur ne voit pas réellement les mises à jour tant que le navigateur n'a pas repeint).

![alt text](https://miro.medium.com/max/828/1*GeEvUL0Zl3FVlSzNhFKiSg.gif)

import React, { useLayoutEffect } from "react";
const APP = (props) => {
  useLayoutEffect(() => {
    //Do something and either return undefined or a cleanup function
    return () => {
      //Do some cleanup here
    };
  }, [dependencies]);
};
Use code with caution.
Js
Style de votre application React
Styles en ligne
Les styles en ligne sont le moyen le plus direct de styliser une application React.

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
Use code with caution.
JavaScript
CSS simple
Au lieu d'utiliser des styles en ligne, il est courant d'importer une feuille de style CSS pour styliser les éléments d'un composant.
Écrire du CSS dans une feuille de style est probablement l'approche la plus courante et la plus élémentaire pour styliser une application React, mais elle ne doit pas être écartée aussi facilement.

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
