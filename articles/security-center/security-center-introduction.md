---
title: Co to jest Azure Security Center? | Microsoft Docs
description: Na tej stronie opisano Security Center kluczowe korzyści — odnajdywanie stanu zabezpieczeń i ulepszanie go z pokryciem zasobów w chmurze i lokalnych.
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
ms.openlocfilehash: c4861192c7f2bbfb2a51e19b88daee45b501949b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634774"
---
# <a name="what-is-azure-security-center"></a>Co to jest usługa Azure Security Center?

Usługa Azure Security Center to ujednolicony system zarządzania bezpieczeństwem infrastruktury. Ma on za zadanie zwiększenie poziomu bezpieczeństwa centrów danych i zapewnienie zaawansowanej ochrony przed zagrożeniami w przypadku obciążeń hybrydowych w chmurze (zarówno na platformie Azure, jak i poza nią) oraz w środowisku lokalnym.

Utrzymywanie bezpieczeństwa zasobów to wspólna odpowiedzialność dostawcy usług w chmurze, platformy Azure i klienta. Podczas przechodzenia do chmury trzeba upewnić się, że obciążenia będą bezpieczne. Trzeba też pamiętać, że przejście na model IaaS (infrastruktura jako usługa) to większa odpowiedzialność po stronie klienta niż w przypadku korzystania z modeli PaaS (platforma jako usługa) i SaaS (oprogramowanie jako usługa). Usługa Azure Security Center zapewnia narzędzia potrzebne do zwiększenia bezpieczeństwa sieci, zabezpieczenia usług i zapewnienia maksymalnego poziomu bezpieczeństwa.

Usługa Azure Security Center rozwiązuje trzy najbardziej pilne problemy związane z zabezpieczeniami:

-   **Szybkie zmienianie obciążeń** — jest to siła i wyzwanie dla chmury. Z jednej strony użytkownicy końcowi mają możliwość robienia więcej. Z drugiej jednak pojawia się problem — jak sprawić, by ciągle zmieniające się usługi, które tworzą i wykorzystują użytkownicy, spełniały standardy bezpieczeństwa i były zgodne z najlepszymi rozwiązaniami dotyczącymi bezpieczeństwa?

-   **Coraz bardziej zaawansowane ataki** — wszędzie tam, gdzie pracujesz, ataki stają się coraz bardziej wyrafinowane. Trzeba zabezpieczyć pracę w chmurze publicznej, która jest połączona z Internetem, co sprawia, że jest jeszcze bardziej narażona na ataki w przypadku braku zastosowania najlepszych zabezpieczeń.

-   **Trudno jest znaleźć pracowników mających odpowiednie umiejętności w zakresie zabezpieczeń** — liczba alertów bezpieczeństwa i systemów zgłaszania alertów przewyższa liczbę administratorów z umiejętnościami i doświadczeniem koniecznymi do zapewniania bezpieczeństwa takiemu środowisku. Bycie na bieżąco z najnowszymi atakami jest dużym wyzwaniem. Nie można stać w miejscu, gdy sytuacja w zakresie zabezpieczeń ciągle się zmienia.

Aby sprostać tym wyzwaniom, usługa Security Center oferuje narzędzia umożliwiające:

-   **Wzmocnienie stan zabezpieczeń**: Security Center ocenia środowisko i umożliwia zrozumienie stanu zasobów oraz tego, czy są one bezpieczne.

-   **Ochrona przed zagrożeniami**: Security Center ocenia obciążenia i wywołuje zalecenia dotyczące zapobiegania zagrożeniom oraz alerty zabezpieczeń.

-   **Szybciej osiągnij bezpieczeństwo**: W usłudze Security Center wszystko jest wykonywane z szybkością chmury. Dzięki natywnej integracji wdrożenie usługi Security Center jest łatwe i zapewnia automatyczne aprowizowanie i ochronę w ramach usług platformy Azure.

