---
title: Co to jest Azure Security Center? | Microsoft Docs
description: Na tej stronie Security Center najważniejsze korzyści związane z zabezpieczeniami — odnajdywanie stanu zabezpieczeń i ulepszanie go przy użyciu zasobów w chmurze i lokalnych.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 741cd68145b262c1f200ced9a7f28b25673b6925
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738922"
---
# <a name="what-is-azure-security-center"></a>Co to jest usługa Azure Security Center?

Usługa Azure Security Center to ujednolicony system zarządzania bezpieczeństwem infrastruktury. Ma on za zadanie zwiększenie poziomu bezpieczeństwa centrów danych i zapewnienie zaawansowanej ochrony przed zagrożeniami w przypadku obciążeń hybrydowych w chmurze (zarówno na platformie Azure, jak i poza nią) oraz w środowisku lokalnym.

Utrzymywanie bezpieczeństwa zasobów to wspólna odpowiedzialność dostawcy usług w chmurze, platformy Azure i klienta. Podczas przechodzenia do chmury trzeba upewnić się, że obciążenia będą bezpieczne. Trzeba też pamiętać, że przejście na model IaaS (infrastruktura jako usługa) to większa odpowiedzialność po stronie klienta niż w przypadku korzystania z modeli PaaS (platforma jako usługa) i SaaS (oprogramowanie jako usługa). Usługa Azure Security Center zapewnia narzędzia potrzebne do zwiększenia bezpieczeństwa sieci, zabezpieczenia usług i zapewnienia maksymalnego poziomu bezpieczeństwa.

Usługa Azure Security Center rozwiązuje trzy najbardziej pilne problemy związane z zabezpieczeniami:

-   **Szybko zmieniające się obciążenia** — jest to zarówno siła, jak i wyzwanie chmury. Z jednej strony użytkownicy końcowi mają możliwość robienia więcej. Z drugiej jednak pojawia się problem — jak sprawić, by ciągle zmieniające się usługi, które tworzą i wykorzystują użytkownicy, spełniały standardy bezpieczeństwa i były zgodne z najlepszymi rozwiązaniami dotyczącymi bezpieczeństwa?

-   **Coraz bardziej zaawansowane ataki** — wszędzie tam, gdzie pracujesz, ataki stają się coraz bardziej wyrafinowane. Trzeba zabezpieczyć pracę w chmurze publicznej, która jest połączona z Internetem, co sprawia, że jest jeszcze bardziej narażona na ataki w przypadku braku zastosowania najlepszych zabezpieczeń.

-   **Trudno jest znaleźć pracowników mających odpowiednie umiejętności w zakresie zabezpieczeń** — liczba alertów bezpieczeństwa i systemów zgłaszania alertów przewyższa liczbę administratorów z umiejętnościami i doświadczeniem koniecznymi do zapewniania bezpieczeństwa takiemu środowisku. Bycie na bieżąco z najnowszymi atakami jest dużym wyzwaniem. Nie można stać w miejscu, gdy sytuacja w zakresie zabezpieczeń ciągle się zmienia.

Aby sprostać tym wyzwaniom, usługa Security Center oferuje narzędzia umożliwiające:

-   **Zwiększenie stanu zabezpieczeń:** Security Center ocenia środowisko i umożliwia zrozumienie stanu zasobów oraz tego, czy są one bezpieczne.

-   **Ochrona przed zagrożeniami:** Security Center ocenia obciążenia i zgłasza zalecenia dotyczące zapobiegania zagrożeniom oraz alerty zabezpieczeń.

-   **Szybciej osiągnij bezpieczeństwo**: W usłudze Security Center wszystko jest wykonywane z szybkością chmury. Dzięki natywnej integracji wdrożenie usługi Security Center jest łatwe i zapewnia automatyczne aprowizowanie i ochronę w ramach usług platformy Azure.

