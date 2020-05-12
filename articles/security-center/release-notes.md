---
title: Informacje o wersji Azure Security Center
description: Opis nowości i zmian w programie Azure Security Center.
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
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210898"
---
# <a name="whats-new-in-azure-security-center"></a>Co nowego w Azure Security Center?

Zabezpieczenia platformy Azure są aktywnie opracowywane i nieustannie odbierają ulepszenia. Aby zachować aktualność w zakresie najnowszych zmian, ta strona zawiera informacje na temat:

- Nowe funkcje
- Poprawki błędów
- Funkcje uznane za przestarzałe

Ta strona jest regularnie aktualizowana, więc często należy ją ponownie odwiedzać. Jeśli szukasz elementów starszych niż sześć miesięcy, znajdziesz je w [archiwum, co nowego w programie Azure Security Center](release-notes-archive.md).


## <a name="may-2020"></a>Maj 2020 r.

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Zmiany dostępu do maszyny wirtualnej just-in-Time (JIT)

Security Center zawiera opcjonalną funkcję ochrony portów zarządzania maszynami wirtualnymi. Zapewnia to obronę przed najbardziej powszechnymi atakami na ataki.

Ta aktualizacja wprowadza następujące zmiany w tej funkcji:

- Zalecenie, które zalecić włączenie JIT na maszynie wirtualnej, zostało zmienione. Wcześniej na maszynach wirtualnych należy zastosować kontrolę dostępu do sieci just-in-Time, która jest teraz: "porty zarządzania maszyn wirtualnych należy chronić za pomocą kontroli dostępu just in Time do sieci".

- Zalecenie zostało wyzwolone tak, aby było wyzwalane tylko wtedy, gdy istnieją otwarte porty zarządzania.

[Dowiedz się więcej o funkcji dostępu JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Zalecenia niestandardowe zostały przeniesione do oddzielnej kontroli zabezpieczeń

Jednym z kontrolek zabezpieczeń wprowadzonych wraz z ulepszonym wynikiem jest "implementacja najlepszych rozwiązań w zakresie zabezpieczeń". Wszelkie niestandardowe zalecenia utworzone dla subskrypcji zostały automatycznie umieszczone w tym formancie. 

Aby ułatwić znalezienie niestandardowych zaleceń, przeniesiono je do dedykowanej kontroli zabezpieczeń, "zalecenia niestandardowe". Ten formant nie ma wpływu na Twój bezpieczny wynik.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Przełącznik dodany do wyświetlania zaleceń w kontrolkach lub jako płaska lista

Funkcje kontroli zabezpieczeń są logicznymi grupami powiązanych zaleceń dotyczących zabezpieczeń. Odzwierciedlają one podatne na ataki. Kontrolka jest zestawem zaleceń dotyczących zabezpieczeń, z instrukcjami, które ułatwiają zaimplementowanie tych zaleceń.

Aby od razu sprawdzić, jak dobrze organizacja zabezpiecza poszczególne osoby atakujące, zapoznaj się z wynikami każdej kontroli zabezpieczeń.

Domyślnie Twoje zalecenia są wyświetlane w obszarze zabezpieczenia. Z tej aktualizacji można także wyświetlić je jako listę. Aby wyświetlić je jako prostą listę posortowaną według stanu kondycji odpowiednich zasobów, Użyj nowego przełącznika "Grupuj według kontrolek". Przełącznik znajduje się nad listą w portalu.

Formanty zabezpieczeń — i ten przełącznik — są częścią nowego bezpiecznego wyniku. Pamiętaj, aby wysłać nam swoją opinię z portalu.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>Zalecenia dotyczące zabezpieczeń konta przeniesione do kontroli zabezpieczeń "najlepsze rozwiązania w zakresie zabezpieczeń"

Jednym z kontrolek zabezpieczeń wprowadzonych w ramach zwiększonego bezpiecznego wyniku jest "najlepszych praktyk w zakresie zabezpieczeń". Gdy zalecenie jest w tej kontrolce, nie ma to wpływu na bezpieczny wynik. 

W przypadku tej aktualizacji trzy zalecenia zostały przeniesione z kontrolek, w których zostały one pierwotnie umieszczone, i do tej kontroli najlepszych rozwiązań. Ten krok został osiągnięty, ponieważ wykryto, że ryzyko tych trzech zaleceń jest mniejsze niż początkowo zostało przemyślane.

Zalecenia są następujące:

- Usługę MFA należy włączyć na kontach z uprawnieniami do odczytu w ramach subskrypcji (wcześniej w formancie "Włącz usługę MFA").
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji (wcześniej w kontrolce "Zarządzanie dostępem i uprawnieniami").
- Dla subskrypcji (początkowo w kontrolce "Zarządzaj dostępem i uprawnieniami") należy wyznaczyć maksymalnie 3 właścicieli.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-generally-available"></a>Zasady niestandardowe z niestandardowymi metadanymi są ogólnie dostępne

Zasady niestandardowe są teraz częścią środowiska zaleceń Security Center, bezpieczeństwa i oceny zgodności z przepisami. Ta funkcja jest teraz ogólnie dostępna i umożliwia zwiększenie zakresu oceny zabezpieczeń w organizacji w Security Center. 

Utwórz niestandardową inicjatywę w usłudze Azure Policy, Dodaj do niej zasady i Dołącz ją do Azure Security Center i Wizualizuj ją jako zalecenia.

Teraz dodano również opcję edytowania niestandardowych metadanych rekomendacji. Opcje metadanych obejmują ważność, kroki korygowania, informacje o zagrożeniach i nie tylko.  

[Dowiedz się więcej o ulepszaniu niestandardowych zaleceń ze szczegółowymi informacjami](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="dynamic-compliance-packages-now-generally-available"></a>Dynamiczne pakiety zgodności są teraz ogólnie dostępne

Pulpit nawigacyjny zgodności z przepisami Azure Security Center obejmuje teraz **dynamiczne pakiety zgodności** (teraz ogólnie dostępne) do śledzenia dodatkowych standardów branżowych i prawnych.

Dynamiczne pakiety zgodności można dodać do swojej subskrypcji lub grupy zarządzania ze strony Security Center zasady zabezpieczeń. Po dodaniu standardowego lub testu porównawczego Standard pojawia się na pulpicie nawigacyjnym zgodności z przepisami, który ma wszystkie skojarzone dane zgodności zamapowane jako oceny. Raport podsumowujący dla dowolnych ze standardów, które zostały dołączone, będzie dostępny do pobrania.

Teraz możesz dodawać standardy, takie jak:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-V2020**
- **Oficjalne Królestwo brytyjskie i Zjednoczone Królestwo NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nowy)** (czyli bardziej kompletna reprezentacja usługi Azure CIS 1.1.0)

Ponadto został niedawno dodany **test testów zabezpieczeń platformy Azure**, oparte na platformie Azure wskazówki dotyczące zabezpieczeń i zgodności w oparciu o typowe struktury zgodności. Dodatkowe standardy będą obsługiwane na pulpicie nawigacyjnym, gdy staną się dostępne.  
 
[Dowiedz się więcej o dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Zalecenia dotyczące tożsamości zawarte obecnie w Azure Security Center warstwy Bezpłatna

Zalecenia dotyczące zabezpieczeń dotyczące tożsamości i dostępu w Azure Security Center warstwy Bezpłatna są teraz ogólnie dostępne. Jest to część nakładu pracy, aby udostępnić funkcje usługi Cloud Security stan Management (CSPM). Do tej pory te zalecenia były dostępne tylko w warstwie cenowej standardowa.

Przykłady zaleceń dotyczących tożsamości i dostępu to:

- "Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji".
- "Dla subskrypcji należy wyznaczyć maksymalnie trzech właścicieli".
- "Przestarzałe konta powinny zostać usunięte z subskrypcji".

Jeśli masz subskrypcje w bezpłatnej warstwie cenowej, będzie to miało wpływ na ich bezpieczeństwo, ponieważ nigdy nie oceniono ich pod kątem bezpieczeństwa tożsamości i dostępu.

[Dowiedz się więcej o zaleceniach dotyczących tożsamości i dostępu](recommendations-reference.md#recs-identity).
[Dowiedz się więcej o monitorowaniu tożsamości i dostępu](security-center-identity-access.md).


## <a name="march-2020"></a>Marzec 2020 r.

### <a name="workflow-automation-is-now-generally-available"></a>Automatyzacja przepływu pracy jest teraz ogólnie dostępna

Funkcja automatyzacji przepływu pracy Azure Security Center jest teraz ogólnie dostępna. Służy do automatycznego wyzwalania Logic Apps na temat alertów zabezpieczeń i zaleceń. Ponadto są dostępne wyzwalacze ręczne dla alertów i wszystkie zalecenia z opcją szybkie rozwiązanie.

Każdy program zabezpieczeń zawiera wiele przepływów pracy dotyczących odpowiedzi na zdarzenia. Procesy te mogą obejmować Powiadamianie właściwych uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych czynności zaradczych. Specjaliści ds. zabezpieczeń zaleca się zautomatyzować dowolną liczbę kroków tych procedur. Automatyzacja redukuje obciążenie i może poprawić bezpieczeństwo, zapewniając, że etapy procesu są wykonywane szybko, spójnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

Aby uzyskać więcej informacji o automatycznym i ręcznym Security Center możliwości uruchamiania przepływów pracy, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

Aby dowiedzieć się więcej na temat tworzenia Logic Apps, zobacz [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integracja Azure Security Center z centrum administracyjnym systemu Windows

Teraz można przenieść lokalne serwery z systemem Windows z centrum administracyjnego systemu Windows bezpośrednio do Azure Security Center. Security Center następnie przejdziesz do jednego okienka Glass, aby wyświetlić informacje o zabezpieczeniach dla wszystkich zasobów centrum administratora systemu Windows, w tym serwerów lokalnych, maszyn wirtualnych i dodatkowych obciążeń PaaS.

Po przeniesieniu serwera z centrum administracyjnego systemu Windows do Azure Security Center można:

- Wyświetlanie alertów zabezpieczeń i zaleceń w Security Center rozszerzeniu centrum administracyjnego systemu Windows.
- Zapoznaj się z zabezpieczeniami stan i Pobierz dodatkowe szczegółowe informacje o zarządzanych serwerach centrum administracyjnego systemu Windows w Security Center w ramach Azure Portal (lub za pośrednictwem interfejsu API).

Dowiedz się więcej o tym, jak [zintegrować Azure Security Center z centrum administracyjnym systemu Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Ochrona usługi Azure Kubernetes Service

Azure Security Center rozszerza funkcje zabezpieczeń kontenerów w celu ochrony usługi Azure Kubernetes Service (AKS).

Popularna Kubernetes platformy Open Source została przyjęta tak, aby była teraz standardem branżowym dla aranżacji kontenerów. Pomimo tej rozległej implementacji nadal nie można zrozumieć, jak zabezpieczyć środowisko Kubernetes. Obronność obszarów ataku aplikacji kontenerowej wymaga ekspertyzy, aby zapewnić, że infrastruktura została skonfigurowana bezpiecznie i stale monitorowana pod kątem potencjalnych zagrożeń.

Ochrona Security Center obejmuje:

- **Odnajdywanie i widoczność** — ciągłe wykrywanie zarządzanych wystąpień AKS w ramach subskrypcji zarejestrowanych do Security Center.
- **Zalecenia dotyczące zabezpieczeń** — zalecenia z możliwością podejmowania działań, które ułatwiają zgodność z najlepszymi rozwiązaniami w zakresie zabezpieczeń dla AKS. Te zalecenia są zawarte w zabezpieczonym wyniku, aby upewnić się, że są one widoczne jako część stan zabezpieczeń organizacji. Przykładem zalecenia powiązanego z AKSem jest "kontrola dostępu oparta na rolach powinna być używana do ograniczania dostępu do klastra usługi Kubernetes Service".
- **Ochrona przed zagrożeniami** — dzięki ciągłej analizie wdrożenia AKS Security Center ostrzega o zagrożeniach i złośliwych działaniach wykrytych na poziomie klastra hosta i AKS.

[Dowiedz się więcej o integracji usług Azure Kubernetes Services z usługą Security Center](azure-kubernetes-service-integration.md).
[Dowiedz się więcej o funkcjach zabezpieczeń kontenera w Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Udoskonalone środowisko just in Time

Funkcje, operacje i interfejs użytkownika dla narzędzi just in Time Azure Security Center, które zabezpieczają porty zarządzania, zostały ulepszone w następujący sposób: 

- **Pole uzasadnienia** — podczas żądania dostępu do maszyny wirtualnej za pomocą strony just-in-time w Azure Portal jest dostępne nowe pole opcjonalne, aby wprowadzić uzasadnienie żądania. Informacje wprowadzane do tego pola mogą być śledzone w dzienniku aktywności. 
- **Automatyczne oczyszczanie nadmiarowych reguł just-in-Time (JIT)** — za każdym razem, gdy aktualizujesz zasady JIT, narzędzie do czyszczenia zostanie automatycznie uruchomione, aby sprawdzić poprawność całego zestawu reguł. Narzędzie szuka niezgodności między regułami w zasadach i regułami w sieciowej grupy zabezpieczeń. Jeśli narzędzie do czyszczenia znajdzie niezgodność, określa przyczynę i, gdy jest to bezpieczne, usuwa wbudowane reguły, które nie są już potrzebne. Oczyszczarka nigdy nie usuwa reguł, które zostały utworzone. 

[Dowiedz się więcej o funkcji dostępu JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dwie zalecenia dotyczące zabezpieczeń dla aplikacji sieci Web przestarzałe

Dwie zalecenia dotyczące zabezpieczeń związane z aplikacjami sieci Web są przestarzałe: 

- Reguły dla aplikacji sieci Web w IaaS sieciowych grup zabezpieczeń powinny być zaostrzone.
    (Powiązane zasady: reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web na IaaS powinny być zaostrzone)

- Dostęp do App Services powinien być ograniczony.
    (Powiązane zasady: dostęp do App Services powinien być ograniczony [wersja zapoznawcza])

Te rekomendacje nie będą już wyświetlane na liście Security Center zaleceń. Powiązane zasady nie będą już uwzględniane w ramach inicjatywy o nazwie "Security Center domyślne".

[Dowiedz się więcej o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).

## <a name="february-2020"></a>Luty 2020 r.

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>Wykrywanie ataków bezplikowych w systemie Linux jest teraz w wersji zapoznawczej

Gdy osoby atakujące zwiększą wykorzystanie metod stealthier, aby uniknąć wykrywania, Azure Security Center rozszerza wykrywanie ataków bez plików dla systemu Linux, a nie tylko z systemem Windows. Ataki bezplikowe wykorzystują luki w zabezpieczeniach, wprowadzają złośliwe ładunki do niegroźnych procesów systemu i ukrywają w pamięci. Techniki te:

- Minimalizowanie lub eliminowanie śladów złośliwego oprogramowania na dysku
- znacznie zmniejsz szanse wykrywania przez rozwiązania skanujące złośliwe oprogramowanie oparte na dyskach

Aby wyeliminować to zagrożenie, Azure Security Center wydane wykrywanie ataków bezplików dla systemu Windows w październiku 2018 i obecnie rozszerzono wykrywanie ataków bezplikowych w systemie Linux. 


## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="enhanced-secure-score"></a>Ulepszony bezpieczny wynik

Rozszerzona wersja funkcji bezpiecznego oceny Azure Security Center jest teraz dostępna w wersji zapoznawczej. W tej wersji wiele zaleceń jest pogrupowanych w kontrolki zabezpieczeń, które lepiej odzwierciedlają zagrożone powierzchnie ataków (na przykład ograniczanie dostępu do portów zarządzania).

Zapoznaj się z informacjami o bezpiecznych zmianach oceny w fazie wersji zapoznawczej i ustal inne korygowanie, które pomogą Ci zapewnić dalsze bezpieczeństwo środowiska.

Dowiedz się więcej w temacie [ulepszony bezpieczny wynik (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).