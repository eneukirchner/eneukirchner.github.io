---
sort: 1
---

# Bouncing Ball

## Curses Library

Mithilfe dieser C-Bibliothek können Programme erstellt werden, die im Terminal eine Art "Pseudo-Grafik" erzeugen. Infos:

- [Wikibook](https://de.wikibooks.org/wiki/Ncurses:_Grundlegendes)
- [Openbook C](http://openbook.rheinwerk-verlag.de/linux_unix_programmierung/Kap13-002.htm)

Installation: ```sudo apt update && sudo apt install libncurses6 libncurses-dev``` 

Optional Doku: ```sudo apt install ncurses-doc ncurses-examples```

_*-dev beinhalten immer die C-Headerfiles der Bibliothek_

Lade eines der Beispielprogramme in deinen Editor und speichere es unter ```example.c``` in einem eigenen Ordner ```Games```. Zum Erstellen des lauffähigen Programmes musst du die ncurses-Library hinzulinken:

```gcc example.c -o example -lncurses -Wall```

_Du kannst diese Kommandozeile auch mittels Makefile automatisieren._

## Beispielprogramm Ball

![bouncing ball](img/bouncing_ball.jpg)

Der folgende Programmcode zeigt ein Beispiel für die Verwendung von ncurses. Kompiliere das Programm und starte es.

```c
#include <ncurses.h>
#include <unistd.h>

// Stores our balls position
typedef struct _Ball {
    int x;
    int y;
} Ball;
// Stores the direction that the ball is moving
typedef struct _Direction {
    int x;
    int y;
} Direction;

int main()
{
    initscr();              // Start ncurses
    nodelay(stdscr, TRUE);  // Don't wait for \n for getch to parse input
    cbreak();               // Switch off input buffering
    curs_set(FALSE);        // Don't place a cursor on the screen

    int height = 0;         // Window height
    int width = 0;          // Window width

    // initialize our ball struct and direction struct
    Ball b = {
        1,1
    };
    Direction d = {
        1,1
    };

    getmaxyx(stdscr, height, width);
    while (getch() == ERR) { 
         // Get terminal window dimensions (rows and characters)
       

        // print ball
        mvprintw(b.y,b.x,"o");

        // move ball for next frame
        b.y += d.y;
        b.x += d.x;

        // Check for ball being outside of our window boundaries
        if (b.x == width - 1 || b.x == 0){
            // change direction
            d.x *= -1;
            b.x += d.x;
        }
        if (b.y == height - 1 || b.y == 0){
            // change direction
            d.y *= -1;
            b.y += d.y;
        }

        refresh();      // Refresh the output
        usleep(50000);  // Sleep to show output (Single frame)
        clear();        // Clear output
    }

    nodelay(stdscr, FALSE);
    nocbreak();
    endwin();
    return 0;

}
```

### Bouncing Ball -  objektorientierte Version

Speichere das Beispielprogramm von vorhin unter ```bouncing.cpp```. Es soll nun so umgebaut werden, dass die Vorteile der Programmierung in C++ genützt werden. Erzeuge folgende Klassen:

- Window: 
    - Konstruktor zum Initialisieren der curses-Umgebung
    - Destruktor zum Zurücksetzen des Terminals auf die Ausgangswerte beim Beenden des Programmes.
- Ball: 
    - Konstruktor 
    - move (Bewegen des Balles)
    - Membervariablen: shape (Zeichen für den Ball, default "o"), Position (xy, Struktur), Geschwindigkeit (xy), erlaubter Fensterbereich (xy)

Kompiliere das Programm unter Verwendung von g++ analog zur Kommandozeile des C-Programmes. Wenn alles funktioniert, teile den Quelltext so auf mehrere Dateien auf, dass jede Klasse ein *.cpp- und ein *.h - File hat. Erstelle dann ein Makefile zum automatisierten Bauen des Binärfiles.









