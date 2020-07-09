---
title: Cennik Azure Security Center warstw
description: Azure Security Center jest oferowana w dwóch warstwach — bezpłatnie i w warstwie Standardowa. Na tej stronie przedstawiono, jak przeprowadzić uaktualnienie z wersji bezpłatna do wersji Standard.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 4487d1452611b269eec756cdbc76e8e55c466cd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801142"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Uaktualnij do warstwy Standardowa w celu uzyskania zwiększonych zabezpieczeń

Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. Zapewnia widoczność i kontrolę nad obciążeniami w chmurze hybrydowej, aktywną obroną, która zmniejsza narażenie na zagrożenia oraz Inteligentne wykrywanie, które ułatwiają szybkie rozwijanie ataków cybernetycznymi.

## <a name="pricing-tiers"></a>Warstwy cenowe
Usługa Security Center jest oferowana w dwóch warstwach:

- Warstwa **bezpłatna** jest włączana we wszystkich subskrypcjach platformy Azure, gdy po raz pierwszy odwiedzasz pulpit nawigacyjny Azure Security Center w Azure Portal lub włączono programowo za pośrednictwem interfejsu API. Warstwa Bezpłatna zapewnia zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń, które ułatwiają ochronę zasobów platformy Azure.

- Warstwa **standardowa** rozszerza możliwości warstwy Bezpłatna do obciążeń działających w prywatnych i innych chmurach publicznych, zapewniając ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Warstwa standardowa dodaje również funkcje ochrony przed zagrożeniami, które wykorzystują wbudowaną analizę behawioralną i uczenie maszynowe, aby identyfikować ataki i luki w zabezpieczeniach, kontrolę dostępu i aplikacji w celu zmniejszenia narażenia na ataki w sieci i złośliwe oprogramowanie. Ponadto warstwa standardowa dodaje skanowanie do maszyn wirtualnych. Możesz bezpłatnie wypróbować warstwę Standardowa. Standard Security Center obsługuje zasoby platformy Azure, w tym maszyny wirtualne, zestawy skalowania maszyn wirtualnych, App Service, serwery SQL i konta magazynu. Jeśli masz Azure Security Center Standard, możesz zrezygnować z obsługi na podstawie typu zasobu. 

Większość ocen zabezpieczeń warstwy Bezpłatna dla maszyn wirtualnych oraz wielu alertów zabezpieczeń warstwy Standardowa wymaga instalacji agenta Log Analytics. Możesz włączyć automatyczną obsługę administracyjną w Security Center, aby automatycznie wdrożyć agenta na maszynach wirtualnych platformy Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Wypróbuj bezpłatnie warstwę Standardowa przez 30 dni
Warstwa standardowa jest bezpłatna przez pierwsze 30 dni. Po upływie 30 dni, jeśli chcesz kontynuować korzystanie z usługi, automatycznie zaczniemy naliczać opłaty za użycie.

Możesz uaktualnić całą subskrypcję platformy Azure do warstwy Standardowa, która jest dziedziczona przez wszystkie zasoby w ramach subskrypcji.

Aby uzyskać warstwę standardową:

1. Wybierz pozycję **Cennik ustawienia &** w menu głównym **Security Center** .
2. Wybierz subskrypcję, którą chcesz uaktualnić do wersji Standard.
3. Wybierz **warstwę cenową**.
4. Wybierz pozycję **standardowa** , aby przeprowadzić uaktualnienie.
5. Kliknij pozycję **Zapisz**.

[![Cennik Security Center](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Aby włączyć wszystkie funkcje Security Center w tym funkcje ochrony przed zagrożeniami, należy zastosować warstwę cenową standardowa do subskrypcji zawierającej odpowiednie obciążenia. Konfigurowanie cen dla obszaru roboczego nie umożliwia dostępu just in Time do maszyny wirtualnej, adaptacyjnych kontroli aplikacji i wykrywania sieci dla zasobów platformy Azure. 
>
> Ochronę przed zagrożeniami dla **kont usługi Azure Storage** można włączyć na poziomie subskrypcji lub na poziomie zasobu.
> Ochronę przed zagrożeniami dla **Azure SQL Database serwerów SQL** można włączyć na poziomie subskrypcji lub na poziomie zasobu.
> Ochronę przed zagrożeniami dla **Azure Database for MariaDB/MySQL/PostgreSQL** można włączyć tylko na poziomie zasobów.


## <a name="why-upgrade-to-standard"></a>Dlaczego warto przeprowadzić uaktualnienie do wersji Standard?
Security Center oferuje zwiększone zabezpieczenia i ochronę przed zagrożeniami dla obciążeń chmury hybrydowej, w tym:

- **Bezpieczeństwo hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i w chmurze. Stosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z wielu źródeł, w tym zapór i innych rozwiązań partnerskich.
- **Alerty zabezpieczeń** — Użyj zaawansowanej analizy i Microsoft Intelligent Security Graph, aby uzyskać krawędzie przed rozwijającym się atakami cybernetycznymi. Korzystaj z wbudowanej analizy behawioralnej i uczenia maszynowego, aby identyfikować ataki i wypróbować programy wykorzystujące zero dni. Monitoruj sieci, maszyny i usługi w chmurze pod kątem ataków przychodzących i działań po naruszeniu. Usprawnij badanie przy użyciu interaktywnych narzędzi i kontekstowej analizy zagrożeń.
- **Skanowanie w poszukiwaniu maszyn wirtualnych** — łatwo Wdrażaj skaner na wszystkich maszynach wirtualnych, które zapewniają najbardziej zaawansowane rozwiązanie do zarządzania lukami w zabezpieczeniach. Wyświetlaj, badaj i Koryguj wyniki bezpośrednio w Security Center. 
- **Kontrola dostępu i aplikacji** — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie listy dozwolonychowych zaleceń dotyczących uczenia maszynowego dostosowanych do określonych obciążeń. Ogranicz obszar ataków sieci z dostępem just-in-Time do portów zarządzania na maszynach wirtualnych platformy Azure. Radykalnie zmniejsza to narażenie na rozżycie i inne ataki sieciowe.
- **Funkcje zabezpieczeń kontenerów** — Skorzystaj z funkcji zarządzania lukami w zabezpieczeniach i ochrony przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów. Po włączeniu zasobu rejestrów kontenerów może upłynąć do 12hrs do momentu włączenia wszystkich funkcji. Opłaty są oparte na liczbie unikatowych obrazów kontenerów, które zostały wypchnięte do dołączonego rejestru. Po jednokrotnym przeskanowaniu obrazu nie zostanie on rozliczony ponownie, chyba że zostanie on zmodyfikowany i wypychany jeszcze raz. 




## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzono cenniki dotyczące Security Center. Aby dowiedzieć się więcej na temat zwiększonych zabezpieczeń warstwy standardowej i zaawansowanej ochrony przed zagrożeniami, zobacz:

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](threat-protection.md)
- [Kontrola dostępu just in Time do maszyny wirtualnej](security-center-just-in-time.md)
- [Omówienie zabezpieczeń kontenera](container-security.md)
- [Szczegóły cennika w wybranej walucie i zgodnie z Twoim regionem](https://azure.microsoft.com/pricing/details/security-center/)