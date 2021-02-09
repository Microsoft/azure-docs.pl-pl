---
title: Omówienie bramy samoobsługowej | Microsoft Docs
description: Dowiedz się, w jaki sposób samodzielna funkcja bramy platformy Azure API Management ułatwia organizacjom Zarządzanie interfejsami API w środowiskach hybrydowych i wielochmurowych.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989092"
---
# <a name="self-hosted-gateway-overview"></a>Omówienie własnej bramy

W tym artykule wyjaśniono, jak funkcja samoobsługowego korzystania z platformy Azure API Management zapewnia obsługę hybrydowej i wielochmurowej usługi API Management, stanowi architekturę wysokiego poziomu i wyróżnia jej możliwości.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybrydowe i wielochmurowe usługi API Management

Funkcja bramy samoobsługowego rozszerza API Management obsługę środowisk hybrydowych i wielochmurowych oraz umożliwia organizacjom wydajne i bezpieczne Zarządzanie interfejsami API hostowanymi lokalnie i w chmurach z poziomu pojedynczej usługi API Management na platformie Azure.

W przypadku bramy samoobsługowego klienci mogą korzystać z elastyczności wdrażania kontenerowej wersji składnika bramy API Management w tych samych środowiskach, w których są hostowane interfejsy API. Wszystkie bramy samoobsługowe są zarządzane przez usługę API Management, z którą są federacyjne, a tym samym zapewniają klientom widoczność i ujednolicone środowisko zarządzania dla wszystkich wewnętrznych i zewnętrznych interfejsów API. Umieszczenie bram blisko interfejsów API pozwala klientom zoptymalizować przepływy ruchu interfejsu API i spełnić wymagania dotyczące zabezpieczeń i zgodności.

Każda usługa API Management składa się z następujących najważniejszych składników:

-   Płaszczyzna zarządzania udostępniana jako interfejs API służący do konfigurowania usługi za pośrednictwem Azure Portal, programu PowerShell i innych obsługiwanych mechanizmów.
-   Brama (lub płaszczyzna danych) jest odpowiedzialna za żądania interfejsu API proxy, stosowanie zasad i zbieranie danych telemetrycznych
-   Portal dla deweloperów używany przez deweloperów do odnajdywania, uczenia i dołączania do korzystania z interfejsów API

Domyślnie wszystkie te składniki są wdrażane na platformie Azure, co powoduje, że cały ruch interfejsu API (wyświetlany jako pełne czarne strzałki na ilustracji poniżej) do przepływu przez platformę Azure, bez względu na to, gdzie zachodzi Implementacja interfejsów API. Prostota działania tego modelu zapewnia koszt zwiększonego opóźnienia, problemy ze zgodnością i w niektórych przypadkach wiąże się z dodatkowymi opłatami za transfer danych.

![Przepływ ruchu interfejsu API bez bram samoobsługowych](media/self-hosted-gateway-overview/without-gateways.png)

Wdrażanie bram samoobsługowych w tych samych środowiskach, w których obsługiwane są implementacje interfejsu API zaplecza, umożliwia ruch związany z INTERFEJSem API bezpośrednio do interfejsów API zaplecza, co zwiększa czas oczekiwania, optymalizuje koszty transferu danych i zapewnia zgodność, zachowując korzyści z używania jednego punktu zarządzania, przestrzegania i odnajdywania wszystkich interfejsów API w organizacji niezależnie od tego, gdzie są hostowane ich wdrożenia.

