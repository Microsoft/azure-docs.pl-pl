---
title: Przykładowe kontrolki ISO 27001
description: Sterowanie mapowaniem przykładu planu ISO 27001. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: a0d5b1118e1e063f7b4f8757e7d1b3935dc1a37c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535767"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Sterowanie mapowaniem przykładu planu ISO 27001

W tym artykule szczegółowo opisano, jak przykładowy plan ISO 27001 strategii platformy Azure jest mapowany na kontrolki ISO 27001. Aby uzyskać więcej informacji na temat kontrolek, zobacz [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Następujące mapowania znajdują się w kontrolkach **ISO 27001:2013** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz ** \[ \] kontrolki Podgląd audytu ISO 27001:2013, a następnie wdróż określone rozszerzenia maszyn wirtualnych, aby zapewnić obsługę zasad inspekcji** wbudowanej.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 Rozdzielenie obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie pozwala na nadmiarowość administracyjną. Bez względu na to, że zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć prawdopodobieństwo naruszenia za pośrednictwem konta właściciela z naruszeniem. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując dwie [Azure Policy](../../../policy/overview.md) definicje, które przeprowadzą inspekcję liczby właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji może pomóc w zaimplementowaniu odpowiedniego rozdzielenia obowiązków.

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

## <a name="a821-classification-of-information"></a>A. 8.2.1 — Klasyfikacja informacji

[Usługa oceny luk w zabezpieczeniach](../../../../azure-sql/database/sql-vulnerability-assessment.md) w systemie Azure może ułatwić odnajdywanie poufnych danych przechowywanych w bazach danych i zawiera zalecenia dotyczące klasyfikowania tych danych. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , aby przeprowadzić inspekcję luk w zabezpieczeniach podczas skanowania za pośrednictwem programu SQL Server.

- Luki w zabezpieczeniach baz danych SQL należy skorygować

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 dostęp do sieci i usług sieciowych

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach (Azure RBAC)](../../../../role-based-access-control/overview.md) na platformie Azure w celu zarządzania dostępem do zasobów platformy Azure. Ten plan ułatwia kontrolowanie dostępu do zasobów platformy Azure przez przypisanie siedmiu [Azure Policy](../../../policy/overview.md) definicji. Te zasady przeprowadzają inspekcję użycia typów zasobów i konfiguracji, które mogą zezwalać na dostęp do zasobów.
Informacje o zasobach, które naruszają te zasady, mogą pomóc w podejmowaniu działań naprawczych w celu zapewnienia dostępu do zasobów platformy Azure tylko autoryzowanym użytkownikom.

- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które mają konta bez hasła
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które mają konta bez hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 Zarządzanie prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując cztery [Azure Policy](../../../policy/overview.md) definicje do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu oraz konta z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Ten plan przypisuje również trzy definicje Azure Policy do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ten plan przypisuje również definicję Azure Policy do inspekcji użycia niestandardowych reguł RBAC platformy Azure. Informacje o tym, gdzie są zaimplementowane niestandardowe reguły RBAC platformy Azure, mogą pomóc w sprawdzeniu potrzeby i poprawnej implementacji, ponieważ niestandardowe reguły RBAC platformy Azure są podatne na błędy.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 zarządzanie tajnymi informacjami o uwierzytelnianiu użytkowników

Ten plan przypisuje trzy definicje [Azure Policy](../../../policy/overview.md) do kont inspekcji, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone. Ten plan również przypisuje dwa definicje Azure Policy, które przeprowadzą inspekcję uprawnień pliku hasła maszyny wirtualnej z systemem Linux w celu alertu, jeśli są ustawione nieprawidłowo. Ta konfiguracja umożliwia podejmowanie działań naprawczych w celu zapewnienia, że uwierzytelniające nie zostały naruszone.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które nie mają uprawnień do pliku haseł ustawione na 0644
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku haseł ustawione na 0644

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 Przegląd praw dostępu użytkownika

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje cztery [Azure Policy](../../../policy/overview.md) definicje do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta z amortyzacją i konta zewnętrzne z podniesionymi uprawnieniami.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 usunięcie lub dostosowanie praw dostępu

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Przy użyciu [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) i kontroli RBAC platformy Azure można aktualizować role użytkowników, aby odzwierciedlały zmiany organizacyjne. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie [Azure Policy](../../../policy/overview.md) definicje do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 bezpieczne procedury logowania

Ten plan przypisuje trzy definicje Azure Policy do kont inspekcji, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Usługa Azure Multi-Factor Authentication zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="a943-password-management-system"></a>System zarządzania hasłami. 9.4.3

