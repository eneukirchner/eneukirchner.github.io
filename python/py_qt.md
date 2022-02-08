---
sort: 3
---

# Grafische Oberflächen mit Qt

## Installation
```sudo apt install python3-pyqt5* pyqt5-dev-tools```

## Programm "Hello"
```py
#!/usr/bin/env python3
import sys
from PyQt5 import QtCore, QtWidgets
from PyQt5.QtWidgets import QMainWindow, QLabel, QWidget, QApplication, QLineEdit, \
    QPushButton
from PyQt5.QtCore import QSize

class MyWindow(QWidget):
    def __init__(self):
        # Konstruktor von QWidget aufrufen
        super().__init__()

        # Fenstergröße und Titel einstellen
        self.setMinimumSize(QSize(300, 100))
        self.setWindowTitle('Greeting')

        # Beschriftung
        nameLabel = QLabel("Name:", self)
        nameLabel.move(20, 20)

        # Eingabefeld
        self.nameEntry = QLineEdit(self)
        self.nameEntry.move(80, 20)
        self.nameEntry.resize(200, 20)

        # Send Button
        sendbutton = QPushButton("Send", self)
        sendbutton.clicked.connect(self.greetMe)
        sendbutton.move(80, 60)
        sendbutton.resize(200, 20)

        # Ausgabe
        self.greetingLabel = QLabel(self)
        self.greetingLabel.move(80, 100)
        self.greetingLabel.resize(200, 20)

        # Widget anzeigen
        self.show()

    def greetMe(self):
        print(self.nameEntry.text())
        self.greetingLabel.setText("Hallo " + self.nameEntry.text() + "!")

# Game loop
app = QApplication(sys.argv)
win = MyWindow()
app.exec_()
```
