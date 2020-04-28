---
title: PRZESTARZAŁE Monitorowanie klastra DC/OS platformy Azure — stos ELK
description: Monitoruj klaster DC/OS w klastrze Azure Container Service z ELK (Elasticsearch, logstash i Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 586b8d25a9f391487640e9b1f8adb3be0e4be6db
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166176"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service przy użyciu ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokazano, jak wdrożyć stos ELK (Elasticsearch, logstash, Kibana) w klastrze DC/OS w Azure Container Service. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [Podłącz](../container-service-connect.md) klaster DC/OS skonfigurowany przez Azure Container Service. Poznaj pulpit nawigacyjny platformy DC/OS i usługi Marathon [tutaj](container-service-mesos-marathon-ui.md). Zainstaluj również [Load Balancer Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, logstash, Kibana)
ELK Stack to kombinacja Elasticsearch, logstash i Kibana, która zapewnia kompleksowy stos, który może służyć do monitorowania i analizowania dzienników w klastrze.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurowanie stosu ELK w klastrze DC/OS
Uzyskaj dostęp do interfejsu użytkownika DC/ `http://localhost:80/` OS za pośrednictwem jednego z tych funkcji w interfejsie użytkownika DC/OS przejdź do programu **Universe**. Wyszukaj i zainstaluj Elasticsearch, logstash i Kibana z platformy DC/OS i w tej konkretnej kolejności. Jeśli przejdziesz do linku **Instalacja zaawansowana** , możesz dowiedzieć się więcej na temat konfiguracji.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Po zakończeniu i uruchomieniu kontenerów ELK należy włączyć dostęp do Kibana przy użyciu Marathon-LB. Przejdź do **Services** > **Kibana**Services, a następnie kliknij pozycję **Edit (Edytuj** ), jak pokazano poniżej.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Przełącz się do **trybu JSON** i przewiń w dół do sekcji etykiety.
Musisz dodać `"HAPROXY_GROUP": "external"` wpis tutaj, jak pokazano poniżej.
Po kliknięciu przycisku **Wdróż zmiany**, kontener zostanie ponownie uruchomiony.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Jeśli chcesz sprawdzić, czy Kibana jest zarejestrowany jako usługa na pulpicie nawigacyjnym HAPROXY, musisz otworzyć port 9090 w klastrze agentów jako HAPROXY uruchomiony na porcie 9090.
Domyślnie otwieramy porty 80, 8080 i 443 w klastrze agentów DC/OS.
Instrukcje dotyczące otwierania portu i zapewniania publicznej oceny są dostępne [tutaj](container-service-enable-public-access.md).

Aby uzyskać dostęp do pulpitu nawigacyjnego HAPROXY, Otwórz interfejs administratora Marathon-LB `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`w:.
Po przejściu do adresu URL powinien zostać wyświetlony pulpit nawigacyjny HAPROXY, jak pokazano poniżej i powinien zostać wyświetlony wpis usługi dla Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Aby uzyskać dostęp do pulpitu nawigacyjnego Kibana, który został wdrożony na porcie 5601, należy otworzyć port 5601. Postępuj zgodnie z instrukcjami [tutaj](container-service-enable-public-access.md). Następnie otwórz pulpit nawigacyjny Kibana w `http://localhost:5601`lokalizacji:.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać przekazywanie i konfigurację dzienników systemu i aplikacji, zobacz [Zarządzanie dziennikami w systemie DC/OS przy użyciu Elk](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Aby filtrować dzienniki, zobacz [filtrowanie dzienników za pomocą Elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

