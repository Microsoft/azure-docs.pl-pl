---
title: PRZESTARZAŁE Monitorowanie klastra Azure Container Service przy użyciu narzędzia Sysdig
description: Monitorowanie klastra usługi Azure Container Service przy użyciu rozwiązania Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 611d3e6d5ecaf986cc09c550c4aee760ed0a0d98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166154"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service przy użyciu narzędzia Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule wdrożymy agentów rozwiązania Sysdig we wszystkich węzłach agentów w klastrze usługi Azure Container Service. Ta konfiguracja wymaga konta z rozwiązaniem Sysdig. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [połącz](../container-service-connect.md) klaster skonfigurowany przez usługę Azure Container Service. Poznaj [interfejs użytkownika Marathon](container-service-mesos-marathon-ui.md). Przejdź do, [https://app.sysdigcloud.com](https://app.sysdigcloud.com) Aby skonfigurować konto w chmurze narzędzia Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig jest usługą monitorowania, która umożliwia monitorowanie kontenerów w klastrze. Usługa Sysdig pomaga w rozwiązywaniu problemów, a także oferuje podstawowe metryki monitorowania procesora, sieci, pamięci i operacji we/wy. Usługa Sysdig ułatwia sprawdzanie, które kontenery pracują najciężej lub po prostu używają największej części pamięci i mocy procesora. Widok ten jest dostępny w sekcji przeglądu zapewnianej obecnie w wersji beta. 

![Interfejs użytkownika usługi Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Konfigurowanie wdrożenia usługi Sysdig przy użyciu usługi Marathon
Te kroki przedstawiają metodę konfigurowania i wdrażania aplikacji usługi Sysdig w klastrze przy użyciu usługi Marathon. 

Uzyskaj dostęp do interfejsu użytkownika DC/OS za pośrednictwem jednego z nich `http://localhost:80/` w interfejsie użytkownika DC/OS przejdź do "Universe", który znajduje się w lewym dolnym rogu, a następnie wyszukaj ciąg "narzędzia Sysdig".

![Usługa Sysdig we wszechświecie rozwiązania DC/OS](./media/container-service-monitoring-sysdig/sysdig1.png)

Ukończenie konfiguracji wymaga konta w chmurze Sysdig lub bezpłatnego konta próbnego. Po zalogowaniu w witrynie sieci Web chmury Sysdig kliknij swoją nazwę użytkownika, a na stronie powinien zostać wyświetlony klucz dostępu. 

![Klucz interfejsu API usługi Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Następnie wprowadź klucz dostępu do konfiguracji usługi Sysdig we wszechświecie DC/OS. 

![Konfiguracja usługi Sysdig we wszechświecie DC/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Teraz ustaw wystąpienia na 10 000 000, dzięki czemu przy każdym dodaniu nowego węzła do klastra usługa Sysdig automatycznie wdroży agenta w tym nowym węźle. Jest to rozwiązanie tymczasowe, które zapewnia wdrożenie usługi Sysdig we wszystkich nowych agentach w klastrze. 

![Konfiguracja usługi Sysdig w wystąpieniach wszechświata DC/OS](./media/container-service-monitoring-sysdig/sysdig4.png)

Po zainstalowaniu pakietu przejdź z powrotem do interfejsu użytkownika usługi Sysdig, a będziesz mógł zbadać różne metryki użycia dla kontenerów w klastrze. 

Możesz także zainstalować pulpity nawigacyjne określone dla rozwiązań Mesos i Marathon, korzystając z [Kreatora nowego pulpitu nawigacyjnego](https://app.sysdigcloud.com/#/dashboards/new).
