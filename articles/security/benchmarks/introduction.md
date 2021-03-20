---
title: Wprowadzenie do testu porównawczego zabezpieczeń platformy Azure
description: Wprowadzenie do testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369195"
---
# <a name="azure-security-benchmark-introduction"></a>Wprowadzenie do usługi Azure Security test

Nowe usługi i funkcje są udostępniane codziennie na platformie Azure, deweloperzy szybko publikują nowe aplikacje w chmurze skompilowane na tych usługach, a atakujący zawsze szukają nowych sposobów korzystania z nieprawidłowo skonfigurowanych zasobów. Chmura jest przenoszona szybko, deweloperzy szybko przesuwają się, a osoby atakujące są zawsze w ruchu. Jak kontynuować i upewnić się, że wdrożenia w chmurze są bezpieczne? Jakie są rozwiązania w zakresie zabezpieczeń dotyczące systemów chmurowych innych niż systemy lokalne? Jak monitorować spójność w wielu niezależnych zespołach programistycznych?

Firma Microsoft stwierdziła, że korzystanie z *testów porównawczych zabezpieczeń* może pomóc szybko zabezpieczyć wdrożenia w chmurze. Zalecenia dotyczące testów porównawczych od dostawcy usług w chmurze stanowią punkt wyjścia do wybierania określonych ustawień konfiguracji zabezpieczeń w danym środowisku i umożliwiają szybkie zmniejszenie ryzyka dla organizacji.

Test porównawczy zabezpieczeń platformy Azure obejmuje kolekcję zaleceń dotyczących zabezpieczeń o dużym wpływie, których można użyć w celu zabezpieczenia usług używanych na platformie Azure:

- **Kontrolki zabezpieczeń**: te zalecenia są zwykle stosowane w ramach dzierżawy platformy Azure i usług platformy Azure. Każde zalecenie określa listę uczestników, którzy zwykle są zaangażowani w planowanie, zatwierdzanie lub implementację testu porównawczego. 
- **Linie bazowe usługi**: te elementy są stosowane do poszczególnych usług platformy Azure w celu zapewnienia zaleceń dotyczących konfiguracji zabezpieczeń tej usługi.

## <a name="implement-the-azure-security-benchmark"></a>Implementowanie testu porównawczego zabezpieczeń platformy Azure
- **Zaplanuj** implementację testu porównawczego zabezpieczeń platformy Azure, przeglądając [dokumentację](overview.md) kontroli przedsiębiorstwa i linii bazowych specyficznych dla usługi w celu zaplanowania struktury kontroli i sposobu mapowania na wskazówki, takie jak CIS (Controls v 7.1) i NIST (SP 800-53).
- **Monitoruj** zgodność ze stanem testu porównawczego zabezpieczeń platformy Azure (i innymi zestawami kontrolnymi) za pomocą [pulpitu nawigacyjnego zgodność z przepisami](../../security-center/security-center-compliance-dashboard.md)Azure Security Center.
- **Ustanów guardrails** do automatyzowania bezpiecznych konfiguracji i wymuszania zgodności z usługą Azure Security test (i innymi wymaganiami w Twojej organizacji) przy użyciu planów i Azure Policy platformy Azure.
 
Zwróć uwagę, że test porównawczy zabezpieczeń platformy Azure v2 jest wyrównany z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń firmy Microsoft](/security/compass/microsoft-security-compass-introduction) (dawniej jest to Twój kompas o zabezpieczeniach platformy Azure), aby test porównawczy zabezpieczeń platformy Azure był jednym skonsolidowanym widokiem zaleceń dotyczących zabezpieczeń

## <a name="common-use-cases"></a>Typowe przypadki użycia

Usługa Azure Security test jest często używana do rozwiązywania tych typowych wyzwań dla klientów lub partnerów usług, którzy są:
- Nowość na platformę Azure i szukamy najlepszych rozwiązań w zakresie zabezpieczeń w celu zapewnienia bezpiecznego wdrożenia.
- Zwiększenie bezpieczeństwa stan istniejących wdrożeń platformy Azure w celu określenia priorytetów najważniejszych zagrożeń i środków zaradczych.
- Zatwierdzanie usług platformy Azure do użycia przez technologie i użycie w firmie w celu spełnienia określonych wytycznych dotyczących zabezpieczeń.
- Spełnianie wymagań prawnych dla klientów, którzy są od instytucji rządowych lub branżowo regulowanej, takich jak finanse i opieka medyczna (lub dostawcy usług, którzy muszą tworzyć systemy dla klientów). Ci klienci muszą upewnić się, że konfiguracja platformy Azure spełnia funkcje zabezpieczeń określone w środowisku branżowym, takim jak CIS, NIST lub PCI. Usługa Azure Security test jest wydajnym podejściem do kontroli już wstępnie mapowanych na te testy branżowe.

## <a name="terminology"></a>Terminologia

Warunki "kontrola", "wzorzec" i "linia bazowa" są często używane w dokumentacji usługi Azure Security test i ważne jest, aby zrozumieć, w jaki sposób platforma Azure korzysta z tych warunków.


| Okres | Opis | Przykład |
|--|--|--|
| Kontrola | Kontrolka to ogólny opis funkcji lub działania, które muszą zostać rozkierowane i nie jest specyficzny dla technologii lub implementacji. | Ochrona danych jest jedną z formantów zabezpieczeń. Ta kontrolka zawiera określone akcje, które należy rozwiązać, aby zapewnić ochronę danych. |
| Punkt odniesienia | Wzorzec porównawczy zawiera zalecenia dotyczące zabezpieczeń dla określonej technologii, takich jak Azure. Zalecenia są podzielone według kontrolki, do której należą. | Test porównawczy zabezpieczeń platformy Azure obejmuje zalecenia dotyczące zabezpieczeń specyficzne dla platformy Azure |
| Punkt odniesienia | Podstawą jest implementacja testu porównawczego w poszczególnych usługach platformy Azure. Każda organizacja podejmuje zalecenie dotyczące testów porównawczych i odpowiednie konfiguracje są zbędne w zakresie implementacji platformy Azure. | Firma Contoso szuka funkcji zabezpieczeń Azure SQL, wykonując konfigurację zalecaną w punkcie odniesienia zabezpieczeń usługi Azure SQL.

Prosimy o opinię na temat testu porównawczego zabezpieczeń platformy Azure! Zachęcamy do podania komentarzy w obszarze opinii poniżej. Jeśli wolisz udostępnić dane wejściowe bardziej prywatnie z zespołem usługi Azure Security Testal, Witamy w wypełnianiu formularza https://aka.ms/AzSecBenchmark
