## Source : https://github.com/HassanHabibTahir/React-cheatsheet.git

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

## Utilisation de LocalStorage dans un effet secondaire

Créons une petite fonction d'aide.

```js
const getStateFromLocalStorage = () => {
  const storage = localStorage.getItem("counterState");
  if (storage) return JSON.parse(storage);
  return { count: 0 };
};
```

Explication : Cette fonction, `getStateFromLocalStorage`, a pour but de récupérer l'état du compteur (s'il existe) depuis le `localStorage` du navigateur. Si une valeur est trouvée sous la clé "counterState", elle est analysée (`parse`) en tant qu'objet JSON et renvoyée. Si aucune valeur n'est trouvée, elle renvoie un objet par défaut `{ count: 0 }`. Ceci est utile pour initialiser l'état du composant avec la dernière valeur persistée.

Nous pouvons ensuite utiliser un callback pour définir `localStorage` lorsque l'état change.

```js
this.setState(increment, () =>
  localStorage.setItem("counterState", JSON.stringify(this.state))
);
```

Sortons cela avec "increment".

```js
const storeStateInLocalStorage = () => {
  localStorage.setItem("counterState", JSON.stringify(this.state));
};

increment() {
  this.setState(increment, storeStateInLocalStorage);
}
```

Ça ne marche pas. C'est dommage. Il serait formidable que la fonction de callback reçoive une copie de l'état, mais ce n'est pas le cas. Nous pourrions l'envelopper dans une fonction, puis transmettre l'état.

Explication : Cette partie souligne un défi lors de l'utilisation des callbacks avec `setState`. La fonction `storeStateInLocalStorage` ne fonctionne pas directement car elle utilise `this.state`, qui peut ne pas refléter la nouvelle valeur de l'état au moment où le callback est exécuté. C'est parce que `setState` est asynchrone, et l'état n'est pas garanti d'être mis à jour immédiatement.

Nous pourrions gérer cela de plusieurs façons.

Nous pourrions utiliser une fonction anonyme, puis la transmettre comme argument.

```js
const storeStateInLocalStorage = (state) => {
  localStorage.setItem("counterState", JSON.stringify(state));
};

increment() {
  this.setState(increment, () => storeStateInLocalStorage(this.state));
}
```

Alternativement, si nous sommes prêts à renoncer aux fonctions fléchées, nous pouvons utiliser `bind`.

```js
function storeStateInLocalStorage() {
  localStorage.setItem("counterState", JSON.stringify(this.state));
}

increment() {
  this.setState(increment, storeStateInLocalStorage.bind(this));
}
```

Enfin, nous pouvons simplement le mettre sur le composant de classe lui-même.

```js
storeStateInLocalStorage() {
  localStorage.setItem('counterState', JSON.stringify(this.state));
}

increment() {
  this.setState(increment, this.storeStateInLocalStorage);
}
```

C'est probablement votre meilleure option.

Explication : Cette section présente différentes solutions pour accéder à la nouvelle valeur de l'état à l'intérieur du callback de `setState`.

- **Fonction anonyme** : Utiliser une fonction anonyme `() => storeStateInLocalStorage(this.state)` permet de capturer la valeur de `this.state` au moment où le callback est exécuté.
- **bind** : Utiliser `storeStateInLocalStorage.bind(this)` permet de forcer `this` à faire référence au composant, ce qui permet d'accéder à la nouvelle valeur de l'état.
- **Méthode de classe** : Définir `storeStateInLocalStorage` comme une méthode de classe (avec la syntaxe `storeStateInLocalStorage() { ... }`) est généralement la meilleure option car elle garantit que `this` se réfère toujours au composant et permet d'accéder à la nouvelle valeur de l'état.

### En résumé :

- Il est important de comprendre que `setState` est asynchrone et que l'état n'est pas garanti d'être mis à jour immédiatement.
- Lors de l'utilisation des callbacks avec `setState`, il est nécessaire de s'assurer que vous avez accès à la nouvelle valeur de l'état.
- Définir les fonctions qui dépendent de l'état comme des méthodes de classe est généralement la meilleure approche.


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

