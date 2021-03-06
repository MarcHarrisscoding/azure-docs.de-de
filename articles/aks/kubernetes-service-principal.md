---
title: Dienstprinzipal für Azure Kubernetes-Cluster
description: Erstellen und Verwalten eines Azure Active Directory-Dienstprinzipals für einen Kubernetes-Cluster in AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 81f455668e81c2a6c21b66d85199da3f475e7265
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2018
---
# <a name="service-principals-with-azure-container-service-aks"></a>Dienstprinzipale mit Azure Container Service (AKS)

Für die Interaktion mit Azure-APIs benötigt ein AKS-Cluster einen [Azure Active Directory-Dienstprinzipal][aad-service-principal]. Der Dienstprinzipal wird für die dynamische Erstellung und Verwaltung von Ressourcen wie [Azure Load Balancer][azure-load-balancer-overview] benötigt.

Dieser Artikel informiert über verschiedene Optionen zum Einrichten eines Dienstprinzipals für Ihren Kubernetes-Cluster in AKS.

## <a name="before-you-begin"></a>Voraussetzungen


Für die Erstellung eines Azure AD-Dienstprinzipals müssen Sie dazu berechtigt sein, eine Anwendung bei Ihrem Azure AD-Mandanten zu registrieren und die Anwendung einer Rolle in Ihrem Abonnement zuzuweisen. Sollten Sie nicht über die erforderlichen Berechtigungen verfügen, können Sie ggf. Ihren Azure AD- oder Abonnementadministrator bitten, Ihnen die erforderlichen Berechtigungen zuzuweisen oder vorab einen Dienstprinzipal für den Kubernetes-Cluster zu erstellen.

Außerdem muss mindestens Version 2.0.27 oder höher der Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][install-azure-cli] Informationen dazu.

## <a name="create-sp-with-aks-cluster"></a>Erstellen eines Dienstprinzipals mit dem AKS-Cluster

Wenn Sie einen AKS-Cluster mit dem Befehl `az aks create` bereitstellen, haben Sie die Möglichkeit, automatisch einen Dienstprinzipal zu generieren.

Im folgenden Beispiel wird ein AKS-Cluster erstellt, und da kein vorhandener Dienstprinzipal angegeben ist, wird ein Dienstprinzipal für den Cluster erstellt. Für diesen Vorgang muss Ihr Konto über die erforderlichen Rechte zum Erstellen eines Dienstprinzipals verfügen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Verwenden eines vorhandenen Dienstprinzipals

Ein vorhandener Azure AD-Dienstprinzipal kann verwendet oder vorab für die Verwendung mit einem AKS-Cluster erstellt werden. Dies ist hilfreich, wenn Sie einen Cluster über das Azure-Portal bereitstellen, in dem Sie die Dienstprinzipalinformationen angeben müssen. Bei Verwendung eines vorhandenen Dienstprinzipals muss das Clientgeheimnis als Kennwort konfiguriert werden.

## <a name="pre-create-a-new-sp"></a>Erstellen eines Dienstprinzipals im Voraus

Verwenden Sie den Befehl [az ad sp create-for-rbac][az-ad-sp-create], um den Dienstprinzipal mit der Azure-Befehlszeilenschnittstelle zu erstellen.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Die Ausgabe sieht in etwa wie folgt aus. Notieren Sie sich die Werte für `appId` und `password`. Diese Werte werden beim Erstellen eines AKS-Clusters verwendet.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Verwenden eines vorhandenen Dienstprinzipals

Geben Sie bei Verwendung eines vorab erstellten Dienstprinzipals die Werte für `appId` und `password` als Argumentwerte für den Befehl `az aks create` an.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Wenn Sie einen AKS-Cluster über das Azure Portal bereitstellen, geben Sie im Konfigurationsformular für den AKS-Cluster den `appId`-Wert in das Feld **Client-ID des Dienstprinzipals** und den `password`-Wert in das Feld **Service principal client secret** (Clientgeheimnis des Dienstprinzipals) ein.

![Abbildung der Navigation zu Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Zusätzliche Überlegungen

Beachten Sie bei Verwendung von AKS und Azure AD-Dienstprinzipalen Folgendes:

* Der Dienstprinzipal für Kubernetes ist Bestandteil der Clusterkonfiguration. Verwenden Sie diese Identität jedoch nicht zum Bereitstellen des Clusters.
* Jeder Dienstprinzipal ist einer Azure AD-Anwendung zugeordnet. Der Dienstprinzipal für einen Kubernetes-Cluster kann einem beliebigen gültigen Azure AD-Anwendungsnamen zugeordnet sein (z.B. `https://www.contoso.org/example`). Bei der URL für die Anwendung muss es sich nicht um einen realen Endpunkt handeln.
* Verwenden Sie zur Angabe der **Client-ID** des Dienstprinzipals den Wert für `appId` (wie in diesem Artikel gezeigt) oder den entsprechenden `name`-Wert des Dienstprinzipals (etwa `https://www.contoso.org/example`).
* Auf dem virtuellen Mastercomputer und den virtuellen Knotencomputern des Kubernetes-Clusters werden die Dienstprinzipal-Anmeldeinformationen in der Datei `/etc/kubernetes/azure.json` gespeichert.
* Wenn Sie den Dienstprinzipal mithilfe des Befehls `az aks create` automatisch generieren, werden die Dienstprinzipal-Anmeldeinformationen auf dem Computer, auf dem der Befehl ausgeführt wird, in die Datei `~/.azure/aksServicePrincipal.json` geschrieben.
* Beim Löschen eines AKS-Clusters, der mit `az aks create` erstellt wurde, wird der automatisch erstellte Dienstprinzipal nicht gelöscht. Verwenden Sie zum Löschen `az ad sp delete --id $clientID`.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory-Dienstprinzipalen finden Sie in der Dokumentation zu Azure AD-Anwendungen.

> [!div class="nextstepaction"]
> [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory (Azure AD)][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md