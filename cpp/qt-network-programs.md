---
sort: 3
---
# Portscanner-Skelett
Das gezeigte Beispiel versucht sich mit einem Server nacheinander auf Port 1... 1024 zu verbinden. Ist `connect` erfolgreich, so ist der Port offen. Das Codebeispiel ist eine Minimalanwendung auf der Textkonsole, Aufruf (Beispiel): `./portscanner localhost`

### Projekt einrichten
QtCreator -> Neues Projekt -> Anwendung(Qt) -> Qt Konsolenanwendung

### portscanner<span>.pro (Auschnitt des Anfangs)
_kein GUI, aber core und network_

```
QT -= gui
QT += core network
```
### portscanner.h
```cpp
#include <QObject>

class Portscanner : public QObject
{
    Q_OBJECT
public:
    explicit Portscanner(int argc, char* argv[], QObject *parent = nullptr);

signals:
    void quit();
};
```
### portscanner.cpp
```c
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

# HTTP-Client
Das Programm arbeitet ebenfalls im Terminal und gibt die gesamte Antwort des (unverschlüsselten) HTTP-Servers aus. Qt-Creator-Setup und *.pro siehe oben.

### myhttpclient.h
```cpp
#include <QObject>
#include <QTcpSocket>
#include <QAbstractSocket>

class MyTcpClient : public QObject
{
    Q_OBJECT
public:
    explicit MyTcpClient(int argc, char* argv[], QObject *parent = nullptr);

signals:

public slots:
    void connected();
    void readyRead();

private:
    QTcpSocket *m_socket;
    QByteArray m_hostname;
};

### myhttpclient.cpp
```cpp
#include "mytcpclient.h"
#include <QDebug>

MyTcpClient::MyTcpClient(int argc, char* argv[], QObject *parent) : QObject(parent)
{
    m_hostname = argv[1];
    unsigned short port = 80;
    m_socket = new QTcpSocket(this);

    // Signal ->  Slot-Verbindung
    connect(m_socket, SIGNAL(connected()), this, SLOT(connected()));
    connect(m_socket, SIGNAL(readyRead()), this, SLOT(readyRead()));

    m_socket->connectToHost(m_hostname, port); // TCP Connect
    if (!m_socket->waitForConnected(5000))
        qDebug() << "Connect failed!";
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
}
```