React implémente un système d'événements synthétiques qui apporte cohérence et haute performance aux applications et interfaces React. Il assure la cohérence en normalisant les événements afin qu'ils aient les mêmes propriétés sur différents navigateurs et plateformes.

Explication : React utilise un système d'événements synthétiques pour masquer les différences entre les navigateurs en matière de gestion des événements. Cela signifie que vous pouvez utiliser les mêmes gestionnaires d'événements (onClick, onChange, etc.) dans votre code React, et React s'occupe de les traduire en code spécifique à chaque navigateur. Cela simplifie le développement multi-navigateur et améliore la cohérence.

Un événement synthétique est un wrapper multi-navigateur autour de l'événement natif du navigateur. Il a la même interface que l'événement natif du navigateur, y compris stopPropagation() et preventDefault(), sauf que les événements fonctionnent de manière identique sur tous les navigateurs.

Explication : Un événement synthétique n'est pas un événement natif du navigateur, mais une abstraction créée par React. Il possède les mêmes méthodes que les événements natifs (comme stopPropagation() pour empêcher la propagation de l'événement et preventDefault() pour empêcher l'action par défaut du navigateur), mais son comportement est garanti d'être cohérent sur tous les navigateurs.

Il offre des performances élevées en utilisant automatiquement la délégation d'événements. En réalité, React n'attache pas les gestionnaires d'événements aux nœuds eux-mêmes. Au lieu de cela, un seul écouteur d'événements est attaché à la racine du document. Lorsqu'un événement est déclenché, React le mappe à l'élément de composant approprié.

Explication : Pour optimiser les performances, React utilise la délégation d'événements. Au lieu d'attacher un gestionnaire d'événements à chaque élément individuel, React attache un seul gestionnaire d'événements à la racine du document. Lorsque un événement se produit, React détermine quel composant doit être notifié et appelle le gestionnaire d'événements approprié. Cela réduit considérablement le nombre de gestionnaires d'événements attachés au DOM, ce qui améliore les performances.

Appeler une fonction en ligne dans un gestionnaire d'événements onClick

Les fonctions en ligne vous permettent d'écrire du code pour la gestion des événements directement dans JSX. Voir l'exemple ci-dessous :

```javascript
import React from "react";

const App = () => {
  return <button onClick={() => alert("Hello!")}>Say Hello</button>;
};

export default App;
```

Ceci est couramment utilisé pour éviter la déclaration de fonction supplémentaire en dehors de JSX, bien que cela puisse être moins lisible et plus difficile à maintenir si le contenu de la fonction en ligne est trop important.

Explication : Cette section explique comment utiliser une fonction en ligne (une fonction définie directement à l'intérieur de l'attribut onClick) pour gérer les événements. L'avantage est que cela permet d'écrire du code concis et d'éviter de déclarer une fonction séparée. L'inconvénient est que cela peut rendre le code moins lisible et plus difficile à maintenir si la fonction en ligne devient trop complexe. Il est souvent préférable d'extraire la fonction dans une fonction séparée si elle est utilisée plusieurs fois ou si elle contient une logique complexe.

En résumé :

- React utilise des événements synthétiques pour normaliser la gestion des événements sur différents navigateurs et plateformes.
- React utilise la délégation d'événements pour optimiser les performances.
- Les fonctions en ligne peuvent être utilisées pour gérer les événements, mais elles doivent être utilisées avec parcimonie pour éviter de rendre le code moins lisible et plus difficile à maintenir.



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
Dans l'exemple ci-dessus, la valeur de useState est modifiée par les boutons "Incrémenter" et "Décrémenter", qui ont la fonction de mise à jour setCount à l'intérieur du gestionnaire d'événements onClick.

Explication : Cette phrase résume simplement comment l'état est modifié en réponse aux interactions de l'utilisateur. Les boutons "Incrémenter" et "Décrémenter" utilisent la fonction setCount pour mettre à jour l'état count, ce qui provoque un nouveau rendu du composant.

## Appeler plusieurs fonctions dans un gestionnaire d'événements onClick

Le gestionnaire d'événements onClick vous permet également d'appeler plusieurs fonctions.

```javascript
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
```

