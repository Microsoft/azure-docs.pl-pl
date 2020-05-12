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
ms.openlocfilehash: c0883e91d5e806fb166c3ddeafc4ce130ff3f66f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210845"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Co nowego w Azure Security Center?

Podstawowe [Informacje o nowościach Azure Active Directory?](release-notes.md) Strona informacje o wersji zawiera aktualizacje dla ostatnich sześciu miesięcy, a ta strona zawiera starsze elementy.

Ta strona zawiera informacje na temat:

- Nowe funkcje
- Poprawki błędów
- Funkcje uznane za przestarzałe

## <a name="november-2019"></a>Listopad 2019 r.

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>Ochrona przed zagrożeniami dla Azure Key Vault w publicznej wersji zapoznawczej w Ameryka Północna regionach

Azure Key Vault to podstawowa usługa służąca do ochrony danych i poprawiania wydajności aplikacji w chmurze, oferując możliwość centralnego zarządzania kluczami, wpisami tajnymi, kluczami kryptograficznymi i zasadami w chmurze. Ponieważ Azure Key Vault przechowuje wrażliwe i krytyczne dane biznesowe, wymaga maksymalnego poziomu zabezpieczeń dla magazynów kluczy i przechowywanych w nich danych.

Azure Security Center obsługa ochrony przed zagrożeniami dla Azure Key Vault stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do magazynów kluczy lub korzystania z nich. Ta nowa warstwa ochrony pozwala klientom na rozwiązywanie zagrożeń związanych z ich magazynami kluczy bez konieczności eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń. Ta funkcja jest dostępna w publicznej wersji zapoznawczej w regionach Ameryka Północna.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Ochrona przed zagrożeniami w usłudze Azure Storage obejmuje osłanianie reputacji złośliwego oprogramowania

Ochrona przed zagrożeniami dla usługi Azure Storage oferuje nowe wykrycia obsługiwane przez funkcję analizy zagrożeń firmy Microsoft w celu wykrywania przeładowania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji i podejrzanego dostępu z aktywnego węzła zakończenia sieci Tor (anonymizing proxy). Można teraz wyświetlać wykryte złośliwe oprogramowanie na kontach magazynu przy użyciu Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatyzacja przepływu pracy z Logic Apps (wersja zapoznawcza)

Organizacje z centralnie zarządzanymi zabezpieczeniami i działem IT/Operations implementują wewnętrzne procesy przepływu pracy, aby zapewnić wymaganą akcję w organizacji w przypadku wykrycia niezgodności w swoich środowiskach. W wielu przypadkach te przepływy pracy są powtarzalnymi procesami, a Automatyzacja może znacznie usprawnić procesy w organizacji.

Dzisiaj wprowadzamy nową funkcję w Security Center, która umożliwia klientom Tworzenie konfiguracji automatyzacji wykorzystujących Azure Logic Apps i tworzenie zasad, które będą automatycznie wyzwalać je na podstawie określonych ustaleń ASC, takich jak zalecenia lub alerty. Aplikację logiki platformy Azure można skonfigurować w taki sposób, aby wykonać dowolną akcję niestandardową obsługiwaną przez rozległą społeczność łączników aplikacji logiki lub użyć jednego z szablonów dostarczonych przez Security Center, takich jak wysyłanie wiadomości e-mail lub otwieranie biletu usługi usługi ServiceNow™.

Aby uzyskać więcej informacji o automatycznym i ręcznym Security Center możliwości uruchamiania przepływów pracy, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

Aby dowiedzieć się więcej na temat tworzenia Logic Apps, zobacz [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


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

[Dowiedz się więcej o ocenach luk w zabezpieczeniach Virtual Machines platformy Azure](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (wersja zapoznawcza)

Azure Security Center obsługa ochrony przed zagrożeniami i oceny luk w zabezpieczeniach usługi SQL baz danych uruchomionych na maszynach wirtualnych IaaS jest teraz w wersji zapoznawczej.

[Ocena luk w zabezpieczeniach](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) to prosta konfiguracja usługi, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów z potencjalnymi lukami w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń w ramach bezpiecznej oceny platformy Azure i zawiera kroki rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.

[Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do programu SQL Server lub korzystania z niego. Nieustannie monitoruje bazę danych pod kątem podejrzanych działań i zapewnia zorientowane na działania alerty zabezpieczeń dotyczące nietypowych wzorców dostępu do bazy danych. Te alerty zawierają szczegóły podejrzanych działań i zalecane akcje do zbadania i ograniczenia zagrożenia.


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

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Zarządzanie regułami przy użyciu ulepszeń kontroli aplikacji

Ulepszono obsługę zarządzania regułami dotyczącymi maszyn wirtualnych przy użyciu adaptacyjnych kontrolek aplikacji. Azure Security Center funkcje adaptacyjnego sterowania aplikacjami ułatwiają kontrolowanie, które aplikacje mogą być uruchamiane na maszynach wirtualnych. Oprócz ogólnej poprawy zarządzania regułami nowe korzyści umożliwiają kontrolowanie, które typy plików będą chronione po dodaniu nowej reguły.

[Dowiedz się więcej na temat adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrola zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy

Azure Security Center zalecenia dotyczące rozwiązywania luk w zabezpieczeniach kontenerów mogą teraz być włączane lub wyłączane za pośrednictwem Azure Policy.

Aby wyświetlić włączone zasady zabezpieczeń, w Security Center otworzyć stronę zasady zabezpieczeń.


## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure 

Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure jest teraz ogólnie dostępny. Korzystaj z niego, aby zabezpieczyć środowiska chronione przez zaporę platformy Azure oprócz środowisk chronionych sieciowej grupy zabezpieczeń.

Dostęp JIT do maszyny wirtualnej zmniejsza narażenie na ataki typu "Sieć" dzięki zapewnianiu kontrolowanego dostępu do maszyn wirtualnych tylko w razie potrzeby przy użyciu reguł sieciowej grupy zabezpieczeń i zapory platformy Azure.

Po włączeniu JIT dla maszyn wirtualnych należy utworzyć zasady określające porty, które mają być chronione, jak długo porty pozostają otwarte i zatwierdzone adresy IP, z których można uzyskać dostęp do tych portów. Te zasady pomagają zachować kontrolę nad tym, co użytkownicy mogą robić, gdy żądają dostępu.

Żądania są rejestrowane w dzienniku aktywności platformy Azure, dzięki czemu można łatwo monitorować i przeprowadzać inspekcję dostępu. Strona just in Time ułatwia również szybkie identyfikowanie istniejących maszyn wirtualnych z włączoną obsługą JIT oraz maszyn wirtualnych, na których jest zalecana metoda JIT.

[Dowiedz się więcej o zaporze platformy Azure](https://docs.microsoft.com/azure/firewall/overview).


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
