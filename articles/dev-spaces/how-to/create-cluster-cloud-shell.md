---
title: Tworzenie klastra Kubernetes z włączonym Azure Dev Spaces Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes z włączoną funkcją Azure Dev Spaces bezpośrednio z przeglądarki bez konieczności instalowania żadnych elementów.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77605307"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Utwórz klaster Kubernetes z włączonym Azure Dev Spaces z Azure Cloud Shell

Możesz użyć [Azure Cloud Shell](/azure/cloud-shell) , aby utworzyć klaster usługi Azure Kubernetes, używając przycisku **Wypróbuj** na tej stronie. Jeśli użytkownik nie jest zalogowany, postępuj zgodnie z monitami, aby zalogować się przy użyciu konta platformy Azure, a następnie wpisz polecenia w wierszu Azure Cloud Shell, gdy zostanie wyświetlony.

## <a name="create-the-cluster"></a>Tworzenie klastra

Najpierw utwórz grupę zasobów w [regionie, który obsługuje Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Utwórz klaster Kubernetes za pomocą następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Utworzenie klastra trwa kilka minut.  Po ukończeniu dane wyjściowe są wyświetlane w formacie JSON. Wyszukaj `provisioningState` i sprawdź, czy jest `Succeeded` .

## <a name="next-steps"></a>Następne kroki

Zobacz [Azure dev Spaces](/azure/dev-spaces/) , aby uzyskać linki do pełnych samouczków.

> [!IMPORTANT]
> Wiele przewodników Szybki Start i samouczków Azure Dev Spaces korzysta z interfejsu wiersza polecenia Azure Dev Spaces do wykonywania operacji. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service