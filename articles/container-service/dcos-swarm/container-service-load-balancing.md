---
title: (PRZESTARZAŁE) Równoważenie obciążenia kontenerów w klastrze DC/OS na platformie Azure
description: Równoważenie obciążenia w wielu kontenerach w klastrze DC/OS usługi Azure Container Service.
author: rgardler
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a8f863f16888e6eca2dbc72c5dd612c38edbe46e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273373"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(PRZESTARZAŁE) Równoważenie obciążenia kontenerów w klastrze DC/OS usługi Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokazujemy, jak utworzyć wewnętrzny moduł równoważenia obciążenia w usłudze Azure Container Service zarządzanej przez rozwiązanie DC/OS przy użyciu narzędzia Marathon-LB. Ta konfiguracja pozwala na skalowanie aplikacji w poziomie. Ponadto umożliwia ona wykorzystanie publicznych i prywatnych klastrów agentów przez umieszczenie modułów równoważenia obciążenia w klastrze publicznym i kontenerów aplikacji w klastrze prywatnym. W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie modułu Marathon Load Balancer
> * Wdrażanie aplikacji przy użyciu modułu równoważenia obciążenia
> * Konfigurowanie modułu Azure Load Balancer

Do wykonania kroków opisanych w tym samouczku potrzebujesz klastra DC/OS usługi ACS. W razie potrzeby [ten przykładowy skrypt](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pomoże Ci go utworzyć.

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Równoważenie obciążenia — omówienie

W klastrze DC/OS usługi Azure Container Service istnieją dwie warstwy równoważenia obciążenia: 

Usługa **Azure Load Balancer** udostępnia publiczne punkty wejścia (do których użytkownicy końcowi uzyskują dostęp). Usługa Azure LB jest dostarczana automatycznie przez usługę Azure Container Service i domyślnie skonfigurowana do udostępniania portów 80, 443 i 8080.

Usługa **Marathon Load Balancer (marathon-lb)** kieruje żądania przychodzące do wystąpień kontenera, które obsługują te żądania. Warstwa marathon-lb jest dynamicznie dostosowywana w przypadku skalowania kontenerów świadczących usługę internetową. Moduł równoważenia obciążenia nie jest oferowany domyślnie przez usługę Container Service, ale można go łatwo zainstalować.

## <a name="configure-marathon-load-balancer"></a>Konfigurowanie modułu Marathon Load Balancer

Moduł Marathon Load Balancer dynamicznie zmienia konfigurację na podstawie wdrożonych kontenerów. Jest on również odporny na utratę kontenera lub agenta. W takim przypadku rozwiązanie Apache Mesos ponownie uruchamia kontener w innym miejscu, a moduł marathon-lb jest dostosowywany.

Przejdź do [https://shell.azure.com](https://shell.azure.com) okna, aby otworzyć Cloud Shell w przeglądarce.

Uruchom poniższe polecenie, aby zainstalować moduł Marathon Load Balancer w klastrze agenta publicznego.

```console
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Wdrażanie aplikacji o zrównoważonym obciążeniu

Teraz po przygotowaniu pakietu marathon-lb możemy wdrożyć kontener aplikacji, dla którego chcemy zrównoważyć obciążenie. 

Najpierw pobierz nazwę FQDN publicznie ujawnionych agentów.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Następnie utwórz plik o nazwie *hello-web.json* i skopiuj poniższą zawartość. Etykietę `HAPROXY_0_VHOST` należy zaktualizować przy użyciu nazwy FQDN agentów DC/OS. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Użyj interfejsu wiersza polecenia DC/OS, aby uruchomić aplikację. Domyślnie moduł Marathon wdraża aplikację w klastrze prywatnym. Oznacza to, że powyższe wdrożenie jest dostępne tylko za pośrednictwem modułu równoważenia obciążenia, co zwykle jest pożądanym zachowaniem.

```console
dcos marathon app add hello-web.json
```

Po wdrożeniu aplikacji przejdź do nazwy FQDN klastra agenta, aby wyświetlić aplikację ze zrównoważonym obciążeniem.

![Obraz aplikacji ze zrównoważonym obciążeniem](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Konfigurowanie modułu Azure Load Balancer

Domyślnie moduł Azure Load Balancer udostępnia porty 80, 8080 i 443. Jeśli używasz jednego z tych trzech portów (tak jak w powyższym przykładzie), nie musisz wykonywać żadnych działań. Nazwa FQDN modułu równoważenia obciążenia agenta powinna być teraz dostępna, a przy każdym odświeżeniu trafisz po kolei na jeden z trzech serwerów internetowych w ramach działania okrężnego. 

Jeśli korzystasz z innego portu, musisz dodać regułę działania okrężnego oraz sondę w module równoważenia obciążenia dla używanego portu. Można to zrobić w [interfejsie wiersza polecenia Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), używając poleceń `azure network lb rule create` i `azure network lb probe create`.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje na temat równoważenia obciążenia w usłudze ACS z przy użyciu modułów równoważenia obciążenia platform Marathon i Azure, w tym następujące akcje:

> [!div class="checklist"]
> * Konfigurowanie modułu Marathon Load Balancer
> * Wdrażanie aplikacji przy użyciu modułu równoważenia obciążenia
> * Konfigurowanie modułu Azure Load Balancer

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat integracji magazynu Azure Storage z rozwiązaniem DC/OS na platformie Azure.

> [!div class="nextstepaction"]
> [Zainstaluj udział plików platformy Azure w klastrze DC/OS](container-service-dcos-fileshare.md)