Explication : Cette section montre comment exécuter plusieurs fonctions en réponse à un seul événement onClick. Vous pouvez utiliser une fonction fléchée pour envelopper plusieurs appels de fonction, en les séparant par des points-virgules. Dans cet exemple, cliquer sur le bouton appelle d'abord la fonction sayHello (qui affiche une alerte), puis met à jour l'état count en incrémentant sa valeur.

## Composants personnalisés et événements dans React

En ce qui concerne les événements dans React, seuls les éléments DOM sont autorisés à avoir des gestionnaires d'événements. Prenons l'exemple d'un composant appelé CustomButton avec un événement onClick. Ce bouton ne répondrait pas aux clics pour la raison ci-dessus.

```javascript
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
```

Explication : Cette section met en évidence une subtilité importante dans React. Les événements natifs du navigateur (comme onClick, onChange, etc.) ne peuvent être attachés directement qu'aux éléments DOM (c'est-à-dire les balises HTML standard). Si vous voulez qu'un composant personnalisé réagisse à un événement, vous devez passer un gestionnaire d'événements en tant que prop et l'attacher à un élément DOM à l'intérieur de ce composant. Dans cet exemple, le composant CustomButton reçoit une prop onPress, qui est ensuite utilisée comme gestionnaire d'événements onClick sur le bouton.

## Cycle de vie

### Montage (Mounting)

Ces méthodes sont appelées dans l'ordre suivant lorsqu'une instance d'un composant est créée et insérée dans le DOM :

- `constructor()`
- `static getDerivedStateFromProps()`
- `render()`
- `componentDidMount()`

### Mise à jour (Updating)

Une mise à jour peut être provoquée par des modifications des props ou de l'état. Ces méthodes sont appelées dans l'ordre suivant lorsqu'un composant est redessiné :

- `static getDerivedStateFromProps()`
- `shouldComponentUpdate()`
- `render()`
- `getSnapshotBeforeUpdate()`
- `componentDidUpdate()`

### Démontage (Unmounting)

Cette méthode est appelée lorsqu'un composant est supprimé du DOM :
```javascipt
componentWillUnmount()
```
Explication : Cette méthode du cycle de vie est appelée juste avant qu'un composant ne soit démonté et supprimé du DOM. C'est l'endroit idéal pour effectuer des opérations de nettoyage, comme annuler des abonnements, supprimer des écouteurs d'événements ou invalider des timers, afin d'éviter des fuites de mémoire et des comportements inattendus.

Hooks ;

useState

useEffect

useEffect s'exécute à chaque rendu, et par conséquent, les gestionnaires d'événements seront inutilement détachés et rattachés à chaque rendu.

```javascript
window.React.useEffect(() => {
  console.log(`adding listener ${count}`);
  window.addEventListener("click", listener);
});
```
Explication : Cet exemple montre une utilisation potentiellement inefficace de useEffect. Si le tableau de dépendances est omis ou si les dépendances sont incorrectes, l'effet (ici, l'ajout d'un écouteur d'événements) sera exécuté à chaque rendu, ce qui peut entraîner des problèmes de performance. Il est important de spécifier correctement les dépendances pour que l'effet ne s'exécute que lorsque cela est nécessaire.

componentWillUnmount

Il s'agit du mécanisme de nettoyage facultatif pour les effets. Chaque effet peut renvoyer une fonction qui nettoie après lui. Cela nous permet de garder la logique pour ajouter et supprimer des abonnements à proximité les uns des autres. Ils font partie du même effet ! l'effet secondaire s'exécute après chaque rendu.

```javascript
window.React.useEffect(() => {
  return () => {
    console.log(`removing listener ${count}`);
    window.removeEventListener("click", listener);
  };
});
```
Explication : Cet exemple illustre le modèle de nettoyage approprié avec useEffect. La fonction renvoyée par useEffect est exécutée lors du démontage du composant ou avant la réexécution de l'effet. Elle permet de supprimer l'écouteur d'événements ajouté précédemment, évitant ainsi des fuites de mémoire.

