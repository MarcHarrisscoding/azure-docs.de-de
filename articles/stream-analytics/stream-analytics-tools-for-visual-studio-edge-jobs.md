---
title: Edgeaufträge in Azure Stream Analytics-Tools für Visual Studio
description: In diesem Artikel wird beschrieben, wie Sie Ihre Stream Analytics-Edgeaufträge mit den Stream Analytics-Tools für Visual Studio verwenden.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/13/2018
ms.openlocfilehash: ec916ea2104df9d694aad2462df7ca5a487017f2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Entwickeln von Stream Analytics-Edgeaufträgen mit Visual Studio-Tools

In diesem Tutorial erfahren Sie, wie Sie Stream Analytics-Tools für Visual Studio zum Erstellen und Debuggen eigener Stream Analytics-Edge-Aufträge verwenden. Nachdem Sie den Auftrag erstellt und getestet haben, können Sie ihn im Azure-Portal auf Ihren Geräten bereitstellen. 

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

* [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) oder [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326) Die Editionen Enterprise (Ultimate/Premium), Professional und Community werden unterstützt. Die Express Edition wird nicht unterstützt.  

* Befolgen Sie die [Installationsanweisungen](stream-analytics-tools-for-visual-studio-edge-jobs.md) für die Stream Analytics-Tools für Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Erstellen eines Stream Analytics-Edge-Projekts 

Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**. Navigieren Sie auf der linken Seite zur Liste **Vorlagen**, und erweitern Sie **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge Application** (Azure Stream Analytics > Stream Analytics-Edge > Azure Stream Analytics-Edge-Anwendung). Geben Sie einen Namen, Speicherort und Projektmappennamen für Ihr Projekt ein, und klicken Sie auf **OK**.

