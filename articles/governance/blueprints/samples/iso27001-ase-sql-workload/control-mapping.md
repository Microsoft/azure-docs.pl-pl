---
title: Przykłady planów obciążeń ISO 27001 ASE/SQL
description: Sterowanie mapowaniem przykładowego planu obciążeń ISO 27001 App Service Environment/SQL Database do Azure Policy i RBAC.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 518c6b86ac658273d428a328b433b35d667d7056
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044757"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Sterowanie mapowaniem przykładowego planu obciążeń ISO 27001 ASE/SQL

W tym artykule szczegółowo przedstawiono sposób mapowania przykładowego planu obciążeń środowiska ASE/SQL w języku ISO 27001 do kontrolek ISO 27001. Aby uzyskać więcej informacji na temat kontrolek, zobacz [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Następujące mapowania znajdują się w kontrolkach **ISO 27001:2013** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz ** \[ \] kontrolki Podgląd audytu ISO 27001:2013, a następnie wdróż określone rozszerzenia maszyn wirtualnych, aby zapewnić obsługę zasad inspekcji** wbudowanej.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 Rozdzielenie obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie pozwala na nadmiarowość administracyjną. Bez względu na to, że zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć prawdopodobieństwo naruszenia za pośrednictwem konta właściciela z naruszeniem. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując dwie [Azure Policy](../../../policy/overview.md) definicje, które przeprowadzą inspekcję liczby właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji może pomóc w zaimplementowaniu odpowiedniego rozdzielenia obowiązków.

- \[Wersja zapoznawcza \] : Inspekcja minimalnej liczby właścicieli dla subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja maksymalnej liczby właścicieli subskrypcji

## <a name="a821-classification-of-information"></a>A. 8.2.1 — Klasyfikacja informacji

[Usługa oceny luk w zabezpieczeniach](../../../../azure-sql/database/sql-vulnerability-assessment.md) w systemie Azure może ułatwić odnajdywanie poufnych danych przechowywanych w bazach danych i zawiera zalecenia dotyczące klasyfikowania tych danych. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , aby przeprowadzić inspekcję luk w zabezpieczeniach podczas skanowania za pośrednictwem programu SQL Server.

- \[Wersja zapoznawcza \] : monitoruj wyniki oceny luk w zabezpieczeniach SQL w Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 dostęp do sieci i usług sieciowych

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC) w celu zarządzania dostępem do zasobów platformy Azure. Ten plan ułatwia kontrolowanie dostępu do zasobów platformy Azure przez przypisanie siedmiu [Azure Policy](../../../policy/overview.md) definicji. Te zasady przeprowadzają inspekcję użycia typów zasobów i konfiguracji, które mogą zezwalać na dostęp do zasobów.
Informacje o zasobach, które naruszają te zasady, mogą pomóc w podejmowaniu działań naprawczych w celu zapewnienia dostępu do zasobów platformy Azure tylko autoryzowanym użytkownikom.

- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji kont maszyn wirtualnych z systemem Linux bez hasła
- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji maszyny wirtualnej z systemem Linux zezwalającej na połączenia zdalne z kont bez hasła
- \[Wersja zapoznawcza \] : Inspekcja kont maszyn wirtualnych z systemem Linux bez haseł
- \[Wersja zapoznawcza \] : Inspekcja maszyn wirtualnych z systemem Linux zezwalająca na połączenia zdalne z kont bez hasła
- Inspekcja użycia klasycznych kont magazynu
- Inspekcja użycia klasycznych maszyn wirtualnych
- Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 Zarządzanie prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując cztery [Azure Policy](../../../policy/overview.md) definicje do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu oraz konta z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Platforma Azure implementuje funkcję kontroli dostępu opartej na rolach (RBAC) w celu zarządzania dostępem do zasobów platformy Azure. Ten plan przypisuje również trzy definicje Azure Policy do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ten plan przypisuje również definicję Azure Policy do inspekcji użycia niestandardowych reguł RBAC. Zrozumienie, gdzie są zaimplementowane niestandardowe reguły RBAC, może pomóc w sprawdzeniu potrzeby i właściwej implementacji, ponieważ niestandardowe reguły RBAC są podatne na błędy.

- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami właściciela, dla których nie włączono uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami do zapisu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja kont zewnętrznych z uprawnieniami właściciela w ramach subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja kont zewnętrznych z uprawnieniami do zapisu w ramach subskrypcji
- Inspekcja aprowizacji Azure Active Directory administratora programu SQL Server
- Inspekcja użycia Azure Active Directory na potrzeby uwierzytelniania klientów w programie Service Fabric
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 zarządzanie tajnymi informacjami o uwierzytelnianiu użytkowników

Ten plan przypisuje trzy definicje [Azure Policy](../../../policy/overview.md) do kont inspekcji, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone. Ten plan również przypisuje dwa definicje Azure Policy, które przeprowadzą inspekcję uprawnień pliku hasła maszyny wirtualnej z systemem Linux w celu alertu, jeśli są ustawione nieprawidłowo. Ta konfiguracja umożliwia podejmowanie działań naprawczych w celu zapewnienia, że uwierzytelniające nie zostały naruszone.

- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami właściciela, dla których nie włączono uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami do odczytu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami do zapisu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji uprawnień pliku haseł maszyny wirtualnej z systemem Linux
- \[Wersja zapoznawcza \] : Inspekcja pliku/etc/passwd maszyny wirtualnej z systemem Linux jest ustawiona na 0644

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 Przegląd praw dostępu użytkownika

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje cztery [Azure Policy](../../../policy/overview.md) definicje do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta z amortyzacją i konta zewnętrzne z podniesionymi uprawnieniami.

- \[Wersja zapoznawcza \] : Inspekcja przestarzałych kont w subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja przestarzałych kont z uprawnieniami właściciela w ramach subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja kont zewnętrznych z uprawnieniami właściciela w ramach subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja kont zewnętrznych z uprawnieniami do zapisu w ramach subskrypcji

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 usunięcie lub dostosowanie praw dostępu

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Korzystając z [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) i RBAC, można aktualizować role użytkowników, aby odzwierciedlały zmiany organizacyjne. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie [Azure Policy](../../../policy/overview.md) definicje do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- \[Wersja zapoznawcza \] : Inspekcja przestarzałych kont w subskrypcji
- \[Wersja zapoznawcza \] : Inspekcja przestarzałych kont z uprawnieniami właściciela w ramach subskrypcji

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 bezpieczne procedury logowania

Ten plan przypisuje trzy definicje Azure Policy do kont inspekcji, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Usługa Azure Multi-Factor Authentication zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami właściciela, dla których nie włączono uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami do odczytu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Wersja zapoznawcza \] : konta inspekcji z uprawnieniami do zapisu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji

## <a name="a943-password-management-system"></a>System zarządzania hasłami. 9.4.3

Ten plan pomaga wymusić silne hasła, przypisując 10 [Azure Policy](../../../policy/overview.md) definicji, które będą przeprowadzać inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji maszyny wirtualnej z systemem Windows wymusza wymagania dotyczące złożoności haseł
- \[Podgląd \] : Wdróż rozszerzenie maszyny wirtualnej w celu przeprowadzenia inspekcji maksymalnego wieku hasła dla maszyny wirtualnej z systemem Windows 70 dni
- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji dla maszyny wirtualnej z systemem Windows minimalne wieku hasła 1 dzień
- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji haseł maszyn wirtualnych z systemem Windows musi wynosić co najmniej 14 znaków
- \[Wersja zapoznawcza \] : rozszerzenie maszyny wirtualnej do inspekcji maszyny wirtualnej z systemem Windows nie powinno zezwalać na poprzednie 24 hasła
- \[Wersja zapoznawcza \] : Inspekcja maszyny wirtualnej z systemem Windows wymusza wymagania dotyczące złożoności haseł
- \[Wersja zapoznawcza \] : Inspekcja maksymalnego wieku hasła dla maszyny wirtualnej systemu Windows 70 dni
- \[Wersja zapoznawcza \] : Inspekcja dla maszyny wirtualnej z systemem Windows minimalne wieku hasła 1 dzień
- \[Wersja zapoznawcza \] : Inspekcja haseł maszyn wirtualnych z systemem Windows musi składać się z co najmniej 14 znaków
- \[Wersja zapoznawcza \] : Inspekcja maszyny wirtualnej systemu Windows nie powinna zezwalać na poprzednie 24 hasła

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A. 10.1.1 zasady korzystania z formantów kryptograficznych

Ten plan pomaga wymusić zasady korzystania z kontrolek cryptograph przez przypisanie 13 [Azure Policy](../../../policy/overview.md) definicji, które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych.
Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Zasady przypisane przez ten plan wymagają szyfrowania dla kont usługi BLOB Storage i kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta usługi Automation; Inspekcja niezabezpieczonych połączeń z kontami magazynu, aplikacjami funkcji, aplikacjami sieci Web, API Apps i Redis Cache; Inspekcja niesłabego szyfrowania hasła maszyny wirtualnej; i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- \[Wersja zapoznawcza \] : Inspekcja dostępu tylko do protokołu HTTPS dla aplikacja funkcji
- \[Wersja zapoznawcza \] : Inspekcja dostępu tylko do protokołu HTTPS dla aplikacji sieci Web
- \[Wersja zapoznawcza \] : Inspekcja dostępu tylko do protokołu HTTPS dla aplikacji interfejsu API
- \[Wersja zapoznawcza \] : Inspekcja braku szyfrowania obiektów BLOB dla kont magazynu
- \[Wersja zapoznawcza \] : Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji maszyny wirtualnej z systemem Windows nie powinno przechowywać haseł przy użyciu szyfrowania odwracalnego
- \[Wersja zapoznawcza \] : Inspekcja maszyny wirtualnej z systemem Windows nie powinna przechowywać haseł przy użyciu szyfrowania odwracalnego
- \[Wersja zapoznawcza \] : Monitoruj nieszyfrowane dyski maszyn wirtualnych w Azure Security Center
- Inspekcja włączenia szyfrowania zmiennych konta usługi Automation
- Inspekcja włączania tylko bezpiecznych połączeń z Redis Cache
- Inspekcja bezpiecznego transferu na konta magazynu
- Przeprowadź inspekcję ustawienia właściwości ClusterProtectionLevel na EncryptAndSign w Service Fabric
- Inspekcja stanu przezroczystego szyfrowania danych
- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="a1241-event-logging"></a>A. 12.4.1 Rejestrowanie zdarzeń

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure.
Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure.