```javascript
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
```
Explication : Cet exemple montre comment utiliser la fonction de nettoyage pour effacer un intervalle (setInterval). L'intervalle est configuré lorsque le composant est monté et effacé lorsque le composant est démonté ou lorsque la prop message change. Cela évite que l'intervalle ne continue à s'exécuter en arrière-plan après que le composant a été supprimé.

useEffect componentDidMount

L'effet secondaire s'exécute une fois après le rendu initial.

```javascript
import { useEffect } from "react";
function Greet() {
  let name = "Hassan Habib Tahir";
  const message = `Hello, ${name}!`; // Calculates output
  useEffect(() => {
    document.title = `Greetings to ${name}`; // Side-effect!
  }, []);
  return <div>{message}</div>; // Calculates output
}
```
Explication : Cet exemple montre comment exécuter un effet secondaire une seule fois après le rendu initial en passant un tableau de dépendances vide ([]) à useEffect.

useMemo

useMemo est un Hook React qui vous permet de mettre en cache le résultat d'un calcul entre les rendus.

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
Explication : Cet exemple illustre l'utilisation de useMemo pour optimiser les performances. En utilisant useMemo, le résultat de la fonction evenNumDouble est mis en cache, et la fonction n'est réexécutée que si la valeur de evenNum change.

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
      <input placeholder="Person" type="text" value={person} onChange={(event) => setPerson(event.target.value)} />
      <input placeholder="Reason" type="text" value={reason} onChange={(event) => setReason(event.target.value)} />
      <input type="submit" />
    </form>
  );
});
export default NewGrudge;
```
Explication : Cet exemple utilise React.memo pour mémoïser un composant fonctionnel.

## useCallback 
useCallback est un Hook React qui vous permet de mettre en cache une définition de fonction entre les rendus.  Cela est particulièrement utile pour optimiser les performances en évitant de recréer la fonction à chaque rendu, surtout si cette fonction est passée comme prop à un composant enfant qui utilise `React.memo`.

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

Dans cet exemple, addGrudge ne sera recréée que si dispatch change. C'est crucial car si addGrudge était recréée à chaque rendu du composant parent, même si les props ne changeaient pas, le composant enfant, si optimisé avec React.memo, serait quand même rendu inutilement.

## useRef

`useRef` est un Hook React qui vous permet de référencer une valeur qui n'est pas nécessaire pour le rendu. Il est principalement utilisé pour créer une variable mutable qui ne provoque pas de re-rendu des composants lorsqu'elle est modifiée. Cela le rend idéal pour stocker des valeurs qui persistent entre les rendus sans affecter l'interface utilisateur.

```javascript
import { useRef } from "react";

function MyComponent() {
  const reference = useRef(initialValue);
  const someHandler = () => {
    // Accéder à la valeur de la référence:
    const value = reference.current;
    // Mettre à jour la valeur de la référence:
    reference.current = newValue;
  };
}
```

Mettre à jour une référence ne déclenche pas de re-rendu du composant.

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

### Accéder aux éléments DOM

`useRef` est également couramment utilisé pour accéder directement aux éléments DOM.

```javascript
import { useRef, useEffect } from "react";

function AccessingElement() {
  const elementRef = useRef();

  useEffect(() => {
    const divElement = elementRef.current;
    console.log(divElement); // affiche <div>Je suis un élément</div>
  }, []);

  return <div ref={elementRef}>Je suis un élément</div>;
}
```

Ici, `elementRef` est passé comme attribut `ref` à un élément div. Dans le `useEffect`, on peut accéder à l'élément DOM réel via `elementRef.current`. Notez que `useEffect` est utilisé pour s'assurer que l'élément est disponible dans le DOM avant d'y accéder.

## useLayoutEffect

Le hook `useEffect` s'exécute de manière asynchrone, tandis que le hook `useLayoutEffect` fonctionne de manière synchrone. `useLayoutEffect` est utile lorsque vous devez effectuer des mesures du DOM (comme obtenir la position de défilement ou d'autres styles pour un élément) et ensuite apporter des modifications au DOM ou déclencher un re-rendu synchrone en mettant à jour l'état.

```javascript
import React, { useLayoutEffect } from "react";