> [!NOTE]
> Ta usługa obsługuje usługę [Azure Lighthouse](../lighthouse/overview.md), dzięki której dostawcy usług mogą logować się do własnej dzierżawy w celu zarządzania subskrypcjami i grupami zasobów delegowanymi przez klientów. W przypadku scenariuszy Azure Security Center należy delegować subskrypcję, a nie pojedyncze grupy zasobów.

## <a name="architecture"></a>Architektura

Ponieważ Security Center jest natywnie częścią platformy Azure, usługi PaaS na platformie Azure — w tym Service Fabric, SQL Database, wystąpienie zarządzane SQL i konta magazynu — są monitorowane i chronione przez Security Center bez konieczności wdrażania.

Ponadto program Security Center chroni serwery spoza platformy Azure i maszyny wirtualne w chmurze lub lokalnie, w przypadku serwerów z systemami Windows i Linux, instalując na nich agenta Log Analytics. Maszyny wirtualne Azure są automatycznie aprowizowane w usłudze Security Center.

Zdarzenia zbierane od agentów i z platformy Azure są skorelowane w aparacie analizy zabezpieczeń, aby zapewnić dostosowane zalecenia (zadania z ograniczeniami funkcjonalności), które należy wykonać, aby upewnić się, że obciążenia są bezpieczne i alerty zabezpieczeń. Alerty należy badać jak najszybciej, aby upewnić się, że nie dochodzi do złośliwych ataków na działające obciążenia.

Po włączeniu Security Center zasady zabezpieczeń wbudowane w Security Center zostaną odzwierciedlone w Azure Policy jako wbudowana inicjatywa w kategorii Security Center. Wbudowana inicjatywa jest automatycznie przypisywana do wszystkich Security Center zarejestrowanych subskrypcji (niezależnie od tego, czy usługa Azure Defender jest włączona). Wbudowana inicjatywa zawiera tylko zasady inspekcji. Aby uzyskać więcej informacji na temat zasad usługi Security Center w usłudze Azure Policy, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Zwiększanie poziomu bezpieczeństwa

Usługa Azure Security Center umożliwia zwiększanie poziomu bezpieczeństwa. Oznacza to, że będzie ona pomagać w identyfikowaniu i wykonywaniu zalecanych jako najlepsze rozwiązania zadań polegających na zwiększaniu poziomu zabezpieczeń, a także we wdrażaniu tych zadań na maszynach, w usługach związanych z danymi i w aplikacjach. Obejmuje to zarządzanie zasadami zabezpieczeń i ich wymuszanie oraz upewnienie się, że maszyny wirtualne na platformie Azure, serwery spoza Azure oraz usługi PaaS platformy Azure je spełniają. Security Center zapewnia narzędzia dające syntetyczne informacje na temat obciążeń ze szczególnym uwzględnieniem stanu zabezpieczeń sieci. 

### <a name="manage-organization-security-policy-and-compliance"></a>Zarządzanie zasadami zabezpieczeń oraz zgodnością w organizacji

Podstawową sprawą jest zapewnienie, że działające obciążenia są bezpieczne. Aby to osiągnąć, trzeba wdrożyć dostosowane zasady zabezpieczeń. Ze względu na to, że wszystkie zasady w Security Center są zbudowane na podstawie Azure Policy kontrolek, uzyskuje się pełen zakres i elastyczność **rozwiązania światowej klasy**. W usłudze Security Center można ustawić uruchamianie zasad bezpieczeństwa na poziomie grupy zarządzania, subskrypcji lub nawet dla jednego dzierżawcy.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Strona zarządzania zasadami":::

Security Center pomaga **identyfikować subskrypcje IT w tle**. Sprawdzając subskrypcje oznaczone na pulpicie nawigacyjnym etykietą **Nieobjęte**, można dowiedzieć się natychmiast, że zostały utworzone nowe subskrypcje, i upewnić się, że są objęte zasadami oraz chronione przez usługę Azure Security Center.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Pulpit nawigacyjny z zasadami w usłudze Security Center":::

