---
sort: 2
---

# git & github
## Youtube-Tutorials:
- [Learn Git in 15 Minutes](https://www.youtube.com/watch?v=USjZcfj8yxE)
- [Learn Github in 20 Minutes](https://www.youtube.com/watch?v=nhNq2kIvi9s)

## git cheat sheet
- eigenes repo clonen (schlüsselbasiert) mit Schreibrechten:

  `git clone git@github.com:<username>/<reponame>`
- öffentliches (public) repo clonen:

  `git clone https://github.com/<username>/<reponame>`

- Im beim Clonen erzeugten Verzeichnis (lokales Repository)  Dateien/Verzeichnisse erstellen oder editieren. Dann Änderungen anzeigen:

  `git status`

- Alle Dateien/Verzeichnisse im lokalen Repository unter Versionskontrolle stellen:

  `git add .`

- Bereitstellen zur Versionierung und commit message schreiben:

  `git commit -m "new file"`

- Auf github hochladen:
  
  `git push`
  
  oder: `git push origin main` (origin: Speicherort am Server, main: branch)

 ## github - Authentifizierung für `push` schlüsselbasiert
 
- `ssh-keygen` in der Konsole, unter `.ssh` findet sich dann eine Datei `*.pub`. Inhalt auf Github->Settings->SSH and GPG keys in das Feld, das beim Drücken von "New Key" aufgeht, hineinkopieren.
- `git remote add origin git@github.com:<accountname>/<repository-name>`


