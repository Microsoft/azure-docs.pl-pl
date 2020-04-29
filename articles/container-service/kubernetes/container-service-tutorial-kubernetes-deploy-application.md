---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — wdrażanie aplikacji
description: Samouczek usługi Azure Container Service — wdrażanie aplikacji
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78274051"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(PRZESTARZAŁE) Uruchamianie aplikacji w usłudze Kubernetes

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego samouczka korzystającego z usługi Azure Kubernetes, zobacz [Samouczek: uruchamianie aplikacji w usłudze Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-deploy-application.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

W tym samouczku (część czwarta z siedmiu) przykładowa aplikacja jest wdrażana w klastrze Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
> * Aktualizowanie plików manifestu Kubernetes
> * Uruchamianie aplikacji w usłudze Kubernetes
> * Testowanie aplikacji

W kolejnych samouczkach ta aplikacja zostanie przeskalowana, zaktualizowana, a usługa Log Analytics zostanie skonfigurowana do monitorowania klastra Kubernetes.

Na potrzeby tego samouczka założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać szczegółowe informacje na jego temat, zapoznaj się z [dokumentacją rozwiązania Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi Kubernetes. 

Do ukończenia tego samouczka potrzebujesz wstępnie utworzonego pliku manifestu usługi Kubernetes `azure-vote-all-in-one-redis.yml`. Ten plik został pobrany z kodu źródłowego aplikacji w poprzednim samouczku. Sprawdź, czy został utworzony klon repozytorium oraz czy katalogi zostały zmienione na sklonowane repozytorium.

Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Aktualizowanie pliku manifestu

W tym samouczku użyto usługi Azure Container Registry (ACR) do przechowywania obrazu kontenera. Przed uruchomieniem aplikacji należy zaktualizować nazwę serwera logowania usługi ACR w pliku manifestu usługi Kubernetes.

Pobierz nazwę serwera logowania usługi ACR przy użyciu polecenia [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Plik manifestu został wstępnie utworzony przy użyciu nazwy serwera logowania `microsoft`. Otwórz plik w dowolnym edytorze tekstu. W tym przykładzie plik jest otwierany przy użyciu narzędzia `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Zastąp element `microsoft` nazwą serwera logowania usługi ACR. Tę wartość można znaleźć w wierszu **47** pliku manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Zapisz i zamknij plik.

## <a name="deploy-application"></a>Wdrażanie aplikacji

Użyj polecenia [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create), aby uruchomić aplikację. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

Dane wyjściowe:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testowanie aplikacji

Utworzenie [usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) powoduje uwidocznienie aplikacji w Internecie. Ten proces może potrwać kilka minut. 

Aby monitorować postęp, użyj polecenia [kubectl get-service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) z argumentem `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Początkowo adres **EXTERNAL-IP** dla usługi `azure-vote-front`jest wyświetlany jako `pending`. Po zmianie adresu EXTERNAL-IP z `pending` na `IP address` użyj polecenia `CTRL-C`, aby zatrzymać proces śledzenia narzędzia kubectl.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Aby wyświetlić aplikację, przejdź do zewnętrznego adresu IP.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja do głosowania na platformie Azure została wdrożona do klastra Kubernetes usługi Azure Container Service. Wykonano następujące zadania:  

> [!div class="checklist"]
> * Pobieranie plików manifestu Kubernetes
> * Uruchamianie aplikacji w usłudze Kubernetes
> * Testowanie aplikacji

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat skalowania aplikacji Kubernetes i bazowej infrastruktury usługi Kubernetes. 

> [!div class="nextstepaction"]
> [Skalowanie aplikacji i infrastruktury rozwiązania Kubernetes](./container-service-tutorial-kubernetes-scale.md)
