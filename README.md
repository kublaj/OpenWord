# OpenWord

OpenWord is a JSON file format for storing localizations (a database of phrases in different languages). It supports partial translations and gradual evolving of the file, as your software evolves. It was created with an idea of collaborative translating.

The repository also contains a little web editor, which allows collaborative work on localization files. You can let your users from across the globe translate your software. Demo: [Photopea Translation](https://www.photopea.com/translate/).

## Example

```json
{ 
  "langs": [
    { "name": "English", "code": "en", "table": 0 },
    { "name": "Česky",   "code": "cs", "table": 1 },
    { "name": "Español", "code": "es", "table": 2 }
  ],
  "tables": [
    [
      "Welcome",
      "Loading",
      ["Black", "Red", "Blue"],
      "Exit"
    ],
    [
      "Vítejte",
      null,
      ["Černá", "Červená", "Modrá"],
      "Ukončit"
    ],
    []
  ]
}
```
`langs` is an array of languages, that are represented in the file. 
* `name` is the name of the language
* `code` is the ISO 639-1 code of the language
* `table` is the index of the table, which contains phrases for that language
 
`tables` is an array of tables. Each table contains phrases for a specific language. A table has a tree-like structure, that can be defined recursively: a table is an array of nodes; a node is either `null`, a string or an array of nodes.

We can refer to each phrase in a table using a path: an array of indices of nested arrays containing specific string (counting from 0). In the previous example, the word "Loading" has a path `[1]`. The word "Blue" has a path `[2,2]`. You can use paths to refer to phrases from your software, without refering to specific table (specific language of the phrase).

When multiple tables contain phrases for a specific path, all these phrases must have the same "meaning" (in different languages). So, when you want to relocate a phrase, you must do it in all tables.

## Best practice

It is convenient to have a complete localization in one of the tables (let's put it into the first table), while other tables may contain partial translations. Such complete table can be used as a source for translators. It also can be used as the fallback, when the localized version of the phrase is not available.

You can start using OpenWord as soon as you start creating your software. Just put all the strings into a JSON OpenWord file with a single table (for a single language). You can add translations at any time in the future (or ask your users to translate it).

It is nice to share your OpenWord translations with others. Different software requires different sets of phrases, but you can make simple programs to crunch lots of OpenWord files to find phrases, that are useful for you.

What about creating a huge OpenWord file, the ultimate database for localization? No.

## String identifiers for each prhase

A table could be an object containing key-value pairs. These keys would be the same in all tables. We would refer to the string "Blue" with a path `clrs.blue` in all tables. You could easily add the new phrase between "Welcome" and "Loading", without changing paths to other phrases (i.e. without rewriting your software), which is not possible in the current format.

Coming up with accurate and elegant key names requires a lot of creative thinking. For large localization files, an author may run out of good ideas for a key name, or simply get tired. It may lead to inventing too long keys, or just using the keys "word26", "word27", "word28" etc. That's why OpenWord identifies phrases by numbers.

# Web Editor

The folder `editor` contains a web editor of an OpenWord file. Replace the "editor/versions/file.json" by your own file (but keep the same name). Put the content of the folder onto your web server. Navigate to the location of the folder in a web browser (so index.html would be opened) and start editing.

You can see it in action at [Photopea Translation](https://www.photopea.com/translate/).

Current server is written in PHP. It receives the JSON in POST request and updates the file "file.json". It also creates a backup each hour. Feel free to rewrite it to any other technology (it is just 10 lines of code). 

Current server allows anybody to write any data to your server (i.e. replace your OpenWord file). Improve the security by sharing the link only with the translators you trust, or rebuild the whole editor. It would be nice to store the history of the file, something like Git.