const APP = (props) => {
  useLayoutEffect(() => {
    // Faire quelque chose et soit retourner undefined, soit une fonction de nettoyage
    return () => {
      // Faire un nettoyage ici
    };
  }, [dependencies]);
};
```

Il est important de noter que `useLayoutEffect` doit être utilisé avec prudence car il peut bloquer le navigateur et affecter les performances s'il contient des opérations coûteuses. Dans la plupart des cas, `useEffect` est suffisant.

## Styliser votre application React

### CSS simple

Au lieu d'utiliser des styles en ligne, il est courant d'importer une feuille de style CSS pour styliser les éléments d'un composant.

```css
/* src/styles.css */

body {
  font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", Roboto,
    "Helvetica Neue", Arial, sans-serif;
  margin: 0;
  font-size: 1rem;
  font-weight: 1.5;
  line-height: 1.5;
  color: #292b2c;
  background-color: #fff;
}
```

### CSS-in-JS

Les librairies CSS-in-JS permettent d'écrire du CSS directement dans les composants JavaScript.

```javascript
import styled from "styled-components";

const Button = styled.button`
  color: limegreen;
  border: 2px solid limegreen;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border-radius: 3px;

  &:hover {
    opacity: 0.9;
  }
`;

export default function App() {
  return (
    <div>
      <Button>Cliquez-moi</Button>
    </div>
  );
}
```

## Utiliser un Reducer

On pourrait essayer d'être intelligent ici avec `useCallback` et `React.memo`, mais comme nous remplaçons toujours le tableau des rancunes, cela ne va jamais vraiment fonctionner.

```javascript
const reducer = (state = [], action) => {
  return state;
};
```

Et puis nous remplaçons ce `useState` par un `useReducer`.

```javascript
const [grudges, dispatch] = useReducer(reducer, initialState);
```

# L'API Context de React: Gestion d'état global simplifiée

## Qu'est-ce que l'API Context?

L'API Context de React est une méthode permettant à une application React de produire efficacement des variables globales qui peuvent être transmises. C'est l'alternative au "prop drilling" ou au déplacement de props du grand-parent à l'enfant en passant par le parent, et ainsi de suite. L'exemple ci-dessus n'était pas si mauvais. Mais, vous pouvez voir comment cela pourrait devenir un peu hors de contrôle à mesure que notre application grandit.

Que faire si deux composants cousins très éloignés avaient besoin des mêmes données?

Les builds modernes de React vous permettent d'utiliser quelque chose appelé l'API Context pour améliorer cela. C'est essentiellement un moyen pour des parties très différentes de votre application de communiquer entre elles.

Nous allons extraire beaucoup de code de `Application.js` et le déplacer vers un nouveau fichier appelé `GrudgeContext.js` et cela ressemblera à quelque chose comme ceci.

```javascript
import React, { useReducer, createContext, useCallback } from "react";
import initialState from "./initialState";
import id from "uuid/v4";

export const GrudgeContext = createContext();

const GRUDGE_ADD = "GRUDGE_ADD";
const GRUDGE_FORGIVE = "GRUDGE_FORGIVE";

const reducer = (state = [], action) => {
  if (action.type === GRUDGE_ADD) {
    return [
      {
        id: id(),
        ...action.payload,
      },
      ...state,
    ];
  }

  if (action.type === GRUDGE_FORGIVE) {
    return state.map((grudge) => {
      if (grudge.id === action.payload.id) {
        return { ...grudge, forgiven: !grudge.forgiven };
      }
      return grudge;
    });
  }

  return state;
};

export const GrudgeProvider = ({ children }) => {
  const [grudges, dispatch] = useReducer(reducer, initialState);

  const addGrudge = useCallback(
    ({ person, reason }) => {
      dispatch({
        type: GRUDGE_ADD,
        payload: {
          person,
          reason,
        },
      });
    },
    [dispatch]
  );

  const toggleForgiveness = useCallback(
    (id) => {
      dispatch({
        type: GRUDGE_FORGIVE,
        payload: {
          id,
        },
      });
    },
    [dispatch]
  );

  return (
    <GrudgeContext.Provider value={{ grudges, addGrudge, toggleForgiveness }}>
      {children}
    </GrudgeContext.Provider>
  );
};
```

Ici, nous créons un Context appelé GrudgeContext et un Provider appelé GrudgeProvider. Le Provider gère l'état des rancunes et les actions pour ajouter et pardonner des rancunes. Il expose ensuite ces valeurs à tous les composants enfants enveloppés par le Provider.

Maintenant, `Application.js` a l'air beaucoup plus allégé.

```javascript
import React from "react";
import Grudges from "./Grudges";
import NewGrudge from "./NewGrudge";

