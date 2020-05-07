## Bibliotheken von Drittanbietern in React

Wir haben zuvor die native Fetch-API eingeführt, um die Hacker News-API abzufragen. Diese wird leider nicht von allen Browsern zuverlässig unterstützt. Insbesonere bei alten Versionen ist die [Browserkompatibilität](https://developer.mozilla.org/de/docs/Web/API/Fetch_API#Browserkompatibilit%C3%A4t) lückenhaft. Spätestens sobald du deine Anwendung in einer [Headless-Browser-Umgebung](https://en.wikipedia.org/wiki/Headless_browser) testest, werden Probleme mit der Fetch-API auftreten. Es gibt verschiedene Möglichkeiten, das Abrufen in älteren Browsern mithilfe von ([Polyfills](https://de.wikipedia.org/wiki/Polyfill)) und die Ausführung in Tests (somorphic fetch) zum Laufen zu bringen. Die genauere Erklärung der Konzepte würde den Rahmen dieses Buchs sprengen.

Eine Alternative besteht darin, die native Fetch-API durch eine stabile Drittanbieter Bibliothek zu ersetzten. Da das Integrieren von externen Modulen Thema dieses Abschnittes ist, schlagen wir im folgenden zwei Fliegen mit einer Klappe. Wir ersetzen die native Fetch-API mit dem Promise-basierten HTTP-Client [axios](https://github.com/axios/axios), welcher asynchrone Anforderungen an Remote-APIs ausführt. In diesem Abschnitt lernst du, wie du eine Bibliothek - in unserem Fall eine native API des Browsers - durch ein Node.js-Module ersetzt. Installiere zunächst axios mithilfe der Befehlszeile:

{title="Command Line",lang="text"}
~~~~~~~
npm install axios
~~~~~~~

Importieren danach Axios in deine Anwendung:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

...
~~~~~~~

Jetzt ist es dir möglich, `axios` anstelle der `Fetch-API` zu verwenden. Die Anwendung ist ähnlich wie zuvor. Axios nimmt die URL als Argument entgegen und gibt ein Promise-Objekt zurück. Es ist nicht mehr erforderlich, die zurückgegebene Antwort in JSON umzuwandeln, da `axios` das Ergebnis in ein JavaScript-Objekt einschließt. Passen wir den Beispielcode im nächsten Schritt an die zurückgegebene Datenstruktur an:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    axios
      .get(url)
# leanpub-end-insert
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
          payload: result.data.hits,
# leanpub-end-insert
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, [url]);

  ...
};
~~~~~~~

In diesem Code rufen wir `axios.get()` für eine explizite [HTTP-GET-Anforderung](https://developer.mozilla.org/de/docs/Web/HTTP/Methods/GET) auf. Es handelt sich um dieselbe HTTP-Methode, die wir standardmäßig mit der nativen `Fetch-API` des Browsers verwendet haben. `axios` ist intuitiv aufgebaut: Für eine [HTTP-POST-Anforderung](https://developer.mozilla.org/de/docs/Web/HTTP/Methods/POST) verwendest du `axios.post()`.

Wie du sieht: `axios` ist eine leistungsstarke Bibliothek zum Zugriff auf Remote-APIs. Ich empfehle dir diese anstelle der native Abruf-API, wenn deine Anforderungen komplex sind, es dir wichtig ist, älteren Browser zu unterstützen und/oder du ein Framework zum Testen verwendest.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Third-Party-Libraries-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Explicit-Data-Fetching-with-React...hs/Third-Party-Libraries-in-React?expand=1).
* Informiere dich über [beliebte Bibliotheken in React](https://www.robinwieruch.de/react-libraries).
* Lese [warum Frameworks unerlässlich sind](https://www.robinwieruch.de/why-frameworks-matter).
* Lese mehr zum Thema [axios](https://github.com/axios/axios).