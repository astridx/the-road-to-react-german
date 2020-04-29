## Setting up a React Project

In Road to React verwenden wir die [create-react-app](https://github.com/facebook/create-react-app), um unsere Anwendung für den Start einzurichten. Es handelt sich um ein von Facebook im Jahr 2016 eingeführtes Starter-Kit für React, das keine Konfiguration enthält. Es wird [von 96% der React-Benutzer für Anfänger empfohlen](https://twitter.com/dan_abramov/status/806985854099062785). *create-react-app* installiert die für die Entwicklung notwendigen Werkzeuge für dich und erledigt die Konfiguration, während du deinen Fokus auf der Implementierung der Anwendung legst.

Öffne die Befehlszeile, nachdem du Node und npm installiert hast. Lege danach ein Verzeichnis für dein Beispielprojekt an. Gibt in diesem Verzeichnis den folgenden Befehl ein. Ich werde dieses Projekt *hacker-stories* nennen. Unabhängig davon kannst du für dein Projekt natürlich jeden anderen beliebigen Namen wählen:

{title="Command Line",lang="text"}
~~~~~~~
npx create-react-app hacker-stories
~~~~~~~

Navigiere nach Abschluss des Setups in den neuen Ordner:

{title="Command Line",lang="text"}
~~~~~~~
cd hacker-stories
~~~~~~~
Öffne jetzt die Anwendung in einem Editor oder einer IDE oder, gib `code .` in die Befehlszeile ein, wenn du Visual Studio Code nutzt. Die folgende Ordnerstruktur wird dargestellt – oder eine Variation davon in Abhängigkeit von der verwendeten Version von *create-react-app*:

{title="Project Structure",lang="text"}
~~~~~~~
hacker-stories/
--node_modules/
--public/
--src/
----App.css
----App.js
----App.test.js
----index.css
----index.js
----logo.svg
--.gitignore
--package-lock.json
--package.json
--README.md
~~~~~~~

Dies sind die wichtigsten Ordner und Dateien:

* **README.md:** The *.md* extension indicates the file is a markdown file. Markdown is a lightweight markup language with plain text formatting syntax. Many source code projects come with a *README*.md file that gives instructions and useful information about the project. When we push projects to platforms like GitHub, the *README.md* file usually displays information about the content contained in its repositories. Because you used create-react-app, your *README.md* should be the same as the official [create-react-app GitHub repository](https://github.com/facebook/create-react-app).
* **node_modules/:** This folder contains all node packages that have been installed via npm. Since we used create-react-app, a couple of node modules are already installed. We'll not touch this folder, since node packages are usually installed and uninstalled with npm via the command line.
* **package.json:** This file shows you a list of node package dependencies and other project configurations.
* **package-lock.json:** This file indicates npm how to break down all node package versions. We'll not touch this file.
* **.gitignore:** This file displays all files and folders that shouldn't be added to your git repository when using git, as such files and folders should be located only in your local project. The *node_modules/* folder is one example. It is enough to share the *package.json* file with others, so they can install dependencies on their end with `npm install` without your entire dependency folder.
* **public/:** This folder holds development files, such as *public/index.html*. The index file is displayed on *localhost:3000* when the app is in development or on a domain that is hosted elsewhere. The default setup handles relating this *index.html* with all the JavaScript from *src/*.

Am Anfang findest du alles, was du brauchst, im Ordner *src/*. Das Hauptaugenmerk liegt auf der Datei *src/App.js*, die eine React-Komponente implementiert. Später teilst du deine Komponente unter Umständen in mehrere Dateien auf. Dann verwaltet jede einzelne Datei eine oder mehrere Komponenten.

Additionally, you will find a *src/App.test.js* file for your tests, and a *src/index.js* as an entry point to the React world. You will get to know both files intimately in later sections. There is also a *src/index.css* and a *src/App.css* file to style your general application and components, which comes with the default style when you open them. You will modify them later as well.

After you have learned about the folder and file structure of your React project, let's go through the available commands to get it started. All your project specific commands can be found in your *package.json* under the *scripts* property. They may look similar to these:

{title="package.json",lang="javascript"}
~~~~~~~
{
  ...
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  ...
}
~~~~~~~

These scripts are executed with the `npm run <script>` command in an IDE-integrated terminal or command line tool. The `run` can be omitted for the `start` and `test` scripts. The commands are as follows:

{title="Command Line",lang="text"}
~~~~~~~
# Runs the application in http://localhost:3000
npm start

# Runs the tests
npm test

# Builds the application for production
npm run build
~~~~~~~

Ein anderer Befehl aus den vorhandenen npm-Skripten mit dem Namen `eject` bleibt hier außen vor. Es ist eine Eingabe, der keine Wiederherstellung erlaubt. Sobald du `eject` ausgeführt hast, gibt es kein Zurück. Im Wesentlichen ist dieser Befehl dazu da, das gesamte Build-Tool und die Konfiguration der create-react-app zu bearbeiten, falls du mit den standardmäßig gesetzten Einstellungen nicht zufrieden bist. Hier werden wir alle Standardeinstellungen beibehalten.

### Übungen:

* Read a bit more through React's [create-react-app documentation](https://github.com/facebook/create-react-app) and [getting started guide](https://create-react-app.dev/docs/getting-started).
  * Lese mehr zum Thema [the supported JavaScript features in create-react-app](https://create-react-app.dev/docs/supported-browsers-features).
* Lese mehr zum Thema [the folder structure in create-react-app](https://create-react-app.dev/docs/folder-structure).
  * Go through all of your React project's folders and files one by one.
* Lese mehr zum Thema [the scripts in create-react-app](https://create-react-app.dev/docs/available-scripts).
  * Start your React application with `npm start` on the command line and check it out in the browser.
    * Exit the command on the command line by pressing `Control + C`.
  * Führe das Skript `npm test` aus.
  * Run the `npm run build` script and verify that a *build/* folder was added to your project (you can remove it afterward). Note that the build folder can be used later on to [deploy your application](https://www.robinwieruch.de/deploy-applications-digital-ocean/).
* Every time we change something in our code throughout the coming learning experience, make sure to check the output in your browser for getting visual feedback.