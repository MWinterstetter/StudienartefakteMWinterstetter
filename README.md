# Java Home Mqtt Interface JHMI

Diese Biblitohek ist ein Java Interface für Smart Home durch Mqtt. Sie ist auf Paho als Mqtt Client und Homey als Hub zur Kontrolle des Smart Homes ausgelegt. Mögliche erweiterungen für weitere Clients oder Smart Home Hub Systeme sind aber mit in die Architektur einbezogen worden.

JHMI baut auf Mqtt auf und bietet keine Funktionen an welche nicht auch durch Beispielsweise den Paho client möglich währen. Das Ziel von JHMI ist es die Integration von Software in die Smart Home umgebung zu erleichtern und bietet dafür eine leicht zu verwendende Bibliothek an welche einige Quality of Live Funktionalitäten für die Kommunikation mit dem Smart Home Hub bietet. Sofern erweiterungen für weitere Clients und Hubs zur verfügung stehen ist durch diese Bibliothek auch der Austausche der Mqtt Clients und der Smart Home Hubs möglich ohne den Java Code anpassen zu müssen.

## Verwenden der Biblithek

JHMI verfügt über drei Kern Funktionalitäten die in diesem Abschnitt erläutert werden. Mit ihnen ist es möglich Nachrichten zu empfangen, zu senden und eine DeviceRepresentation zu erstellen welche den Zustand eines Gerätes im Smart Home darstellen soll.

**MqttSenderManager**

Die Standard Methode zum Senden von Nachrichten mit dem MqttSenderManager wird im folgenden Code Snippet dargstellt.

```Java
ConnectionOptions options = new ConnectionOptions();
MqttSenderManager sender = new MqttSenderManager("tcp://127.0.0.1:1883");
String client = sender.addMqttClient(options);
String result1 = sender.sendMessage("testopic/test", client, "Die erste Nachricht");

String result2 = sender.sendMessage("testopic/test", client, "Die zweite Nachricht");
    
sender.closeMqttClient(client);
```

Durch diesen, wie oben dargstellsten code wird ein MqttManager erstellt welcher Clients verwalten soll die sich zu dem Broker unter der Addresse tcp://127.0.0.1:1883 verbinden sollen. Für diesen Manager wird daraufhin ein client erstellt welcher zwei Nachrichten schickt. Zuerst wird die erste Nachricht geschickt und das ergebniss in result1 gespeichert, das ergebniss des Sendens der zweiten Nachricht wird in result 2 gespeichert. Nach dem Senden der beiden Nachrichten wird der erstellt Client geschlossen

Im Folgenden wird der Code Zeile für Zeile Beschrieben.


```Java
		ConnectionOptions options = new ConnectionOptions();
 ```
In dieser Zeile wird ein neues ConnectionOptions Objekt erstellt. In diesem Objekt werden die verschiedenen Optionen gespeichert welche für die Verbindung zwischen dem Client und dem Broker verwendet werden können. Dazu gehört Beispielsweise ob sich der Client Automatisch wieder mit dem Broker verbinden soll, wenn die Verbindung abgebrochen ist. 

**Wichtig!** Über die ConnectionOptions werden auch der Nutzername und das Passwort für den Broker gesetzt zu dem sich der Client Verbinden soll.























