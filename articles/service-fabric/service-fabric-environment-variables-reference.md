---
title: Umgebungsvariablen für Azure Service Fabric | Microsoft-Dokumentation
description: Referenzdokumentation zu Service Fabric-Umgebungsvariablen
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: a9faefb43b9d5da81dddef8f326a3867b32842f7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-environment-variables"></a>Service Fabric-Umgebungsvariablen

Service Fabric verfügt über Umgebungsvariablen, die für jede Dienstinstanz festgelegt werden. Im Folgenden finden Sie die vollständige Liste der Umgebungsvariablen:

| Umgebungsvariable                         | BESCHREIBUNG                                                            | Beispiel                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Der Fabric-URI-Name der Anwendung                                 | fabric:/MeineAnwendung                                                |
| Fabric_CodePackageName                       | Name des Codepakets, zu dem der Prozess gehört              | Code                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*Name_Dienstendpunkt*     | IP-Adresse oder FQDN des Endpunkts                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*Name_Dienstendpunkt*              | Portnummer für den Endpunkt                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Protokollordner                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Ordner für temporäre Dateien                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Arbeitsordner                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12\\\\work     |
| Fabric_Folder_Application                    | Stammordner der Anwendung                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12             |
| Fabric_IsContainerHost                       | Boolescher Wert, der angibt, ob der Prozess ein Container ist                   | false                                                                |
| Fabric_NodeId                                | Knoten-ID des Knotens, auf dem der Prozess ausgeführt wird                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP-Adresse oder FQDN des Knotens wie in der Manifestdatei des Clusters | „localhost“ oder 10.0.0.1                                                |
| Fabric_NodeName                              | Name des Knotens, auf dem der Prozess ausgeführt wird                          | _Node_0                                                              |
| Fabric_ServiceName                           | Der Name des Diensts, wenn dieser im ExclusiveProcess-Modus gehostet wird. Dieser Variablenwert ist nur verfügbar, wenn Sie den Dienst mit „ServicePackageActivationMode ExclusiveProcess“ erstellen.  | MeinDienst                                               |
| Fabric_ServicePackageActivationId            | Aktivierungs-ID des Dienstpakets                                         | Eine GUID                                                               |
| Fabric_ServicePackageName                    | Name des Dienstpakets, zu dem der Prozess gehört                     | Web1Pkg                                                              |

Interne Umgebungsvariablen, die von der Service Fabric-Runtime verwendet werden:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName