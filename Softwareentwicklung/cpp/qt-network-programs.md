---
sort: 6
---
# TCP-Client / Server

## 1. Basics
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

## 2. Portscanner-Skelett mit Qt
Das gezeigte Kommandozeilen-Beispiel versucht sich mit einem Server nacheinander auf Port 1... 1024 zu verbinden. Ist `connect` erfolgreich, so ist der Port offen. Das Codebeispiel ist eine Minimalanwendung auf der Textkonsole, Aufruf (Beispiel): `./portscanner localhost`

### Projekt einrichten
QtCreator -> Neues Projekt -> Anwendung(Qt) -> Qt Konsolenanwendung

### portscanner<span>.pro (Auschnitt des Anfangs)
_kein GUI, aber core und network_

```
QT -= gui
QT += core network
```
### main.c
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

### portscanner.h
```cpp
#include <QObject>

class Portscanner : public QObject
{
    Q_OBJECT
public:
    explicit Portscanner(int argc, char* argv[], QObject *parent = nullptr);
}
```
### portscanner.cpp
```cpp
#include <QCoreApplication>
#include <QAbstractSocket>
#include <QTcpSocket>
#include "portscanner.h"

Portscanner::Portscanner(int argc, char* argv[], QObject *parent) : QObject(parent)
{
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

## 3. HTTP-Client
Das Programm arbeitet ebenfalls im Terminal und gibt die gesamte Antwort des (unverschlüsselten) HTTP-Servers aus. Qt-Creator-Setup und *.pro siehe oben.

*(Ein lokaler Webserver kann gestartet werden, indem in einem Verzeichnis, das ein `index.html` enthält, `python -m http.server` aufgerufen wird. Abruf der Seite im Browser mit `http://localhost:8000`)*



### main.cpp
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

### mytcpclient.h
```cpp
#include <QObject>
#include <QTcpSocket>
#include <QAbstractSocket>

const int HTTP_PORT = 80; // standard port
const int TIMEOUT = 2000; // sec

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
    QString m_hostname;
};
```

### mytcpclient.cpp
```cpp
#include "mytcpclient.h"
#include <QDebug>
#include <QCoreApplication>

MyTcpClient::MyTcpClient(int argc, char* argv[], QObject *parent) :
    QObject(parent),
    m_socket(new QTcpSocket(this)),
    m_hostname(argv[1])

{
    // Signal ->  Slot-Verbindung
    connect(m_socket, &QTcpSocket::connected, this, &MyTcpClient::connected);
    connect(m_socket, &QTcpSocket::readyRead, this, &MyTcpClient::readyRead);

    m_socket->connectToHost(m_hostname, HTTP_PORT); // TCP Connect
    if (!m_socket->waitForConnected(TIMEOUT)) {
        qDebug() << "Connect failed!";
        exit(1);
    }
}

void MyTcpClient::connected()
{
    QTextStream request(m_socket);
    request << "GET / HTTP/1.1\r\nHost:" + m_hostname + "\r\n\r\n";
}

void MyTcpClient::readyRead()
{
    // HTTP-Antwort vom Server
    QTextStream answer(m_socket);
    qDebug() << answer.readAll();
    m_socket->disconnectFromHost();
    QCoreApplication::quit();
}
```
## 4. Mirror Server
Diese TCP-Server-Konsolenanwendung sendet die vom Client empfangene Zeichenkette in umgekehrter Reihenfolge zurück.  
_*.pro wie bei den Clientanwendungen_

### main.cpp
```cpp
#include <QCoreApplication>
#include "mirrorserver.h"

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    MirrorServer server;
    try {
        server.start();
        qDebug() << "server started";
    } catch(QString& str) {
        qDebug() << str;
        return 1;
    }
    return a.exec();
}
```

### mirrorserver.h
```cpp
#ifndef MIRRORSERVER_H
#define MIRRORSERVER_H

#include <QObject>
#include <QTcpServer>

class MirrorServer : public QObject
{
    Q_OBJECT
public:
    explicit MirrorServer(int port = 8000, QObject *parent = nullptr);
    void start();

signals:

public slots:
    void newConnection();
    void clientDisconnect();
    void serverRead();

private:
    QTcpServer* m_server;
    int m_port;
};

#endif // MIRRORSERVER_H
```
### mirrorserver.cpp
```cpp
#include "mirrorserver.h"
#include <QTcpSocket>
#include <QDebug>

MirrorServer::MirrorServer(int port, QObject *parent) : 
    QObject(parent),
    m_server(new QTcpServer(this)),
    m_port(port)
{
    connect(m_server, &QTcpServer::newConnection, this, &MirrorServer::newConnection);
    qDebug() << "Server started!";
}

void MirrorServer::start()
{
    if (!m_server->listen(QHostAddress::Any, m_port))
        throw m_server->errorString(); // happens when PORT already in use or privileged
}

// when connected from client
void MirrorServer::newConnection()
{
    QTcpSocket* clientsocket = m_server->nextPendingConnection(); 
    connect(clientsocket, &QTcpSocket::disconnected, this, &MirrorServer::clientDisconnect);
    connect(clientsocket, &QTcpSocket::readyRead, this, &MirrorServer::serverRead);
}

// connection is closed by client: cleanup
void MirrorServer::clientDisconnect()
{
    QTcpSocket* clientsocket = qobject_cast<QTcpSocket *>(sender());
    if (clientsocket)
        clientsocket->deleteLater(); 
}

void MirrorServer::serverRead()
{
    qDebug() << "serverRead";
    QTcpSocket* clientsocket = qobject_cast<QTcpSocket *>(sender());
    if (!clientsocket)
        return;

    QTextStream in(clientsocket);
    QString msg = in.readAll();
    qDebug() << msg;

    QTextStream out(clientsocket);
    std::reverse(msg.begin(), msg.end());
    out << msg << "\n";
    clientsocket->flush();
    
}
```

## Übungsbeispiel
Programmiere einen HTTP-Server unter Verwendung des oben angeführten Mirrorserver-Beispiels.   
Tipps: 
- Der Standard-Port für HTTP ist 80. Der Server muss dazu allerdings mit `sudo` gestartet werden (privilegierter Port). Alternativ kann auch ein höherer Port verwendet werden. Im Browser ist dann beispielsweise die URL `http://localhost:8000` einzugeben.
- Protokoll: Der Client sendet einen Request. Dieser kann vom Server gelesen werden und muss dann nicht weiter
  bearbeitet werden. Unmittelbar danach sendet der Server dem Client Header und Inhalt der Seite:  
```html
HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<html><body><h1>Hi BULME</h1><br></body></html>
```  
- Gemäß HTTP schließt der Server unmittelbar nach einer Client-Abfrage den Socket (im Gegensatz zum obigen Beispiel, wo der Client die Verbindung abbricht).