### <a name="continuous-assessments"></a>Ciągłe oceny

Usługa Security Center ciągle wykrywa nowe zasoby wdrażane w różnych obciążeniach i ocenia, czy są one skonfigurowane zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Zasoby, które nie spełniają tego wymogu, są oznaczane, a przy tym generowana jest lista zaleceń z określonymi priorytetami. Zalecenia wskazują, co należy zrobić, aby chronić maszyny. Ta lista zaleceń jest włączana i wspierana przez [usługę Azure Security test](../security/benchmarks/introduction.md), czyli zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są stosowane w oparciu o typowe struktury zgodności. Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

Aby ułatwić zrozumienie, jak ważne są poszczególne zalecenia, Security Center grupuje zalecenia do kontroli zabezpieczeń i dodaje **bezpieczną wartość oceny** do każdej kontrolki. Jest to kluczowe znaczenie w umożliwieniu **określania priorytetów pracy w zabezpieczeniach**.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Wskaźnik bezpieczeństwa w usłudze Security Center":::

### <a name="network-map"></a>Mapa sieci

Jednym z najbardziej zaawansowanych narzędzi Security Center zapewnia stałe monitorowanie stanu zabezpieczeń sieci to **Mapa sieciowa**. Mapa umożliwia wyświetlenie topologii obciążeń, aby sprawdzić, czy każdy węzeł jest odpowiednio skonfigurowany. Można zobaczyć, jak węzły są połączone, co pomaga blokować niechciane połączenia, które mogą potencjalnie ułatwić osobie atakującej dostanie się do sieci.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Mapa sieci w usłudze Security Center":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optymalizacja i zwiększanie bezpieczeństwa dzięki konfigurowaniu zalecanych środków kontroli

Główną wartość usługi Azure Security Center stanowią zalecenia. Zalecenia są dostosowane do określonych problemów z bezpieczeństwem wykrytych w obciążeniach klienta. Usługa Security Center wykonuje pracę administratora zabezpieczeń, nie tylko znajdując luki w zabezpieczeniach, ale też udostępniając szczegółowe instrukcje na temat sposobu ich usunięcia.

W ten sposób usługa Security Center umożliwia nie tylko ustanawianie zasad zabezpieczeń, ale też zastosowanie bezpiecznych standardów konfiguracji we wszystkich zasobach.

Zalecenia pomagają zmniejszyć obszar ataków w każdym z zasobów. Obejmują one maszyny wirtualne platformy Azure, serwery spoza platformy Azure oraz usługi PaaS platformy Azure takie jak konta SQL i konta magazynu. Każdy rodzaj zasobu jest oceniany oddzielnie i ma swoje własne standardy.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Przykład zaleceń usługi Security Center":::

## <a name="protect-against-threats"></a>Ochrona przed zagrożeniami

Ochrona przed zagrożeniami w usłudze Security Center umożliwia wykrywanie zagrożeń i zapobieganie im w warstwie infrastruktury jako usługi (IaaS), na serwerach spoza platformy Azure, jak również w przypadku platform działających jako usługa (PaaS) na platformie Azure.

Ochrona przed zagrożeniami w usłudze Security Center obejmuje kompleksową analizę łańcucha ataku, która automatycznie koreluje alerty w danym środowisku na podstawie modelu łańcucha ataku cybernetycznego. Dzięki temu uzyskujesz lepszy wgląd we wszystkie etapy ataku, jego punkt początkowy oraz wpływ na zasoby.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista alertów zabezpieczeń Azure Security Center":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Integracja z usługą Microsoft Defender dla punktu końcowego

Usługa Azure Defender dla serwerów zawiera automatyczną, natywną integrację z usługą Microsoft Defender dla punktu końcowego. Dowiedz się więcej, [Chroń punkty końcowe przy użyciu zintegrowanego rozwiązania EDR Security Center: Microsoft Defender for Endpoint](security-center-wdatp.md)


