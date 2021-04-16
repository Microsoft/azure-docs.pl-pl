---
title: Samouczek — usuwanie Azure Red Hat OpenShift klastra
description: Z tego samouczka dowiesz się, jak usunąć klaster Azure Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: sakthi-vetrivel
ms.custom: fasttrack-edit, devx-track-azurecli
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 65bb27f1f85b7a26e35074da84cfc27b2a5761a1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484765"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: usuwanie klastra Azure Red Hat OpenShift 4

W tym samouczku (część 3 z trzech) zostanie usunięty Azure Red Hat OpenShift z systemem OpenShift 4. Omawiane kwestie:

> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Zanim rozpoczniesz

W poprzednich samouczkach utworzono klaster Azure Red Hat OpenShift połączony z usługą przy użyciu konsoli internetowej OpenShift. Jeśli nie zostały wykonane te kroki i chcesz wykonać te czynności, zacznij od części Samouczek 1 — tworzenie klastra usługi [Azure Red Hat Openshift 4.](tutorial-create-cluster.md)

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom , `az login` aby zalogować się do platformy Azure.

```bash
az login
```

Jeśli masz dostęp do wielu subskrypcji, uruchom zastąpienie `az account set -s {subscription ID}` `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="delete-the-cluster"></a>Usuwanie klastra

W poprzednich samouczkach zostały ustawione następujące zmienne.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Korzystając z tych wartości, usuń klaster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Następnie zostanie wyświetlony monit o potwierdzenie usunięcia klastra. Po potwierdzeniu za pomocą `y` opcji usuń klaster po kilku minutach. Po zakończeniu polecenia cała grupa zasobów i wszystkie zasoby wewnątrz itâ€"łącznie z clusterâ€" zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift 4

Dowiedz się więcej o używaniu usługi OpenShift z oficjalną dokumentacją [usługi Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
