---
title: Ändern der Größe eines virtuellen Linux-Computers über die Azure-Befehlszeilenschnittstelle 1.0 | Microsoft-Dokumentation
description: So skalieren Sie einen virtuellen Linux-Computer zentral hoch oder herunter, indem Sie die VM-Größe ändern.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4407a3662e6a50d650ab4ecc6dd1c2dad0c53bdb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Ändern der Größe einer Linux-VM mit Azure CLI 1.0

## <a name="overview"></a>Übersicht

Nachdem Sie einen virtuellen Computer (VM) bereitstellen, können Sie ihn zentral hoch- oder herunterskalieren, indem Sie die [VM-Größe][vm-sizes] ändern. In einigen Fällen müssen Sie zuerst die Zuordnung des virtuellen Computers aufheben. Dies ist möglicherweise der Fall, falls die neue Größe auf dem Hardwarecluster nicht verfügbar ist, auf dem die VM gehostet wird.

In diesem Artikel wird beschrieben, wie Sie die Größe eines virtuellen Linux-Computers mithilfe der [Azure-Befehlszeilenschnittstelle][azure-cli] ändern.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>CLI-Versionen zum Durchführen dieser Aufgabe
Führen Sie die Aufgabe mit einer der folgenden CLI-Versionen durch:

- [Azure-CLI 1.0](#resize-a-linux-vm): Unsere CLI für das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell (in diesem Artikel)
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): Unsere CLI der nächsten Generation für das Resource Manager-Bereitstellungsmodell


## <a name="resize-a-linux-vm"></a>Ändern der Größe eines virtuellen Linux-Computers
Führen Sie zum Ändern der Größe eines virtuellen Computers die folgenden Schritte aus.

1. Führen Sie den folgenden CLI-Befehl aus. Der Befehl listet die VM-Größen auf, die auf dem Hardwarecluster verfügbar sind, auf dem die VM gehostet wird.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Führen Sie den folgenden Befehl aus, um die Größe der VM zu ändern, sofern die gewünschte Größe aufgelistet ist.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    Der virtuelle Computer wird während dieses Vorgangs neu gestartet. Nach dem Neustart werden Ihr vorhandenes Betriebssystem und die Datenträger neu zugeordnet. Alle Daten auf dem temporären Datenträger gehen verloren.
   
    Verwenden Sie die Option `--enable-boot-diagnostics`, damit die [Startdiagnose][boot-diagnostics] alle Fehler protokollieren kann, die mit dem Startvorgang verbunden sind.
3. Führen Sie die folgenden Befehle aus, falls die gewünschte Größe nicht aufgelistet ist. Damit heben Sie die Zuordnung der VM auf, ändern ihre Größe und starten sie neu.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Die Aufhebung der Zuordnung der VM gibt auch jegliche dynamische IP-Adressen frei, die der VM zugewiesen sind. Das Betriebssystem und die Datenträger sind nicht betroffen.
   > 
   > 

## <a name="next-steps"></a>Nächste Schritte
Führen Sie mehrere VM-Instanzen aus, und skalieren Sie diese zentral hoch, um zusätzliche Skalierbarkeit zu erhalten. Weitere Informationen finden Sie unter [Automatisches Skalieren von Linux-Computern in einer VM-Skalierungsgruppe][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
