# Styling in React

Es gibt viele Möglichkeiten, eine React-Anwendung zu gestalten, und es gibt ausgiebige Debatten über die beste **Styling-Strategie** und den besten **Styling-Ansatz**. Wir werden einige dieser Ansätze durchgehen. Wie überall im Leben gibt es für jede Sichtweise Vor- und Nachteile. Hier gehe ich darauf ein, wie Entwickler und Teams herausfinde, was für sie am besten passt.

Wir beginnen mit allgemeinem CSS und sehen uns dann fortgeschrittene Alternativen an:  **CSS-in-CSS** (**CSS Modules**) und **CSS-in-JS** (**Styled Components**). CSS-Module und Styled Components sind nur zwei von vielen Ansätzen in beiden Gruppen. Außerdem lernst du, wie du skalierbare Vektorgrafiken (SVGs), wie zum Beispiel ein Logo oder Icons, in deine React-Anwendung integrierst.

Wenn du planst allgemeine UI-Komponenten (z. B. Schaltfläche, Dialog, Dropdown) nicht selbst einzufügen, ist es möglich, eine [beliebte React-Bibliothek für React] auszuwählen(https://www.robinwieruch.de/react-libraries). Du befindest dich aber auf einer soliden Basis, wenn du React lernst und diese Komponenten selbst erstellst, bevor du eine vorgefertigte Lösung verwendst. Daher werden wir hier im Buch keine der UI-Komponentenbibliotheken nutzen.

Die nachfolgenden Ansätze für das Styling sind in der `create-react-app` vorkonfiguriert. Wenn du die Build-Tools (beispielsweise Webpack) selbst verwaltest, sind unter Umständen Konfigurationsschritte auf deiner Seite erforderlich. Da wir die `create-react-app` verwenden, nutzen wir alles als Assets.

## CSS in React

Allgemeines CSS in React ähnelt dem Standard-CSS, das du gelernt hast. Jede Webanwendung gibt HTML-Elementen ein Attribut `class` (in React ist es `className`), das später in einer CSS-Datei mit Eigenschaften konkretisiert wird.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className="container">
      <h1 className="headline-primary">My Hacker Stories</h1>
# leanpub-end-insert

      <SearchForm
        searchTerm={searchTerm}
        onSearchInput={handleSearchInput}
        onSearchSubmit={handleSearchSubmit}
      />

      {stories.isError && <p>Something went wrong ...</p>}

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}
    </div>
  );
};
~~~~~~~

Das HTML-Element `<hr />` wurde entfernt, da CSS sich zukünftig um die Randbereich kümmern wird. Wir importieren die CSS-Datei mithilfe von `create-react-app`:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

# leanpub-start-insert
import './App.css';
# leanpub-end-insert
~~~~~~~

This CSS file will define the two (and more) CSS classes we used in the App component. In your *src/App.css* file, define them like the following:

{title="src/App.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headline-primary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

You should see the first stylings taking effect in your application when you start it again. Next, we will head over to the Item component. Some of its elements receive the `className` attribute too, however, we are also using a new styling technique here:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div className="item">
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
# leanpub-start-insert
        className="button button_small"
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

As you can see, we can also use the native `style` attribute for HTML elements. In JSX, style can be passed as an inline JavaScript object to these attributes. This way we can define dynamic style properties in JavaScript files rather than mostly static CSS files. This approach is called **inline style**, which is useful for quick prototyping and dynamic style definitions. Inline style should be used sparingly, however, as a separate style definition keeps the JSX more concise.

In your *src/App.css* file, define the new CSS classes. Basic CSS features are used. Advanced CSS features (e.g. nesting) from CSS extensions (e.g. Sass) are not included in this example, as they are [optional configurations](https://create-react-app.dev/docs/adding-a-sass-stylesheet/).

{title="src/App.css",lang="css"}
~~~~~~~
.item {
  display: flex;
  align-items: center;
  padding-bottom: 5px;
}

.item > span {
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.item > span > a {
  color: inherit;
}
~~~~~~~

The button style from the previous component is still missing, so we'll define a base button style and two more specific specific button styles (small and large). One of the button specifications has been used, the other will be used in the next steps.

{title="src/App.css",lang="css"}
~~~~~~~
.button {
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;
}

.button:hover {
  background: #171212;
  color: #ffffff;
}

.button_small {
  padding: 5px;
}

.button_large {
  padding: 10px;
}
~~~~~~~

Apart from styling approaches in React, naming conventions ([CSS guidelines](https://developer.mozilla.org/en-US/docs/MDN/Contribute/Guidelines/Code_guidelines/CSS)) are a whole other topic. The last CSS snippet followed BEM rules by defining modifications of a class with an underscore (`_`). Choose whatever naming convention suits you and your team. Without further ado, we will style the next React component:

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className="search-form">
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className="button button_large"
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

We can also pass the `className` attribute as a prop to React components. For example, we can use this option to pass the SearchForm component a flexible style with a `className` prop from a range of predefined classes from a CSS file. Lastly, style the InputWithLabel component:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className="label">
# leanpub-end-insert
        {children}
      </label>
      &nbsp;
      <input
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
# leanpub-start-insert
        className="input"
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

In your *src/App.css* file, add the remaining classes:

{title="src/App.css",lang="css"}
~~~~~~~
.search-form {
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
}

.label {
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
}

.input {
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
}
~~~~~~~

For simplicity, we styled elements like label and input individually in the *src/App.css* file. However, in a real application it may be better to define these elements once in the *src/index.css* file globally. As React components are split into multiple files, sharing style becomes a necessity.

This is the basic CSS most of us have already learned, written with an inline style that is more dynamic. Without CSS extensions like Sass (Syntactically Awesome Style Sheets) inline styles can become burdensome, though, because features like CSS nesting are not available in native CSS.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/CSS-in-React?expand=1).
* Lese mehr zum Thema [CSS stylesheets in create-react-app](https://create-react-app.dev/docs/adding-a-stylesheet).
* Lese mehr zum Thema [Sass in create-react-app](https://create-react-app.dev/docs/adding-a-sass-stylesheet) for taking advantage of more advanced CSS features like nesting.
* Try to pass `className` prop from App to SearchForm component, either with the value `button_small` or `button_large` and use this as `className` for the button element.