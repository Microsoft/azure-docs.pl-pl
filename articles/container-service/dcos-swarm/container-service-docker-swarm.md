---
title: PRZESTARZAŁE Zarządzanie klastrem usługi Azure Swarm za pomocą interfejsu API platformy Docker
description: Wdrażanie kontenerów w klastrze Docker Swarm w Azure Container Service
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: c76701ce74aafcccdbb2f1a2454f9528b52fc096
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136231"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>PRZESTARZAŁE Zarządzanie kontenerami przy użyciu rozwiązania Docker Swarm

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Rozwiązanie Docker Swarm oferuje środowisko wdrażania konteneryzowanych obciążeń w puli zestawów hostów Docker. Rozwiązanie Docker Swarm używa natywnego interfejsu API Docker. Przepływ pracy zarządzania kontenerami w rozwiązaniu Docker Swarm jest niemal identyczny jak w przypadku pojedynczego hosta kontenera. Ten dokument zawiera proste przykłady wdrażania konteneryzowanych obciążeń w wystąpieniu usługi kontenera platformy Azure w rozwiązaniu Docker Swarm. Szczegółową dokumentację dotyczącą rozwiązania Docker Swarm można znaleźć na stronie [Docker Swarm w witrynie Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Wymagania wstępne dotyczące ćwiczeń opisanych w tym dokumencie:

[Utworzenie klastra Swarm usługi kontenera platformy Azure](container-service-deployment.md)

[Połączenie z klastrem Swarm w usłudze kontenera platformy Azure](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Wdrażanie nowego kontenera
Aby utworzyć nowy kontener w rozwiązaniu Docker Swarm, użyj polecenia `docker run` (co zapewni otwarcie tunelu SSH do serwerów głównych zgodnie z powyższymi wymaganiami wstępnymi). W tym przykładzie kontener jest tworzony na podstawie obrazu `yeasy/simple-web`:

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Po utworzeniu kontenera użyj polecenia `docker ps` w celu uzyskania zwróconych informacji o kontenerze. Zauważ, że na liście znajduje się agent Swarm hostujący kontener:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Możesz teraz uzyskiwać dostęp do aplikacji, która działa w tym kontenerze, za pośrednictwem publicznej nazwy DNS modułu równoważenia obciążenia agenta Swarm. Te informacje można znaleźć w witrynie Azure Portal:  

![Rzeczywiste wyniki dotyczące odwiedzin](./media/container-service-docker-swarm/real-visit.jpg)  

Domyślnie moduł równoważenia obciążenia ma otwarte porty 80, 8080 i 443. Jeśli chcesz się połączyć na innym porcie, musisz otworzyć ten port w module Azure Load Balancer dla puli agenta.

## <a name="deploy-multiple-containers"></a>Wdrażanie wielu kontenerów
Ze względu na to, że uruchamia się wiele kontenerów przez wielokrotne wykonanie polecenia „docker run”, możesz użyć polecenia `docker ps`, aby zobaczyć, na których hostach działają kontenery. W poniższym przykładzie trzy kontenery zostały rozmieszczone równomiernie w obrębie trzech agentów Swarm:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Wdrażanie kontenerów przy użyciu rozwiązania Docker Compose
Rozwiązania Docker Compose możesz używać do automatyzowania wdrażania i konfigurowania wielu kontenerów. W tym celu upewnij się, że utworzono tunel Secure Shell (SSH) oraz że ustawiono zmienną DOCKER_HOST (patrz wymagania wstępne powyżej).

Utwórz plik docker-compose.yml w systemie lokalnym. W tym celu użyj tego przykładu:

```dockerfile
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Uruchom polecenie `docker-compose up -d`, aby uruchomić wdrożenia kontenera:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Na koniec zostanie zwrócona lista uruchomionych kontenerów. Będzie ona zawierać kontenery wdrożone przy użyciu rozwiązania Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturalnie można użyć polecenia `docker-compose ps` do zbadania tylko kontenerów zdefiniowanych w pliku `compose.yml`.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej na temat rozwiązania Docker Swarm](https://docs.docker.com/swarm/)

