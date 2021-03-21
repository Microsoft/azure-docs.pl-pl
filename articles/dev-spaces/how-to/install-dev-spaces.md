---
title: Włączanie Azure Dev Spaces na AKS & instalowania narzędzi po stronie klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Dowiedz się, jak włączyć Azure Dev Spaces w klastrze AKS i zainstalować narzędzia po stronie klienta.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
ms.openlocfilehash: 177496a53d204306b2b655b8736ce063dedf0f61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202250"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Włącz Azure Dev Spaces w klastrze AKS i zainstaluj narzędzia po stronie klienta

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

W tym artykule przedstawiono kilka sposobów włączania Azure Dev Spaces w klastrze AKS oraz instalowania narzędzi po stronie klienta.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Włączanie Azure Dev Spaces przy użyciu interfejsu wiersza polecenia

Aby można było włączyć funkcję Spaces dev przy użyciu interfejsu wiersza polecenia, potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][az-portal-create-account].
* [Zainstalowano interfejs wiersza polecenia platformy Azure][install-cli].
* [Klaster AKS][create-aks-cli] w [obsługiwanym regionie][supported-regions].

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w KLASTRZE AKS i postępuj zgodnie z monitami.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Powyższe polecenie włącza miejsca deweloperskie w klastrze *myAKSCluster* w grupie Grupa *zasobów* i tworzy *domyślny* obszar dev.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces`Polecenie instaluje również interfejs wiersza polecenia Azure dev Spaces.

## <a name="install-the-client-side-tools"></a>Instalowanie narzędzi po stronie klienta

Za pomocą narzędzi po stronie klienta Azure Dev Spaces można współdziałać z miejscami deweloperskimi w klastrze AKS z komputera lokalnego. Istnieje kilka sposobów instalacji narzędzi po stronie klienta:

* W [Visual Studio Code][vscode]zainstaluj [rozszerzenie Azure dev Spaces][vscode-extension].
* W programie [Visual Studio 2019][visual-studio]Zainstaluj obciążenie Programowanie na platformie Azure.
* Pobierz i zainstaluj interfejs wiersza polecenia [systemu Windows][cli-win], [Mac][cli-mac]lub [Linux][cli-linux] .

## <a name="remove-azure-dev-spaces-using-the-cli"></a>Usuwanie Azure Dev Spaces przy użyciu interfejsu wiersza polecenia

Aby usunąć Azure Dev Spaces z klastra AKS, użyj `azds remove` polecenia.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Poniższe przykładowe dane wyjściowe pokazują usuwanie Azure Dev Spaces z klastra *MyAKS* .

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Wszystkie obszary nazw utworzone za pomocą Azure Dev Spaces pozostaną razem z ich obciążeniami, ale nowe obciążenia w tych obszarach nazw nie będą Instrumentacją Azure Dev Spaces. Ponadto w przypadku ponownego uruchomienia wszystkich istniejących zasobników z Azure Dev Spaces mogą pojawić się błędy. Te zasobniki muszą zostać wdrożone ponownie bez Azure Dev Spaces narzędzi. Aby w pełni usunąć Azure Dev Spaces z klastra, Usuń wszystkie zasobniki ze wszystkich przestrzeni nazw, w których włączono Azure Dev Spaces.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
