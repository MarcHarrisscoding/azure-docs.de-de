---
title: Laden von Daten in Azure Data Lake Store mit Azure Data Factory | Microsoft-Dokumentation
description: Kopieren von Daten in Azure Data Lake Store mithilfe von Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: bf0d607d63a68a222a1d44d9cb05253497d12591
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Laden von Daten in Azure Data Lake Store mit Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) ist ein unternehmensweites riesiges Repository für Big Data-Analyseworkloads. Mit Azure Data Lake können Sie Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit erfassen. Die Daten werden an einer einzelnen Stelle zur Durchführung operativer und explorativer Analysen erfasst.

Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie den Lake mit Daten aus dem vorhandenen System füllen und Zeit beim Erstellen von Analyselösungen sparen.

Azure Data Factory bietet die folgenden Vorteile beim Laden von Daten in Azure Data Lake Store:

* **Mühelose Einrichtung**: Intuitiver Assistent mit 5 Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Hohe Leistung**: Bis zu 1 GB/s Datenladegeschwindigkeit in Azure Data Lake Store. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum _Laden von Daten aus Amazon S3 in Azure Data Lake Store_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

> [!NOTE]
> Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Azure Data Lake Store mithilfe von Azure Data Factory](connector-azure-data-lake-store.md).
>
> Dieser Artikel bezieht sich auf Version 2 von Azure Data Factory, die sich derzeit in der Vorschauphase befindet. Wenn Sie Version 1 des Data Factory-Diensts verwenden, der allgemein verfügbar (Generally Available, GA) ist, finden Sie unter [Kopieraktivität in Version 1 von Azure Data Factory](v1/data-factory-data-movement-activities.md) weitere Informationen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Azure Data Lake Store: Wenn Sie über kein Data Lake Store-Konto verfügen, lesen Sie die Anweisungen unter [Erstellen eines Data Lake Store-Kontos](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: Dieser Artikel zeigt, wie Daten aus Amazon S3 kopiert werden. Sie können andere Datenspeicher verwenden, indem Sie ähnliche Schritte ausführen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Klicken Sie im linken Menü auf **Neu** > **Daten + Analysen** > **Data Factory**:
   
   ![Erstellen einer neuen Data Factory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an: 
      
   ![Seite „Neue Data Factory“](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadADLSDemo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Wählen Sie **V2 (Vorschau)** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. Diese Datenspeicher umfassen Azure Data Lake Store, Azure Storage, Azure SQL-Datenbank usw.

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   ![Data Factory-Startseite](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-data-lake-store"></a>Laden von Daten in Azure Data Lake Store

1. Wählen Sie auf der Seite **Erste Schritte** die Kachel **Daten kopieren** aus, um das Tool zum Kopieren von Daten zu starten: 

   ![Kachel für das Tool zum Kopieren von Daten](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** den Namen **CopyFromAmazonS3ToADLS** ein, und klicken Sie dann auf **Weiter**:

    ![Eigenschaftenseite](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Wählen Sie auf der Seite **Quelldatenspeicher** die Option **Amazon S3** aus, und klicken Sie dann auf **Weiter**:

    ![Seite „Quelldatenspeicher“](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Führen Sie auf der Seite **Amazon S3-Verbindung angeben** die folgenden Schritte aus: 
   1. Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
   2. Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
   3. Klicken Sie auf **Weiter**.
   
   ![Angeben des Amazon S3-Kontos](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Navigieren Sie auf der Seite **Eingabedatei oder -ordner auswählen** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner/die Datei aus, klicken Sie auf **Auswählen**, und klicken Sie dann auf **Weiter**:

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Wählen Sie auf der Seite **Zieldatenspeicher** die Option **Azure Data Lake Store** aus, und klicken Sie dann auf **Weiter**:

    ![Seite „Zieldatenspeicher“](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Wählen Sie das Kopierverhalten aus, indem Sie die Optionen **Dateien rekursiv kopieren** und **Binärkopie** (unverändertes Kopieren von Dateien) aktivieren. Klicken Sie auf **Weiter**:

    ![Angeben des Ausgabeordners](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Führen Sie auf der Seite **Data Lake Store-Verbindung angeben** die folgenden Schritte aus: 

   1. Wählen Sie Ihren Data Lake Store für den **Data Lake Store-Kontonamen** aus.
   2. Geben Sie die Dienstprinzipalinformationen an: **Mandant**, **Dienstprinzipal-ID** und **Dienstprinzipalschlüssel**.
   3. Klicken Sie auf **Weiter**.
   
   > [!IMPORTANT]
   > In dieser exemplarischen Vorgehensweise verwenden Sie einen _Dienstprinzipal_, um Data Lake Store zu authentifizieren. Achten Sie darauf, dass Sie dem Dienstprinzipal die entsprechenden Berechtigungen in Azure Data Lake Store erteilen. Befolgen Sie dazu [diese Anweisungen](connector-azure-data-lake-store.md#using-service-principal-authentication).
   
   ![Angeben des Azure Data Lake Store-Kontos](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Geben Sie auf der Seite **Ausgabedatei oder -ordner auswählen** die Zeichenfolge **copyfroms3** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**: 

    ![Angeben des Ausgabeordners](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**:

    ![Seite "Einstellungen"](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**:

    ![Seite „Zusammenfassung“](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen:

    ![Bereitstellungsseite](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. In der Spalte **Aktionen** werden Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt:

    ![Überwachen der Pipelineausführungen](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen**, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Klicken Sie oben auf den Link **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. 

    ![Überwachung der Aktivitätsausführungen](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität wählen Sie in der Aktivitätsüberwachungsansicht unter **Aktionen** den Link **Details** aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen:

    ![Überwachen der Details zur Aktivitätsausführung](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Stellen Sie sicher, dass die Daten in Ihren Azure Data Lake Store kopiert werden: 

    ![Überprüfen der Data Lake Store-Ausgabe](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den folgenden Artikel, um mehr über die Unterstützung von Azure Data Lake Store zu erfahren: 

> [!div class="nextstepaction"]
>[Azure Data Lake Store-Connector](connector-azure-data-lake-store.md)