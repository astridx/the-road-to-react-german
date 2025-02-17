## Styled Components in React

**Styled Components** ist einer von mehreren Ansätzen für **CSS-in-JS**. Die Bibliothek erfreut sich großer Beliebtheit unter React-Entwicklern, deshalb habe ich sie ausgewählt. Es handelt sich um ein externes Werkzeug und wird über die Befehlszeile installiert:

{title="Command Line",lang="text"}
~~~~~~~
npm install styled-components
~~~~~~~

Importiere **Styled Components** nach der Installation in deine Anwendung. Füge die Anweisung hierzu in die *src/App.js*-Datei ein:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';
# leanpub-start-insert
import styled from 'styled-components';
# leanpub-end-insert
~~~~~~~

Wie die Bezeichnung CSS-in-JS vermuten lässt, erstellst du **Styled Components** innerhalb von JavaScript. Definiere deine ersten gestalteten Komponenten in der Datei *src/App.js*:

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledContainer = styled.div`
  height: 100vw;
  padding: 20px;

  background: #83a4d4;
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
`;

const StyledHeadlinePrimary = styled.h1`
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
`;
~~~~~~~

Wenn du gestaltete Komponenten einsetzt, verwendest du [Template-Strings](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/template_strings) genauso wie [Funktionen](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Function). Alles zwischen den Backticks ist ein Argument, und das `styled`-Objekt gibt dir Zugriff auf alle erforderlichen HTML-Elemente (beispielsweise div, h1). Sobald eine Funktion mit dem Stil aufgerufen wird, gibt sie eine Komponente zurück, die in der App verwendbar ist:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <StyledContainer>
      <StyledHeadlinePrimary>My Hacker Stories</StyledHeadlinePrimary>
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
# leanpub-start-insert
    </StyledContainer>
# leanpub-end-insert
  );
};
~~~~~~~

**Styled Components** folgen denselben Regeln wie gewöhnliche. Alles, was zwischen den Element-Tags eingefügt ist, wird als `children`-Eigenschaft (Props) übergeben. Für Item verwenden wir diesmal keine Inline-Styles, sondern definieren eine gestaltete Komponente. `StyledColumn` erhält seine Stile dynamisch mithilfe einer Eigenschaft (Props):

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <StyledItem>
    <StyledColumn width="40%">
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
# leanpub-start-insert
    </StyledColumn>
    <StyledColumn width="30%">{item.author}</StyledColumn>
    <StyledColumn width="10%">{item.num_comments}</StyledColumn>
    <StyledColumn width="10%">{item.points}</StyledColumn>
    <StyledColumn width="10%">
      <StyledButtonSmall
# leanpub-end-insert
        type="button"
        onClick={() => onRemoveItem(item)}
      >
        Dismiss
# leanpub-start-insert
      </StyledButtonSmall>
    </StyledColumn>
  </StyledItem>
# leanpub-end-insert
);
~~~~~~~

Die flexible `width`-Eigenschaft (Props) ist über den Template-String der gestalteten Komponente als Argument einer Inline-Funktion zugreifbar. Der Rückgabewert der Funktion ist ein String. Wir benötigen keine explizite Return-Anweisung. Deshalb überarbeiten wir die Pfeilfunktion zu einer knappen und prägnanten Inline-Funktion:

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledItem = styled.div`
  display: flex;
  align-items: center;
  padding-bottom: 5px;
`;

const StyledColumn = styled.span`
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;

  a {
    color: inherit;
  }

  width: ${props => props.width};
`;
~~~~~~~

Erweiterte Funktionen wie die CSS-Verschachtelung sind standardmäßig in gestalteten Komponenten verfügbar. Greife auf verschachtelte Elemente zu und wähle das aktuelle mit dem CSS-Operator `&` aus:

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledButton = styled.button`
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;

  &:hover {
    background: #171212;
    color: #ffffff;
  }
`;
~~~~~~~

Erstelle spezielle Versionen von gestalteten Komponenten, indem du eine andere an die Funktion `styled` übergibst. StyledButtonSmall und StyledButtonLarge übernehmen alle Stile der zuvor definierten StyledButton-Komponente:


{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledButtonSmall = styled(StyledButton)`
  padding: 5px;
`;

const StyledButtonLarge = styled(StyledButton)`
  padding: 10px;
`;

const StyledSearchForm = styled.form`
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
`;
~~~~~~~

Wenn wir eine gestaltete Komponente wie StyledSearchForm verwenden, werden die zugrunde liegenden Elemente (`form`,` button`) in der realen HTML-Ausgabe verwendet. So ist es möglich, weiterhin die nativen HTML-Attribute (`onSubmit`,` type`, `disabled`) zu verwenden:

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <StyledSearchForm onSubmit={onSearchSubmit}>
# leanpub-end-insert
    <InputWithLabel
      id="search"
      value={searchTerm}
      isFocused
      onInputChange={onSearchInput}
    >
      <strong>Search:</strong>
    </InputWithLabel>

# leanpub-start-insert
    <StyledButtonLarge type="submit" disabled={!searchTerm}>
# leanpub-end-insert
      Submit
# leanpub-start-insert
    </StyledButtonLarge>
  </StyledSearchForm>
# leanpub-end-insert
);
~~~~~~~

Zuletzt integrieren wir die bisher nicht definierten Komponenten StyledLabel und StyledInput in InputWithLabel:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <StyledLabel htmlFor={id}>{children}</StyledLabel>
# leanpub-end-insert
      &nbsp;
# leanpub-start-insert
      <StyledInput
# leanpub-end-insert
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
      />
    </>
  );
};
~~~~~~~

Die Komponenten StyledLabel und StyledInput definieren wir in derselben Datei:

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledLabel = styled.label`
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
`;

const StyledInput = styled.input`
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
`;
~~~~~~~

**Styled Components** sind im Grunde genommen Styles in Form von React-Komponenten ohne CSS-Zwischendatei. Wenn eine solche in JavaScript definiert ist, aber nicht verwendet wird, erkennt eine [integrierte Entwicklungsumgebung](https://de.wikipedia.org/w/index.php?title=Integrierte_Entwicklungsumgebung&oldid=197630316) (IDE) dies und gibt dir einen Hinweis. Beide Methoden, CSS-in-JS und CSS-in-CSS, und ihre Ansätze (zum Beispiel gestaltete Komponenten und CSS-Module) sind bei React-Entwicklern beliebt. Deshalb war es mir wichtig, sie dir hier vorzustellen. Du bist aber völlig frei: Verwende an Werkzeugen ausschließlich das, was am besten zu dir und deinem Team passt.

### Übungen:

* Begutachte den [Quellcode dieses Abschnitts](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Styled-Components-in-React).
  * Reflektiere die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Styled-Components-in-React?expand=1).
* Lese mehr zum Thema [Styled Components](https://www.robinwieruch.de/react-styled-components).
  * Normalerweise gibt es keine *src/index.css*-Datei für globale Stile, wenn **Styled Components** verwendet werden. Finde heraus, ob und wie es möglich ist, globale Stile in Kombination mit gestalteten Komponenten zu verwenden.
 