- \[Wersja zapoznawcza \] : inspekcja Agent zależności wdrożenia — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : inspekcja Agent zależności wdrożenia w programie VMSS-VM Image (OS) bez listy
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3 dzienniki administratorów i operatorów

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu Azure Policy definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure.

- \[Wersja zapoznawcza \] : inspekcja Agent zależności wdrożenia — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : inspekcja Agent zależności wdrożenia w programie VMSS-VM Image (OS) bez listy
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="a1244-clock-synchronization"></a>Synchronizacja zegara. 12.4.4

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu Azure Policy definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Dzienniki systemu Azure są oparte na zsynchronizowaniu zegarów wewnętrznych w celu utworzenia skorelowanego rekordu zdarzeń między zasobami.

- \[Wersja zapoznawcza \] : inspekcja Agent zależności wdrożenia — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : inspekcja Agent zależności wdrożenia w programie VMSS-VM Image (OS) bez listy
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 Instalacja oprogramowania w systemach operacyjnych

Adaptacyjna kontrola aplikacji to rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję Azure Policy, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ta funkcja ułatwia sterowanie instalacją oprogramowania i aplikacji na maszynach wirtualnych platformy Azure.

- \[Wersja zapoznawcza \] : monitorowanie możliwej listy dozwolonych aplikacji w Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 Zarządzanie lukami w zabezpieczeniach

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego przez przypisanie pięciu [Azure Policy](../../../policy/overview.md) definicji, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego i luki w zabezpieczeniach programu SQL Server w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym.

- \[Wersja zapoznawcza \] : Monitoruj brakujące Endpoint Protection w Azure Security Center
- \[Wersja zapoznawcza \] : Monitoruj brakujące aktualizacje systemu w Azure Security Center
- \[Wersja zapoznawcza \] : monitorowanie luk w zabezpieczeniach systemu operacyjnego w Azure Security Center
- \[Wersja zapoznawcza \] : monitoruj wyniki oceny luk w zabezpieczeniach SQL w Azure Security Center
- \[Wersja zapoznawcza \] : monitorowanie luk w zabezpieczeniach maszyn wirtualnych w Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2 ograniczenia dotyczące instalacji oprogramowania

Adaptacyjna kontrola aplikacji to rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję Azure Policy, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ograniczenia dotyczące instalacji oprogramowania mogą pomóc w zmniejszeniu prawdopodobieństwa wprowadzenia luk w zabezpieczeniach oprogramowania.

- \[Wersja zapoznawcza \] : monitorowanie możliwej listy dozwolonych aplikacji w Azure Security Center

## <a name="a1311-network-controls"></a>A. 13.1.1, kontrolki sieci

Ten plan ułatwia zarządzanie sieciami i sterowanie nimi przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , która monitoruje sieciowe grupy zabezpieczeń z regułami ograniczającymi. Reguły, które są zbyt ograniczane, mogą zezwalać na niezamierzony dostęp do sieci i powinny być przeglądane. Ten plan przypisuje również trzy definicje Azure Policy, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, i konta magazynu z nieograniczonym dostępem mogą zezwalać na nieograniczony dostęp do informacji zawartych w systemie informacyjnym.

- \[Wersja zapoznawcza \] : monitorowanie uprawnień dostępu do sieci w Azure Security Center
- \[Wersja zapoznawcza \] : Monitoruj niechronione punkty końcowe sieci w Azure Security Center
- \[Wersja zapoznawcza \] : Monitoruj niechronione aplikacje internetowe w Azure Security Center
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1 — zasady i procedury transferu informacji

Plan pomaga zapewnić, że transfer informacji w ramach usług platformy Azure jest bezpieczny, przypisując dwie [Azure Policy](../../../policy/overview.md) definicje do inspekcji niezabezpieczonych połączeń z kontami magazynu i Redis Cache.

- Inspekcja włączania tylko bezpiecznych połączeń z Redis Cache
- Inspekcja bezpiecznego transferu na konta magazynu

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli przykładowego planu obciążeń ISO 27001 App Service Environment/SQL Database zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o architekturze i sposobie wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plan obciążeń ISO 27001 App Service Environment/SQL Database — Omówienie](./index.md) 
>  [Plan obciążeń ISO 27001 App Service Environment/SQL Database — kroki wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