const Application = () => {
  return (
    <div className="Application">
      <NewGrudge />
      <Grudges />
    </div>
  );
};

export default Application;
```

### Envelopper l'application dans votre nouveau Provider

```javascript
ReactDOM.render(
  <GrudgeProvider>
    <Application />
  </GrudgeProvider>,
  rootElement
);
```

Cela fonctionne et c'est cool, mais cela manque toujours l'essentiel. L'intérêt principal du Context est de simplifier le partage d'état entre composants, ce qui rend le code plus propre et plus maintenable.

### Connecter l'API Context

```javascript
import React from "react";
import Grudge from "./Grudge";
import { GrudgeContext } from "./GrudgeContext";

const Grudges = () => {
  const { grudges } = React.useContext(GrudgeContext);

  return (
    <section className="Grudges">
      <h2>Grudges ({grudges.length})</h2>
      {grudges.map((grudge) => (
        <Grudge key={grudge.id} grudge={grudge} />
      ))}
    </section>
  );
};

export default Grudges;
```

### Rancunes individuelles

```javascript
import React from "react";
import { GrudgeContext } from "./GrudgeContext";

const Grudge = ({ grudge }) => {
  const { toggleForgiveness } = React.useContext(GrudgeContext);

  return (
    <article className="Grudge">
      <h3>{grudge.person}</h3>
      <p>{grudge.reason}</p>
      <div className="Grudge-controls">
        <label className="Grudge-forgiven">
          <input
            type="checkbox"
            checked={grudge.forgiven}
            onChange={() => toggleForgiveness(grudge.id)}
          />{" "}
          Pardonné
        </label>
      </div>
    </article>
  );
};

export default Grudge;
```

L'API Context, combinée avec les reducers, offre une solution puissante pour gérer l'état global dans les applications React. Elle permet d'éviter le "prop drilling" et de rendre le code plus propre, plus maintenable et plus facile à comprendre.

# Pourquoi Redux?

Le transfert d'état entre les composants est assez désordonné dans React, car il est difficile de suivre de quel composant proviennent les données. Cela devient vraiment compliqué si les utilisateurs travaillent avec un grand nombre d'états dans une application. Redux Redux résout le problème du transfert d'état en stockant tous les états dans un seul endroit appelé un store. Ainsi, la gestion et le transfert des états deviennent plus faciles car tous les états sont stockés dans le même store pratique. Chaque composant de l'application peut alors accéder directement à l'état requis à partir de ce store.  Redux centralise la gestion de l'état, simplifiant ainsi la communication entre les composants et la logique de mise à jour de l'état.

## Qu'est-ce que Redux?

Redux est un conteneur d'état prévisible pour les applications JavaScript. Il vous aide à écrire des applications qui se comportent de manière cohérente, s'exécutent dans différents environnements (client, serveur et natif) et sont faciles à tester. Redux gère l'état d'une application avec un seul objet global appelé Store.

## Piliers de Redux

Voici les principaux piliers de Redux:

### Store:

Un store est un objet qui contient l'arborescence d'état de l'application. Il ne devrait y avoir qu'un seul store dans une application Redux, car la composition se produit au niveau du reducer. `getState()` renvoie l'état actuel du store. Le store est le centre névralgique de Redux, contenant l'état global de l'application.

### dispatch()

`dispatch()` distribue une action. C'est la seule façon de mettre à jour l'état de l'application.

```javascript
() => dispatch({ msg: "ADD_SOMETHING" });
```

`dispatch()` est la fonction utilisée pour envoyer des actions au store.

### subscribe()

`subscribe()` abonne un écouteur de changement à l'état. `unsubscribe()` est utile lorsque vous ne voulez plus appeler votre méthode d'écouteur lorsque l'état change. `subscribe()` permet de réagir aux changements d'état dans le store.

### Actions:

Une action est un simple objet qui représente une intention de modifier l'état. Elles doivent avoir une propriété pour indiquer le type d'action à effectuer.

Les actions sont des payloads d'informations qui envoient des données de votre application à votre store.

Toutes les données, qu'elles proviennent d'événements d'interface utilisateur ou de callbacks réseau, doivent éventuellement être dispatchées en tant qu'actions.

Les actions doivent avoir un champ `type`, indiquant le type d'action à effectuer.

Les actions sont des objets qui décrivent une intention de modifier l'état. Elles contiennent un champ `type` qui identifie l'action à effectuer et, éventuellement, des données supplémentaires (payload).

### Reducers:

Les reducers sont des fonctions pures qui spécifient comment l'état de l'application change en réponse aux actions envoyées au store.

Les actions décrivent seulement ce qui s'est passé, pas comment l'état de l'application change.

Un reducer est une fonction qui accepte l'état courant et l'action, et renvoie un nouvel état avec l'action effectuée.

L'utilitaire `combineReducers()` peut être utilisé pour combiner tous les reducers de l'application en un seul reducer d'index, ce qui facilite grandement la maintenabilité.

Les reducers sont des fonctions qui prennent l'état courant et une action et renvoient un nouvel état. Ils sont responsables de la logique de mise à jour de l'état.

## Application Web avec Redux

### Créer le Store

Créez un store dans le fichier `index.js`

```javascript
import { createStore } from "redux";
import { composeWithDevTools } from "redux-devtools-extension";
import rootReducer from "./rootReducer";