![Neues Edge-Projekt](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Nachdem das Projekt erstellt wurde, wechseln Sie zum **Projektmappen-Explorer**, um die Ordnerhierarchie anzuzeigen.

![Ansicht im Projektmappen-Explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Auswählen des richtigen Abonnements

1. Klicken Sie in Visual Studio im Menü **Ansicht** auf **Server-Explorer**.  

2. Klicken Sie mit der rechten Maustaste auf **Azure**, wählen Sie **Verbindung mit Microsoft Azure-Abonnement herstellen** aus, und melden Sie sich dann mit Ihrem Azure-Konto an.

## <a name="define-inputs"></a>Definieren der Eingaben

1. Erweitern Sie im **Projektmappen-Explorer** den Knoten **Eingaben**. Sie sollten eine Eingabe mit dem Namen **EdgeInput.json** sehen. Doppelklicken Sie darauf, um die zugehörigen Einstellungen anzuzeigen.  

2. Stellen Sie sicher, dass „Quelltyp“ auf **Datenstrom**, „Quelle“ auf **Edge Hub**, „Ereignisserialisierungsformat“ auf **Json** und „Codierung“ auf **UTF8** festgelegt ist. Sie können den **Eingabealias** optional umbenennen, für dieses Beispiel ändern wir ihn jedoch nicht. Falls Sie den Eingabealias umbenennen, verwenden Sie den Namen, den Sie beim Definieren der Abfrage angegeben haben. Klicken Sie auf **Speichern**, um die Einstellungen zu speichern.  
   ![Eingabekonfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definieren der Ausgaben

1. Erweitern Sie im **Projektmappen-Explorer** den Knoten **Ausgaben**. Sie sollten eine Ausgabe mit dem Namen **EdgeOutput.json** sehen. Doppelklicken Sie darauf, um die zugehörigen Einstellungen anzuzeigen.  

2. Stellen Sie sicher, dass „Senke“ auf **Edge Hub**, „Ereignisserialisierungsformat“ auf **Json**, „Codierung“ auf **UTF8** und „Format“ auf **Array** festgelegt ist. Optional können Sie den **Ausgabealias** umbenennen, für dieses Beispiel ändern wir ihn jedoch nicht. Falls Sie den Ausgabealias umbenennen, verwenden Sie den Namen, den Sie beim Definieren der Abfrage angegeben haben. Klicken Sie auf **Speichern**, um die Einstellungen zu speichern. 
   ![Ausgabekonfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definieren der Transformationsabfrage

In den Edge-Umgebungen bereitgestellte Stream Analytics-Aufträge unterstützen den Großteil der [Stream Analytics-Abfragesprache](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Die folgenden Vorgänge werden gegenwärtig jedoch nicht für Edge-Aufträge unterstützt: 


|**Kategorie**  | **Befehl**  |
|---------|---------|
|Räumliche Operatoren |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Andere Operatoren | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Ausdrucksparameter im COUNT-Operator</li><li>Mikrosekunden in DATE- und TIME-Funktionen</li><li>JavaScript-UDA (dieses Feature ist noch immer als Vorschauversion für in der Cloud bereitgestellte Aufträge verfügbar)</li></ul>   |

Wenn Sie im Portal einen Edge-Auftrag erstellen und dabei einen nicht unterstützten Operator verwenden, warnt der Compiler Sie automatisch.

Definieren Sie in Visual Studio die folgende Transformationsabfrage im Abfrage-Editor (Datei **script.asaql**).

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Lokales Testen des Auftrags

Um die Abfrage lokal zu testen, sollten Sie die Beispieldaten hochladen. Sie können Beispieldaten abrufen, indem Sie Registrierungsdaten aus dem [GitHub-Repository](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) herunterladen und auf Ihrem lokalen Computer speichern. 

1. Klicken Sie zum Hochladen von Beispieldaten mit der rechten Maustaste auf die Datei **EdgeInput.json**, und wählen Sie **Lokale Eingabe hinzufügen** aus.  

2. Klicken Sie im Popupfenster auf **Durchsuchen**, wählen Sie die Beispieldaten am lokalen Pfad aus, und klicken Sie auf **Speichern**.
   ![Konfiguration der lokalen Eingabe](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Ihrem Ordner für Eingaben wird automatisch eine Datei namens **local_EdgeInput.json** hinzugefügt.  
4. Sie können die Abfrage lokal ausführen oder an Azure übermitteln. Klicken Sie zum Testen der Abfrage auf **Lokal ausführen**.  
   ![Ausführungsoptionen](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. Im Eingabeaufforderungsfenster wird der Status des Auftrags angezeigt. Bei erfolgreicher Ausführung des Auftrags wird ein Ordner wie „2018-02-23-11-31-42“ an Ihrem Projektordnerpfad „Visual Studio 2015\Projekte\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42“ erstellt. Navigieren Sie zum Ordnerpfad, um die Ergebnisse im lokalen Ordner anzuzeigen:

   Sie können sich auch beim Azure-Portal anmelden und überprüfen, ob der Auftrag erstellt wurde. 

   ![Ergebnisordner](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Übermitteln des Auftrags an Azure

1. Bevor Sie den Auftrag an Azure übermitteln, müssen Sie eine Verbindung mit Ihrem Azure-Abonnement herstellen. Öffnen Sie den **Server-Explorer**, klicken Sie mit der rechten Maustaste auf **Azure** > **Verbindung mit Microsoft Azure-Abonnement herstellen**, und melden Sie sich bei Ihrem Azure-Abonnement an.  

2. Um den Auftrag an Azure zu übermitteln, navigieren Sie zum Abfrage-Editor, und wählen Sie **Submit to Azure** (An Azure senden) aus.  

3. Es wird ein Popupfenster geöffnet, in dem Sie auswählen können, ob Sie einen vorhandenen Edge-Auftrag aktualisieren oder einen neuen Auftrag erstellen möchten. Wenn Sie einen vorhandenen Auftrag aktualisieren, wird dadurch die gesamte Auftragskonfiguration ersetzt. In diesem Szenario veröffentlichen Sie einen neuen Auftrag. Wählen Sie **Create a New Azure Stream Analytics Job** (Neuen Azure Stream Analytics-Auftrag erstellen) aus, geben Sie einen Namen für Ihren Auftrag ein (z. B. **MyASAEdgeJob**), wählen Sie das erforderliche **Abonnement**, die **Ressourcengruppe** und den **Speicherort** aus, und klicken Sie auf **Senden**.

   ![Übermitteln an Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Nachdem Ihr Stream Analytics-Edge-Auftrag erstellt wurde, können Sie ihn wie im [Tutorial zum Ausführen von Aufträgen unter IoT Edge](stream-analytics-edge.md) beschrieben auf Ihren Geräten bereitstellen. 

## <a name="manage-the-job"></a>Verwalten des Auftrags 

Sie können den Status des Auftrags und das Auftragsdiagramm im Server-Explorer anzeigen. Klicken Sie auf **Server-Explorer** > **Stream Analytics**, erweitern Sie das Abonnement und anschließend die Ressourcengruppe, in der Sie den Edge-Auftrag bereitgestellt haben. Nun können Sie den Auftrag „MyASAEdgejob“ mit dem Status **Erstellt** anzeigen. Erweitern Sie Ihren Auftragsknoten, und doppelklicken Sie darauf, um die Auftragsansicht zu öffnen.

![Optionen im Server-Explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Im Auftragsansichtsfenster können Sie den Auftrag aktualisieren oder löschen, über das Azure-Portal öffnen usw.

![Auftragsdiagramm und weitere Optionen](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Azure IoT Edge](../iot-edge/how-iot-edge-works.md)
* [Tutorial zu ASA unter IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Füllen Sie diese Umfrage aus, um Feedback an das Team zu senden](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
