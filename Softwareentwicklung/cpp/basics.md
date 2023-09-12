---
sort: 1
---

# Grundlagen Objektorientierung

#### Mächtigere Datentypen
##### Basisdatentypen (C / C++)

`int x = 0;`

`int` … Datentyp („Bauplan“) 

`x` … Instanz (eigentliche Element im Speicher)

##### Struktur: erweiterter Datentyp mit mehreren Basisdatentypen (C / C++)
```
struct schueler {
    char name[20];
    int kennzahl;
}; 
```

##### Klasse: erweiterter Datentyp mit wichtiger Zusatzfunktionalität (C++)

```
class schueler {
    public:
        schueler(char *name, int kennzahl); // Konstruktor
        void show(); // Lesen
        void setData(char *name, int kennzahl); // Schreiben
    private:
        char name[20]; // Membervariablen
        int kennzahl;
}; 
```

#### Motivation
Ziel: Bessere Strukturierung von Programmen, sauberes API („Schnittstelle“, Application Programmers Interface), Wiederverwertbarkeit. Die wichtigsten Mechanismen zum Erreichen dieses Ziels:

* Kapselung: Zugriffsbeschränkung auf die Daten (Membervariablen)
  * private: nur innerhalb der Klasse
  * public: von außerhalb
  * protected: auch von abgeleiteten Klassen
* Funktionen: (Member functions, Methoden)
  * Lese- und Schreibzugriff auf die Membervariablen
  * Spezielle: Konstruktor, Destruktor
* Vererbung: Ableitung von einer oder mehreren Basisklassen (person -> schueler)
* Überladen von Funktionen: Auswahl der verwendeten Funktion anhand der Übergabeparameter:

```
setData(char *name);
setData(char *name, int kennzahl);
```
* Polymorphie: Basisfunktion, die in den vererbten Klassen unterschiedlich definiert wird.
* Unterschied struct – class: Members bei struct standardmäßig public, bei class private.
* Beispiele für objektorientierte Sprachen: C++, C#, Java, Python, Javascript
* Objekt: Instanz einer Klasse

```
schueler x; // schueler -> Klasse, x -> Objekt
schueler x(„franz“, 6014572018001);// Erzeugung+Initialisierung
```
