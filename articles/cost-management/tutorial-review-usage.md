---
title: 'Tutorial: Überprüfen von Nutzung und Kosten in Azure Cost Management | Microsoft-Dokumentation'
description: In diesem Tutorial überprüfen Sie die Nutzung und die Kosten, um Trends nachzuverfolgen, Ineffizienz zu erkennen und Warnungen zu erstellen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 820fea1aa2eb93fb383dca4def9ed607515c29b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Tutorial: Überprüfen der Nutzung und der Kosten

Azure Cost Management zeigt die Nutzung und Kosten auf, sodass Sie Trends nachverfolgen, Ineffizienzen erkennen und Warnungen erstellen können. Alle Nutzungs- und Kostendaten werden in Cloudyn-Dashboards und -Berichten angezeigt. In den Beispielen dieses Tutorials erhalten Sie schrittweise Anleitungen zum Überprüfen von Nutzung und Kosten anhand von Dashboards und Berichten. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Nachverfolgen der Nutzung und der Kosten
> * Erkennen von Ineffizienz bei der Nutzung
> * Erstellen von Warnungen für ungewöhnliche oder zu hohe Ausgaben

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Abonnement.
- Sie müssen entweder über eine Registrierung für die Testversion oder ein kostenpflichtiges Abonnement für Azure Cost Management verfügen.

## <a name="open-the-cloudyn-portal"></a>Öffnen des Cloudyn-Portals

Die Nutzung und die Kosten werden generell im Cloudyn-Portal überprüft. Öffnen Sie das Cloudyn-Portal aus dem Azure-Portal, oder navigieren Sie zu https://azure.cloudyn.com, und melden Sie sich dort an.

## <a name="track-usage-and-cost-trends"></a>Nachverfolgen der Nutzung und der Kosten

Sie verfolgen die tatsächlich für die Nutzung getätigten Ausgaben und die Kosten mit Berichten über den Lauf der Zeit, um Trends zu erkennen. Betrachten Sie Trends ausgehend vom Bericht über die tatsächlichen Kosten im Zeitverlauf. Klicken Sie im Menü oben im Portal auf **Cost** (Kosten) > **Cost Analysis** (Kostenanalyse) > **Actual Cost Over Time** (Tatsächliche Kosten im Zeitverlauf). Beim erstmaligen Öffnen des Berichts sind keine Gruppen oder Filter darauf angewendet.

Dies ist ein Beispiel für einen Bericht:

![Beispielbericht](./media/tutorial-review-usage/actual-cost01.png)

Im Bericht werden alle Ausgaben in den letzten 30 Tagen aufgeführt. Wenn nur die Ausgaben für Azure-Dienste angezeigt werden sollen, wenden Sie die Gruppe „Service“ (Dienst) an, und filtern Sie nach allen Azure-Diensten. Die folgende Abbildung stellt gefilterte Dienste dar.

![Gefilterte Dienste](./media/tutorial-review-usage/actual-cost02.png)

Im vorherigen Beispiel fielen ab dem 31.08.2017 geringere Ausgaben als davor an. Dieser Kostentrend setzt sich für die verschiedenen Dienste über etwa neun Tage fort. Anschließend fallen wie zuvor zusätzliche Ausgaben an. Eine zu große Anzahl von Spalten kann jedoch einen offensichtlichen Trend verdecken. Sie können die Berichtsanzeige in ein Linien- oder Flächendiagramm ändern, um die Daten in anderen Darstellungen zu untersuchen. In der folgenden Abbildung ist der Trend deutlicher erkennbar.

![Trend im Bericht](./media/tutorial-review-usage/actual-cost03.png)

Im Beispiel wird klar ersichtlich, dass die Kosten für Azure Storage ab dem 31.08.2017 abfielen, während die Ausgaben für andere Azure-Dienste unverändert blieben. Wodurch wurde diese Kostensenkung verursacht? In diesem Beispiel waren einige Mitarbeiter im Urlaub und somit nicht am Arbeitsplatz, sodass sie den Storage-Dienst nicht nutzten.