![Przepływ ruchu interfejsu API za pomocą bram samoobsługowych](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Pakowanie i funkcje

Brama samoobsługowa to kontener, czyli funkcjonalnie równoważna wersja bramy zarządzanej wdrożonej na platformie Azure w ramach każdej usługi API Management. Brama samoobsługowa jest dostępna jako [kontener](https://aka.ms/apim/sputnik/dhub) platformy Docker oparty na systemie Linux z Container Registry firmy Microsoft. Można ją wdrożyć na platformie Docker, Kubernetes lub innych rozwiązań aranżacji kontenerów uruchomionych w klastrze serwerów w środowisku lokalnym, infrastrukturze chmurowej lub na potrzeby oceny i programowania na komputerze osobistym.

Następujące funkcje dostępne w bramach zarządzanych są **niedostępne** w bramach samoobsługowych:

- Dzienniki usługi Azure Monitor
- Nadrzędna (wewnętrzna strona zaplecza) wersja protokołu TLS i zarządzanie szyfrowaniem
- Sprawdzanie poprawności certyfikatów serwera i klienta przy użyciu [certyfikatów głównych urzędu certyfikacji](api-management-howto-ca-certificates.md) przekazanych do usługi API Management. Aby uzyskać więcej informacji, zobacz [weryfikowanie certyfikatów w bramie samohostowanej](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway).
- Integracja z [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- Wznawianie sesji protokołu TLS
- Ponowne negocjowanie certyfikatu klienta. Oznacza to, że w przypadku uwierzytelniania za pomocą [certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) dla użytkowników interfejsu API należy przedstawić swoje certyfikaty jako część początkowego UZGADNIANIA protokołu TLS. Aby upewnić się, że należy włączyć ustawienie Negocjuj certyfikat klienta podczas konfigurowania niestandardowej nazwy hosta bramy samohostowanej.
- Wbudowana pamięć podręczna. Zapoznaj się z tym [dokumentem](api-management-howto-cache-external.md) , aby dowiedzieć się więcej o korzystaniu z zewnętrznej pamięci podręcznej w ramach bram

## <a name="connectivity-to-azure"></a>Łączność z platformą Azure

Bramy samoobsługowe wymagają połączenia wychodzącego TCP/IP z platformą Azure na porcie 443. Każda Brama samoobsługowa musi być skojarzona z jedną usługą API Management i jest konfigurowana za pośrednictwem jej płaszczyzny zarządzania. Brama samoobsługowa używa łączności z platformą Azure dla:

-   Raportowanie stanu przez wysyłanie komunikatów pulsu co minutę
-   Regularne sprawdzanie (co 10 sekund) i stosowanie aktualizacji konfiguracji za każdym razem, gdy są dostępne
-   Wysyłanie dzienników żądań i metryk do Azure Monitor, jeśli zostały skonfigurowane w tym celu
-   Wysyłanie zdarzeń do Application Insights, jeśli zostały ustawione na to zrób

Gdy łączność z platformą Azure zostanie utracona, Brama samoobsługowa nie będzie mogła odbierać aktualizacji konfiguracji, zgłaszać stanu ani przesyłać danych telemetrycznych.

Brama samoobsługowa jest zaprojektowana pod kątem "Niepowodzenie static" i może przetrwać tymczasową utratę łączności z platformą Azure. Można ją wdrożyć z lokalną kopią zapasową lub bez niej. W poprzednim przypadku bramy samoobsługowe będą regularnie zapisywać kopię zapasową najnowszej pobranej konfiguracji na woluminie trwałym dołączonym do jego kontenera lub pod.

Gdy kopia zapasowa konfiguracji jest wyłączona, a łączność z platformą Azure zostanie przerwana:

-   Uruchamianie bram samoobsługowych będzie nadal działać przy użyciu kopii w pamięci konfiguracji
-   Zatrzymane bramy samoobsługowe nie będą mogły być uruchamiane

Po włączeniu tworzenia kopii zapasowej i nawiązaniu połączenia z platformą Azure:

-   Uruchamianie bram samoobsługowych będzie nadal działać przy użyciu kopii w pamięci konfiguracji
-   Zatrzymane bramy samoobsługowe będą mogły rozpocząć korzystanie z kopii zapasowej konfiguracji

Po przywróceniu łączności każda Brama samoobsługowa, której dotyczy awaria, będzie automatycznie ponownie łączyć się ze skojarzoną z nią usługą API Management i pobrać wszystkie aktualizacje konfiguracji, które wystąpiły, gdy Brama była "offline".

## <a name="next-steps"></a>Następne kroki

-   [Przeczytaj oficjalny dokument dotyczący dodatkowego tła tego tematu](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Wdrażanie bramy samohostowanej na platformie Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Wdróż bramę samoobsługową do Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
