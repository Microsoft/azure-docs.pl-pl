---
title: Podstawa zabezpieczeń platformy Azure dla Content Delivery Network
description: Linia bazowa zabezpieczeń Content Delivery Network zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-cdn
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64731ce443ddd806402efd33fe22300ab8362d18
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663649"
---
# <a name="azure-security-baseline-for-content-delivery-network"></a>Podstawa zabezpieczeń platformy Azure dla Content Delivery Network

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Content Delivery Network. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Content Delivery Network. **Kontrolki** nie mają zastosowania do Content Delivery Network zostały wykluczone.

Aby dowiedzieć się, jak Content Delivery Network całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Content Delivery Network pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Wiadomości błyskawiczne — 6: Ogranicz dostęp do zasobów platformy Azure na podstawie warunków

**Wskazówki**: ograniczanie dostępu do zawartości na Content Delivery Network według kraju lub regionu. Utwórz reguły dla określonych ścieżek w punkcie końcowym usługi CDN, aby zezwalać na zawartość lub blokować ją w wybranych krajach lub regionach przy użyciu funkcji filtrowania geograficznego.

- [Ogranicz zawartość Azure CDN według kraju lub regionu](cdn-restrict-access-by-country.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="privileged-access"></a>Uprzywilejowany dostęp

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) w Content Delivery Network, aby izolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, w których się znajdują.

Należy również ograniczyć dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępów do firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnych kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access) 

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: używanie uprzywilejowanych stacji roboczych dostępu

**Wskazówki**: zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa ról poufnych, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. Użyj Azure Active Directory (Azure AD), usługi Microsoft Defender Advanced Threat Protection (ATP) i/lub Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe linie bazowe, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: przestrzeganie wystarczającej liczby czynności administracyjnych (najmniejsza zasada uprawnień) 

**Wskazówki**: Content Delivery Network jest zintegrowany z kontrolą dostępu opartą na ROLACH (RBAC) na platformie Azure w celu zarządzania swoimi zasobami. Funkcja RBAC platformy Azure umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Przypisz te role do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. 

Ogranicz uprawnienia przypisane do zasobów za pomocą usługi Azure RBAC zgodnie z wymaganiami ról. To uzupełnia podejście just in Time (JIT) Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane. 

Ponadto przy użyciu wbudowanych ról można przydzielić uprawnienia i utworzyć tylko rolę niestandardową, jeśli jest to wymagane.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) 

- [Jak skonfigurować RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby monitorować zagrożenia bezpieczeństwa przy użyciu Azure Security Center. 

W zależności od tego, jak są strukturalne obowiązki zespołu zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu zabezpieczeń lub zespołu lokalnego. Informacje o zabezpieczeniach i ryzyka muszą jednak zostać zawsze zagregowane w organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (głównej grupy zarządzania) lub zakresu do grup zarządzania lub określonych subskrypcji. 

Uwaga: w celu wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie Grupy zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu zasobów i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md) 

- [Przewodnik po decyzjach dotyczących nazewnictwa i tagowania zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy do inspekcji i ograniczania usług, które użytkownicy mogą inicjować w Twoim środowisku. Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących zasobów i odnajdywania ich w ramach subskrypcji. Za pomocą Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zapewnij bezpieczeństwo zarządzania cyklem życia zasobów

**Wskazówki**: w celu zapewnienia bezpieczeństwa zasobów w procesie zarządzania cyklem życia nie można używać sieci danych zawartości. Klient jest odpowiedzialny za obsługę atrybutów i konfiguracji sieci zasobów, które są uważane za duże znaczenie. Zaleca się, aby klient utworzył proces przechwytywania atrybutów i zmian konfiguracji sieci, mierzyć wpływ zmian i tworzyć zadania korygowania, zgodnie z potrzebami.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: Content Delivery Network nie zapewnia natywnych możliwości monitorowania zagrożeń bezpieczeństwa związanych ze swoimi zasobami.

Przekazuj wszystkie dzienniki z Content Delivery Network do rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), które mogą służyć do konfigurowania niestandardowych wykryć zagrożeń. 

Monitorowanie różnych typów zasobów platformy Azure pod kątem potencjalnych zagrożeń i anomalii z fokusem na uzyskiwanie alertów o wysokiej jakości w celu zredukowania fałszywych wyników przez analityków do sortowania. Alerty mogą być źródłem danych dziennika, agentów lub innych danych.

- [Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](../sentinel/tutorial-detect-threats-custom.md) 

