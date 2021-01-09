# Studienartefakte

Dieses Repository entählt verschiedene Artefakte die während der Studie für die erstellung einer Architektur, für die Integraiton von Software in die Smart Home Umgebung, erstellt wurde. Die wichtigsten Artefakte sind dabei die Use Cases und die Java Version des Interface Prototypen, das Java Home MQTT Interface (JHMI)

## Java Home Mqtt Interface JHMI

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

Durch diesen, wie oben dargstellsten code wird ein MqttManager erstellt welcher Clients verwalten soll die sich zu dem Broker unter der Addresse tcp://127.0.0.1:1883 verbinden sollen. Für diesen Manager wird daraufhin ein client erstellt welcher zwei Nachrichten schickt. Zuerst wird die erste Nachricht an das Topic "testopic/test" geschickt und das ergebniss in result1 gespeichert, die Zweute Nachricht wird an das Selbe Topic gesendet und das ergebniss des Sendens der zweiten Nachricht wird in result 2 gespeichert. Nach dem Senden der beiden Nachrichten wird der erstellt Client geschlossen

Im Folgenden wird der Code Zeile für Zeile Beschrieben.


```Java
ConnectionOptions options = new ConnectionOptions();
 ```
In dieser Zeile wird ein neues ConnectionOptions Objekt erstellt. In diesem Objekt werden die verschiedenen Optionen gespeichert welche für die Verbindung zwischen dem Client und dem Broker verwendet werden können. Dazu gehört Beispielsweise ob sich der Client Automatisch wieder mit dem Broker verbinden soll, wenn die Verbindung abgebrochen ist. Weiter Informationen zu den optionen können in den Javadocs gefunden werden.

**Wichtig!** Über die ConnectionOptions werden auch der Nutzername und das Passwort für den Broker gesetzt zu dem sich der Client Verbinden soll.

```Java
MqttSenderManager sender = new MqttSenderManager("tcp://127.0.0.1:1883");
 ```
In dieser Zeile wird ein neuer MqttSenderManager erstellt. Jeder Client der durch diesen Manager erstellt wird, verbindet sich zu dem Broker der unter 127.0.0.1:1883
läuft.

```Java
String client = sender.addMqttClient(options);
 ```
In dieser Zeile wird ein neuer Client für den MqttSenderManager "sender" erstellt. Dieser Client verwendet die ConnectionOptions "options" für seine Verbindung zum Broker. Beim erstellen des client wird für den Client ein Name generiert welcher in der Variable client gespeichert wird. Dieser Name wird beim senden benötigt um ein Client zu spezifizieren welcher die Nachricht an das spezifiziert Topic sendet.

```Java
String result1 = sender.sendMessage("testopic/test", client, "Die erste Nachricht");
String result2 = sender.sendMessage("testopic/test", client, "Die zweite Nachricht");
 ```
Durch diese beiden Zeilen wird jeweils eine Nachricht gesendet. Die Nachricht "Die erste Nachricht" wird an das Topic "testopic/test". Das Ergebnis des sendens der Nachricht wird in resultq gespeichert. Das selbe gilt für die zweite Nachricht "Die zweite Nachricht".

```Java
sender.closeMqttClient(client);
 ```
Die letzte Zeile schliest den spezifizierten Client und macht ihn unverwendbar. Um neue Nachrichten zu schicken muss ein anderer Client verwendet werden.

**MqttReceiverManager**

Der ReceiverManager verhält sich ähnlich wie der SenderManager. Wie ein ReceiverManager erstellt und verwendet werden kann wird im folgden dargestellt.

```Java
ConnectionOptions options2 = new ConnectionOptions();
MqttReceiverManager receiver = new MqttReceiverManager("tcp://127.0.0.1:1883");
String receiverClient = receiver.newSubscriber("testopic/test", options2, new IMqttReceiver() {
    @Override
    public void messageReceived(String topic, String messageString, int messageId) {
        //Do Something here
    }
});
```

