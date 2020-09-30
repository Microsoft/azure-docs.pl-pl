---
title: Przykładowe kontrolki Federalnej PBMM planu Kanady
description: Sterowanie mapowaniem przykładowych planów PBMMów federalnych Kanady. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 07/31/2020
ms.topic: sample
ms.openlocfilehash: b3879ee847a0cb7a4cb6d00e842c613de53579e2
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541071"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>Sterowanie mapowaniem przykładu Federal PBMM Canada

W tym artykule szczegółowo przedstawiono sposób, w jaki usługa Azure planuje zaplanowanie, ochrona "Średnia integralność, średnia dostępność (PBMM)" w programie Canada, jest mapowana na federalne kontrolki PBMM. Aby uzyskać więcej informacji na temat kontrolek, zobacz [federalne Federal PBMM](https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html).

Następujące mapowania dotyczą **federalnych** kontrolek PBMM. Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz wersję ** \[ zapoznawczą \] : Audit Federal PBMM Controls** , wbudowana inicjatywa Policy.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/canada-federal-pbmm/control-mapping.md).

## <a name="location-constraints"></a>Ograniczenia lokalizacji

Ten plan pomaga ograniczyć lokalizację wdrożenia wszystkich zasobów i grup zasobów do "Kanada Środkowa" i "Kanada Wschodnia", przypisując następujące definicje Azure Policy:

- Dozwolone lokalizacje (zostały trwale zakodowane na "Kanada Środkowa" i "Kanada Wschodnia")
- Dozwolone lokalizacje dla grup zasobów (zostały trwale zakodowane na "Kanada Środkowa" i "Kanada Wschodnia")

## <a name="ac-2-account-management"></a>Zarządzanie kontami AC-2

Ten plan ułatwia przeglądanie kont, które mogą nie być zgodne z wymaganiami dotyczącymi zarządzania kontami w organizacji. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję kont zewnętrznych z uprawnieniami odczyt, zapis i właściciel na kontach subskrypcji i przestarzałych. Przeglądając konta poddane inspekcji przez te zasady, można podjąć odpowiednie działania w celu zapewnienia spełnienia wymagań dotyczących zarządzania kontami.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Zarządzanie kontami | Schematy oparte na rolach

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach (Azure RBAC)](../../../../role-based-access-control/overview.md) na platformie Azure, która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje również definicje [Azure Policy](../../../policy/overview.md) do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ponadto ten plan przypisuje definicję Azure Policy, aby przeprowadzić inspekcję użycia niestandardowych reguł RBAC platformy Azure. Informacje o tym, gdzie są zaimplementowane niestandardowe reguły RBAC platformy Azure, mogą pomóc w sprawdzeniu potrzeby i właściwej implementacji, ponieważ niestandardowe reguły RBAC platformy Azure są podatne na błędy.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów

## <a name="ac-4-information-flow-enforcement"></a>Wymuszanie przepływu informacji AC-4

Współużytkowanie zasobów między źródłami (CORS) może zezwalać na żądania zasobów App Services z domeny zewnętrznej. Firma Microsoft zaleca, aby zezwalać na współdziałanie z interfejsami API, funkcjami i aplikacjami sieci Web tylko wymagane domeny. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia monitorowanie ograniczeń dostępu do zasobów CORS w programie Azure Security Center.
Zrozumienie implementacji mechanizmu CORS może pomóc w sprawdzeniu, czy są zaimplementowane kontrolki przepływu informacji.

- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web

## <a name="ac-5-separation-of-duties"></a>Opłaty za 5

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie pozwala na nadmiarowość administracyjną. Bez względu na to, że zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć prawdopodobieństwo naruszenia za pośrednictwem konta właściciela z naruszeniem. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję liczby właścicieli subskrypcji platformy Azure. Ten plan przypisuje również definicje Azure Policy, które ułatwiają kontrolowanie członkostwa w grupie Administratorzy na maszynach wirtualnych z systemem Windows. Zarządzanie właścicielami subskrypcji i uprawnieniami administratora maszyny wirtualnej może pomóc w zaimplementowaniu odpowiedniego rozdzielenia obowiązków.

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich

## <a name="ac-6-least-privilege"></a>Najniższe uprawnienia AC-6

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach (Azure RBAC)](../../../../role-based-access-control/overview.md) na platformie Azure, która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) do kont inspekcji, dla których należy określić priorytety. Przejrzenie tych wskaźników konta może pomóc w zapewnieniu implementacji najniższych uprawnień.

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich

