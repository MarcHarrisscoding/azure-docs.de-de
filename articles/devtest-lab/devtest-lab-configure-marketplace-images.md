---
title: Konfigurieren von Azure Marketplace-Image-Einstellungen in Azure DevTest Labs | Microsoft Docs
description: "Konfigurieren Sie, welche Azure Marketplace-Images beim Erstellen eines virtuellen Computers in Azure DevTest Labs verwendet werden können."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: v-craic
ms.openlocfilehash: 5f7c9be115b27d6033429c1224fa15fd7b844c1d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurieren von Azure Marketplace-Image-Einstellungen in Azure DevTest Labs
DevTest Labs unterstützt die Erstellung von virtuellen Computern (VMs, Virtual Machines) auf Basis von Azure Marketplace-Images, abhängig davon, wie Sie Azure Marketplace-Images zur Verwendung in Ihrem Lab konfiguriert haben. In diesem Artikel erfahren Sie, wie Sie ggf. angeben, welche Azure Marketplace-Images zum Erstellen virtueller Computer in einem Lab verwendet werden können. Dadurch wird sichergestellt, dass das Team nur Zugriff auf die Marketplace-Images hat, die es benötigt. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Auswählen, welche Azure Marketplace-Images beim Erstellen eines virtuellen Computers zulässig sind
1. Melden Sie sich auf dem [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus. 
4. Wählen Sie auf dem Blatt des Labs die Option **Konfiguration und Richtlinien** aus.
5. Wählen Sie auf dem Blatt **Konfiguration und Richtlinien** des Labs unter **Virtual Machine Bases** (VM-Basis) die Option **Marketplace-Images**.
6. Geben Sie an, ob alle qualifizierten Azure Marketplace-Images für die Verwendung als Basis einer neuen VM verfügbar sein sollen. Bei Auswahl von **Ja**sind alle Azure Marketplace-Images im Lab zulässig, die alle folgenden Kriterien erfüllen:
   
   * Das Image erstellt einen einzelnen virtuellen Computer, **und**
   * Das Image verwendet den Azure-Resource-Manager zur Bereitstellung von VMs, **und**
   * Das Image erfordert nicht den Erwerb eines zusätzlichen Lizenzplans.
     
    Wenn keine Images zulässig sein sollen, oder Sie angeben möchten, welche Bilder verwendet werden können, wählen Sie **Nein**.
     
     ![Option, die Verwendung aller Marketplace-Images als Basis-Images für virtuelle Computer zuzulassen](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Bei Auswahl von **Nein** im vorherigen Schritt ist das Kontrollkästchen **Allowed images/Select all** (Zulässige Images/Alle auswählen) aktiviert. 
   Sie können diese Option zusammen mit dem Suchfeld verwenden, um schnell alle in der Liste angezeigten Elemente auszuwählen oder ihre Auswahl aufzuheben.
   * Wählen Sie die Azure Marketplace-Images aus, die Sie für die Erstellung des virtuellen Computers zulassen möchten, indem Sie das Kontrollkästchen jedes entsprechenden Images aktivieren.
   * Wählen Sie nichts aus der Liste, wenn keine Azure Marketplace-Images im Lab verwendet werden sollen.
   
    ![Sie können angeben, welche Azure Marketplace-Images als Basis-Images für virtuelle Computer verwendet werden können.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie konfiguriert haben, auf welche Weise Azure Marketplace-Images beim Erstellen eines virtuellen Computers zulässig sind, ist der nächste Schritt das [Hinzufügen eines virtuellen Computers zu Ihrem Lab](devtest-lab-add-vm.md).

