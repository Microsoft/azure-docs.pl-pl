---
title: Archiwum nowości w programie Azure Security Center
description: Opis nowości i zmian w Azure Security Center z sześciu miesięcy temu i wcześniejszych.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: 2d156887416fb9a587c9690c3b275229e91f1dbe
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339802"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Co nowego w Azure Security Center?

Podstawowe [Informacje o nowościach Azure Security Center?](release-notes.md) Strona informacje o wersji zawiera aktualizacje dla ostatnich sześciu miesięcy, a ta strona zawiera starsze elementy.

Ta strona zawiera informacje na temat:

- Nowe funkcje
- Poprawki błędów
- Funkcje uznane za przestarzałe


## <a name="april-2020"></a>Kwiecień 2020 r.

Aktualizacje w kwietniu obejmują:
- [Dynamiczne pakiety zgodności są teraz ogólnie dostępne](#dynamic-compliance-packages-are-now-generally-available)
- [Zalecenia dotyczące tożsamości zawarte obecnie w Azure Security Center warstwy Bezpłatna](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamiczne pakiety zgodności są teraz ogólnie dostępne

Pulpit nawigacyjny zgodności z przepisami Azure Security Center obejmuje teraz **dynamiczne pakiety zgodności** (teraz ogólnie dostępne) do śledzenia dodatkowych standardów branżowych i prawnych.

Dynamiczne pakiety zgodności można dodać do swojej subskrypcji lub grupy zarządzania ze strony Security Center zasady zabezpieczeń. Po dodaniu standardowego lub testu porównawczego Standard pojawia się na pulpicie nawigacyjnym zgodności z przepisami, który ma wszystkie skojarzone dane zgodności zamapowane jako oceny. Raport podsumowujący dla dowolnych ze standardów, które zostały dołączone, będzie dostępny do pobrania.

Teraz możesz dodawać standardy, takie jak:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-V2020**
- **Oficjalne Królestwo brytyjskie i Zjednoczone Królestwo NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nowy)** (czyli bardziej kompletna reprezentacja usługi Azure CIS 1.1.0)

Ponadto został niedawno dodany **test testów zabezpieczeń platformy Azure**, oparte na platformie Azure wskazówki dotyczące zabezpieczeń i zgodności w oparciu o typowe struktury zgodności. Dodatkowe standardy będą obsługiwane na pulpicie nawigacyjnym, gdy staną się dostępne.  
 
Dowiedz się więcej o [dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Zalecenia dotyczące tożsamości zawarte obecnie w Azure Security Center warstwy Bezpłatna

Zalecenia dotyczące zabezpieczeń dotyczące tożsamości i dostępu w Azure Security Center warstwy Bezpłatna są teraz ogólnie dostępne. Jest to część nakładu pracy, aby udostępnić funkcje usługi Cloud Security stan Management (CSPM). Do tej pory te zalecenia były dostępne tylko w warstwie cenowej standardowa.

Przykłady zaleceń dotyczących tożsamości i dostępu to:

- "Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji".
- "Dla subskrypcji należy wyznaczyć maksymalnie trzech właścicieli".
- "Przestarzałe konta powinny zostać usunięte z subskrypcji".

Jeśli masz subskrypcje w bezpłatnej warstwie cenowej, będzie to miało wpływ na ich bezpieczeństwo, ponieważ nigdy nie oceniono ich pod kątem bezpieczeństwa tożsamości i dostępu.

Dowiedz się więcej o [zaleceniach dotyczących tożsamości i dostępu](recommendations-reference.md#recs-identity).

Dowiedz się więcej o [monitorowaniu tożsamości i dostępu](security-center-identity-access.md).



## <a name="march-2020"></a>Marzec 2020 r.

Aktualizacje w marcu obejmują:

- [Automatyzacja przepływu pracy jest teraz ogólnie dostępna](#workflow-automation-is-now-generally-available)
- [Integracja Azure Security Center z centrum administracyjnym systemu Windows](#integration-of-azure-security-center-with-windows-admin-center)
- [Ochrona usługi Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Udoskonalone środowisko just in Time](#improved-just-in-time-experience)
- [Dwie zalecenia dotyczące zabezpieczeń dla aplikacji sieci Web przestarzałe](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Automatyzacja przepływu pracy jest teraz ogólnie dostępna

Funkcja automatyzacji przepływu pracy Azure Security Center jest teraz ogólnie dostępna. Służy do automatycznego wyzwalania Logic Apps na temat alertów zabezpieczeń i zaleceń. Ponadto są dostępne wyzwalacze ręczne dla alertów i wszystkie zalecenia z opcją szybkie rozwiązanie.

Każdy program zabezpieczeń zawiera wiele przepływów pracy dotyczących odpowiedzi na zdarzenia. Procesy te mogą obejmować Powiadamianie właściwych uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych czynności zaradczych. Specjaliści ds. zabezpieczeń zaleca się zautomatyzować dowolną liczbę kroków tych procedur. Automatyzacja redukuje obciążenie i może poprawić bezpieczeństwo, zapewniając, że etapy procesu są wykonywane szybko, spójnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

Aby uzyskać więcej informacji o automatycznym i ręcznym Security Center możliwości uruchamiania przepływów pracy, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

Dowiedz się więcej na temat [tworzenia Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integracja Azure Security Center z centrum administracyjnym systemu Windows

Teraz można przenieść lokalne serwery z systemem Windows z centrum administracyjnego systemu Windows bezpośrednio do Azure Security Center. Security Center następnie przejdziesz do jednego okienka Glass, aby wyświetlić informacje o zabezpieczeniach dla wszystkich zasobów centrum administratora systemu Windows, w tym serwerów lokalnych, maszyn wirtualnych i dodatkowych obciążeń PaaS.

Po przeniesieniu serwera z centrum administracyjnego systemu Windows do Azure Security Center można:

- Wyświetlanie alertów zabezpieczeń i zaleceń w Security Center rozszerzeniu centrum administracyjnego systemu Windows.
- Zapoznaj się z zabezpieczeniami stan i Pobierz dodatkowe szczegółowe informacje o zarządzanych serwerach centrum administracyjnego systemu Windows w Security Center w ramach Azure Portal (lub za pośrednictwem interfejsu API).

Dowiedz się więcej o tym [, jak zintegrować Azure Security Center z centrum administracyjnym systemu Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Ochrona usługi Azure Kubernetes Service

Azure Security Center rozszerza funkcje zabezpieczeń kontenerów w celu ochrony usługi Azure Kubernetes Service (AKS).

Popularna Kubernetes platformy Open Source została przyjęta tak, aby była teraz standardem branżowym dla aranżacji kontenerów. Pomimo tej rozległej implementacji nadal nie można zrozumieć, jak zabezpieczyć środowisko Kubernetes. Obronność obszarów ataku aplikacji kontenerowej wymaga ekspertyzy, aby zapewnić, że infrastruktura została skonfigurowana bezpiecznie i stale monitorowana pod kątem potencjalnych zagrożeń.

Ochrona Security Center obejmuje:

- **Odnajdywanie i widoczność** — ciągłe wykrywanie zarządzanych wystąpień AKS w ramach subskrypcji zarejestrowanych do Security Center.
- **Zalecenia dotyczące zabezpieczeń** — zalecenia z możliwością podejmowania działań, które ułatwiają zgodność z najlepszymi rozwiązaniami w zakresie zabezpieczeń dla AKS. Te zalecenia są zawarte w zabezpieczonym wyniku, aby upewnić się, że są one widoczne jako część stan zabezpieczeń organizacji. Przykładem zalecenia powiązanego z AKSem jest "kontrola dostępu oparta na rolach powinna być używana do ograniczania dostępu do klastra usługi Kubernetes Service".
- **Ochrona przed zagrożeniami** — dzięki ciągłej analizie wdrożenia AKS Security Center ostrzega o zagrożeniach i złośliwych działaniach wykrytych na poziomie klastra hosta i AKS.

Dowiedz się więcej o [integracji usług Azure Kubernetes Services z usługą Security Center](defender-for-kubernetes-introduction.md).

Dowiedz się więcej o [funkcjach zabezpieczeń kontenera w Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Udoskonalone środowisko just in Time

Funkcje, operacje i interfejs użytkownika dla narzędzi just in Time Azure Security Center, które zabezpieczają porty zarządzania, zostały ulepszone w następujący sposób: 

- **Pole uzasadnienia** — podczas żądania dostępu do maszyny wirtualnej za pomocą strony just-in-time w Azure Portal jest dostępne nowe pole opcjonalne, aby wprowadzić uzasadnienie żądania. Informacje wprowadzane do tego pola mogą być śledzone w dzienniku aktywności. 
- **Automatyczne oczyszczanie nadmiarowych reguł just-in-Time (JIT)** — za każdym razem, gdy aktualizujesz zasady JIT, narzędzie do czyszczenia zostanie automatycznie uruchomione, aby sprawdzić poprawność całego zestawu reguł. Narzędzie szuka niezgodności między regułami w zasadach i regułami w sieciowej grupy zabezpieczeń. Jeśli narzędzie do czyszczenia znajdzie niezgodność, określa przyczynę i, gdy jest to bezpieczne, usuwa wbudowane reguły, które nie są już potrzebne. Oczyszczarka nigdy nie usuwa reguł, które zostały utworzone. 

Dowiedz się więcej o [funkcji dostępu JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dwie zalecenia dotyczące zabezpieczeń dla aplikacji sieci Web przestarzałe

Dwie zalecenia dotyczące zabezpieczeń związane z aplikacjami sieci Web są przestarzałe: 

- Reguły dla aplikacji sieci Web w IaaS sieciowych grup zabezpieczeń powinny być zaostrzone.
    (Powiązane zasady: reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web na IaaS powinny być zaostrzone)

- Dostęp do App Services powinien być ograniczony.
    (Powiązane zasady: dostęp do App Services powinien być ograniczony [wersja zapoznawcza])

Te rekomendacje nie będą już wyświetlane na liście Security Center zaleceń. Powiązane zasady nie będą już uwzględniane w ramach inicjatywy o nazwie "Security Center domyślne".

Dowiedz się więcej o [zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).




## <a name="february-2020"></a>Luty 2020 r.

### <a name="fileless-attack-detection-for-linux-preview"></a>Wykrywanie ataków bezplikowych dla systemu Linux (wersja zapoznawcza)

Gdy osoby atakujące zwiększą wykorzystanie metod stealthier, aby uniknąć wykrywania, Azure Security Center rozszerza wykrywanie ataków bez plików dla systemu Linux, a nie tylko z systemem Windows. Ataki bezplikowe wykorzystują luki w zabezpieczeniach, wprowadzają złośliwe ładunki do niegroźnych procesów systemu i ukrywają w pamięci. Techniki te:

- Minimalizowanie lub eliminowanie śladów złośliwego oprogramowania na dysku
- znacznie zmniejsz szanse wykrywania przez rozwiązania skanujące złośliwe oprogramowanie oparte na dyskach

Aby wyeliminować to zagrożenie, Azure Security Center wydane wykrywanie ataków bezplików dla systemu Windows w październiku 2018 i obecnie rozszerzono wykrywanie ataków bezplikowych w systemie Linux. 



## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="enhanced-secure-score-preview"></a>Ulepszony bezpieczny wynik (wersja zapoznawcza)

Rozszerzona wersja funkcji bezpiecznego oceny Azure Security Center jest teraz dostępna w wersji zapoznawczej. W tej wersji wiele zaleceń jest pogrupowanych w kontrolki zabezpieczeń, które lepiej odzwierciedlają zagrożone powierzchnie ataków (na przykład ograniczanie dostępu do portów zarządzania).

Zapoznaj się z informacjami o bezpiecznych zmianach oceny w fazie wersji zapoznawczej i ustal inne korygowanie, które pomogą Ci zapewnić dalsze bezpieczeństwo środowiska.

Dowiedz się więcej o [ulepszonym zabezpieczeniu (wersja zapoznawcza)](secure-score-security-controls.md).



## <a name="november-2019"></a>Listopad 2019 r.

Aktualizacje w listopadzie obejmują:
 - [Ochrona przed zagrożeniami dla Azure Key Vault w regionach Ameryka Północna (wersja zapoznawcza)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Ochrona przed zagrożeniami w usłudze Azure Storage obejmuje osłanianie reputacji złośliwego oprogramowania](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatyzacja przepływu pracy z Logic Apps (wersja zapoznawcza)](#workflow-automation-with-logic-apps-preview)
 - [Szybka poprawka dla zasobów zbiorczych jest ogólnie dostępna](#quick-fix-for-bulk-resources-generally-available)
 - [Skanuj obrazy kontenerów dla luk w zabezpieczeniach (wersja zapoznawcza)](#scan-container-images-for-vulnerabilities-preview)
 - [Dodatkowe standardy zgodności z przepisami (wersja zapoznawcza)](#additional-regulatory-compliance-standards-preview)
 - [Ochrona przed zagrożeniami dla usługi Azure Kubernetes Service (wersja zapoznawcza)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Ocena luk w zabezpieczeniach maszyn wirtualnych (wersja zapoznawcza)](#virtual-machine-vulnerability-assessment-preview)
 - [Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (wersja zapoznawcza)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Obsługa zasad niestandardowych (wersja zapoznawcza)](#support-for-custom-policies-preview)
 - [Rozszerzanie zakresu Azure Security Center przy użyciu platformy dla społeczności i partnerów](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Zaawansowane integracje z eksportem zaleceń i alertów (wersja zapoznawcza)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Dołączanie serwerów premium do Security Center z centrum administracyjnego systemu Windows (wersja zapoznawcza)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Ochrona przed zagrożeniami dla Azure Key Vault w regionach Ameryka Północna (wersja zapoznawcza)

Azure Key Vault to podstawowa usługa służąca do ochrony danych i poprawiania wydajności aplikacji w chmurze, oferując możliwość centralnego zarządzania kluczami, wpisami tajnymi, kluczami kryptograficznymi i zasadami w chmurze. Ponieważ Azure Key Vault przechowuje wrażliwe i krytyczne dane biznesowe, wymaga maksymalnego poziomu zabezpieczeń dla magazynów kluczy i przechowywanych w nich danych.

Azure Security Center obsługa ochrony przed zagrożeniami dla Azure Key Vault stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do magazynów kluczy lub korzystania z nich. Ta nowa warstwa ochrony pozwala klientom na rozwiązywanie zagrożeń związanych z ich magazynami kluczy bez konieczności eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń. Ta funkcja jest dostępna w publicznej wersji zapoznawczej w regionach Ameryka Północna.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Ochrona przed zagrożeniami w usłudze Azure Storage obejmuje osłanianie reputacji złośliwego oprogramowania

Ochrona przed zagrożeniami dla usługi Azure Storage oferuje nowe wykrycia obsługiwane przez funkcję analizy zagrożeń firmy Microsoft w celu wykrywania przeładowania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji i podejrzanego dostępu z aktywnego węzła zakończenia sieci Tor (anonymizing proxy). Można teraz wyświetlać wykryte złośliwe oprogramowanie na kontach magazynu przy użyciu Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatyzacja przepływu pracy z Logic Apps (wersja zapoznawcza)

Organizacje z centralnie zarządzanymi zabezpieczeniami i działem IT/Operations implementują wewnętrzne procesy przepływu pracy, aby zapewnić wymaganą akcję w organizacji w przypadku wykrycia niezgodności w swoich środowiskach. W wielu przypadkach te przepływy pracy są powtarzalnymi procesami, a Automatyzacja może znacznie usprawnić procesy w organizacji.

Dzisiaj wprowadzamy nową funkcję w Security Center, która umożliwia klientom Tworzenie konfiguracji automatyzacji wykorzystujących Azure Logic Apps i tworzenie zasad, które będą automatycznie wyzwalać je na podstawie określonych ustaleń ASC, takich jak zalecenia lub alerty. Aplikację logiki platformy Azure można skonfigurować w taki sposób, aby wykonać dowolną akcję niestandardową obsługiwaną przez rozległą społeczność łączników aplikacji logiki lub użyć jednego z szablonów dostarczonych przez Security Center, takich jak wysyłanie wiadomości e-mail lub otwieranie biletu usługi usługi ServiceNow™.

Aby uzyskać więcej informacji o automatycznym i ręcznym Security Center możliwości uruchamiania przepływów pracy, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

Aby dowiedzieć się więcej na temat tworzenia Logic Apps, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Szybka poprawka dla zasobów zbiorczych jest ogólnie dostępna

Dzięki wielu zadań, które użytkownik otrzymuje w ramach bezpiecznego oceny, zdolność do skutecznego korygowania problemów w ramach dużej floty może stać się trudne.

Aby uprościć korygowanie nieprawidłowych konfiguracji zabezpieczeń i mieć możliwość szybkiego korygowania zaleceń dotyczących ilości zasobów i zwiększenia bezpiecznego wyniku, należy użyć funkcji szybkiego rozwiązywania problemów.

Ta operacja umożliwi wybranie zasobów, do których chcesz zastosować korygowanie, i uruchomienie akcji korygowania, która spowoduje skonfigurowanie ustawienia w Twoim imieniu.

Szybka poprawka jest ogólnie dostępna dla klientów jako część strony rekomendacje Security Center.

Zapoznaj się ze wskazówkami dotyczącymi szybkiego rozwiązywania problemów w [przewodniku dotyczącym zabezpieczeń](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Skanuj obrazy kontenerów dla luk w zabezpieczeniach (wersja zapoznawcza)

Azure Security Center teraz można skanować obrazy kontenerów w Azure Container Registry w celu uzyskania luk w zabezpieczeniach.

Skanowanie obrazów działa przez analizowanie pliku obrazu kontenera, a następnie sprawdzenie, czy istnieją znane luki w zabezpieczeniach (obsługiwane przez Qualys).

Skanowanie jest automatycznie wyzwalane podczas wypychania nowych obrazów kontenera do Azure Container Registry. Znalezione luki w zabezpieczeniach będą przedstawiane jako zalecenia dotyczące Security Center zaleceń i zawartych w zestawieniu zabezpieczeń platformy Azure wraz z informacjami na temat sposobu ich poprawek w celu zmniejszenia dopuszczalnej powierzchni ataków.


### <a name="additional-regulatory-compliance-standards-preview"></a>Dodatkowe standardy zgodności z przepisami (wersja zapoznawcza)

Pulpit nawigacyjny zgodności z przepisami zapewnia wgląd w stan zgodności na podstawie ocen Security Center. Pulpit nawigacyjny pokazuje, jak środowisko jest zgodne z kontrolkami i wymaganiami określonymi przez określone standardy prawne i testy porównawcze oraz zawiera zalecenia dotyczące sposobu rozwiązywania tych wymagań.

Pulpit nawigacyjny zgodności z przepisami ma w tym przypadku cztery wbudowane standardy: Azure CIS 1.1.0, PCI-DSS, ISO 27001 i SOC-TSP. Teraz ogłaszamy publiczną wersję zapoznawczą dodatkowych obsługiwanych standardów: NIST SP 800-53 R4, SWIFT CSP CSCF V2020, Kanada Federal PBMM i Zjednoczone Królestwo z Wielkiej Brytanii NHS. Firma Microsoft udostępnia również zaktualizowaną wersję usługi Azure CIS 1.1.0, która obejmuje więcej kontroli z poziomu standardowej i rozszerzanej rozszerzalności.

[Dowiedz się więcej o dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Kubernetes Service (wersja zapoznawcza)

Kubernetes szybko staje się nowym standardem wdrażania oprogramowania i zarządzania nim w chmurze. Kilka osób ma rozległe doświadczenie z Kubernetes i wiele koncentruje się na ogólnej inżynierii i administracji oraz zapomina aspektu zabezpieczeń. Środowisko Kubernetes należy skonfigurować uważnie, aby było bezpieczne, co sprawia, że żadne drzwi nie będące częścią ataku nie są otwarte. Security Center rozszerza swoją obsługę w przestrzeni kontenerów na jedną z najszybszych rosnących usług na platformie Azure — Azure Kubernetes Service (AKS).

Nowe możliwości w tej publicznej wersji zapoznawczej obejmują:

- **Odnajdowanie & widoczność** — ciągłe wykrywanie zarządzanych wystąpień AKS w ramach zarejestrowanych subskrypcji Security Center.
- **Zaleceń dotyczących oceny** — elementy poddaje się do działania, aby ułatwić klientom przestrzeganie najlepszych rozwiązań w zakresie zabezpieczeń w AKS w ramach bezpiecznego wyniku klienta, na przykład "Access Control opartych na rolach powinna być używana do ograniczania dostępu do klastra usługi Kubernetes Service".
- **Wykrywanie zagrożeń** — Analiza oparta na hoście i klastrze, taka jak "wykryto uprzywilejowany kontener".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych (wersja zapoznawcza)

Aplikacje zainstalowane na maszynach wirtualnych mogą często mieć luki w zabezpieczeniach, które mogą spowodować naruszenie maszyny wirtualnej. Ogłaszamy, że Security Center warstwa standardowa zawiera wbudowaną ocenę luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych opłat. Ocena luk w zabezpieczeniach, obsługiwana przez Qualys w publicznej wersji zapoznawczej, umożliwia ciągłe skanowanie wszystkich zainstalowanych aplikacji na maszynie wirtualnej w celu znalezienia luk w zabezpieczeniach i zaprezentowania wyników w środowisku portalu Security Center. Security Center bierze pod uwagę wszystkie operacje wdrażania, dzięki czemu użytkownik nie wymaga dodatkowej pracy. W przyszłości planujemy udostępnienie opcji oceny luk w zabezpieczeniach w celu obsługi unikatowych potrzeb firmy klientów.

[Dowiedz się więcej o ocenach luk w zabezpieczeniach Virtual Machines platformy Azure](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (wersja zapoznawcza)

Azure Security Center obsługa ochrony przed zagrożeniami i oceny luk w zabezpieczeniach usługi SQL baz danych uruchomionych na maszynach wirtualnych IaaS jest teraz w wersji zapoznawczej.

[Ocena luk w zabezpieczeniach](../azure-sql/database/sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa umożliwiająca odnajdywanie i śledzenie potencjalnych luk w zabezpieczeniach bazy danych oraz pomagająca w ich usuwaniu. Zapewnia wgląd w stan zabezpieczeń w ramach bezpiecznej oceny platformy Azure i zawiera kroki rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.

[Zaawansowana ochrona przed zagrożeniami](../azure-sql/database/threat-detection-overview.md) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do programu SQL Server lub korzystania z niego. Nieustannie monitoruje bazę danych pod kątem podejrzanych działań i zapewnia zorientowane na działania alerty zabezpieczeń dotyczące nietypowych wzorców dostępu do bazy danych. Te alerty zawierają szczegóły podejrzanych działań i zalecane akcje do zbadania i ograniczenia zagrożenia.


### <a name="support-for-custom-policies-preview"></a>Obsługa zasad niestandardowych (wersja zapoznawcza)

Azure Security Center obsługuje teraz zasady niestandardowe (w wersji zapoznawczej).

Nasi klienci chcą zwiększyć swoje bieżące oceny zabezpieczeń w Security Center z uwzględnieniem własnych ocen zabezpieczeń opartych na zasadach utworzonych w programie Azure Policy. Obecnie jest to możliwe dzięki obsłudze zasad niestandardowych.

Te nowe zasady będą częścią środowiska zaleceń Security Center, bezpieczeństwa i oceny zgodności z przepisami. Dzięki obsłudze zasad niestandardowych można teraz utworzyć inicjatywę niestandardową w Azure Policy, a następnie dodać ją jako zasadę w Security Center i wizualizować ją jako rekomendacje.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Rozszerzanie zakresu Azure Security Center przy użyciu platformy dla społeczności i partnerów

Użyj Security Center, aby otrzymywać zalecenia nie tylko od firmy Microsoft, ale również z istniejących rozwiązań od partnerów, takich jak Check Point, Tenable i CyberArk z wieloma innymi integracją.  Prosty przepływ dołączania do usługi Security Center umożliwia łączenie istniejących rozwiązań z Security Center, co pozwala na wyświetlanie zaleceń stan dotyczących zabezpieczeń w jednym miejscu, uruchamianie ujednoliconych raportów i korzystanie ze wszystkich Security Center możliwości w przypadku zaleceń wbudowanych i partnerskich. Możesz również wyeksportować zalecenia dotyczące Security Center do produktów partnerskich.

[Dowiedz się więcej o skojarzeniu Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Zaawansowane integracje z eksportem zaleceń i alertów (wersja zapoznawcza)

Aby włączyć scenariusze na poziomie przedsiębiorstwa na Security Center, można teraz używać alertów Security Center i zaleceń w dodatkowych miejscach poza Azure Portal lub interfejsem API. Można je bezpośrednio wyeksportować do centrum zdarzeń i Log Analytics obszary robocze. Poniżej przedstawiono kilka przepływów pracy, które można utworzyć wokół tych nowych funkcji:

- Za pomocą funkcji eksportowania do Log Analytics obszaru roboczego można tworzyć niestandardowe pulpity nawigacyjne z Power BI.
- Za pomocą eksportu do centrum zdarzeń można eksportować Security Center alerty i zalecenia dotyczące rozwiązań Siem innych firm, do rozwiązania innych firm w czasie rzeczywistym lub Eksplorator danych platformy Azure.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Dołączanie serwerów premium do Security Center z centrum administracyjnego systemu Windows (wersja zapoznawcza)

Centrum administracyjne systemu Windows to portal zarządzania dla serwerów z systemem Windows, które nie są wdrożone na platformie Azure, oferując im kilka funkcji zarządzania platformy Azure, takich jak aktualizacje kopii zapasowych i system. Niedawno dodaliśmy możliwość dołączenia tych serwerów spoza platformy Azure do ochrony przez funkcję ASC bezpośrednio w środowisku centrum administracyjnego systemu Windows.

Dzięki temu nowemu doświadczeniu użytkownicy będą mogli dołączyć serwer WAC do Azure Security Center i włączyć wyświetlanie alertów zabezpieczeń i zaleceń bezpośrednio w środowisku centrum administracyjnego systemu Windows.


## <a name="september-2019"></a>Wrzesień 2019 r.

Aktualizacje we wrześniu obejmują:

 - [Zarządzanie regułami przy użyciu ulepszeń kontroli aplikacji](#managing-rules-with-adaptive-application-controls-improvements)
 - [Kontrola zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Zarządzanie regułami przy użyciu ulepszeń kontroli aplikacji

Ulepszono obsługę zarządzania regułami dotyczącymi maszyn wirtualnych przy użyciu adaptacyjnych kontrolek aplikacji. Azure Security Center funkcje adaptacyjnego sterowania aplikacjami ułatwiają kontrolowanie, które aplikacje mogą być uruchamiane na maszynach wirtualnych. Oprócz ogólnej poprawy zarządzania regułami nowe korzyści umożliwiają kontrolowanie, które typy plików będą chronione po dodaniu nowej reguły.

[Dowiedz się więcej na temat adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrola zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy

Azure Security Center zalecenia dotyczące rozwiązywania luk w zabezpieczeniach kontenerów mogą teraz być włączane lub wyłączane za pośrednictwem Azure Policy.

Aby wyświetlić włączone zasady zabezpieczeń, w Security Center otworzyć stronę zasady zabezpieczeń.


## <a name="august-2019"></a>Sierpień 2019 r.

Aktualizacje w sierpniu obejmują:

 - [Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Korygowanie z jednym kliknięciem w celu zwiększenia stan zabezpieczeń (wersja zapoznawcza)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Zarządzanie wieloma dzierżawami](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure 

Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure jest teraz ogólnie dostępny. Korzystaj z niego, aby zabezpieczyć środowiska chronione przez zaporę platformy Azure oprócz środowisk chronionych sieciowej grupy zabezpieczeń.

Dostęp JIT do maszyny wirtualnej zmniejsza narażenie na ataki typu "Sieć" dzięki zapewnianiu kontrolowanego dostępu do maszyn wirtualnych tylko w razie potrzeby przy użyciu reguł sieciowej grupy zabezpieczeń i zapory platformy Azure.

Po włączeniu JIT dla maszyn wirtualnych należy utworzyć zasady określające porty, które mają być chronione, jak długo porty pozostają otwarte i zatwierdzone adresy IP, z których można uzyskać dostęp do tych portów. Te zasady pomagają zachować kontrolę nad tym, co użytkownicy mogą robić, gdy żądają dostępu.

Żądania są rejestrowane w dzienniku aktywności platformy Azure, dzięki czemu można łatwo monitorować i przeprowadzać inspekcję dostępu. Strona just in Time ułatwia również szybkie identyfikowanie istniejących maszyn wirtualnych z włączoną obsługą JIT oraz maszyn wirtualnych, na których jest zalecana metoda JIT.

[Dowiedz się więcej o zaporze platformy Azure](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Korygowanie z jednym kliknięciem w celu zwiększenia stan zabezpieczeń (wersja zapoznawcza)

Security score to narzędzie, które pomaga ocenić stan bezpieczeństwa obciążeń. Przegląda zalecenia dotyczące zabezpieczeń i ustala ich priorytety, dlatego wiesz, które zalecenia należy wykonać w pierwszej kolejności. Ułatwia to znalezienie najważniejszych luk w zabezpieczeniach, aby określić priorytety badania.

Aby uprościć korygowanie nieprawidłowych konfiguracji zabezpieczeń i pomóc szybko poprawić swój Bezpieczny wynik, dodaliśmy nową funkcję, która pozwala skorygować zalecenie dotyczące zbiorczych zasobów w jednym kliknięciem.

Ta operacja umożliwi wybranie zasobów, do których chcesz zastosować korygowanie, i uruchomienie akcji korygowania, która spowoduje skonfigurowanie ustawienia w Twoim imieniu.

Zapoznaj się ze wskazówkami dotyczącymi szybkiego rozwiązywania problemów w [przewodniku dotyczącym zabezpieczeń](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Zarządzanie wieloma dzierżawami

Security Center teraz obsługuje scenariusze zarządzania między dzierżawcami w ramach usługi Azure Lighthouse. Dzięki temu można uzyskać wgląd w stan zabezpieczeń wielu dzierżawców i zarządzać nimi w Security Center. 

[Dowiedz się więcej na temat środowisk zarządzania między dzierżawcami](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="updates-to-network-recommendations"></a>Aktualizacje zaleceń dotyczących sieci

Azure Security Center (ASC) uruchomił nowe zalecenia dotyczące sieci i udoskonalono niektóre z nich. Teraz użycie Security Center zapewnia jeszcze większą ochronę sieci dla zasobów. 

[Dowiedz się więcej na temat zaleceń dotyczących sieci](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="adaptive-network-hardening---generally-available"></a>Adaptacyjne ograniczanie sieci — ogólnie dostępne

Jednym z największych powierzchni ataku dla obciążeń działających w chmurze publicznej są połączenia z i z publicznego Internetu. Nasi klienci mogą wiedzieć, które reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) powinny być stosowane, aby upewnić się, że obciążenia platformy Azure są dostępne tylko dla wymaganych zakresów źródłowych. Korzystając z tej funkcji, Security Center uczenie ruchu sieciowego i wzorców łączności obciążeń platformy Azure i zapewnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu. Dzięki temu Klient może lepiej skonfigurować zasady dostępu do sieci i ograniczyć ich narażenie na ataki. 

[Dowiedz się więcej o ograniczaniu funkcjonalności sieci adaptacyjnej](security-center-adaptive-network-hardening.md).