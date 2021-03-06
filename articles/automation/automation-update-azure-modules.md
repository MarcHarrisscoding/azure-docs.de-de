---
title: Aktualisieren von Azure-Modulen in Azure Automation
description: In diesem Artikel wird beschrieben, wie Sie jetzt häufig verwendete Azure PowerShell-Module, die standardmäßig in Azure Automation bereitgestellt werden, aktualisieren können.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f1f7068de1781d1cc66a412752f6fd99d603a6be
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Aktualisieren von Azure PowerShell-Modulen in Azure Automation

Die am häufigsten verwendeten Azure PowerShell-Module werden standardmäßig in jedem Automation-Konto bereitgestellt. Das Azure-Team aktualisiert die Azure-Module regelmäßig. Über das Automation-Konto erhalten Sie die Möglichkeit, die Module im Konto zu aktualisieren, sobald neue Versionen im Portal verfügbar sind.  

Da Module von der Produktgruppe regelmäßig aktualisiert werden, können Änderungen bei den enthaltenen Cmdlets auftreten. Je nachdem, welche Änderung ausgeführt wurde, z.B. ob nein Parameter umbenannt oder ein Cmdlet vollständig eingestellt wurde, können negative Auswirkungen auf Ihre Runbooks auftreten. Um Auswirkungen auf Ihre Runbooks und die Prozesse zu vermeiden, die sie automatisieren, wird empfohlen, vor dem Fortsetzen des Vorgangs Tests und Überprüfungen auszuführen. Wenn Sie für diesen Zweck kein dediziertes Automation-Konto haben, sollten Sie eines erstellen. So können Sie während der Entwicklung Ihrer Runbooks viele verschiedene Szenarios und Permutationen testen und iterative Änderungen wie das Aktualisieren der PowerShell-Module ausführen. Nachdem die Ergebnisse überprüft werden und Sie alle erforderlichen Änderungen angewendet haben, koordinieren Sie die Migration aller Runbooks, für die Änderungen erforderlich waren, und führen Sie das folgende Update wie beschrieben in der Produktion aus.

## <a name="updating-azure-modules"></a>Aktualisieren von Azure-Modulen

1. Auf der Seite „Module“ Ihres Automation-Kontos gibt es eine Option namens **Azure-Module aktualisieren**. Sie ist immer aktiviert.<br><br> ![Option zum Aktualisieren von Azure-Modulen auf der Seite „Module“](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Klicken Sie auf **Azure-Module aktualisieren**. Daraufhin wird eine Bestätigungsbenachrichtigung angezeigt, in der Sie gefragt werden, ob Sie den Vorgang fortsetzen möchten.<br><br> ![Benachrichtigung zum Aktualisieren von Azure-Modulen](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klicken Sie auf **Ja**, um den Aktualisierungsvorgang für die Module zu starten. Der Aktualisierungsvorgang dauert etwa 15 bis 20 Minuten, und die folgenden Module werden aktualisiert:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Wenn die Module bereits auf dem neuesten Stand sind, ist der Prozess in wenigen Sekunden abgeschlossen. Wenn der Updatevorgang abgeschlossen ist, werden Sie benachrichtigt.<br><br> ![Aktualisierungsstatus beim Aktualisieren der Azure-Module](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> In Azure Automation werden die neuesten Module in Ihrem Automation-Konto verwendet, wenn ein neuer geplanter Auftrag ausgeführt wird.    

Wenn Sie Cmdlets aus diesen Azure PowerShell-Modulen in Ihren Runbooks verwenden, dann sollten Sie diesen Updatevorgang ungefähr jeden Monat ausführen, um sicherzustellen, dass Sie über die neuesten Module verfügen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Integrationsmodulen und zum Erstellen benutzerdefinierter Module, um Automation weiter in andere Systeme, Dienste oder Lösungen zu integrieren, finden Sie unter [Integrationsmodule](automation-integration-modules.md).

* Sie sollten die Verwendung der Integration von Quellcodeverwaltung mit [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) oder [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) in Betracht ziehen, um Releases Ihrer Automation-Runbooks und Konfigurationsportfolios zentral zu verwalten und zu steuern.  