## <a name="ac-7-security-attributes"></a>Atrybuty zabezpieczeń AC-7

Funkcja odnajdywania i klasyfikowania danych Advanced Data Security dla Azure SQL Database zapewnia możliwości odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami. Zaawansowane zabezpieczenia danych mogą pomóc w zapewnieniu informacji związanych z odpowiednimi atrybutami zabezpieczeń w organizacji. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) do monitorowania i wymuszania korzystania z zaawansowanych zabezpieczeń danych w programie SQL Server.

- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC – 17 (1) dostęp zdalny | Zautomatyzowane monitorowanie/kontrola

Ten plan ułatwia monitorowanie i kontrolowanie dostępu zdalnego przez przypisanie definicji [Azure Policy](../../../policy/overview.md) do monitorowania, że debugowanie zdalne dla Azure App Service aplikacji jest wyłączone. Plan przypisuje również definicje zasad, które przeprowadzają inspekcję maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez haseł. Ponadto plan przypisuje definicję Azure Policy, która ułatwia monitorowanie nieograniczonego dostępu do kont magazynu. Monitorowanie tych wskaźników może pomóc zapewnić zgodność metod dostępu zdalnego z zasadami zabezpieczeń.

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez hasła
- \[Wersja zapoznawcza \] : wdrażanie wymagań do inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API
- Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web

## <a name="au-3-2-content-of-audit-records"></a>AU-3 (2) zawartość rekordów inspekcji

Dane dziennika zbierane przez Azure Monitor są przechowywane w Log Analytics obszarze roboczym, co pozwala na scentralizowaną konfigurację i zarządzanie. Ten plan pomaga zapewnić, że zdarzenia są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które przeprowadzają inspekcję i egzekwowanie wdrożenia agenta log Analytics na maszynach wirtualnych platformy Azure.

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : inspekcja log Analytics obszaru roboczego dla maszyny wirtualnej — niezgodność raportów
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Odpowiedź na błędy przetwarzania inspekcji w usłudze AU-5

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które monitorują konfiguracje inspekcji i rejestrowania zdarzeń. Monitorowanie tych konfiguracji może zapewnić wskaźnik awarii systemu inspekcji lub błędnej konfiguracji i pomóc w podejmowaniu działań naprawczych.

- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja w programie SQL Server powinna być włączona
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) przegląd, analiza i raportowanie inspekcji | Przegląd i analiza Centralna

Dane dziennika zbierane przez Azure Monitor są przechowywane w Log Analytics obszarze roboczym, co umożliwia scentralizowane raportowanie i analizę. Ten plan pomaga zapewnić, że zdarzenia są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które przeprowadzają inspekcję i egzekwowanie wdrożenia agenta log Analytics na maszynach wirtualnych platformy Azure.

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Obszar roboczy Log Analytics inspekcji dla maszyny wirtualnej — niezgodność raportów
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows

## <a name="au-12-audit-generation"></a>Generowanie inspekcji w usłudze AU-12

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Te definicje zasad przeprowadzają inspekcję i egzekwowanie wdrożenia agenta Log Analytics na maszynach wirtualnych platformy Azure oraz konfigurację ustawień inspekcji dla innych typów zasobów platformy Azure. Te definicje zasad umożliwiają również inspekcję konfiguracji dzienników diagnostycznych w celu zapewnienia wglądu w operacje wykonywane w ramach zasobów platformy Azure. Ponadto na serwerach SQL są konfigurowane inspekcje i zaawansowane zabezpieczenia danych.

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja Log Analytics obszaru roboczego dla maszyny wirtualnej — raport 

- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja w programie SQL Server powinna być włączona
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Wdrażanie inspekcji na serwerach SQL
- Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM — 7 (5) — najmniejsza funkcjonalność | Autoryzowane oprogramowanie/listy dozwolonych

Adaptacyjna kontrola aplikacji w Azure Security Center to inteligentne, zautomatyzowane, kompleksowe rozwiązanie do tworzenia list, które może blokować uruchamianie określonego oprogramowania na maszynach wirtualnych lub uniemożliwiać jego działanie. Kontrola aplikacji ułatwia tworzenie zatwierdzonych list aplikacji dla maszyn wirtualnych. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, w których lista dozwolonych aplikacji jest zalecana, ale nie została jeszcze skonfigurowana.

- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="cm-11-user-installed-software"></a>Program (CM) 11 oprogramowanie zainstalowane przez użytkownika

