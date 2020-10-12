---
title: SWIFT dostawcy CSP — przykładowe kontrolki planu CSCF V2020
description: Kontrolowanie mapowania SWIFT przykładowego planu CSP-CSCF V2020. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 08/18/2020
ms.topic: sample
ms.openlocfilehash: 868971b0523e077f0f76c0340a87df550448913a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540816"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Sterowanie mapowaniem SWIFT programu CSP-CSCF V2020 plan

W poniższym artykule szczegółowo opisano sposób, w jaki usługa Azure planuje SWIFT programu CSP — przykład strategii CSCF V2020 Maps do kontrolek SWIFT CSP-CSCF V2020. Aby uzyskać więcej informacji na temat kontrolek, zobacz [SWIFT CSP-CSCF V2020](https://www.swift.com/myswift/customer-security-programme-csp).

Następujące mapowania są do kontrolek **SWIFT CSP-CSCF V2020** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję ** \[ wersja zapoznawcza \] : Inspekcja SWIFT programu CSP — CSCF V2020 Controls i Wdróż określone rozszerzenia maszyn wirtualnych w celu obsługi funkcji inspekcji** wbudowanych w ramach zasad.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>Zarządzanie kontami 1,2 i 5,1

Ten plan ułatwia przeglądanie kont, które mogą nie być zgodne z wymaganiami dotyczącymi zarządzania kontami w organizacji. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję kont zewnętrznych z uprawnieniami odczyt, zapis i właściciel na kontach subskrypcji i przestarzałych. Przeglądając konta poddane inspekcji przez te zasady, można podjąć odpowiednie działania w celu zapewnienia spełnienia wymagań dotyczących zarządzania kontami.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2,6, 5,1, 6,4 i 6.5 Zarządzanie kontami | Role-Based schematy

[Kontrola dostępu oparta na rolach](../../../../role-based-access-control/overview.md) (Azure RBAC), która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje również definicje [Azure Policy](../../../policy/overview.md) do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ponadto ten plan przypisuje definicję Azure Policy, aby przeprowadzić inspekcję użycia niestandardowych reguł RBAC platformy Azure. Informacje o tym, gdzie są zaimplementowane niestandardowe reguły RBAC platformy Azure, mogą pomóc w sprawdzeniu potrzeby i poprawnej implementacji, ponieważ niestandardowe reguły RBAC platformy Azure są podatne na błędy.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9 zarządzanie kontami | Monitorowanie konta/nietypowe użycie

Dostęp z maszyn wirtualnych just-in-Time (JIT) blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Wszystkie żądania JIT dotyczące dostępu do maszyn wirtualnych są rejestrowane w dzienniku aktywności, co pozwala na monitorowanie nietypowego użycia. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in Time, ale jeszcze nie zostały skonfigurowane.

- Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci

## <a name="13-51-and-64-separation-of-duties"></a>1,3, 5,1 i 6,4 separacja obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie pozwala na nadmiarowość administracyjną. Bez względu na to, że zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć prawdopodobieństwo naruszenia za pośrednictwem konta właściciela z naruszeniem. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję liczby właścicieli subskrypcji platformy Azure. Ten plan przypisuje również definicje Azure Policy, które ułatwiają kontrolowanie członkostwa w grupie Administratorzy na maszynach wirtualnych z systemem Windows. Zarządzanie właścicielami subskrypcji i uprawnieniami administratora maszyny wirtualnej może pomóc w zaimplementowaniu odpowiedniego rozdzielenia obowiązków.

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1,3, 5,1 i 6,4 — najniższe uprawnienia | Przegląd uprawnień użytkownika

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) do kont inspekcji, dla których należy określić priorytety. Przejrzenie tych wskaźników konta może pomóc w zapewnieniu implementacji najniższych uprawnień.

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie są przyłączone do określonej domeny
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które nie są przyłączone do określonej domeny
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

## <a name="22-and-27-security-attributes"></a>2,2 i 2,7 atrybuty zabezpieczeń

Funkcja odnajdywania i klasyfikowania danych Advanced Data Security dla Azure SQL Database zapewnia możliwości odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami. Zaawansowane zabezpieczenia danych mogą pomóc w zapewnieniu informacji związanych z odpowiednimi atrybutami zabezpieczeń w organizacji. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) do monitorowania i wymuszania korzystania z zaawansowanych zabezpieczeń danych w programie SQL Server. 

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2,2, 2,7, 4,1 i 6,1 dostępu zdalnego | Zautomatyzowane monitorowanie/kontrola

