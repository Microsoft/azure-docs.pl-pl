---
title: PRZESTARZAŁE Zarządzanie klastrem DC/OS platformy Azure przy użyciu interfejsu API REST Marathon
description: Wdrażanie kontenerów w klastrze DC/OS Azure Container Service przy użyciu interfejsu API REST Marathon.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277783"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>PRZESTARZAŁE Zarządzanie kontenerami DC/OS za pomocą interfejsu API REST Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Platforma DC/OS dostarcza środowisko wdrażania i skalowania obciążeń klastrowanych, zapewniając jednocześnie abstrakcyjność sprzętu bazowego. Ponad systemem DC/OS istnieje platforma, która zarządza planowaniem i wykonywaniem obciążeń obliczeniowych. Chociaż struktury są dostępne dla wielu popularnych obciążeń, ten dokument umożliwia rozpoczęcie tworzenia i skalowania wdrożeń kontenerów za pomocą interfejsu API REST Marathon. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do pracy nad tymi przykładami będziesz potrzebować klastra DC/OS skonfigurowanego w usłudze kontenera platformy Azure. Potrzebna będzie także zdalna łączność z tym klastrem. Aby uzyskać więcej informacji na temat tych elementów, zobacz następujące artykuły:

* [Wdrażanie klastra usługi Azure Container Service](container-service-deployment.md)
* [Łączenie z klastrem usługi Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Dostęp do interfejsów API platformy DC/OS
Po nawiązaniu połączenia z klastrem Azure Container Service dostęp do platformy DC/OS i związanych z nimi interfejsów API REST można uzyskać\/za pomocą protokołu http:/localhost: Local-Port. W przykładach przedstawionych w tym dokumencie założono, że tunelowanie korzysta z portu 80. Na przykład punkty końcowe Marathon można uzyskać w identyfikatorach URI zaczynających się od\/http:/localhost/Marathon/v2/. 

Aby uzyskać więcej informacji o różnych interfejsach API, zobacz dokumentację Mesosphere dotyczącą [interfejsu API platformy Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) i [interfejsu API programu Chronos](https://mesos.github.io/chronos/docs/api.html) oraz dokumentację Apache dotyczącą [interfejsu API aplikacji Mesos Scheduler](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Gromadzenie informacji z platform DC/OS i Marathon
Przed wdrożeniem kontenerów w klastrze DC/OS należy zebrać pewne informacje o klastrze DC/OS, takie jak nazwy i stan agentów DC/OS. W tym celu wykonaj zapytanie w punkcie końcowym `master/slaves` interfejsu API REST platformy DC/OS. Jeśli operacja zostanie wykonana pomyślnie, zapytanie zwróci listę agentów DC/OS i szereg właściwości każdego z nich.

```bash
curl http://localhost/mesos/master/slaves
```

Teraz użyj punktu końcowego `/apps` platformy Marathon, aby sprawdzić bieżące wdrożenia aplikacji w klastrze DC/OS. Jeśli jest to nowy klaster, pojawi się pusta tablica aplikacji.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Wdrażanie kontenera w formacie programu Docker
Kontenery z systemem Docker można wdrażać za pomocą interfejsu API REST Marathon przy użyciu pliku JSON, który opisuje zamierzone wdrożenie. Poniższy przykład służy do wdrażania kontenera Nginx na prywatnym agencie w klastrze. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Aby wdrożyć kontener w formacie platformy Docker, Zapisz plik JSON w dostępnej lokalizacji. Następnie w celu wdrożenia kontenera uruchom następujące polecenie. Określ nazwę pliku JSON (`marathon.json` w tym przykładzie).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Dane wyjściowe będą podobne do następujących:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Teraz po wykonaniu zapytania dotyczącego aplikacji na platformie Marathon nowa aplikacja pojawi się w danych wyjściowych.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Dotarcie do kontenera

Można sprawdzić, czy nginx jest uruchomiona w kontenerze na jednym z agentów prywatnych w klastrze. Aby znaleźć hosta i port, na którym działa kontener, należy wykonać zapytania Marathon dla uruchomionych zadań: 

```bash
curl localhost/marathon/v2/tasks
```

Znajdź wartość `host` w polu wynik (adres IP podobny do `10.32.0.x`) i wartość parametru. `ports`


Teraz należy nawiązać połączenie terminalu SSH (nie połączenie tunelowe) z nazwą FQDN zarządzania klastra. Po nawiązaniu połączenia wykonaj następujące żądanie, zastępując poprawne wartości `host` i: `ports`

```bash
curl http://host:ports
```

Dane wyjściowe serwera Nginx są podobne do następujących:

![Nginx z kontenera](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Skalowanie kontenerów
Korzystając z interfejsu API Marathon, można skalować w poziomie wdrożenia aplikacji lub skalować je w poziomie. W poprzednim przykładzie wdrożono jedno wystąpienie aplikacji. Wykonamy teraz skalowanie w poziomie, aby uzyskać trzy wystąpienia aplikacji. W tym celu utwórz plik JSON zawierający następujący tekst JSON i zapisz go w dostępnej lokalizacji.

```json
{ "instances": 3 }
```

W przypadku połączenia tunelowanego Uruchom następujące polecenie, aby skalować aplikację w poziomie.

> [!NOTE]
> Identyfikator URI to http:\//localhost/Marathon/v2/Apps/, a po nim identyfikator aplikacji do skalowania. Jeśli używasz przykładu Nginx, który jest podany w tym miejscu, identyfikator URI będzie http:\//localhost/Marathon/v2/Apps/Nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Na koniec wykonaj zapytanie dotyczące aplikacji w punkcie końcowym platformy Marathon. Widoczne będą trzy kontenery Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Równoważne polecenia programu PowerShell
Te same akcje można wykonać za pomocą poleceń programu PowerShell w systemie Windows.

Aby zebrać informacje o klastrze DC/OS, takie jak nazwy agentów i stan agenta, uruchom następujące polecenie:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Kontenery w formacie programu Docker można wdrażać za pośrednictwem platformy Marathon przy użyciu pliku JSON, który opisuje zamierzone wdrożenie. W poniższym przykładzie wdrożono kontener Nginx, który powiąże port 80 agenta DC/OS z portem 80 kontenera.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Aby wdrożyć kontener w formacie platformy Docker, Zapisz plik JSON w dostępnej lokalizacji. Następnie w celu wdrożenia kontenera uruchom następujące polecenie. Określ ścieżkę do pliku JSON (`marathon.json` w tym przykładzie).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Interfejs API platformy Marathon umożliwia także skalowanie w poziomie oraz skalowanie na zewnątrz wdrożeń aplikacji. W poprzednim przykładzie wdrożono jedno wystąpienie aplikacji. Wykonamy teraz skalowanie w poziomie, aby uzyskać trzy wystąpienia aplikacji. W tym celu utwórz plik JSON zawierający następujący tekst JSON i zapisz go w dostępnej lokalizacji.

```json
{ "instances": 3 }
```

Uruchom następujące polecenie, aby skalować aplikację w poziomie:

> [!NOTE]
> Identyfikator URI to http:\//localhost/Marathon/v2/Apps/, a po nim identyfikator aplikacji do skalowania. Jeśli używasz przykładu Nginx w tym miejscu, identyfikator URI to http:\//localhost/Marathon/v2/Apps/Nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj więcej na temat punktów końcowych HTTP Mesos](https://mesos.apache.org/documentation/latest/endpoints/)
* [Dowiedz się więcej o interfejsie API REST Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)

