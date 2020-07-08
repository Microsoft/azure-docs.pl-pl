---
title: PRZESTARZAŁE Szybki Start — klaster Azure Kubernetes dla systemu Windows
description: Szybka nauka tworzenia klastra Kubernetes dla kontenerów systemu Windows w usłudze Azure Container Service za pomocą interfejsu wiersza polecenia platformy Azure.
author: dlepow
ms.service: container-service
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 2e36de9f2a6af3643b6f609339d413968f6a8d6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277644"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-windows-containers"></a>PRZESTARZAŁE Wdrażanie klastra Kubernetes dla kontenerów systemu Windows

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku zawarto szczegółowe informacje dotyczące korzystania z interfejsu wiersza polecenia platformy Azure w celu wdrożenia klastra [Kubernetes](https://kubernetes.io/docs/home/) w [usłudze Azure Container Service](../container-service-intro.md). Po wdrożeniu klastra nawiążesz z nim połączenie za pomocą narzędzia wiersza polecenia `kubectl` usługi Kubernetes i wdrożysz swój pierwszy kontener systemu Windows.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Obsługa kontenerów systemu Windows w rozwiązaniu Kubernetes w usłudze Azure Container Service jest dostępna w wersji zapoznawczej. 
>

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Tworzenie klastra Kubernetes
Utwórz klaster Kubernetes w usłudze Azure Container Service za pomocą polecenia [az acs create](/cli/azure/acs#az-acs-create). 

W poniższym przykładzie tworzony jest klaster o nazwie *myK8sCluster* z jednym węzłem głównym systemu Linux i dwoma węzłami agenta systemu Windows. Ten przykład tworzy klucze SSH potrzebne do nawiązania połączenia z węzłem głównym systemu Linux. W tym przykładzie *azureuser* to nazwa użytkownika administracyjnego, a *myPassword12* to hasło w węzłach systemu Windows. Zmień te wartości, aby pasowały do Twojego środowiska. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Po kilku minutach polecenie zostanie zakończone i wyświetlone zostaną informacje o wdrożeniu.

## <a name="install-kubectl"></a>Instalowanie narzędzia kubectl

Aby nawiązać połączenie z klastrem Kubernetes z komputera klienckiego, użyj [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) polecenia Kubernetes Client line. 

Jeśli korzystasz z usługi Azure CloudShell, narzędzie `kubectl` jest już zainstalowane. Jeśli chcesz zainstalować je lokalnie, możesz użyć polecenia [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Poniższy przykład interfejsu wiersza polecenia platformy Azure instaluje narzędzie `kubectl` w systemie. W systemie Windows należy uruchomić to polecenie jako administrator.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Nawiązywanie połączenia przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, uruchom polecenie [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Poniższy przykład pobiera konfigurację klastra dla klastra Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Aby sprawdzić połączenie z klastrem ze swojej maszyny, spróbuj uruchomić następujące polecenie:

```azurecli-interactive
kubectl get nodes
```

`kubectl` wyświetli węzeł główny i węzły agenta.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Wdrażanie kontenera usług IIS systemu Windows

Kontener platformy Docker można uruchomić wewnątrz *zasobnika* rozwiązania Kubernetes zawierającego co najmniej jeden kontener. 

W tym podstawowym przykładzie użyto pliku JSON do określenia kontenera usług Microsoft Internet Information Server (IIS), a następnie utworzono zasobnik przy użyciu polecenia `kubctl apply`. 

Utwórz plik lokalny o nazwie `iis.json` i skopiuj poniższy tekst. Ten plik nakazuje rozwiązaniu Kubernetes uruchomienie usług IIS w systemie Windows Server 2016 Nano Server przy użyciu obrazu publicznego kontenera z witryny [Docker Hub](https://hub.docker.com/r/microsoft/iis/). Kontener korzysta z portu 80, ale początkowo jest dostępny tylko w ramach sieci klastra.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Aby uruchomić zasobnik, wpisz:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Aby śledzić wdrożenie, wpisz:
  
```azurecli-interactive
kubectl get pods
```

Podczas wdrażania zasobnika ustawiany jest stan `ContainerCreating`. Może upłynąć kilka minut, zanim stan kontenera zmieni się na `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Aby udostępnić zasobnik światu z publicznym adresem IP, wpisz następujące polecenie:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

To polecenie spowoduje utworzenie przez rozwiązanie Kubernetes usługi i reguły usługi Azure Load Balancer z publicznym adresem IP usługi. 

Uruchom poniższe polecenie, aby wyświetlić stan usługi.

```azurecli-interactive
kubectl get svc
```

Początkowo adres IP będzie widoczny jako `pending`. Po kilku minutach zostanie ustawiony zewnętrzny adres IP zasobnika `iis`:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Możesz użyć wybranej przeglądarki internetowej, aby wyświetlić domyślną stronę powitalną usług IIS pod zewnętrznym adresem IP:

![Obraz przedstawiający przechodzenie do usług IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Usuwanie klastra
Gdy klaster nie będzie już potrzebny, możesz usunąć grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Kubernetes, nawiązano połączenie z narzędziem `kubectl` i wdrożono zasobnik za pomocą kontenera usług IIS. Aby dowiedzieć się więcej na temat usługi Azure Container Service, przejdź do samouczka dotyczącego rozwiązania Kubernetes.

> [!div class="nextstepaction"]
> [Zarządzanie klastrem Kubernetes usługi ASC](container-service-tutorial-kubernetes-prepare-app.md)
