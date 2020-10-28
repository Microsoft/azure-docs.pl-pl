---
title: Samouczek — usuwanie klastra usługi Azure Red Hat OpenShift
description: W tym samouczku dowiesz się, jak usunąć klaster Red Hat OpenShift platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 254a9737b805aeeae2008923a8178cd971602132
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677760"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: usuwanie klastra usługi Azure Red Hat OpenShift 4

W tym samouczku trzecia część trzech, klaster Red Hat OpenShift systemu Azure z systemem OpenShift 4 został usunięty. Omawiane kwestie:

> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach klaster usługi Azure Red Hat OpenShift został utworzony i połączony z użyciem konsoli sieci Web OpenShift. Jeśli nie wykonano tych kroków, a chcesz postępować zgodnie z [samouczkiem 1, Utwórz klaster Red Hat OpenShift 4 platformy Azure.](tutorial-create-cluster.md)

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom polecenie, `az login` Aby zalogować się do platformy Azure.

```bash
az login
```

Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` z subskrypcją, której chcesz użyć.

## <a name="delete-the-cluster"></a>Usuwanie klastra

W poprzednich samouczkach zostały ustawione następujące zmienne.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Korzystając z tych wartości, Usuń klaster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Następnie zostanie wyświetlony monit o potwierdzenie, czy chcesz usunąć klaster. Po potwierdzeniu z programem `y` usunięcie klastra zajmie kilka minut. Po zakończeniu wykonywania polecenia cała grupa zasobów i wszystkie znajdujące się w niej zasoby — w tym klaster — zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift 4

Dowiedz się więcej o korzystaniu z OpenShift z oficjalną [dokumentacją firmy Red Hat OpenShift](https://www.openshift.com/try)
