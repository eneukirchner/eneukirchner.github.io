---
sort: 3
---
# TCP-Clients mit Qt

## Basics
Mit dem Shell-Kommando `nc` lässt sich auf einfache Weise ein Portscanner simulieren:  
`nc -v -z www.bulme.at 80`

Das Abholen von Daten von einem (unverschlüsselten) Webserver ist damit ebenfalls möglich:
`nc -v -C localhost 80`  
Nach der Erfolgsmeldung spielen wir den http-Dialog nach:  
`GET / HTTP/1.1`  
`Host:localhost`  
&#9166;   
&#9166;

_Welche Bedeutung haben die einzelnen Optionen von `nc`? Schlage in der Online-Dokumentation nach:  
`man nc`   
`tldr nc` (gegebenenfalls nachinstallieren)_

## Portscanner-Skelett
Das gezeigte Beispiel versucht sich mit einem Server nacheinander auf Port 1... 1024 zu verbinden. Ist `connect` erfolgreich, so ist der Port offen. Das Codebeispiel ist eine Minimalanwendung auf der Textkonsole, Aufruf (Beispiel): `./portscanner localhost`

#### Projekt einrichten
QtCreator -> Neues Projekt -> Anwendung(Qt) -> Qt Konsolenanwendung

#### portscanner<span>.pro (Auschnitt des Anfangs)
_kein GUI, aber core und network_

```
QT -= gui
QT += core network
```
#### main.h
```cpp
#include <QCoreApplication>
#include "portscanner.h"

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    Portscanner scanner(argc, argv);
    // hier nicht nötig weil keine Signals/Slots
    // return a.exec();
}
```

#### portscanner.h
```cpp
#include <QObject>

class Portscanner : public QObject
{
    Q_OBJECT
public:
    explicit Portscanner(int argc, char* argv[], QObject *parent = nullptr);
}
```
#### portscanner.cpp
```cpp
#include <QCoreApplication>
#include <QAbstractSocket>
#include <QTcpSocket>
#include "portscanner.h"

Portscanner::Portscanner(int argc, char* argv[], QObject *parent) : QObject(parent)
{
    qDebug("Argc = %d", argc);
    for ( int i = 0; i < argc; i++)
        qDebug("argc %d = %s", i, argv[i]);

    QTcpSocket* socket = new QTcpSocket(this);

    if ( argc == 2 ) {
        QString host = argv[1];
        quint16 port; 

        for ( port = 1; port < 1024; port++) {
            socket->connectToHost( host, port);

            if (!socket->waitForConnected(1000)) {
                //qDebug("Port %d: Can not connect", port);
            } else {
                qDebug("Port %d: Connected !", port);
                socket->disconnectFromHost();
            }
        }
    } 
    else {
        qDebug("Format: Portscanner <hostname>");
    }
}
```

## HTTP-Client
Das Programm arbeitet ebenfalls im Terminal und gibt die gesamte Antwort des (unverschlüsselten) HTTP-Servers aus. Qt-Creator-Setup und *.pro siehe oben.

#### main.cpp
```cpp
#include <QCoreApplication>
#include "mytcpclient.h"

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    MyTcpClient client(argc, argv);
    return a.exec();
}
```

#### myhttpclient.h
```cpp
#include <QObject>
#include <QTcpSocket>
#include <QAbstractSocket>

class MyTcpClient : public QObject
{
    Q_OBJECT
public:
    explicit MyTcpClient(int argc, char* argv[], QObject *parent = nullptr);

public slots:
    void connected();
    void readyRead();

private:
    QTcpSocket *m_socket;
    QByteArray m_hostname;
};
```

#### myhttpclient.cpp
```cpp
#include "mytcpclient.h"
#include <QDebug>
#include <QCoreApplication>

MyTcpClient::MyTcpClient(int argc, char* argv[], QObject *parent) : QObject(parent)
{
    m_hostname = argv[1];
    unsigned short port = 80;
    m_socket = new QTcpSocket(this);

    // Signal ->  Slot-Verbindung
    connect(m_socket, &QTcpSocket::connected, this, &MyTcpClient::connected);
    connect(m_socket, &QTcpSocket::readyRead, this, &MyTcpClient::readyRead);

    m_socket->connectToHost(m_hostname, port); // TCP Connect
    if (!m_socket->waitForConnected(5000)) {
        qDebug() << "Connect failed!";
        exit(1);
    }
}

void MyTcpClient::connected()
{
    // normgerechter HTTP Request zum Server
    m_socket->write("GET / HTTP/1.1\r\nHost:" + m_hostname + "\r\n\r\n");
}

void MyTcpClient::readyRead()
{
    // HTTP-Antwort vom Server
    qDebug() << m_socket->readAll();
    m_socket->disconnectFromHost();
    QCoreApplication::quit();
}
```
