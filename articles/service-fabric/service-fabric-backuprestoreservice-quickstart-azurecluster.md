---
title: Regelmäßiges Sichern und Wiederherstellen in Azure Service Fabric (Vorschau) | Microsoft-Dokumentation
description: Verwenden Sie das Feature für regelmäßige Sicherungen und Wiederherstellungen von Service Fabric, um Ihre Anwendungen vor Datenverlusten zu schützen.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: f2ab583ecc77122f2c76f905a1cd7b936278bd41
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Regelmäßiges Sichern und Wiederherstellen in Azure Service Fabric (Vorschau)
> [!div class="op_single_selector"]
> * [Cluster in Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Eigenständige Cluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric ist eine Plattform für verteilte Systeme, die das Entwickeln und Verwalten zuverlässiger, verteilter auf Microservices basierender Cloudanwendungen vereinfacht. Sie ermöglicht die Ausführung von zustandslosen und zustandsbehafteten Microservices. Zustandsbehaftete Dienste können einen änderbaren, autoritativen Zustand über die Anforderung und die Antwort oder eine vollständige Transaktion hinaus beibehalten. Wenn ein zustandsbehafteter Dienst für längere Zeit ausfällt oder Informationen aufgrund eines Notfalls verloren gehen, muss der Dienst möglicherweise mit einer aktuellen Sicherung des Zustands wiederhergestellt werden, damit er wieder verfügbar ist, nachdem er erneut gestartet wurde.

Service Fabric repliziert den Zustand über mehrere Knoten, um sicherzustellen, dass der Dienst hoch verfügbar ist. Auch wenn ein Knoten im Cluster ausfällt, bleibt der Dienst verfügbar. In bestimmten Fällen ist es jedoch noch immer wünschenswert, dass die Dienstdaten auch bei größeren Ausfällen zuverlässig bleiben.
 
Ein Dienst könnte Daten beispielsweise zum Schutz vor folgenden Szenarien sichern:
- Im Fall des dauerhaften Verlusts eines gesamten Service Fabric-Clusters
- Dauerhafter Verlust eines Großteils der Replikate einer Dienstpartition
- Administrative Fehler, durch die der Zustand versehentlich gelöscht oder beschädigt wird. Ein Administrator mit ausreichenden Berechtigungen löscht z.B. versehentlich den Dienst.
- Fehler im Dienst, die zu einer Beschädigung von Daten führen. Dies kann beispielsweise bei einem Dienstcode-Upgrade geschehen, bei dem fehlerhafte Daten in eine Reliable Collection geschrieben werden. In diesem Fall müssen unter Umständen der Code und die Daten in einen früheren Zustand zurückversetzt werden.
- Offline-Datenverarbeitung. Es kann zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, offline zu verarbeiten.

Service Fabric bietet eine integrierte API zum [Sichern und Wiederherstellen](service-fabric-reliable-services-backup-restore.md) des Diensts zu einem bestimmten Zeitpunkt. Anwendungsentwickler können diese APIs verwenden, um den Zustand des Diensts in regelmäßigen Abständen zu sichern. Wenn Dienstadministratoren eine Sicherung zu einem bestimmten Zeitpunkt von außerhalb des Diensts auslösen möchten, wie z.B. vor dem Upgrade der Anwendung, müssen Entwickler zudem das Sichern (und Wiederherstellen) als API über den Dienst verfügbar machen. Für das Verwalten von Sicherungen fallen zusätzliche Kosten an. Beispiel: Sie möchten jede halbe Stunde fünf inkrementelle Sicherungen und dann eine vollständige Sicherung erstellen. Nach der vollständigen Sicherung können Sie die vorherigen inkrementellen Sicherungen löschen. Dieser Ansatz erfordert zusätzlichen Code, der zu zusätzlichen Kosten während der Anwendungsentwicklung führt.

Die Sicherung der Anwendungsdaten in regelmäßigen Abständen ist eine grundlegende Notwendigkeit beim Verwalten einer verteilten Anwendung und zum Schutz vor Datenverlusten oder einer längeren Beeinträchtigung der Verfügbarkeit des Diensts. Service Fabric bietet einen optionalen Dienst für Sicherungen und Wiederherstellungen, mit dem Sie die regelmäßige Sicherung der statusbehafteten zuverlässigen Dienste (einschließlich der Actordienste) konfigurieren können, ohne zusätzlichen Code schreiben zu müssen. Damit wird auch das Wiederherstellen der zuvor erstellten Sicherungen vereinfacht. 

> [!NOTE]
> Das Feature für regelmäßige Sicherungen und Wiederherstellungen ist gegenwärtig in der **Vorschauversion** und wird für produktive Workloads nicht unterstützt. 
>

Service Fabric stellt einen Satz von APIs für die folgende Funktionalität im Zusammenhang mit dem Feature für regelmäßige Sicherungen und Wiederherstellungen bereit:

- Planen regelmäßiger Sicherungen der statusbehafteten zuverlässigen Dienste und der Reliable Actors mit Unterstützung zum Hochladen von Sicherungen in (externe) Speicherorte. Unterstützte Speicherorte
    - Azure Storage
    - Dateifreigabe (lokal)
- Auflisten von Sicherungen
- Auslösen einer Ad-hoc-Sicherung einer Partition
- Wiederherstellen einer Partition mithilfe der vorherigen Sicherung
- Zeitweiliges Aussetzen von Sicherungen
- Verwalten der Aufbewahrung von Sicherungen (demnächst)

## <a name="prerequisites"></a>Voraussetzungen
* Service Fabric-Cluster mit Fabric-Version 6.2 und höher. Der Cluster muss mit Windows Server eingerichtet werden. Schritte zum Erstellen eines Service Fabric-Clusters mit der Azure-Ressourcevorlage finden Sie in diesem [Artikel](service-fabric-cluster-creation-via-arm.md).
* X.509-Zertifikat für die Verschlüsselung der Geheimnisse, die für die Verbindung mit dem Speicher zum Speichern von Sicherungen benötigt werden. Informationen zum Abrufen oder Erstellen eines X.509-Zertifikats finden Sie in diesem [Artikel](service-fabric-cluster-creation-via-arm.md).
* Service Fabric-Anwendung für statusbehaftete zuverlässige Dienste, die mit dem Service Fabric SDK, Version 3.0 oder höher, erstellt wurde. Für Anwendungen für .NET Core 2.0 muss die Anwendung mit dem Service Fabric SDK, Version 3.1 oder höher, erstellt werden.
* Erstellen Sie ein Azure Storage-Konto zum Speichern von Anwendungssicherungen.

## <a name="enabling-backup-and-restore-service"></a>Aktivieren des Diensts für Sicherungen und Wiederherstellungen
Zuerst müssen Sie den _Dienst für Sicherungen und Wiederherstellungen_ in Ihrem Cluster aktivieren. Rufen Sie die Vorlage für den Cluster ab, den Sie bereitstellen möchten. Sie können entweder die [Beispielvorlagen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) verwenden oder eine Resource Manager-Vorlage erstellen. Aktivieren Sie den _Dienst für Sicherungen und Wiederherstellungen_ mit den folgenden Schritten:

1. Überprüfen Sie, ob `apiversion` für die `Microsoft.ServiceFabric/clusters`-Ressource auf **`2018-02-01`** festgelegt ist. Wenn nicht, aktualisieren Sie sie wie im folgenden Codeausschnitt:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Jetzt aktivieren Sie den _Dienst für Sicherungen und Wiederherstellungen_ wie im folgenden Codeausschnitt, indem Sie den folgenden `addonFeatures`-Abschnitt unter dem Abschnitt `properties` hinzufügen: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurieren Sie das X.509-Zertifikat für die Verschlüsselung der Anmeldeinformationen. Dies ist wichtig, um sicherzustellen, dass die Anmeldeinformationen, die für die Verbindung mit dem Speicher bereitgestellt wurden, vor dem Speichern verschlüsselt werden. Konfigurieren Sie das Verschlüsselungszertifikat wie im folgenden Codeausschnitt, indem Sie den folgenden `BackupRestoreService`-Abschnitt unter dem Abschnitt `fabricSettings` hinzufügen: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Nachdem Sie die Clustervorlage mit den vorhergehenden Änderungen aktualisiert haben, wenden Sie die Änderungen an und schließen die Bereitstellung/das Upgrade ab. Anschließend wird der _Dienst für Sicherungen und Wiederherstellungen_ im Cluster gestartet. Der URI für diesen Dienst lautet `fabric:/System/BackupRestoreService`, und Sie finden den Dienst im Abschnitt mit Systemdiensten im Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors
Jetzt erläutern wir schrittweise das Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors. Diese Schritte setzen Folgendes voraus:
- Der Cluster wurde unter Verwendung von X.509-Sicherheit mit dem _Dienst für Sicherungen und Wiederherstellungen_ eingerichtet.
- Ein zuverlässiger zustandsbehafteter Dienst wurde im Cluster bereitgestellt. Für diese Schnellstartanleitung lautet der Anwendungs-URI `fabric:/SampleApp`, und der URI für den zuverlässigen zustandsbehafteten Dienst, der zu dieser Anwendung gehört, lautet `fabric:/SampleApp/MyStatefulService`. Dieser Dienst wird mit einer einzelnen Partition bereitgestellt, und die Partitions-ID ist `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Das Clientzertifikat mit der Administratorrolle wird im Speichernamen _My_ (_Personal_) im Speicherort des _CurrentUser_-Zertifikatspeichers auf dem Computer installiert, auf dem die Skripts aufgerufen werden. In diesem Beispiel wird `1b7ebe2174649c45474a4819dafae956712c31d3` als Fingerabdruck dieses Zertifikats verwendet. Weitere Informationen zu Clientzertifikaten finden Sie unter [Rollenbasierte Zugriffssteuerung für Service Fabric-Clients](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Erstellen der Sicherungsrichtlinie

Der erste Schritt ist das Erstellen der Sicherungsrichtlinie, die den Sicherungszeitplan, den Zielspeicher für Sicherungsdaten, den Richtliniennamen und die maximal zulässige Anzahl inkrementeller Sicherungen vor dem Auslösen einer vollständigen Sicherung beschreibt. 

Verwenden Sie für den Sicherungsspeicher das oben erstellte Azure Storage-Konto. In diesem Beispiel wird ein Azure Storage-Konto mit dem Namen `sfbackupstore` verwendet. Der Container `backup-container` ist zum Speichern von Sicherungen konfiguriert. Ein Container mit diesem Namen wird beim Hochladen von Sicherungen erstellt, wenn er nicht bereits vorhanden ist. Geben Sie für `ConnectionString` die gültige Verbindungszeichenfolge für das Azure Storage-Konto an.

Führen Sie das folgende PowerShell-Skript zum Aufrufen der erforderlichen REST-API aus, um die neue Richtlinie zu erstellen.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=sfbackupstore;AccountKey=64S+3ykBgOuKhd2DK1qHJJtDml3NtRzgaZUa+8iwwBAH4EzuGt95JmOm7mp/HOe8V3l645iv5l8oBfnhhc7dJA==;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Aktivieren der regelmäßigen Sicherung
Nach dem Definieren der Sicherungsrichtlinie zum Erfüllen der Datenschutzanforderungen der Anwendung muss die Sicherungsrichtlinie mit der Anwendung verknüpft werden. Je nach Anforderungen kann die Sicherungsrichtlinie einer Anwendung, einem Dienst oder einer Partition zugeordnet werden.

Führen Sie das folgende PowerShell-Skript zum Aufrufen der erforderlichen REST-API aus, um die Sicherungsrichtlinie mit dem Namen `BackupPolicy1`, die im obigen Schritt erstellt wurde, der Anwendung `SampleApp` zuzuordnen.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Sicherstellen, dass die regelmäßigen Sicherungen funktionieren

Nach der Aktivierung der Sicherung auf Anwendungsebene werden alle Partitionen, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors unter der Anwendung gehören, in regelmäßigen Abständen gemäß der zugeordneten Sicherungsrichtlinie gesichert. 

![Integritätsereignis der gesicherten Partition][0]

### <a name="list-backups"></a>Auflisten von Sicherungen

Sicherungen, die mit allen Partitionen verknüpft sind, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors der Anwendung gehören, können mithilfe der _GetBackups_-API aufgelistet werden. Sicherungen können für eine Anwendung, einen Dienst oder eine Partition aufgelistet werden.

Führen Sie das folgende PowerShell-Skript zum Aufrufen der HTTP-API aus, um die für alle Partitionen in der Anwendung `SampleApp` erstellten Sicherungen aufzulisten.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Beispielausgabe für die oben genannte Ausführung:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 

BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 

BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>Einschränkungen der Vorschauversion
- Keine in Service Fabric integrierten PowerShell-Cmdlets.
- Keine Unterstützung für die Service Fabric-CLI.
- Keine Unterstützung für das automatisierte Löschen von Sicherungen. Erfordert die manuelle Bereinigung von Sicherungen.
- Keine Unterstützung für Service Fabric-Cluster unter Linux.

## <a name="next-steps"></a>Nächste Schritte
- [REST-API-Referenz zu Sicherung/Wiederherstellung](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