const store = createStore(rootReducer, composeWithDevTools());

export default store;
```

### Main

```javascript
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import { Provider } from "react-redux";
import store from "./redux/store";

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

Le composant `Provider` rend le store Redux disponible pour tous les composants connectés dans l'application.

### Reducer

```javascript
import { combineReducers } from "redux";
import {
  GET_ALL_PRODUCT,
  GET_NUMBER_CART,
  ADD_CART,
  DECREASE_QUANTITY,
  INCREASE_QUANTITY,
  DELETE_CART,
} from "./types";

const initProduct = {
  numberCart: 0,
  Carts: [],
  _products: [],
};

function todoProduct(state = initProduct, action) {
  switch (action.type) {
    case GET_ALL_PRODUCT:
      return {
        ...state,
        _products: action.payload,
      };
    case ADD_CART:
      if (state.numberCart === 0) {
        let cart = {
          id: action.payload.id,
          quantity: 1,
          name: action.payload.name,
          image: action.payload.image,
          price: action.payload.price,
        };
        state.Carts.push(cart);
      } else {
        let check = false;
        state.Carts.forEach((item, key) => {
          if (item.id === action.payload.id) {
            state.Carts[key].quantity++;
            check = true;
          }
        });
        if (!check) {
          let _cart = {
            id: action.payload.id,
            quantity: 1,
            name: action.payload.name,
            image: action.payload.image,
            price: action.payload.price,
          };
          state.Carts.push(_cart);
        }
      }
      return {
        ...state,
        numberCart: state.numberCart + 1,
      };
    default:
      return state;
  }
}

const ShopApp = combineReducers({
  _todoProduct: todoProduct,
});

export default ShopApp;
```

## Différence entre Context API et Redux

Dans Redux, vous pouvez définir les données dans un composant et d'autres composants ont le droit d'y accéder. D'autre part, Context les traite tels qu'ils se produisent au niveau du composant. Redux est plus adapté aux applications avec un état complexe et global, tandis que Context API est plus simple et peut être utilisé pour des cas d'utilisation plus limités.

## Différence entre Context API et Props

Les props fonctionnent avec une seule direction principale, les données sont partagées dans un seul composant, l'API context permet de partager des données avec plusieurs composants. Les props sont utilisées pour transmettre des données directement d'un parent à un enfant, tandis que Context API permet de partager des données entre des composants éloignés sans devoir passer les props manuellement à chaque niveau.