Ein Videotutorial zur Nachverfolgung von Nutzungs- und Kostentrends finden Sie unter [Analyzing your cloud billing data vs. time with Azure Cost Management](https://youtu.be/7LsVPHglM0g) (Analysieren Ihrer Cloudabrechnungsdaten im Vergleich zur Abrechnung anhand von Zeitintervallen mit Azure Cost Management).

## <a name="detect-usage-inefficiencies"></a>Erkennen von Ineffizienz bei der Nutzung

Optimizer-Berichte ermöglichen es, die Effizienz zu steigern, die Nutzung zu optimieren und Möglichkeiten zu ermitteln, Kosten für Ihre Cloudressourcen einzusparen. Sie sind insbesondere hilfreich durch Empfehlungen zur Kosteneffizienz, anhand derer VMs im Leerlauf oder kostenintensive VMs reduziert werden können.

Ein häufiges Problem, das in Organisationen auftritt, wenn sie erstmals Ressourcen in die Cloud verschieben, besteht in ihrer Virtualisierungsstrategie. Häufig wird ein Ansatz verfolgt, der auch beim Erstellen von virtuellen Computern für die lokale Virtualisierungsumgebung angewendet wird. Zudem wird davon ausgegangen, dass Kosten allein durch das Verschieben der lokalen VMs in die Cloud gesenkt werden, ohne dass diese dazu geändert werden. Ein solcher Ansatz trägt jedoch wahrscheinlich nicht zur Kostensenkung bei.

Das Problem liegt darin, dass die vorhandene Infrastruktur bereits bezahlt wurde. Benutzer können große VMs erstellen und betreiben. Ob sich diese im Leerlauf befinden oder nicht, hat nur geringfügige Konsequenzen. Das Verschieben großer oder im Leerlauf befindlicher VMs in die Cloud zieht mit großer Wahrscheinlichkeit einen *Anstieg* der Kosten nach sich. Die Kostenzuordnung für Ressourcen ist unverzichtbar, wenn Sie Verträge mit Anbietern von Clouddiensten abschließen. Sie haben für Ihre vertraglichen Pflichten zu zahlen, egal, ob Sie die Ressource vollständig nutzen oder nicht.

Im Bericht mit Empfehlungen zu kostengünstigen Größenanpassungen werden potenzielle jährliche Einsparungen ausgewiesen. Dabei wird die Kapazität des jeweiligen VM-Instanztyps mit den historischen Daten zu CPU- und Speichernutzung verglichen.  

Klicken Sie im Menü oben im Portal auf **Optimizer** (Optimierung) > **Sizing Optimization** (Größenoptimierung) > **Cost Effective Sizing Recommendations** (Empfehlungen zu kostengünstigen Größenanpassungen). Geben Sie im Filter Azure als Anbieter an, um ausschließlich Azure-VMs zu betrachten. In der folgenden Abbildung finden Sie ein Beispiel.

![Virtuelle Azure-Computer](./media/tutorial-review-usage/sizing01.png)

In diesem Beispiel können 3.114 USD eingespart werden, wenn die Empfehlungen hinsichtlich der Änderung der VM-Instanztypen befolgt werden. Klicken Sie auf das Pluszeichen (+) unter **Details**, um die erste Empfehlung aufzurufen. Dies sind die Details der Empfehlung.

![Empfehlungsdetails](./media/tutorial-review-usage/sizing02.png)

Zeigen Sie die IDs der VM-Instanz an, indem Sie auf das Pluszeichen neben **List of Candidates** (Liste der Kandidaten) klicken.

![Liste der Kandidaten](./media/tutorial-review-usage/sizing03.png)

Ein Videotutorial zum Ermitteln von Ineffizienzen bei der Nutzung finden Sie unter [Optimizing VM Size in Azure Cost Management](https://youtu.be/1xaZBNmV704) (Optimieren der VM-Größe in Azure Cost Management).

## <a name="create-alerts-for-unusual-spending"></a>Erstellen von Warnungen für ungewöhnliche Ausgaben

Sie können Beteiligte automatisch über Anomalien bei den Ausgaben und Risiken der Budgetüberschreitung benachrichtigen. Sie können schnell und einfach Warnungen auf der Grundlage von Berichten erstellen, die Warnungen anhand von Budget- und Kostenschwellenwerten ausgeben.

Für Ausgaben erstellen Sie eine Warnung anhand eines Kostenberichts. In diesem Beispiel wird mithilfe des Berichts über die tatsächlichen Kosten im Zeitverlauf eine Benachrichtigung an Sie ausgegeben, wenn sich die Azure VM-Ausgaben Ihrem Gesamtbudget nähern. Zum Erstellen der Warnung sind folgende Schritte erforderlich: Klicken Sie im Menü oben im Portal auf **Cost** (Kosten) > **Cost Analysis** (Kostenanalyse) > **Actual Cost Over Time** (Tatsächliche Kosten im Zeitverlauf). Legen Sie **Groups** (Gruppen) auf **Service** (Dienst) und **Filter on the service** (Filtern nach Dienst) auf **Azure/VM** fest. Klicken Sie in der oberen rechten Ecke des Berichts auf **Actions** (Aktionen), und wählen Sie anschließend **Schedule report** (Bericht planen) aus.

Verwenden Sie die Registerkarte **Scheduling** (Planung), um sich den Bericht in den gewünschten Intervallen per E-Mail zusenden zu lassen. Wählen Sie unbedingt **Send via email** (Per E-Mail senden). Sämtliche verwendeten Tags, Gruppierungen und Filter sind in dem per E-Mail versendeten Bericht enthalten. Klicken Sie auf die Registerkarte **Threshold** (Schwellenwert), und wählen Sie **Actual Cost vs. Threshold** (Tatsächliche Kosten im Vergleich zu Schwellenwert) aus. Wenn Sie über ein Gesamtbudget von 500.000 USD verfügen und eine Benachrichtigung wünschen, wenn sich die Kosten der Hälfte des Budgetbetrags annähern, erstellen Sie eine **rote Warnung** bei 250.000 USD und eine **gelbe Warnung** bei 240.000 USD. Verwenden Sie keine Kommas in eingegebenen Werten. Wählen Sie anschließend die Anzahl der aufeinanderfolgenden Warnungen aus. Wenn Sie die festgelegte Gesamtzahl von Warnungen erhalten haben, werden keine weiteren Warnungen mehr versendet. Speichern Sie den geplanten Bericht.

![Beispielbericht](./media/tutorial-review-usage/schedule-alert01.png)

Sie können auch die Schwellenwertmetrik „Cost Percentage vs. Budget“ (Prozentuale Kosten im Vergleich zu Budget) verwenden, um Warnungen zu erstellen. Mit dieser Metrik können Sie Budgetprozentsätze anstelle von Geldbeträgen verwenden.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Nachverfolgen der Nutzung und der Kosten
> * Erkennen von Ineffizienz bei der Nutzung
> * Erstellen von Warnungen für ungewöhnliche oder zu hohe Ausgaben


Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie anhand von Verlaufsdaten Ausgaben vorhersagen.

> [!div class="nextstepaction"]
> [Vorhersage zukünftiger Ausgaben](tutorial-forecast-spending.md)