Ten plan pomaga wymusić silne hasła, przypisując 10 [Azure Policy](../../../policy/overview.md) definicji, które będą przeprowadzać inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows bez maksymalnego wieku hasła wynoszącego 70 dni
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A. 10.1.1 zasady korzystania z formantów kryptograficznych

Ten plan pomaga wymusić zasady korzystania z kontrolek cryptograph przez przypisanie 13 [Azure Policy](../../../policy/overview.md) definicji, które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych.
Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Zasady przypisane przez ten plan wymagają szyfrowania dla kont usługi BLOB Storage i kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta usługi Automation; Inspekcja niezabezpieczonych połączeń z kontami magazynu, aplikacjami funkcji, aplikacjami sieci Web, API Apps i Redis Cache; Inspekcja niesłabego szyfrowania hasła maszyny wirtualnej; i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Zmienne konta usługi Automation powinny być szyfrowane
- Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis
- Należy włączyć bezpieczny transfer na konta magazynu
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="a1241-event-logging"></a>A. 12.4.1 Rejestrowanie zdarzeń

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure.
Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure.

- Inspekcja wdrożenia agenta zależności — obraz maszyny wirtualnej (OS) nie został wystawiony
- Inspekcja wdrożenia agenta zależności w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (system operacyjny) nie został wystawiony
- [Wersja zapoznawcza]: Inspekcja wdrożenia agenta Log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (OS) nie został wystawiony
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja w programie SQL Server powinna być włączona

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3 dzienniki administratorów i operatorów

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu Azure Policy definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure.

- Inspekcja wdrożenia agenta zależności — obraz maszyny wirtualnej (OS) nie został wystawiony
- Inspekcja wdrożenia agenta zależności w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (system operacyjny) nie został wystawiony
- [Wersja zapoznawcza]: Inspekcja wdrożenia agenta Log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (OS) nie został wystawiony
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja w programie SQL Server powinna być włączona

## <a name="a1244-clock-synchronization"></a>Synchronizacja zegara. 12.4.4

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu Azure Policy definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Dzienniki systemu Azure są oparte na zsynchronizowaniu zegarów wewnętrznych w celu utworzenia skorelowanego rekordu zdarzeń między zasobami.

- Inspekcja wdrożenia agenta zależności — obraz maszyny wirtualnej (OS) nie został wystawiony
- Inspekcja wdrożenia agenta zależności w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (system operacyjny) nie został wystawiony
- [Wersja zapoznawcza]: Inspekcja wdrożenia agenta Log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (OS) nie został wystawiony
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja w programie SQL Server powinna być włączona

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 Instalacja oprogramowania w systemach operacyjnych

Adaptacyjna kontrola aplikacji to rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję Azure Policy, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ta funkcja ułatwia sterowanie instalacją oprogramowania i aplikacji na maszynach wirtualnych platformy Azure.

- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 Zarządzanie lukami w zabezpieczeniach

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego przez przypisanie pięciu [Azure Policy](../../../policy/overview.md) definicji, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego i luki w zabezpieczeniach programu SQL Server w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym.

- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Należy zainstalować aktualizacje systemu na maszynach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2 ograniczenia dotyczące instalacji oprogramowania

Adaptacyjna kontrola aplikacji to rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję Azure Policy, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ograniczenia dotyczące instalacji oprogramowania mogą pomóc w zmniejszeniu prawdopodobieństwa wprowadzenia luk w zabezpieczeniach oprogramowania.

- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.

## <a name="a1311-network-controls"></a>A. 13.1.1, kontrolki sieci

Ten plan ułatwia zarządzanie sieciami i sterowanie nimi przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , która monitoruje sieciowe grupy zabezpieczeń z regułami ograniczającymi. Reguły, które są zbyt ograniczane, mogą zezwalać na niezamierzony dostęp do sieci i powinny być przeglądane. Ten plan przypisuje również trzy definicje Azure Policy, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, i konta magazynu z nieograniczonym dostępem mogą zezwalać na nieograniczony dostęp do informacji zawartych w systemie informacyjnym.

- Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony
- Konta magazynu powinny ograniczać dostęp do sieci

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1 — zasady i procedury transferu informacji

Plan pomaga zapewnić, że transfer informacji w ramach usług platformy Azure jest bezpieczny, przypisując dwie [Azure Policy](../../../policy/overview.md) definicje do inspekcji niezabezpieczonych połączeń z kontami magazynu i Redis Cache.

- Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis
- Należy włączyć bezpieczny transfer na konta magazynu

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli planu ISO 27001 zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o architekturze i sposobie wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plan ISO 27001 — Omówienie](./index.md) 
>  [Plan ISO 27001 — kroki wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