> [!NOTE]
> Ta usługa obsługuje [Azure Lighthouse](../lighthouse/overview.md), co umożliwia dostawcom usług logowanie się do własnej dzierżawy w celu zarządzania subskrypcjami i grupami zasobów delegowanymi przez klientów. W Azure Security Center scenariuszach subskrypcja musi być delegowana, a nie poszczególne grupy zasobów.

## <a name="architecture"></a>Architektura

Ponieważ usługa Security Center jest natywnie częścią platformy Azure, usługi PaaS na platformie Azure — w tym Service Fabric, SQL Database, SQL Managed Instance i konta magazynu — są monitorowane i chronione przez usługę Security Center bez konieczności wdrażania.

Ponadto usługa Security Center serwery spoza platformy Azure i maszyny wirtualne w chmurze lub lokalnie dla serwerów z systemami Windows i Linux, instalując na nich agenta usługi Log Analytics. Maszyny wirtualne Azure są automatycznie aprowizowane w usłudze Security Center.

Zdarzenia zbierane z agentów i z platformy Azure są skorelowane z aparatem analizy zabezpieczeń w celu zapewnienia dostosowanych zaleceń (zadań wzmacniania zabezpieczeń), które należy wykonać, aby upewnić się, że obciążenia są bezpieczne, oraz alertów zabezpieczeń. Alerty należy badać jak najszybciej, aby upewnić się, że nie dochodzi do złośliwych ataków na działające obciążenia.

Po włączeniu Security Center zasady zabezpieczeń wbudowane w usługę Security Center są odzwierciedlane w programie Azure Policy jako wbudowana inicjatywa w ramach Security Center kategorii. Wbudowana inicjatywa jest automatycznie przypisywana do wszystkich Security Center zarejestrowanych subskrypcji (niezależnie od tego, czy mają Azure Defender włączone). Wbudowana inicjatywa zawiera tylko zasady inspekcji. Aby uzyskać więcej informacji na temat zasad usługi Security Center w usłudze Azure Policy, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Zwiększanie poziomu bezpieczeństwa

Usługa Azure Security Center umożliwia zwiększanie poziomu bezpieczeństwa. Oznacza to, że będzie ona pomagać w identyfikowaniu i wykonywaniu zalecanych jako najlepsze rozwiązania zadań polegających na zwiększaniu poziomu zabezpieczeń, a także we wdrażaniu tych zadań na maszynach, w usługach związanych z danymi i w aplikacjach. Obejmuje to zarządzanie zasadami zabezpieczeń i ich wymuszanie oraz upewnienie się, że maszyny wirtualne na platformie Azure, serwery spoza Azure oraz usługi PaaS platformy Azure je spełniają. Security Center zapewnia narzędzia dające syntetyczne informacje na temat obciążeń ze szczególnym uwzględnieniem stanu zabezpieczeń sieci. 

### <a name="manage-organization-security-policy-and-compliance"></a>Zarządzanie zasadami zabezpieczeń oraz zgodnością w organizacji

Podstawową sprawą jest zapewnienie, że działające obciążenia są bezpieczne. Aby to osiągnąć, trzeba wdrożyć dostosowane zasady zabezpieczeń. Ponieważ wszystkie zasady w Security Center są zbudowane na podstawie kontrolek Azure Policy, zapewniasz pełny zakres i elastyczność światowej **klasy rozwiązania zasad.** W usłudze Security Center można ustawić uruchamianie zasad bezpieczeństwa na poziomie grupy zarządzania, subskrypcji lub nawet dla jednego dzierżawcy.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Strona Zarządzania zasadami":::

Security Center pomaga **identyfikować subskrypcje it w tle.** Sprawdzając subskrypcje oznaczone na pulpicie nawigacyjnym etykietą **Nieobjęte**, można dowiedzieć się natychmiast, że zostały utworzone nowe subskrypcje, i upewnić się, że są objęte zasadami oraz chronione przez usługę Azure Security Center.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Pulpit nawigacyjny z zasadami w usłudze Security Center":::

### <a name="continuous-assessments"></a>Ciągłe oceny

