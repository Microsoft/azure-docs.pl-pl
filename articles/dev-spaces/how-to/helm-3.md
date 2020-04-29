---
title: Konfigurowanie klastra usługi Azure Dev Spaces do korzystania z rozwiązania Helm 3 (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Dowiedz się, jak skonfigurować klaster programu dev Spaces do korzystania z Helm 3
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454299"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Konfigurowanie klastra usługi Azure Dev Spaces do korzystania z rozwiązania Helm 3 (wersja zapoznawcza)

Azure Dev Spaces domyślnie używa Helm 2 w celu zainstalowania usług użytkowników w miejscach deweloperskich w klastrze AKS. Możesz włączyć Azure Dev Spaces, aby używać Helm 3 zamiast Helm 2 do instalowania usług użytkowników w miejscach deweloperskich. Bez względu na wersję programu Helm Azure Dev Spaces używaną do instalowania usług użytkowników, można nadal używać klienta Helm 2 lub 3 do zarządzania własnymi wersjami w tym samym klastrze.

Po włączeniu Helm 3 Azure Dev Spaces działa inaczej podczas instalowania usług użytkowników w miejscach deweloperskich w następujący sposób:

* Do klastra nie jest już wdrażany w przestrzeni nazw *azds* .
* Helm przechowuje informacje o wersji w przestrzeni nazw, w której zainstalowano usługę zamiast przestrzeni nazw *azds* .
* Informacje o wersji Helm 3 pozostają w przestrzeni nazw, w której zainstalowano usługę po usunięciu kontrolera.
* Możesz bezpośrednio korzystać z dowolnej wersji zarządzanej przez Azure Dev Spaces w klastrze za pomocą klienta Helm 3.

W tym przewodniku dowiesz się, jak włączyć Helm 3 dla Azure Dev Spaces, aby zainstalować usługi użytkowników w miejscach deweloperskich.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Rejestrowanie funkcji Helm3Preview Preview

Aby umożliwić Azure Dev Spaces do instalowania usług użytkowników w miejscach programistycznych za pomocą Helm 3, najpierw włącz flagę funkcji *Helm3Preview* w subskrypcji za pomocą polecenia *AZ Feature Register* :

> [!WARNING]
> Wszystkie klastry AKS Azure Dev Spaces włączane przy użyciu flagi funkcji *Helm3Preview* będą korzystać z tego środowiska w wersji zapoznawczej. Aby kontynuować Włączanie w pełni obsługiwanych Azure Dev Spaces w klastrach AKS, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Użyj oddzielnej subskrypcji testowej lub deweloperskiej platformy Azure do testowania funkcji w wersji zapoznawczej.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Ukończenie rejestracji trwa kilka minut. Sprawdź stan rejestracji za pomocą polecenia *AZ Feature show* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Po *zarejestrowaniu* *stanu* Odśwież rejestrację *Microsoft. DevSpaces* za pomocą polecenia *AZ Provider Register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Ograniczenia

Gdy ta funkcja jest dostępna w wersji zapoznawczej, obowiązują następujące ograniczenia:

* Tej funkcji nie można używać w przypadku klastrów AKS z istniejącymi obciążeniami. Należy utworzyć nowy klaster AKS.

## <a name="create-your-cluster"></a>Tworzenie klastra

Utwórz nowy klaster AKS w regionie, który ma tę funkcję w wersji zapoznawczej. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i nowy klaster AKS o nazwie *MyAKS*:

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Włącz Azure Dev Spaces

Użyj polecenia *use-dev-Spaces* , aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy domyślny obszar dev.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Sprawdź, czy w obszarze dev Spaces jest uruchomiony program Helm 3

Sprawdź, czy nie uruchomiono programu do tworzenia listy wdrożeń w przestrzeni nazw *azds* :

```cmd
kubectl get deployment -n azds
```

Potwierdź, że *wdrożenie* nie jest uruchomione w przestrzeni nazw azds. Przykład:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md