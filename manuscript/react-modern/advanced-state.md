## React Advanced State

Das gesamte Statusmanagement in unserer Anwendung verwendet den useState Hook von React. Eine ausgefeiltere Statusverwaltung nutzt zusätzlich **Reacts useReducer Hook**. Da sich beim Konzept der Reduzierungen in JavaScript die Geister scheiden, werden ich hier nicht ausführlich darauf eingehen. Unbeachtet lasse ich das Thema aber nicht. Die Übungen am Ende dieses Abschnitts gibt dir genug Material, um dir deine eigene Meinung zu bilden.

Wir werden die Statusverwaltung der `stories` vom `useState`-Hook in einen neuen `useReducer`-Hook verschieben. Führe zunächst eine Reduzierfunktion außerhalb deiner Komponenten ein. Eine solche Funktion empfängt immer einen `state` und eine `action`. Basierend auf diesen beiden Argumenten gibt ein Reduzierer einen neuen Status zurück:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const storiesReducer = (state, action) => {
  if (action.type === 'SET_STORIES') {
    return action.payload;
  } else {
    throw new Error();
  }
};
# leanpub-end-insert
~~~~~~~

Eine Reduktionsaktion wird oft mit `type` assoziiert. Wenn dieser Typ einer Bedingung im Reduzierer entspricht, dann führe eine Aktion aus. Wenn dies nicht so ist, dann gib einen Fehler aus. So erinnerst du dich selbst daran, dass hier die Implementierung lückenhaft ist. Die Funktion `storiesReducer` deckt einen `type` ab und gibt dann die `Nutzlast` der eingehenden Aktion zurück, ohne den aktuellen Status zur Berechnung des neuen Status zu verwenden. Der neue Zustand ist die `Nutzlast`.

A reducer `action` is often associated with a `type`. If this type matches a condition in the reducer, do something. If it isn't covered by the reducer, throw an error to remind yourself the implementation isn't covered. The `storiesReducer` function covers one `type, and then returns the `payload` of the incoming action without using the current state to compute the new state. The new state is simply the `payload`.

In the App component, exchange `useState` for `useReducer` for managing the `stories`. The new hook receives a reducer function and an initial state as arguments and returns an array with two items. The first item is the *current state*; the second item is the *Statusaktualisierungsfunktion* (also called *dispatch function*):

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    []
  );
# leanpub-end-insert

  ...
};
~~~~~~~

The new dispatch function can be used instead of the `setStories` function, which was returned from `useState`. Instead of setting state explicitly with the Statusaktualisierungsfunktion from `useState`, the `useReducer` Statusaktualisierungsfunktion dispatches an action for the reducer. The action comes with a `type` and an optional payload:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
    setIsLoading(true);

    getAsyncStories()
      .then(result => {
# leanpub-start-insert
        dispatchStories({
          type: 'SET_STORIES',
          payload: result.data.stories,
        });
# leanpub-end-insert
        setIsLoading(false);
      })
      .catch(() => setIsError(true));
  }, []);

  ...

  const handleRemoveStory = item => {
    const newStories = stories.filter(
      story => item.objectID !== story.objectID
    );

# leanpub-start-insert
    dispatchStories({
      type: 'SET_STORIES',
      payload: newStories,
    });
# leanpub-end-insert
  };

  ...
};
~~~~~~~

The application appears the same in the browser, though a reducer and React's `useReducer` hook are managing the state for the stories now. Let's bring the concept of a reducer to a minimal version by handling more than one state transition.

So far, the `handleRemoveStory` handler computes the new stories. It's valid to move this logic into the reducer function and manage the reducer with an action, which is another case for moving from imperative to declarative programming. Instead of doing it ourselves by saying *how it should be done*, we are telling the reducer *what to do*. Everything else is hidden in the reducer.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleRemoveStory = item => {
# leanpub-start-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
# leanpub-end-insert
  };

  ...
};
~~~~~~~

Now the reducer function has to cover this new case in a new conditional state transition. If the condition for removing a story is met, the reducer has all the implementation details needed to remove the story. The action gives all the necessary information, an item's identifier`, to remove the story from the current state and return a new list of filtered stories as state.

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  if (action.type === 'SET_STORIES') {
    return action.payload;
# leanpub-start-insert
  } else if (action.type === 'REMOVE_STORY') {
    return state.filter(
      story => action.payload.objectID !== story.objectID
    );
  } else {
# leanpub-end-insert
    throw new Error();
  }
};
~~~~~~~

All these if else statements will eventually clutter when adding more state transitions into one reducer function. Refactoring it to a switch statement for all the state transitions makes it more readable:

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
# leanpub-start-insert
  switch (action.type) {
    case 'SET_STORIES':
      return action.payload;
    case 'REMOVE_STORY':
      return state.filter(
        story => action.payload.objectID !== story.objectID
      );
    default:
      throw new Error();
  }
# leanpub-end-insert
};
~~~~~~~

What we've covered is a minimal version of a reducer in JavaScript. It covers two state transitions, shows how to compute current state and action into a new state, and uses some business logic (removal of a story). Now we can set a list of stories as state for the asynchronously arriving data, and remove a story from the list of stories, with just one state managing reducer and its associated `useReducer` hook.

To fully grasp the concept of reducers in JavaScript and the usage of React's useReducer Hook, visit the linked resources in the exercises.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Advanced-State).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Conditional-Rendering...hs/React-Advanced-State?expand=1).
* Lese mehr zum Thema [reducers in JavaScript](https://www.robinwieruch.de/javascript-reducer).
* Lese mehr zum Thema reducers and useReducer in React ([0](https://www.robinwieruch.de/react-usereducer-hook), [1](https://de.reactjs.org/docs/hooks-reference.html#usereducer)).