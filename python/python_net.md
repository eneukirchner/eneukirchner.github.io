---
sort: 2
---

# Netzwerkprogramme mit Python

[TCP Socket Flowchart](http://www.netzmafia.de/skripten/inetprog/serverclient.gif)

[Python Low-level Socket](https://docs.python.org/3/library/socket.html#module-socket)

[socketserver - A framework for network servers](https://docs.python.org/3/library/socketserver.html)

## Beispiel Morse-Server
### Teil I

- Unter Verwendung des [Beispiels mit dem StreamRequestHandler]( https://docs.python.org/3/library/socketserver.html#socketserver-tcpserver-example) soll ein TCP-Server erstellt werden.

- Die Portnummer, auf der der Morseserver lauscht, soll beim Aufruf in der Kommandozeile als erstes Argument mitgegeben werden: ```./morseserver.py 1234```

- Der Server gibt den vom Client empfangenen String (Zeichen 0...9 und a...f, alle anderen ignorieren!) auf der Konsole im Morsecode aus (Beispiel "1affe"):
```
pi@bulmix:~/FSST/34AKWIB/MorseServer $ ./morseserver.py 1234
127.0.0.1 wrote:
.----
.-
..-.
..-.
.
```
_Tipp: Verwenden Sie für die Codetabelle den Datentyp dictionary._

### Teil II

- Der Server aus Teilaufgabe I soll nun so erweitert werden, dass eine der LEDs am Zusatzboard (GPIO 18 / 23 / 24 / 25) den Code der empfangenen Zeichen nacheinander als Lichtsignal morst. 
- Timing: Basis ist die Dauer eines Punktes - hier 0.2 Sekunden. [Infos zum Morsecode](https://de.wikipedia.org/wiki/Morsecode#Zeitschema_und_Veranschaulichung)
- Die Verwendung der Python-Library [gpiozero](https://gpiozero.readthedocs.io/en/stable/) wird dringend angeraten. Tipp: Die Erzeugung von langen und kurzen Lichtpulsen lässt sich kompakt in einer Zeile bewerkstelligen: ```led.blink(TDOT * 3, TDOT, 1, False)``` 