Wie auch beim Sender muss beim Receiver ein Client erstellt werden dem Optionen übergeben werden müssen, welche die verbindung mit dem Broker beschreiben. Anders als beim Senden, kann ein Client nur ein Topic Abbonieren. Wenn ein neues Topic Abboniert werden soll, muss also auch ein neuer Client über den Befehl newSubscriber erstellt werden. Eine Empfangene Nachricht wird über das IMqttReceiver Interface verarbeitet welches beim erstellen des Clients übergeben werden muss.

```Java
String receiverClient = receiver.newSubscriber("testopic/test", options2, new IMqttReceiver() {
    @Override
    public void messageReceived(String topic, String messageString, int messageId) {
        //Do Something here
    }
});
```

Dieses Interface stellt eine Funktion bereit welcher drei Parameter übergeben werden. Die messageId welche die Empfangene Nachricht Identifiziert, der messageString welcher die Empfangene Nachricht enthält und das Topic zu welchem die Nachricht gesendet wurde.

**DeviceRepresentationManager**

Anders als wie bei dem SenderManager und ReceiverManager verwaltet der DeviceRepresentationManager keine ClientInterfaces direkt. Über den DeviceRepresentationManager werden DeviceRepresentations verwaltet welche ClientInterfaces haben. Im folgenden wird beschreiben wie ein DeviceRepresentationManager erstellt wird.

```Java
DeviceRepresentationManager devMan = new DeviceRepresentationManager("tcp://127.0.0.1:1883");
devMan.addDeviceRepresentation("representedDeviceName", options, "testopic/testDevice");
```

Zuerst muss wie auch bei den anderen Managern der Manager selbst mit der Addresse des Brokers erstellt werden. Für die erstellung einer DeviceRepresentation muss die addDeviceRepresentation Funktion mit dem Namen der DeviceRepresentation (Am besten der Name des Representierten Gerätes), den Optionen für die Verbindung und dem Zieltopic, verwendet werden. Auf die erstellte DeviceRepresentation kann über die getDeviceRepresentation Funktion des DeviceRepresentationManagers Zugegriffen werden.

```Java
devMan.getDeviceRepresentation("representedDeviceName");
```
Eine Device Representation verfügt über Funktionen zum Senden und zum Empfangen von Nachrichten. Dafür können wie beim SenderManager Nachrichten gespeichert werden. Jede erstellte DeviceRepresentation Abonniert automatisch auf das übergebene Topic mit einer Wildcard. Dadurch werden alle Nachrichten die unter diesem Topic eigehen gelesen und in ein Array geschrieben. Dadurch enthält der Array den aktuellen Status des Gerätes das er Representiert. Die wichtigsten der verfügbaren Funktionen können wie folgt verwendet werden.

Ein weiteres Abbonent wird durch addCallback erstellt.
```Java
devMan.getDeviceRepresentation("representedDeviceName").addCallback(topic, IMqttReceiver());
```
Auf die aktuellen Daten des Gerätes wird mit getDeviceValue zugegriffen.
```Java
devMan.getDeviceRepresentation("representedDeviceName").getDeviceValue(topic);
```
Eine Nachricht kann mit registerMessage gespeichert werden.
```Java
devMan.getDeviceRepresentation("representedDeviceName").registerMessage(name, topic, value);
```

Wenn der verwendete Smart Home Hub von JHMI unterstütz wird gibt kann auch eine Spezialisierte DeviceRepresentation verwendet werden. Diese ermöglicht gegebenfalls das verwendet von Funktionen die nur von diesem Smart Home Hub angeboten werden. Durch das verwenden dieser spezialisierten DeviceRepresentations ist der aufwand beim wechseln des Smart Home Hubs allerdings größer da die DeviceRepresentations angepasst werden müssen wenn sie diese spezialisierten Funktionen verwenden.





