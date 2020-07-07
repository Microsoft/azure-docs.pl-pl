---
title: PRZESTARZAŁE Monitorowanie klastra DC/OS platformy Azure — usługi Datadog
description: Monitoruj klaster Azure Container Service przy użyciu usługi Datadog. Użyj interfejsu użytkownika sieci Web platformy DC/OS do wdrożenia agentów usługi Datadog w klastrze.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166153"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>PRZESTARZAŁE Monitorowanie klastra DC/OS Azure Container Service przy użyciu usługi Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule wdrażamy agentów usługi Datadog do wszystkich węzłów agenta w klastrze Azure Container Service. Do tej konfiguracji będzie potrzebne konto z usługi Datadog. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [połącz](../container-service-connect.md) klaster skonfigurowany przez usługę Azure Container Service. Poznaj [interfejs użytkownika Marathon](container-service-mesos-marathon-ui.md). Przejdź do, [https://datadoghq.com](https://datadoghq.com) Aby skonfigurować konto usługi Datadog. 

## <a name="datadog"></a>Datadog
Usługi Datadog to usługa monitorowania, która zbiera dane monitorowania z kontenerów w klastrze Azure Container Service. Usługi Datadog ma pulpit nawigacyjny integracji platformy Docker, w którym można zobaczyć określone metryki w kontenerach. Metryki zbierane z kontenerów są zorganizowane według procesora CPU, pamięci, sieci i operacji we/wy. Usługi Datadog dzieli metryki na kontenery i obrazy. Poniżej przedstawiono przykład tego, jak wygląda interfejs użytkownika na potrzeby użycia procesora CPU.

![Interfejs użytkownika usługi Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurowanie wdrożenia usługi Datadog za pomocą Marathon
W tych krokach pokazano, jak skonfigurować i wdrożyć aplikacje usługi Datadog w klastrze przy użyciu Marathon. 

Uzyskaj dostęp do interfejsu użytkownika DC/OS za pośrednictwem programu `http://localhost:80/` . Raz w interfejsie użytkownika DC/OS przejdź do "Universe", który znajduje się w lewym dolnym rogu, a następnie wyszukaj ciąg "usługi Datadog", a następnie kliknij pozycję "Zainstaluj".

![Pakiet usługi Datadog w ramach platformy DC/OS](./media/container-service-monitoring/datadog1.png)

Teraz, aby ukończyć konfigurację, musisz mieć konto usługi Datadog lub bezpłatne konto próbne. Po zalogowaniu się do witryny sieci Web usługi Datadog Znajdź w lewo i przejdź do pozycji integracji — > następnie [interfejsy API](https://app.datadoghq.com/account/settings#api). 

![Klucz interfejsu API usługi Datadog](./media/container-service-monitoring/datadog2.png)

Następnie wprowadź klucz interfejsu API do konfiguracji usługi Datadog w ramach platformy DC/OS. 

![Konfiguracja usługi Datadog na platformie DC/OS](./media/container-service-monitoring/datadog3.png) 

W powyższych wystąpieniach konfiguracji ustawiono 10000000, więc za każdym razem, gdy do klastra zostanie dodany nowy węzeł usługi Datadog, zostanie automatycznie wdrożony Agent do tego węzła. Jest to rozwiązanie przejściowe. Po zainstalowaniu pakietu należy przejść z powrotem do witryny sieci Web usługi Datadog i znaleźć "[pulpity nawigacyjne](https://app.datadoghq.com/dash/list)". W tym miejscu będą widoczne pulpity nawigacyjne niestandardowych i integracji. [Pulpit nawigacyjny platformy Docker](https://app.datadoghq.com/screen/integration/docker) będzie miał wszystkie metryki kontenera potrzebne do monitorowania klastra. 