Usługa Security Center ciągle wykrywa nowe zasoby wdrażane w różnych obciążeniach i ocenia, czy są one skonfigurowane zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Zasoby, które nie spełniają tego wymogu, są oznaczane, a przy tym generowana jest lista zaleceń z określonymi priorytetami. Zalecenia wskazują, co należy zrobić, aby chronić maszyny. Ta lista zaleceń jest włączona i obsługiwana przez test porównawczy zabezpieczeń platformy [Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)— zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na wspólnych platformach zgodności, który został przez firmę Microsoft określony na platformie Azure. Ten szeroko stosowany test porównawczy opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) z naciskiem na zabezpieczenia skoncentrowane na chmurze.

Aby ułatwić zrozumienie, jak ważne jest każde zalecenie dla ogólnego stanu zabezpieczeń, Security Center grupuje zalecenia w mechanizmy kontroli zabezpieczeń i dodaje wartość bezpiecznego wyniku do każdej kontrolki.  Ma to kluczowe znaczenie dla umożliwienia **ustalania priorytetów pracy nad zabezpieczeniami.**

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Wskaźnik bezpieczeństwa w usłudze Security Center":::

### <a name="network-map"></a>Mapa sieci

Jednym z najbardziej zaawansowanych narzędzi dostępnych Security Center do ciągłego monitorowania stanu zabezpieczeń sieci jest **mapa sieci**. Mapa umożliwia wyświetlenie topologii obciążeń, aby sprawdzić, czy każdy węzeł jest odpowiednio skonfigurowany. Można zobaczyć, jak węzły są połączone, co pomaga blokować niechciane połączenia, które mogą potencjalnie ułatwić osobie atakującej dostanie się do sieci.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Mapa sieci w usłudze Security Center":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optymalizacja i zwiększanie bezpieczeństwa dzięki konfigurowaniu zalecanych środków kontroli

Główną wartość usługi Azure Security Center stanowią zalecenia. Zalecenia są dostosowane do określonych problemów z bezpieczeństwem wykrytych w obciążeniach klienta. Usługa Security Center wykonuje pracę administratora zabezpieczeń, nie tylko znajdując luki w zabezpieczeniach, ale też udostępniając szczegółowe instrukcje na temat sposobu ich usunięcia.

W ten sposób usługa Security Center umożliwia nie tylko ustanawianie zasad zabezpieczeń, ale też zastosowanie bezpiecznych standardów konfiguracji we wszystkich zasobach.

Zalecenia pomagają zmniejszyć obszar ataków w każdym z zasobów. Obejmują one maszyny wirtualne platformy Azure, serwery spoza platformy Azure oraz usługi PaaS platformy Azure takie jak konta SQL i konta magazynu. Każdy rodzaj zasobu jest oceniany oddzielnie i ma swoje własne standardy.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Przykład zaleceń usługi Security Center":::

## <a name="protect-against-threats"></a>Ochrona przed zagrożeniami

Ochrona przed zagrożeniami w usłudze Security Center umożliwia wykrywanie zagrożeń i zapobieganie im w warstwie infrastruktury jako usługi (IaaS), na serwerach spoza platformy Azure, jak również w przypadku platform działających jako usługa (PaaS) na platformie Azure.

Ochrona przed zagrożeniami w usłudze Security Center obejmuje kompleksową analizę łańcucha ataku, która automatycznie koreluje alerty w danym środowisku na podstawie modelu łańcucha ataku cybernetycznego. Dzięki temu uzyskujesz lepszy wgląd we wszystkie etapy ataku, jego punkt początkowy oraz wpływ na zasoby.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center alertów zabezpieczeń":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Integracja z usługą Microsoft Defender dla punktu końcowego

