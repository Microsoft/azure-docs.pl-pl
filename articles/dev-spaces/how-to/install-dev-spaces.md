---
title: Włącz Azure Dev Spaces u usługi AKS & zainstalować narzędzia po stronie klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Dowiedz się, jak włączyć Azure Dev Spaces klastrze usługi AKS i zainstalować narzędzia po stronie klienta.
ms.custom: devx-track-azurecli
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 079a9e1b28b315457ac20d3aa9e7d29ce28fa077
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505364"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Włączanie Azure Dev Spaces w klastrze usługi AKS i instalowanie narzędzi po stronie klienta

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

W tym artykule przedstawiono kilka sposobów włączania Azure Dev Spaces w klastrze usługi AKS, a także instalowania narzędzi po stronie klienta.

## <a name="enable-azure-dev-spaces-using-the-azure-cli"></a>Włączanie Azure Dev Spaces przy użyciu interfejsu wiersza polecenia platformy Azure

Przed włączeniem usługi Dev Spaces przy użyciu interfejsu wiersza polecenia potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][az-portal-create-account].
* [Interfejs wiersza polecenia platformy Azure zainstalował program][install-cli].
* [Klaster usługi AKS][create-aks-cli] w [obsługiwanym regionie][supported-regions].

Użyj polecenia `use-dev-spaces` , aby włączyć usługę Dev Spaces w klastrze usługi AKS i postępuj zgodnie z monitami.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Powyższe polecenie włącza usługę Dev Spaces w klastrze *myAKSCluster* w grupie *myResourceGroup* i tworzy *domyślną przestrzeń* dewelopera.

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

Polecenie `use-dev-spaces` instaluje również interfejs Azure Dev Spaces wiersza polecenia.

## <a name="install-the-client-side-tools"></a>Instalowanie narzędzi po stronie klienta

Za pomocą tych Azure Dev Spaces po stronie klienta można wchodzić w interakcje z przestrzeniami dewelopersko-dewelopercznymi w klastrze usługi AKS z komputera lokalnego. Istnieje kilka sposobów instalowania narzędzi po stronie klienta:

* W [Visual Studio Code][vscode]zainstaluj [rozszerzenie Azure Dev Spaces .][vscode-extension]
* W [Visual Studio 2019][visual-studio]r. zainstaluj obciążenie Azure Development.
* Pobierz i zainstaluj interfejs wiersza polecenia systemu [Windows,][cli-win] [Mac][cli-mac] [lub Linux.][cli-linux]

## <a name="remove-azure-dev-spaces-using-the-azure-cli"></a>Usuwanie Azure Dev Spaces przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć Azure Dev Spaces z klastra usługi AKS, użyj `azds remove` polecenia .

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Poniższe przykładowe dane wyjściowe pokazują usuwanie Azure Dev Spaces klastra *MyAKS.*

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Wszystkie przestrzenie nazw utworzone za pomocą Azure Dev Spaces pozostaną razem z ich obciążeniami, ale nowe obciążenia w tych przestrzeniach nazw nie będą instrumentowane przy użyciu Azure Dev Spaces. Ponadto jeśli ponownie uruchomisz wszystkie istniejące zasobniki z instrumentami Azure Dev Spaces, mogą wystąpić błędy. Te zasobniki należy ponownie wdać bez Azure Dev Spaces narzędzi. Aby całkowicie usunąć Azure Dev Spaces z klastra, usuń wszystkie zasobniki we wszystkich przestrzeniach nazw, Azure Dev Spaces włączono.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, Azure Dev Spaces działa.

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