Ten plan ułatwia monitorowanie i kontrolowanie dostępu zdalnego przez przypisanie definicji [Azure Policy](../../../policy/overview.md) do monitorów, że debugowanie zdalne dla Azure App Service aplikacji jest wyłączone i definicje zasad, które przeprowadzają inspekcję maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez haseł. Ten plan przypisuje również definicję Azure Policy, która ułatwia monitorowanie nieograniczonego dostępu do kont magazynu. Monitorowanie tych wskaźników może pomóc zapewnić zgodność metod dostępu zdalnego z zasadami zabezpieczeń.

- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Konta magazynu powinny ograniczać dostęp do sieci
- Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API
- Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1,3 i 6,4 zawartość rekordów inspekcji | Scentralizowane zarządzanie zaplanowaną zawartością rekordu inspekcji

Dane dziennika zbierane przez Azure Monitor są przechowywane w Log Analytics obszarze roboczym, co pozwala na scentralizowaną konfigurację i zarządzanie. Ten plan pomaga zapewnić, że zdarzenia są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które przeprowadzają inspekcję i egzekwowanie wdrożenia agenta log Analytics na maszynach wirtualnych platformy Azure.

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2,2, 2,7 i 6,4 odpowiedź na błędy przetwarzania inspekcji

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które monitorują konfiguracje inspekcji i rejestrowania zdarzeń. Monitorowanie tych konfiguracji może zapewnić wskaźnik awarii systemu inspekcji lub błędnej konfiguracji i pomóc w podejmowaniu działań naprawczych.

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja w programie SQL Server powinna być włączona

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>Przegląd, analiza i raportowanie inspekcji 1,3 i 6,4 | Przegląd i analiza Centralna

Dane dziennika zbierane przez Azure Monitor są przechowywane w Log Analytics obszarze roboczym, co umożliwia scentralizowane raportowanie i analizę. Ten plan pomaga zapewnić, że zdarzenia są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które przeprowadzają inspekcję i egzekwowanie wdrożenia agenta log Analytics na maszynach wirtualnych platformy Azure.

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows

## <a name="13-22-27-64-and-65a-audit-generation"></a>1,3, 2,2, 2,7, 6,4 i 6.5 Generowanie inspekcji

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Te definicje zasad przeprowadzają inspekcję i egzekwowanie wdrożenia agenta Log Analytics na maszynach wirtualnych platformy Azure oraz konfigurację ustawień inspekcji dla innych typów zasobów platformy Azure. Te definicje zasad umożliwiają również inspekcję konfiguracji dzienników diagnostycznych w celu zapewnienia wglądu w operacje wykonywane w ramach zasobów platformy Azure. Ponadto na serwerach SQL są konfigurowane inspekcje i zaawansowane zabezpieczenia danych.

- Inspekcja wdrożenia agenta Log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Wdróż VM Scale Sets Log Analytics agenta dla systemu Linux (VMSS)
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux
- Wdróż Log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Inspekcja w programie SQL Server powinna być włączona
- Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń

## <a name="11-least-functionality--prevent-program-execution"></a>1,1 najmniejsza funkcjonalność | Zapobiegaj wykonywaniu programu

Adaptacyjna kontrola aplikacji w Azure Security Center to inteligentne, zautomatyzowane kompleksowe rozwiązanie do filtrowania aplikacji, które może blokować uruchamianie określonego oprogramowania na maszynach wirtualnych lub uniemożliwiać jego działanie. Kontrolka aplikacji może działać w trybie wymuszania, który uniemożliwia uruchomienie niezatwierdzonej aplikacji. Ten plan przypisuje definicję Azure Policy, która pomaga monitorować maszyny wirtualne, w których lista dozwolonych aplikacji jest zalecana, ale nie została jeszcze skonfigurowana.

- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1,1 najmniejsza funkcjonalność | Autoryzowane oprogramowanie/listy dozwolonych

Adaptacyjna kontrola aplikacji w Azure Security Center to inteligentne, zautomatyzowane kompleksowe rozwiązanie do filtrowania aplikacji, które może blokować uruchamianie określonego oprogramowania na maszynach wirtualnych lub uniemożliwiać jego działanie. Kontrola aplikacji ułatwia tworzenie zatwierdzonych list aplikacji dla maszyn wirtualnych. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, w których lista dozwolonych aplikacji jest zalecana, ale nie została jeszcze skonfigurowana.

- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.

## <a name="11-user-installed-software"></a>1,1 User-Installed oprogramowanie

Adaptacyjna kontrola aplikacji w Azure Security Center to inteligentne, zautomatyzowane kompleksowe rozwiązanie do filtrowania aplikacji, które może blokować uruchamianie określonego oprogramowania na maszynach wirtualnych lub uniemożliwiać jego działanie. Kontrola aplikacji może ułatwić wymuszanie i monitorowanie zgodności z zasadami ograniczeń oprogramowania. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, w których lista dozwolonych aplikacji jest zalecana, ale nie została jeszcze skonfigurowana.

- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4,2 Identyfikacja i uwierzytelnianie (Użytkownicy w organizacji) | Dostęp sieciowy do kont uprzywilejowanych

Ten plan pomaga ograniczyć dostęp uprzywilejowany i kontrolować go, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w subskrypcji

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4,2 Identyfikacja i uwierzytelnianie (Użytkownicy w organizacji) | Dostęp sieciowy do kont nieuprzywilejowanych

Ten plan pomaga ograniczyć i kontrolować dostęp przez przypisanie definicji [Azure Policy](../../../policy/overview.md) do inspekcji kont z uprawnieniami do odczytu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

## <a name="23-and-41-authenticator-management"></a>Zarządzanie wystawcy 2,3 i 4,1

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez haseł i/lub mają ustawione nieprawidłowe uprawnienia w pliku haseł. Ten plan przypisuje również definicje zasad, które przeprowadzają inspekcję konfiguracji typu szyfrowania hasła dla maszyn wirtualnych z systemem Windows. Monitorowanie tych wskaźników pomaga upewnić się, że uwierzytelniające systemu są zgodne z zasadami identyfikacji i uwierzytelniania w organizacji.

- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które nie mają uprawnień do pliku haseł ustawione na 0644
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku haseł ustawione na 0644
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które mają konta bez hasła
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które mają konta bez hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego
- Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2,3 i 4,1 Zarządzanie wystawcy Uwierzytelnianie Password-Based

Ten plan pomaga wymusić silne hasła, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję maszyn wirtualnych systemu Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących haseł. Świadomość maszyn wirtualnych z naruszeniem zasad dotyczących siły haseł pomaga podejmować działania naprawcze w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami haseł obowiązującymi w organizacji.

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows bez maksymalnego wieku hasła wynoszącego 70 dni
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego

## <a name="22-and-27-vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach 2,2 i 2,7

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacji przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , które monitorują luki w zabezpieczeniach systemu operacyjnego, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyn wirtualnych w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym. Ten plan przypisuje również definicje zasad, które przeprowadzają inspekcję i wymuszanie zaawansowanych zabezpieczeń danych na serwerach SQL. Zaawansowane zabezpieczenia danych obejmują ocenę luk w zabezpieczeniach i zaawansowane funkcje ochrony przed zagrożeniami, które ułatwiają zrozumienie luk w zabezpieczeniach wdrożonych zasobów.

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Inspekcja w programie SQL Server powinna być włączona
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować 
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować

## <a name="13-denial-of-service-protection"></a>1,3 odmowa usługi

Funkcja rozproszonej odmowy usługi (DDoS) platformy Azure oferuje dodatkowe funkcje i możliwości ograniczenia w porównaniu z podstawową warstwą usług. Te dodatkowe funkcje obejmują integrację Azure Monitor i możliwość przeglądania raportów dotyczących łagodzenia po ataku. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która przeprowadza inspekcję po włączeniu warstwy Standard DDoS. Zrozumienie różnic między warstwami usług może ułatwić wybranie najlepszego rozwiązania umożliwiającego odmowę ochrony w środowisku platformy Azure.

- Należy włączyć Standard Azure DDoS Protection

## <a name="11-and-61-boundary-protection"></a>1,1 i 6,1 — ochrona granic

Ten plan ułatwia zarządzanie granicą systemu i sterowanie nią przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , która monitoruje zalecenia dotyczące ograniczania funkcjonalności sieciowych grup zabezpieczeń w programie Azure Security Center. Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, aby zmniejszyć potencjalną powierzchnię ataku.
Ponadto ten plan przypisuje również definicje zasad, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, i konta magazynu z nieograniczonym dostępem mogą zezwalać na nieograniczony dostęp do informacji zawartych w systemie informacyjnym.

- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.
- Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

## <a name="29a-boundary-protection--access-points"></a>2.9 ochrona granic | Punkty dostępu

Dostęp z maszyn wirtualnych just-in-Time (JIT) blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Dostęp do maszyny wirtualnej JIT pomaga ograniczyć liczbę połączeń zewnętrznych do zasobów na platformie Azure. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in Time, ale jeszcze nie zostały skonfigurowane.

- Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9 ochrona granic | Zewnętrzne usługi telekomunikacyjne

Dostęp z maszyn wirtualnych just-in-Time (JIT) blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Dostęp do maszyny wirtualnej JIT pomaga zarządzać wyjątkami względem zasad przepływu ruchu, ułatwiając proces żądania dostępu i zatwierdzania. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in Time, ale jeszcze nie zostały skonfigurowane.

- Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2,1, 2,4, 2.4 a, 2.5 A i 2,6 poufność i integralność transmisji | Kryptograficzna lub alternatywna ochrona fizyczna

Ten plan pomaga chronić poufne i integralność przesyłanych informacji przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które ułatwiają monitorowanie mechanizmu kryptograficznego zaimplementowanego dla protokołów komunikacyjnych. Zapewnienie, że komunikacja jest prawidłowo zaszyfrowana, może pomóc spełnić wymagania organizacji lub chronić informacje przed nieautoryzowanym ujawnieniem i modyfikacją.

- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Wdróż wymagania wstępne w celu inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Należy włączyć bezpieczny transfer na konta magazynu
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2,2, 2,3, 2,5, 4,1 i 2,7 ochrona informacji w stanie spoczynku | Ochrona kryptograficzna

Ten plan pomaga wymusić zasady korzystania z formantów cryptograph, aby chronić informacje przechowywane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych. Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Definicje zasad przypisane przez ten plan wymagają szyfrowania dla kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; i Przeprowadź inspekcję braku szyfrowania dla baz danych SQL, dysków maszyny wirtualnej i zmiennych konta usługi Automation.

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Wdróż przezroczyste szyfrowanie danych SQL DB
- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="13-22-and-27-flaw-remediation"></a>1,3, 2,2 i 2,7 Luka w zabezpieczeniach

Ten plan ułatwia zarządzanie usterkami systemu informacyjnego przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, luki SQL i luki w zabezpieczeniach maszyn wirtualnych w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym. Ten plan przypisuje również definicję zasad, która zapewnia stosowanie poprawek systemu operacyjnego dla zestawów skalowania maszyn wirtualnych.

- Wymagaj automatycznej poprawki obrazu systemu operacyjnego na Virtual Machine Scale Sets
- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych
- Aktualizacje systemu powinny być zainstalowane na maszynach wirtualnych
- Inspekcja wdrożenia agenta zależności w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (system operacyjny) nie został wystawiony
- Zmienne konta usługi Automation powinny być szyfrowane
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach wirtualnych należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować

## <a name="61-malicious-code-protection"></a>6,1 złośliwe zabezpieczenie kodu

Ten plan ułatwia zarządzanie programem Endpoint Protection, w tym złośliwą ochroną kodu, przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brak ochrony punktu końcowego na maszynach wirtualnych w Azure Security Center i wymuszają rozwiązanie Microsoft chroniące przed złośliwym oprogramowaniem na maszynach wirtualnych z systemem Windows.

- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager

## <a name="61-malicious-code-protection--central-management"></a>6,1 złośliwe zabezpieczenie kodu | Zarządzanie centralne

Ten plan ułatwia zarządzanie programem Endpoint Protection, w tym złośliwą ochroną kodu, przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brak ochrony punktu końcowego na maszynach wirtualnych w programie Azure Security Center. Azure Security Center udostępnia funkcje scentralizowanego zarządzania i raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym.

- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1,1, 1,3, 2,2, 2,7, 2,8 i 6,4 monitorowanie systemu informacji

Ten plan ułatwia monitorowanie systemu przez inspekcjonowanie i wymuszanie rejestrowania i zabezpieczeń danych w ramach zasobów platformy Azure. W odniesieniu do zasad są przypisane inspekcje i egzekwowanie wdrożenia agenta Log Analytics oraz ulepszone ustawienia zabezpieczeń baz danych SQL, kont magazynu i zasobów sieciowych. Te możliwości mogą pomóc w wykrywaniu nietypowych zachowań i wskaźników ataków, co pozwala na podejmowanie odpowiednich działań.

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Wdróż VM Scale Sets Log Analytics agenta dla systemu Linux (VMSS)
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux
- Wdróż Log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)
- Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Wdrażanie inspekcji na serwerach SQL
- Wdróż obserwatora sieciowego po utworzeniu sieci wirtualnych
- Wdrażanie wykrywania zagrożeń na serwerach SQL

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitorowanie systemu informacji 2,2 i 2,8 | Analizuj ruch/przekonwertować Eksfiltracji

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Alerty ochrony obejmują nietypowe wzorce dostępu, wyodrębniania anomalii/operacje przekazywania oraz podejrzane działania magazynu. Te wskaźniki mogą pomóc wykryć przekonwertować eksfiltracji informacji.

- Wdrażanie wykrywania zagrożeń na serwerach SQL

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych.

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontrolki SWIFT planu CSP-CSCF V2020, odwiedź następujące artykuły, aby dowiedzieć się więcej na temat planu i sposobu wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF V2020 — Omówienie](./index.md) 
>  [SWIFT CSP-CSCF V2020 plan — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