Adaptacyjna kontrola aplikacji w Azure Security Center to inteligentne, zautomatyzowane, kompleksowe rozwiązanie do tworzenia list, które może blokować uruchamianie określonego oprogramowania na maszynach wirtualnych lub uniemożliwiać jego działanie. Kontrola aplikacji może ułatwić wymuszanie i monitorowanie zgodności z zasadami ograniczeń oprogramowania. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, w których lista dozwolonych aplikacji jest zalecana, ale nie została jeszcze skonfigurowana.

- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="cp-7-alternate-processing-site"></a>Alternatywna lokacja przetwarzania CP-7

Azure Site Recovery replikuje obciążenia uruchomione na maszynach wirtualnych z lokalizacji głównej do lokacji dodatkowej. W przypadku wystąpienia awarii w lokacji głównej obciążenie kończy się niepowodzeniem w lokalizacji pomocniczej. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która przeprowadza inspekcję maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii. Monitorowanie tego wskaźnika może pomóc w zapewnieniu niezbędnych kontroli awaryjnych.

- Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Uwierzytelnianie i uwierzytelnienie IA-2 (1) (Użytkownicy w organizacji) | Dostęp sieciowy do kont uprzywilejowanych

Ten plan pomaga ograniczyć dostęp uprzywilejowany i kontrolować go, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="ia-5-authenticator-management"></a>Zarządzanie wystawą IA-5

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez haseł i/lub mają ustawione nieprawidłowe uprawnienia w pliku haseł. Ten plan przypisuje również definicje zasad, które przeprowadzają inspekcję konfiguracji typu szyfrowania hasła dla maszyn wirtualnych z systemem Windows. Monitorowanie tych wskaźników pomaga upewnić się, że uwierzytelniające systemu są zgodne z zasadami identyfikacji i uwierzytelniania w organizacji.

- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które nie mają uprawnień do pliku haseł ustawione na 0644
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które mają konta bez hasła
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku haseł ustawione na 0644
- Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które mają konta bez hasła

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>Zarządzanie wystawą IA-5 (1) | Uwierzytelnianie oparte na hasłach

Ten plan pomaga wymusić silne hasła, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję maszyn wirtualnych systemu Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących haseł. Świadomość maszyn wirtualnych z naruszeniem zasad dotyczących siły haseł pomaga podejmować działania naprawcze w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami haseł obowiązującymi w organizacji.

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- Wdróż wymagania w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- Wdróż wymagania w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>Uwierzytelnianie i uwierzytelnienie IA-8 (100) (Użytkownicy niebędący organizacjami) | Poziomy tożsamości i gwarancji poświadczeń

Ten plan pomaga ograniczyć dostęp uprzywilejowany i kontrolować go, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="ra-5-vulnerability-scanning"></a>Skanowanie w poszukiwaniu luk w zabezpieczeniach RA-5

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacji przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , które monitorują luki w zabezpieczeniach systemu operacyjnego, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyn wirtualnych w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym. Ten plan przypisuje również definicje zasad, które przeprowadzają inspekcję i wymuszanie zaawansowanych zabezpieczeń danych na serwerach SQL. Zaawansowane zabezpieczenia danych obejmują ocenę luk w zabezpieczeniach i zaawansowane funkcje ochrony przed zagrożeniami, które ułatwiają zrozumienie luk w zabezpieczeniach wdrożonych zasobów.

- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach wirtualnych należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="sc-5-denial-of-service-protection"></a>SC-5 odmowa usługi ochrony

Funkcja rozproszonej odmowy usługi (DDoS) platformy Azure oferuje dodatkowe funkcje i możliwości ograniczenia w porównaniu z podstawową warstwą usług. Te dodatkowe funkcje obejmują integrację Azure Monitor i możliwość przeglądania raportów dotyczących łagodzenia po ataku. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która przeprowadza inspekcję po włączeniu warstwy Standard DDoS. Zrozumienie różnic między warstwami usług może ułatwić wybranie najlepszego rozwiązania umożliwiającego odmowę ochrony w środowisku platformy Azure.

- Należy włączyć Standard DDoS Protection

## <a name="sc-7-boundary-protection"></a>Ochrona graniczna SC-7

Ten plan ułatwia zarządzanie granicą systemu i sterowanie nią przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , która monitoruje zalecenia dotyczące ograniczania funkcjonalności sieciowych grup zabezpieczeń w programie Azure Security Center. Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, aby zmniejszyć potencjalną powierzchnię ataku.
Ponadto ten plan przypisuje również definicje zasad, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, i konta magazynu z nieograniczonym dostępem mogą zezwalać na nieograniczony dostęp do informacji zawartych w systemie informacyjnym.

- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.
- Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) ochrona granic | Punkty dostępu

Dostęp z maszyn wirtualnych just-in-Time (JIT) blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Dostęp do maszyny wirtualnej JIT pomaga ograniczyć liczbę połączeń zewnętrznych do zasobów na platformie Azure. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in Time, ale jeszcze nie zostały skonfigurowane.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Ochrona graniczna | Zewnętrzne usługi telekomunikacyjne

Dostęp z maszyn wirtualnych just-in-Time (JIT) blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Dostęp do maszyny wirtualnej JIT pomaga zarządzać wyjątkami względem zasad przepływu ruchu, ułatwiając proces żądania dostępu i zatwierdzania. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in Time, ale jeszcze nie zostały skonfigurowane.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) poufność i integralność przekazywania | Kryptograficzna lub alternatywna ochrona fizyczna

Ten plan pomaga chronić poufne i integralność przesyłanych informacji przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które ułatwiają monitorowanie mechanizmu kryptograficznego zaimplementowanego dla protokołów komunikacyjnych. Zapewnienie, że komunikacja jest prawidłowo zaszyfrowana, może pomóc spełnić wymagania organizacji lub chronić informacje przed nieautoryzowanym ujawnieniem i modyfikacją.

- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Wdróż wymagania w celu inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć bezpieczny transfer na konta magazynu

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) Ochrona informacji w czasie spoczynku

Ten plan pomaga wymusić zasady korzystania z formantów cryptograph, aby chronić informacje przechowywane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych. Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Definicje zasad przypisane przez ten plan wymagają szyfrowania dla kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; i Przeprowadź inspekcję braku szyfrowania dla baz danych SQL, dysków maszyny wirtualnej i zmiennych konta usługi Automation.

- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Wdróż przezroczyste szyfrowanie danych SQL DB
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Wymagaj szyfrowania na kontach Data Lake Store
- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="si-2-flaw-remediation"></a>Korygowanie z SI 2

Ten plan ułatwia zarządzanie usterkami systemu informacyjnego przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, luki SQL i luki w zabezpieczeniach maszyn wirtualnych w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym. Ten plan przypisuje również definicję zasad, która zapewnia stosowanie poprawek systemu operacyjnego dla zestawów skalowania maszyn wirtualnych.

- Wymagaj automatycznej poprawki obrazu systemu operacyjnego na Virtual Machine Scale Sets
- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych
- Aktualizacje systemu powinny być zainstalowane na maszynach wirtualnych
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach wirtualnych należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="si-3-malicious-code-protection"></a>Ochrona przed złośliwym kodem SI-3

Ten plan ułatwia zarządzanie programem Endpoint Protection, w tym złośliwą ochroną kodu, przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brak ochrony punktu końcowego na maszynach wirtualnych w Azure Security Center i wymuszają rozwiązanie Microsoft chroniące przed złośliwym oprogramowaniem na maszynach wirtualnych z systemem Windows.

- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>Ochrona przed złośliwym kodem SI-3 (1) | Zarządzanie centralne

Ten plan ułatwia zarządzanie programem Endpoint Protection, w tym złośliwą ochroną kodu, przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brak ochrony punktu końcowego na maszynach wirtualnych w programie Azure Security Center. Azure Security Center udostępnia funkcje scentralizowanego zarządzania i raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym.

- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Monitorowanie systemu informacji SI-4

Ten plan ułatwia monitorowanie systemu przez inspekcjonowanie i wymuszanie rejestrowania i zabezpieczeń danych w ramach zasobów platformy Azure. W odniesieniu do zasad są przypisane inspekcje i egzekwowanie wdrożenia agenta Log Analytics oraz ulepszone ustawienia zabezpieczeń baz danych SQL, kont magazynu i zasobów sieciowych. Te możliwości mogą pomóc w wykrywaniu nietypowych zachowań i wskaźników ataków, co pozwala na podejmowanie odpowiednich działań.

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : inspekcja log Analytics obszaru roboczego dla maszyny wirtualnej — niezgodność raportów
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Wdróż zaawansowane zabezpieczenia danych na serwerach SQL
- Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu
- Wdrażanie inspekcji na serwerach SQL
- Wdróż obserwatora sieciowego po utworzeniu sieci wirtualnych
- Wdrażanie wykrywania zagrożeń na serwerach SQL

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Sprawdzono mapowanie kontroli dla przykładu Federal PBMM Canada. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Kanada Federalna PBMM — Omówienie](./control-mapping.md) 
>  [Kanada Federalna PBMM plan — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