Azure Defender dla serwerów obejmuje automatyczną, natywną integrację z usługą Microsoft Defender for Endpoint. Dowiedz się [więcej: Protect your endpoints with Security Center's integrated EDR solution: Microsoft Defender for Endpoint (Ochrona](security-center-wdatp.md) punktów końcowych za pomocą zintegrowanego rozwiązania EDR firmy Security Center: Microsoft Defender for Endpoint


### <a name="protect-paas"></a>Ochrona usług PaaS

Usługa Security Center ułatwia wykrywanie zagrożeń w usługach PaaS platformy Azure. Możesz wykrywać zagrożenia dotyczące usług platformy Azure, w tym usługi Azure App Service, Azure SQL, konta usługi Azure Storage i innych usług danych. Możesz również korzystać z natywnej integracji z funkcją analizy zachowań użytkowników i jednostek (UEBA) dostępną w usłudze Microsoft Cloud App Security w celu wykrywania anomalii w dziennikach aktywności platformy Azure.

### <a name="block-brute-force-attacks"></a>Blokowanie ataków siłowych

Usługa Security Center pomaga ograniczyć narażenie na ataki siłowe. Ograniczenie dostępu do portów maszyny wirtualnej i korzystanie z dostępu just-in-time do maszyny wirtualnej umożliwia zwiększenie bezpieczeństwa sieci dzięki zapobieganiu niepotrzebnemu dostępowi. Można ustawić zasady bezpiecznego dostępu na wybranych portach tylko dla autoryzowanych użytkowników, dozwolonych zakresów źródłowych adresów IP lub określonych adresów IP oraz przez określony czas.

### <a name="protect-data-services"></a>Ochrona usług danych

Usługa Security Center obejmuje funkcje, które ułatwiają wykonywanie automatycznej klasyfikacji danych w Azure SQL. Możesz także uzyskać oceny potencjalnych luk w zabezpieczeniach w usługach Azure SQL i Storage oraz zalecenia dotyczące sposobu rozwiązania tych problemów.

## <a name="get-secure-faster"></a>Szybsze osiągnięcie bezpieczeństwa

Natywna integracja z platformą Azure (w tym dzienniki usług Azure Policy i Azure Monitor) w połączeniu z bezproblemową integracją z innymi rozwiązaniami zabezpieczeń firmy Microsoft, takimi jak Microsoft Cloud App Security i Usługa Microsoft Defender dla punktu końcowego, pomaga upewnić się, że twoje rozwiązanie zabezpieczeń jest kompleksowe, a także łatwe do dołączania i śledzenia.

Ponadto można rozszerzyć pełne rozwiązanie poza platformę Azure na obciążenia działające w innych chmurach, jak i w lokalnych centrach danych.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatyczne wykrywanie i dodawanie zasobów platformy Azure

Usługa Security Center zapewnia bezproblemową natywną integrację z platformą Azure i z zasobami Azure. Oznacza to, że można zebrać wszystkie ustawienia zabezpieczeń obejmujące zasady usługi Azure Policy i wbudowane zasady usługi Security Center we wszystkich zasobach Azure oraz mieć pewność, że w przypadku nowych zasobów od razu po ich utworzeniu na platformie Azure te zasady są automatycznie stosowane.

Rozbudowane kolekcje dzienników — dzienniki z systemów Windows i Linux są wykorzystywane przez aparat analizy zabezpieczeń i używane do tworzenia zaleceń i alertów.

## <a name="next-steps"></a>Następne kroki

- Do rozpoczęcia pracy z usługą Security Center jest wymagana subskrypcja platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

- Security Center bezpłatna warstwa cenowa usługi Security Center jest włączona we wszystkich bieżących subskrypcjach platformy Azure po pierwszym użyciu pulpitu nawigacyjnego usługi Azure Security Center w usłudze Azure Portal lub jeśli jest włączona programowo za pośrednictwem interfejsu API. Aby korzystać z zaawansowanych funkcji zarządzania zabezpieczeniami i wykrywania zagrożeń, należy włączyć Azure Defender. Azure Defender bezpłatnie przez 30 dni. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

- Jeśli chcesz teraz włączyć tę funkcję, Azure Defender Szybki [start:](security-center-get-started.md) konfigurowanie Azure Security Center krok po instrukcje.