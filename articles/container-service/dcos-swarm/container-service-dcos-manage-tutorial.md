---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — zarządzanie systemem DC/OS
description: Samouczek usługi Azure Container Service — zarządzanie systemem DC/OS
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4212277dbdf29705152832f3830692b43b8d1297
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78402815"
---
# <a name="deprecated-azure-container-service-tutorial---manage-dcos"></a>(PRZESTARZAŁE) Samouczek usługi Azure Container Service — zarządzanie systemem DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

System DC/OS zapewnia rozproszoną platformę dla nowoczesnych, konteneryzowanych aplikacji. Usługa Azure Container Service umożliwia łatwe i szybkie inicjowanie obsługi produkcyjnego klastra DC/OS. W tym przewodniku szybki start przedstawiono podstawowe kroki niezbędne do wdrożenia klastra DC/OS i uruchamiania podstawowego obciążenia.

> [!div class="checklist"]
> * Tworzenie klastra DC/OS usługi ACS
> * Łączenie z klastrem
> * Instalowanie interfejsu wiersza polecenia DC/OS
> * Wdrażanie aplikacji w klastrze
> * Skalowanie aplikacji w klastrze
> * Skalowanie węzłów klastra DC/OS
> * Zarządzanie podstawowe systemem DC/OS
> * Usuwanie klastra DC/OS

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Tworzenie klastra DC/OS

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Aby następnie utworzyć klaster DC/OS, użyj polecenia [az acs create](/cli/azure/acs#az-acs-create).

Następujący przykład umożliwia utworzenie klastra DC/OS o nazwie *myDCOSCluster* oraz kluczy SSH, jeśli jeszcze nie istnieją. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Po kilku minutach wykonywanie polecenia zostanie zakończone i zwrócone zostaną informacje o wdrożeniu.

## <a name="connect-to-dcos-cluster"></a>Nawiązywanie połączenia z klastrem DC/OS

Po utworzeniu klastra DC/OS można uzyskać do niego dostęp za pośrednictwem tunelu SSH. Uruchom następujące polecenie, aby zwrócić publiczny adres klastra DC/OS. Adres IP jest przechowywany w zmiennej i zostanie użyty w kolejnym kroku.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Aby utworzyć tunel SSH, uruchom następujące polecenie, po czym postępuj zgodnie z instrukcjami na ekranie. Jeśli port 80 jest już używany, wykonanie polecenia nie powiedzie się. Zmień port tunelu na port, który nie jest używany, na przykład `85:localhost:80`. 

```console
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Instalowanie interfejsu wiersza polecenia DC/OS

Zainstaluj interfejs wiersza polecenia DC/OS, używając polecenia [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Jeśli korzystasz z usługi Azure CloudShell, interfejs wiersza polecenia DC/OS jest już zainstalowany. Jeśli używasz interfejsu wiersza polecenia platformy Azure w systemie macOS lub Linux, konieczne może być uruchomienie polecenia za pomocą programu sudo.

```azurecli
az acs dcos install-cli
```

Aby można było użyć interfejsu wiersza polecenia z klastrem, należy skonfigurować go tak, aby korzystał z tunelu SSH. W tym celu uruchom następujące polecenie, w razie potrzeby dostosowując port.

```console
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Uruchamianie aplikacji

Domyślny mechanizm planowania w przypadku klastra DC/OS w usłudze ACS to Marathon. Platforma Marathon jest używana w celu uruchomienia aplikacji i zarządzania jej stanem w klastrze DC/OS. Aby zaplanować uruchomienie aplikacji za pośrednictwem platformy Marathon, utwórz plik o nazwie **marathon-app.json** i skopiuj do niego następującą zawartość. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Uruchom następujące polecenie, aby zaplanować uruchomienie aplikacji w klastrze DC/OS.

```console
dcos marathon app add marathon-app.json
```

Aby wyświetlić stan wdrożenia aplikacji, uruchom następujące polecenie.

```console
dcos marathon app list
```

Zmiana wartości w kolumnie **ZADANIA** z *0/1* na *1/1* oznacza, że wdrażanie aplikacji zostało zakończone.

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Skalowanie aplikacji platformy Marathon

W poprzednim przykładzie została utworzona aplikacja pojedynczego wystąpienia. Aby zaktualizować to wdrożenie w celu udostępnienia trzech wystąpień aplikacji,otwórz plik **marathon-app.json**, a następnie zaktualizuj właściwość wystąpienia do wartości 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Zaktualizuj aplikację przy użyciu polecenia `dcos marathon app update`.

```console
dcos marathon app update demo-app-private < marathon-app.json
```

Aby wyświetlić stan wdrożenia aplikacji, uruchom następujące polecenie.

```console
dcos marathon app list
```

Zmiana wartości w kolumnie **ZADANIA** z *1/3* na *3/1* oznacza, że wdrażanie aplikacji zostało zakończone.

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Uruchamianie aplikacji dostępnej z Internetu

Klaster DC/OS usługi ACS składa się z dwóch zestawów węzłów: publicznego, który jest dostępny przez Internet, i prywatnego, który nie jest dostępny przez Internet. Domyślny jest zestaw węzłów prywatnych, którego użyto w ostatnim przykładzie.

Aby aplikacja była dostępna przez Internet, należy ją wdrożyć w zestawie węzłów publicznych. Aby to zrobić, nadaj obiektowi `acceptedResourceRoles` wartość `slave_public`.

Utwórz plik o nazwie **nginx-public.json** i skopiuj do niego poniższą zawartość.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Uruchom następujące polecenie, aby zaplanować uruchomienie aplikacji w klastrze DC/OS.

```console
dcos marathon app add nginx-public.json
```

Uzyskaj publiczny adres IP agentów klastra publicznego DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Po przejściu do tego adresu zostanie zwrócona domyślna witryna serwera NGINX.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Skalowanie klastra DC/OS

W poprzednim przykładzie aplikacja została przeskalowana do wielu wystąpień. Infrastrukturę DC/OS można też skalować w taki sposób, aby zapewnić więcej lub mniej pojemności obliczeniowej. Służy do tego polecenie [az acs scale](/cli/azure/acs#az-acs-scale). 

Aby wyświetlić bieżącą liczbę agentów DC/OS, użyj polecenia [az acs show](/cli/azure/acs#az-acs-show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Aby zwiększyć tę liczbę do 5, użyj polecenia [az acs scale](/cli/azure/acs#az-acs-scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Usuwanie klastra DC/OS

Gdy klaster nie będzie już potrzebny, można użyć polecenia [az group delete](/cli/azure/group#az-group-delete), aby usunąć grupę zasobów, klaster DC/OS oraz wszystkie pokrewne zasoby.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowe zadania zarządzania systemem DC/OS, w tym poniższe. 

> [!div class="checklist"]
> * Tworzenie klastra DC/OS usługi ACS
> * Łączenie z klastrem
> * Instalowanie interfejsu wiersza polecenia DC/OS
> * Wdrażanie aplikacji w klastrze
> * Skalowanie aplikacji w klastrze
> * Skalowanie węzłów klastra DC/OS
> * Usuwanie klastra DC/OS

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o aplikacji równoważenia obciążenia w systemie DC/OS na platformie Azure. 

> [!div class="nextstepaction"]
> [Równoważenie obciążenia aplikacji](container-service-load-balancing.md)
