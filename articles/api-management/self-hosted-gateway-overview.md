---
title: Usługa Azure API Management Gateway — Omówienie Microsoft Docs
description: Dowiedz się, jak samodzielna usługa Azure API Management Gateway ułatwia organizacjom Zarządzanie interfejsami API w środowiskach hybrydowych i wielochmurowych.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73513720"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Samodzielna Brama API Management — Omówienie

W tym artykule wyjaśniono, jak funkcja bramy samoobsługowego umożliwia tworzenie hybrydowych i wielochmurowych interfejsów API, przedstawia jej architekturę wysokiego poziomu i wyróżnia podstawowe możliwości.

> [!NOTE]
> Funkcja samoobsługowego bramy jest dostępna w wersji zapoznawczej. W wersji zapoznawczej Brama samoobsługowa jest dostępna tylko w warstwach deweloper i Premium bez dodatkowych opłat. Warstwa dewelopera jest ograniczona do jednego wdrożenia bramy samoobsługowego.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybrydowe i wielochmurowe usługi API Management

Funkcja bramy samoobsługowego rozszerza API Management obsługę środowisk hybrydowych i wielochmurowych oraz umożliwia organizacjom wydajne i bezpieczne Zarządzanie interfejsami API hostowanymi lokalnie i w chmurach z poziomu pojedynczej usługi API Management na platformie Azure.

W przypadku bramy samoobsługowego klienci mogą korzystać z elastyczności wdrażania kontenerowej wersji składnika bramy API Management w tych samych środowiskach, w których są hostowane interfejsy API. Wszystkie bramy samoobsługowe są zarządzane przez usługę API Management, z którą są federacyjne, a tym samym zapewniają klientom widoczność i ujednolicone środowisko zarządzania dla wszystkich wewnętrznych i zewnętrznych interfejsów API. Umieszczenie bram blisko interfejsów API pozwala klientom zoptymalizować przepływy ruchu interfejsu API i spełnić wymagania dotyczące zabezpieczeń i zgodności.

Każda usługa API Management składa się z następujących najważniejszych składników:

-   Płaszczyzna zarządzania udostępniana jako interfejs API służący do konfigurowania usługi za pośrednictwem Azure Portal, programu PowerShell i innych obsługiwanych mechanizmów.
-   Brama (lub płaszczyzna danych) jest odpowiedzialna za żądania interfejsu API proxy, stosowanie zasad i zbieranie danych telemetrycznych
-   Portal dla deweloperów używany przez deweloperów do odnajdywania, uczenia i dołączania do korzystania z interfejsów API

Domyślnie wszystkie te składniki są wdrażane na platformie Azure, co powoduje, że cały ruch interfejsu API (wyświetlany jako pełne czarne strzałki na ilustracji poniżej) do przepływu przez platformę Azure, bez względu na to, gdzie zachodzi Implementacja interfejsów API. Prostota działania tego modelu zapewnia koszt zwiększonego opóźnienia, problemy ze zgodnością i w niektórych przypadkach wiąże się z dodatkowymi opłatami za transfer danych.

![Przepływ ruchu interfejsu API bez bram samoobsługowych](media/self-hosted-gateway-overview/without-gateways.png)

Wdrażanie bram samoobsługowych w tych samych środowiskach co implementacje interfejsu API zaplecza i dodanie ich do usługi API Management pozwala ruch interfejsów API bezpośrednio do interfejsów API zaplecza, co zwiększa czas oczekiwania, optymalizuje koszty transferu danych i zapewnia zgodność, zachowując korzyści z korzystania z jednego punktu zarządzania i odnajdywania wszystkich interfejsów API w organizacji niezależnie od tego, gdzie są hostowane ich wdrożenia.

![Przepływ ruchu interfejsu API za pomocą bram samoobsługowych](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Pakowanie i funkcje

Brama samoobsługowa jest kontenerem, a funkcjonalnie równoważną wersją zarządzanej bramy wdrożoną na platformie Azure jako część każdej usługi API Management. Brama samoobsługowa jest dostępna jako kontener platformy Docker oparty na systemie Linux z Container Registry firmy Microsoft. Można ją wdrożyć na platformie Docker, Kubernetes lub innym rozwiązaniu aranżacji kontenerów działającej na komputerze stacjonarnym, klastrze serwerów lub infrastrukturze chmurowej.

> [!IMPORTANT]
> Niektóre funkcje dostępne w ramach bramy zarządzanej nie są jeszcze dostępne w wersji zapoznawczej. W szczególności: Logowanie do zasad centrum zdarzeń, integracja Service Fabric, podrzędny protokół HTTP/2. Nie ma planu udostępnienia wbudowanej pamięci podręcznej w ramach bramy samoobsługowej.

## <a name="connectivity-to-azure"></a>Łączność z platformą Azure

Brama samoobsługowa wymaga łączności TCP/IP z platformą Azure na porcie 443. Każda Brama samoobsługowa musi być skojarzona z jedną usługą API Management i jest konfigurowana za pośrednictwem jej płaszczyzny zarządzania. Brama samoobsługowa używa łączności z platformą Azure dla:

-   Raportowanie stanu przez wysyłanie komunikatów pulsu co minutę
-   Regularne sprawdzanie (co 10 sekund) i stosowanie aktualizacji konfiguracji za każdym razem, gdy są dostępne
-   Wysyłanie dzienników żądań i metryk do Azure Monitor, jeśli zostały skonfigurowane w tym celu
-   Wysyłanie zdarzeń do Application Insights, jeśli zostały ustawione na to zrób

Gdy łączność z platformą Azure zostanie utracona, Brama samoobsługowa nie będzie mogła odbierać aktualizacji konfiguracji, zgłaszać stanu ani przesyłać danych telemetrycznych.

Brama samoobsługowa jest zaprojektowana pod kątem "Niepowodzenie static" i może przetrwać tymczasową utratę łączności z platformą Azure. Można ją wdrożyć z włączoną lokalną kopią zapasową lub bez niej. W poprzednim przypadku bramy samoobsługowe będą regularnie zapisywać kopię zapasową konfiguracji na woluminie trwałym dołączonym do kontenera lub pod.

Gdy kopia zapasowa konfiguracji jest wyłączona, a łączność z platformą Azure zostanie przerwana:

-   Uruchomione przez siebie bramy będą nadal działać przy użyciu kopii konfiguracji w pamięci
-   Zatrzymane bramy samoobsługowe nie będą mogły być uruchamiane

Po włączeniu tworzenia kopii zapasowej i nawiązaniu połączenia z platformą Azure:

-   Uruchomione przez siebie bramy będą nadal działać przy użyciu kopii konfiguracji w pamięci
-   Zatrzymane bramy samoobsługowe rozpoczną korzystanie z kopii zapasowej konfiguracji

Po przywróceniu łączności każda Brama samoobsługowa, której dotyczy awaria, będzie automatycznie ponownie łączyć się ze skojarzoną z nią usługą API Management i pobrać wszystkie aktualizacje konfiguracji, które wystąpiły, gdy Brama była "offline".

## <a name="next-steps"></a>Następne kroki

-   [Przeczytaj oficjalny dokument dotyczący dodatkowego tła tego tematu](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Wdrażanie bramy samohostowanej na platformie Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Wdróż bramę samoobsługową do Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