- [Analiza zagrożeń cybernetycznymi z użyciem platformy Azure](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania dla aktywności sieci platformy Azure

**Wskazówki**: Content Delivery Network nie jest przeznaczona do wdrożenia w sieciach wirtualnych. z tego powodu nie można włączyć rejestrowania przepływu sieciowych grup zabezpieczeń, kierować ruchem przez zaporę lub przechwycić pakiety.

Content Delivery Network rejestruje cały ruch sieciowy, który przetwarza dla dostępu klienta. Włącz możliwość przepływu sieci w ramach wdrożonych zasobów oferty i skonfiguruj te dzienniki w taki sposób, aby były wysyłane do konta magazynu w celu długoterminowego przechowywania i inspekcji.

- [Samouczek — jak skonfigurować dziennik diagnostyczny na platformie Azure Content Delivery Network](cdn-azure-diagnostic-logs.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

**Wskazówki**: dzienniki aktywności, które są automatycznie dostępne, zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów Content Delivery Network z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Aby włączyć dzienniki zasobów platformy Azure dla usługi CDN, możesz użyć Azure Security Center i Azure Policy do włączenia dzienników zasobów i zbierania danych dzienników. Te dzienniki mogą być niezbędne do późniejszego badania zdarzeń związanych z bezpieczeństwem i wykonywania ćwiczeń śledczej.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: przygotowanie — aktualizacja procesu reagowania na incydenty dla platformy Azure

**Wskazówki**: Upewnij się, że organizacja ma procesy odpowiadające na zdarzenia związane z bezpieczeństwem, Zaktualizowano te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik odwołuje się do odpowiedzi na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: przygotowanie — powiadomienie o zdarzeniu instalacji

**Wskazówki**: Konfigurowanie informacji kontaktowych dotyczących zdarzeń zabezpieczeń w Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nielegalną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówki**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można uczyć się lekcji od przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą one tracić czasu na fałszywie dodatnich. 

Alerty o wysokiej jakości mogą być tworzone na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności i narzędzi przeznaczonych do generowania i czyszczenia alertów przez odmowę i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty o wysokiej jakości dla wielu zasobów platformy Azure. Łącznika danych ASC można użyć do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. Wskaźnik oceny platformy Azure umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały sposób ciągły.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: wykrywanie i analiza — Zbadaj zdarzenie

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń, aby zbudować pełny wgląd w to, co się stało. Należy zebrać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w łańcuchu kasowania, aby uniknąć niewidomych plamek.  Należy również upewnić się, że szczegółowe informacje i uczenia są przechwytywane dla innych analityków i w przyszłości.  

Źródła danych do zbadania obejmują scentralizowane źródła rejestrowania, które są już zbierane z usług w zakresie i działających systemów, ale mogą również obejmować:

- Dane sieci — używanie dzienników przepływów sieciowych grup zabezpieczeń, platformy Azure Network Watcher i Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj możliwości natywnego zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji Snapshot w ramach usług platformy Azure lub własnej funkcji oprogramowania, aby utworzyć migawki uruchomionych systemów.

Platforma Azure — wskaźnik przedstawia szeroką analizę danych w praktycznie dowolnym źródle dziennika i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje o analizie podczas badania mogą być powiązane ze zdarzeniami w celu śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure obsługi informacji diagnostycznych i kolekcji zrzutów pamięci](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzenia mają być skoncentrowane na początku, na podstawie ważności alertów i poufności zasobów. 

Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć zasoby przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zawieranie, likwidowanie i odzyskiwanie — Automatyzacja obsługi zdarzeń

**Wskazówki**: automatyzuje ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę incydentów, które może obsłużyć analityk. Zadania ręczne zwiększają również zmęczenie analityków, które zwiększają ryzyko wystąpienia błędu ludzkiego powodującego opóźnienia i obniżają możliwości skoncentrowania się na skomplikowanych zadaniach analityków. Korzystając z funkcji automatyzacji przepływu pracy w Azure Security Center i wskaźnik platformy Azure, można automatycznie wyzwalać akcje lub uruchamiać element PlayBook w celu reagowania na przychodzące alerty zabezpieczeń. Element PlayBook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia w Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="posture-and-vulnerability-management"></a>Stan i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>WA-3: Ustanów bezpieczne konfiguracje dla zasobów obliczeniowych

**Wskazówki**: nie dotyczy; Content Delivery Network nie zapewnia żadnych możliwości konfiguracji zabezpieczeń.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: szybkie i automatyczne korygowanie luk w zabezpieczeniach oprogramowania

**Wskazówki**: szybkie wdrażanie aktualizacji oprogramowania w celu skorygowania luk w zabezpieczeniach systemów operacyjnych i aplikacji. 

Ustalanie priorytetów korygowania przy użyciu typowego programu oceny ryzyka (na przykład typowego rozwiązania do oceny luk w zabezpieczeniach) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy. Dostosowywać do środowiska przy użyciu kontekstu, w którym aplikacje stwarzają wysokie ryzyko związane z zabezpieczeniami i które wymagają dużego czasu pracy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: przeprowadzenie regularnej symulacji ataków

**Wskazówki**: w razie potrzeby przeprowadź testowanie penetracji lub czerwone działania zespołu na Twoich zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z Microsoft Cloud regułami testowania penetracji, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Testowanie penetracji na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security Tests: ładu and strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania zasobami i ochrony danych 

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią ciągłego monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo zasobów w ramach cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z funkcji ochrony danych natywnych i firmowych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Więcej informacji można znaleźć w łączach, do których istnieją odwołania.

- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test wydajności Azure Security — zarządzanie zasobami](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — Ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki**: Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami formantów, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także z kontrolkami dostępu i dostępem do aplikacji.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównaj segmentację sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stan zabezpieczeń

**Wskazówki**: stałe mierzenie i łagodzenie ryzyka dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Wyrównaj role organizacji, obowiązki i accountabilities

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu zapewnienia jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, przenosząc wszystkie osoby na współużytkowany model odpowiedzialności i przeszkolej zespoły techniczne dotyczące technologii, aby zabezpieczyć chmurę.

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówki**: ustanowienie podejścia zabezpieczeń sieci platformy Azure jako części ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i lokalnej

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dostępu Identity i Privileged Access

**Wskazówki**: ustanowienie podejścia do tożsamości platformy Azure i dostępu uprzywilejowanego w ramach ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Więcej informacji można znaleźć w łączach, do których istnieją odwołania.

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami o wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów:

-   Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i zainteresowanymi stronami

-   Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Więcej informacji można znaleźć w łączach, do których istnieją odwołania.

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie platformy Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie usługi Azure Security test w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