### <a name="protect-paas"></a>Ochrona usług PaaS

Usługa Security Center ułatwia wykrywanie zagrożeń w usługach PaaS platformy Azure. Możesz wykrywać zagrożenia dotyczące usług platformy Azure, w tym usługi Azure App Service, Azure SQL, konta usługi Azure Storage i innych usług danych. Możesz również korzystać z natywnej integracji z funkcją analizy zachowań użytkowników i jednostek (UEBA) dostępną w usłudze Microsoft Cloud App Security w celu wykrywania anomalii w dziennikach aktywności platformy Azure.

### <a name="block-brute-force-attacks"></a>Blokowanie ataków siłowych

Usługa Security Center pomaga ograniczyć narażenie na ataki siłowe. Ograniczenie dostępu do portów maszyny wirtualnej i korzystanie z dostępu just-in-time do maszyny wirtualnej umożliwia zwiększenie bezpieczeństwa sieci dzięki zapobieganiu niepotrzebnemu dostępowi. Można ustawić zasady bezpiecznego dostępu na wybranych portach tylko dla autoryzowanych użytkowników, dozwolonych zakresów źródłowych adresów IP lub określonych adresów IP oraz przez określony czas.

### <a name="protect-data-services"></a>Ochrona usług danych

Usługa Security Center obejmuje funkcje, które ułatwiają wykonywanie automatycznej klasyfikacji danych w Azure SQL. Możesz także uzyskać oceny potencjalnych luk w zabezpieczeniach w usługach Azure SQL i Storage oraz zalecenia dotyczące sposobu rozwiązania tych problemów.

## <a name="get-secure-faster"></a>Szybsze osiągnięcie bezpieczeństwa

Natywna integracja platformy Azure (w tym Azure Policy i dzienników Azure Monitor) z bezproblemową integracją z innymi rozwiązaniami zabezpieczeń firmy Microsoft, takimi jak Microsoft Cloud App Security i Microsoft Defender dla punktów końcowych, upewnij się, że rozwiązanie zabezpieczeń jest kompleksowe, a także proste do dołączenia.

Ponadto można rozszerzyć pełne rozwiązanie poza platformę Azure na obciążenia działające w innych chmurach, jak i w lokalnych centrach danych.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatyczne wykrywanie i dodawanie zasobów platformy Azure

Usługa Security Center zapewnia bezproblemową natywną integrację z platformą Azure i z zasobami Azure. Oznacza to, że można zebrać wszystkie ustawienia zabezpieczeń obejmujące zasady usługi Azure Policy i wbudowane zasady usługi Security Center we wszystkich zasobach Azure oraz mieć pewność, że w przypadku nowych zasobów od razu po ich utworzeniu na platformie Azure te zasady są automatycznie stosowane.

Rozbudowane kolekcje dzienników — dzienniki z systemów Windows i Linux są wykorzystywane przez aparat analizy zabezpieczeń i używane do tworzenia zaleceń i alertów.

## <a name="next-steps"></a>Następne kroki

- Do rozpoczęcia pracy z usługą Security Center jest wymagana subskrypcja platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

- Bezpłatna warstwa cenowa Security Center jest włączona we wszystkich bieżących subskrypcjach platformy Azure, gdy po raz pierwszy zostanie odwiedzony pulpit nawigacyjny Azure Security Center w Azure Portal lub włączono programowo za pośrednictwem interfejsu API. Aby skorzystać z zaawansowanych możliwości zarządzania zabezpieczeniami i wykrywania zagrożeń, należy włączyć usługę Azure Defender. Usługę Azure Defender można bezpłatnie próbować przez 30 dni. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

- Jeśli wszystko jest gotowe do włączenia usługi Azure Defender, [Szybki Start: konfigurowanie Azure Security Center](security-center-get-started.md) przeprowadzi Cię przez kolejne kroki.