---
title: 'Azure AD Connect: instalacja niestandardowa | Microsoft Docs'
description: Ten dokument zawiera szczegółowe informacje dotyczące opcji niestandardowej instalacji programu Azure AD Connect. Korzystając z tych instrukcji, można zainstalować usługę Active Directory za pośrednictwem programu Azure AD Connect.
services: active-directory
keywords: co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki usługi Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: db10f53033e305aa2306bce230e7880140f35189
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578290"
---
# <a name="custom-installation-of-azure-ad-connect"></a>Niestandardowa instalacja programu Azure AD Connect
Azure AD Connect **Ustawienia niestandardowe** są używane, gdy chcesz uzyskać więcej opcji instalacji.  Na przykład jeśli masz wiele lasów lub chcesz skonfigurować funkcje opcjonalne. Jest przydatna w każdej sytuacji, gdy opcja [**instalacji ekspresowej**](how-to-connect-install-express.md) nie zaspokaja potrzeb związanych z wdrożeniem lub topologią.

Przed rozpoczęciem instalacji należy [pobrać program Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) i wykonać czynności związane z wymaganiami wstępnymi opisane w temacie [Azure AD Connect: sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md). Sprawdź także, czy dostępne są wymagane konta, zgodnie z opisem w temacie [Konta i uprawnienia w programie Azure AD Connect](reference-connect-accounts-permissions.md).

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Instalacja programu Azure AD Connect z ustawieniami niestandardowymi

### <a name="express-settings"></a>Ustawienia ekspresowe
Na tej stronie kliknij opcję **Dostosuj**, aby rozpocząć instalację z ustawieniami niestandardowymi.  Pozostała część tego dokumentu przeprowadzi Cię przez poszczególne ekrany kreatora instalacji niestandardowej.  Poniższe linki umożliwiają szybkie przechodzenie do informacji o konkretnym ekranie kreatora.

- [Instalacja wymaganych składników](#install-required-components)
- [Logowanie użytkowników](#user-sign-in)
- [Łączenie z usługą Azure AD](#connect-to-azure-ad)
- [Strony w sekcji Synchronizacja](#pages-under-the-sync-section)

### <a name="install-required-components"></a>Instalacja wymaganych składników
Podczas instalowania usług synchronizacji sekcja konfiguracji opcjonalnej może pozostać niezaznaczona, a wszystkie ustawienia zostaną automatycznie skonfigurowane w programie Azure AD Connect. Konfiguruje wystąpienie SQL Server 2012 Express LocalDB, tworzy odpowiednie grupy i przypisuje uprawnienia. Jeśli chcesz zmienić ustawienia domyślne, możesz użyć do tego celu, zaznaczając odpowiednie pola.  Poniższa tabela zawiera podsumowanie tych opcji i linki do dodatkowych informacji. 

![Wymagane składniki](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Konfiguracja opcjonalna | Opis |
| --- | --- |
|Określ niestandardową lokalizację instalacji| Umożliwia zmianę domyślnej ścieżki instalacji Azure AD Connect.|
| Użyj istniejącego serwera SQL Server |Pozwala określić nazwę serwera SQL Server i nazwę wystąpienia. Wybierz tę opcję, jeśli masz już serwer baz danych, którego chcesz użyć. Wprowadź nazwę wystąpienia, a następnie przecinek i numer portu w polu **Nazwa wystąpienia**, jeśli na serwerze SQL Server jest wyłączona funkcja przeglądania.  Następnie określ nazwę bazy danych Azure AD Connect.  Twoje uprawnienia SQL określają, czy zostanie utworzona nowa baza danych, czy administrator SQL musi najpierw utworzyć bazę danych.  Jeśli masz uprawnienia SQL SA, zobacz [jak zainstalować program przy użyciu istniejącej bazy danych](how-to-connect-install-existing-database.md).  Jeśli masz uprawnienia delegowane (DBO), zobacz [instalowanie Azure AD Connect z uprawnieniami administratora delegowanego SQL](how-to-connect-install-sql-delegation.md). |
| Użyj istniejącego konta usługi |Domyślnie program Azure AD Connect korzysta z wirtualnego konta usługi na potrzeby usług synchronizacji. Jeśli używasz zdalnego serwera SQL lub serwera proxy wymagającego uwierzytelnienia, musisz użyć **zarządzanego konta usługi** lub konta usługi w domenie, do którego znasz hasło. W takich przypadkach wprowadź konto do użycia. Upewnij się, że użytkownik wykonujący instalację jest administratorem systemu na serwerze SQL, aby można było utworzyć identyfikator logowania dla konta usługi.  Zobacz [konta i uprawnienia Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Od ostatniej kompilacji aprowizowanie bazy danych może wykonać poza pasmem administrator usługi SQL, a jej instalację może następnie przeprowadzić administrator programu Azure AD Connect z uprawnieniami właściciela bazy danych.  Aby uzyskać więcej informacji, zobacz temat [Install Azure AD Connect using SQL delegated administrator permissions (Instalowanie programu Azure AD Connect za pomocą delegowanych uprawnień administratora usługi SQL)](how-to-connect-install-sql-delegation.md).|
| Określ niestandardowe grupy synchronizacji |Domyślnie program Azure AD Connect tworzy cztery grupy lokalne na serwerze podczas instalowania usług synchronizacji. Są to: grupa administratorów, grupa operatorów, grupa przeglądania i grupa resetowanie hasła. Możesz tu określić własne grupy. Grupy muszą być lokalne na serwerze i nie mogą znajdować się w domenie. |
|Importuj ustawienia synchronizacji (wersja zapoznawcza)|Umożliwia importowanie ustawień z innych wersji Azure AD Connect.  Aby uzyskać więcej informacji [, zobacz Importowanie i eksportowanie ustawień konfiguracji Azure AD Connect](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Logowanie użytkowników
Po zainstalowaniu wymaganych składników zostanie wyświetlony monit o wybranie metody logowania jednokrotnego dla użytkowników. Poniższa tabela zawiera krótki opis dostępnych opcji. Pełny opis metod logowania znajduje się w temacie [Logowanie użytkowników](plan-connect-user-signin.md).

![Zrzut ekranu przedstawiający stronę "Logowanie użytkownika" z wybraną opcją "Synchronizacja skrótów haseł".](./media/how-to-connect-install-custom/usersignin4.png)

| Opcja logowania jednokrotnego | Opis |
| --- | --- |
| Synchronizacja skrótów haseł |Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej. Hasła użytkowników są synchronizowane z usługą Azure AD jako skrót hasła, a uwierzytelnianie odbywa się w chmurze. Więcej informacji znajduje się w temacie [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md). |
|Uwierzytelnianie przekazywane|Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej.  Hasło użytkownika jest przekazywane do lokalnego kontrolera domeny usługi Active Directory w celu przeprowadzenia walidacji.
| Federacja z usługami AD FS |Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej.  Użytkownicy są przekierowywani do wystąpienia lokalnych usług AD FS w celu zalogowania, a uwierzytelnianie odbywa się lokalnie. |
| Federacja z serwerem PingFederate|Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej.  Użytkownicy są przekierowywani do lokalnego wystąpienia serwera PingFederate w celu zalogowania, a uwierzytelnianie odbywa się lokalnie. |
| Nie konfiguruj |Żadna z funkcji logowania użytkownika nie została zainstalowana ani skonfigurowana. Wybierz tę opcję, jeśli masz już serwer federacyjny innej firmy lub korzystasz z innego rozwiązania. |
|Włącz logowanie jednokrotne|Ta opcja jest dostępna w przypadku synchronizacji skrótów haseł i uwierzytelniania przekazywanego. Udostępnia ona funkcję logowania jednokrotnego użytkownikom pulpitu w sieci firmowej. Aby uzyskać więcej informacji, zobacz [Logowanie jednokrotne](how-to-connect-sso.md). </br>W przypadku klientów usług AD FS ta opcja jest niedostępna, ponieważ usługi AD FS umożliwiają logowanie jednokrotne na tym samym poziomie.</br>

### <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD
Na ekranie Łączenie z usługą Azure AD wprowadź konto administratora globalnego i hasło. W przypadku wybrania opcji **Federacja z usługami AD FS** na poprzedniej stronie nie loguj się na koncie w domenie, którą planujesz włączyć dla federacji. Zaleca się użycie konta w domyślnej domenie **onmicrosoft.com**, która jest dołączona do dzierżawy usługi Azure AD.

To konto służy tylko do tworzenia konta usługi w usłudze Azure AD i nie jest używane po zakończeniu pracy kreatora.  
![Logowanie użytkownika](./media/how-to-connect-install-custom/connectaad.png)

Jeśli na koncie administratora globalnego jest włączone uwierzytelnianie MFA, musisz ponownie podać hasło w menu podręcznym logowania i wykonać żądanie uwierzytelniania MFA. Żądanie może obejmować podanie kodu weryfikacyjnego lub rozmowę telefoniczną.  
![Logowanie użytkownika w usłudze MFA](./media/how-to-connect-install-custom/connectaadmfa.png)

Na koncie administratora globalnego może być również włączona opcja [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

Jeśli wystąpi błąd lub problemy z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).

## <a name="pages-under-the-sync-section"></a>Strony w sekcji Synchronizacja

### <a name="connect-your-directories"></a>Podłączanie katalogów
Aby połączyć się z usługami Active Directory Domain Services, program Azure AD Connect potrzebuje nazwy lasu i poświadczeń konta z wystarczającymi uprawnieniami.

![Zrzut ekranu przedstawiający stronę "łączenie katalogów".](./media/how-to-connect-install-custom/connectdir01.png)

Po wprowadzeniu nazwy lasu i kliknięciu pozycji **Dodaj katalog** zostanie wyświetlone podręczne okno dialogowe z prośbą o wybranie odpowiedniej opcji:

| Opcja | Opis |
| --- | --- |
| Utwórz nowe konto | Wybierz tę opcję, jeśli chcesz, aby kreator programu Azure AD Connect utworzył konto usługi AD DS wymagane przez program Azure AD Connect na potrzeby nawiązywania połączenia z lasem usługi AD podczas synchronizacji katalogów. Jeśli wybierzesz tę opcję, wprowadź nazwę użytkownika i hasło konta administratora przedsiębiorstwa. Podane konto administratora przedsiębiorstwa będzie używane przez kreatora programu Azure AD Connect do utworzenia wymaganego konta usługi AD DS. Możesz wprowadzić domenę w formacie NetBios lub FQDN, czyli FABRIKAM\administrator lub fabrikam.com\administrator. |
| Użyj istniejącego konta | Wybierz tę opcję, jeśli chcesz, aby podczas synchronizacji katalogów do nawiązywania połączenia z lasem usługi AD program Azure AD Connect używał istniejącego konta usługi AD DS. Możesz wprowadzić domenę w formacie NetBios lub FQDN, tj. FABRIKAM\syncuser lub fabrikam.com\syncuser. To konto może być kontem zwykłego użytkownika, ponieważ wymaga tylko domyślnych uprawnień odczytu. Jednak w zależności od scenariusza, mogą być potrzebne większe uprawnienia. Więcej informacji znajduje się w temacie [Konta i uprawnienia w programie Azure AD Connect](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Podłączanie katalogu](./media/how-to-connect-install-custom/connectdir02.png)

#### <a name="enterprise-admin-and-domain-admin-accounts-not-supported"></a>Konta administratorów przedsiębiorstwa i administratorów domeny nie są obsługiwane
W przypadku kompilacji 1.4.18.0 nie jest już obsługiwane używanie konta administratora przedsiębiorstwa lub administratora domeny jako konta łącznika AD DS.  Jeśli podczas określania **użycia istniejącego konta**zostanie podjęta próba wprowadzenia konta administratora przedsiębiorstwa lub administratora domeny, zostanie wyświetlony następujący błąd:

  **"Korzystanie z konta administratora przedsiębiorstwa lub domeny dla konta lasu usługi AD jest niedozwolone.  Zezwól Azure AD Connect utworzyć konta lub określ konto synchronizacji z odpowiednimi uprawnieniami.  &lt;Dowiedz się więcej &gt; "**

### <a name="azure-ad-sign-in-configuration"></a>Konfiguracja logowania się w usłudze Azure AD
Ta strona umożliwia przeglądanie domen UPN obecnych w lokalnych usługach AD DS oraz tych, które zostały zweryfikowane w usłudze Azure AD. Umożliwia również skonfigurowanie atrybutu dla właściwości userPrincipalName.

![Niezweryfikowane domeny](./media/how-to-connect-install-custom/aadsigninconfig2.png)  
Sprawdź wszystkie domeny z oznaczeniem **Nie dodano** lub **Nie zweryfikowano**. Upewnij się, że używane domeny zostały zweryfikowane w usłudze Azure AD. Po zweryfikowaniu domen kliknij symbol Odśwież. Więcej informacji znajduje się w temacie zawierającym opis sposobów [dodawania i weryfikowania domeny](../fundamentals/add-custom-domain.md)

**UserPrincipalName** — Atrybut userPrincipalName jest używany podczas logowania do usługi Azure AD i Microsoft 365. Używane domeny, zwane również sufiksem głównej nazwy użytkownika, należy zweryfikować w usłudze Azure AD przed zsynchronizowaniem użytkowników. Firma Microsoft zaleca zachowanie domyślnego atrybutu userPrincipalName. Jeśli atrybut ten jest nierutowalny i nie można go zweryfikować, możliwe jest wybranie innego atrybutu. Można na przykład wybrać adres e-mail jako atrybut zawierający identyfikator logowania. Użycie atrybutu innego niż userPrincipalName jest określane jako **alternatywny identyfikator**. Wartość atrybutu alternatywnego identyfikatora musi być zgodna ze standardem RFC822. Alternatywny identyfikator może być używany na potrzeby synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. Atrybut nie może zostać zdefiniowany w usłudze Active Directory jako wielowartościowy nawet wtedy, gdy ma tylko jedną wartość. Aby uzyskać więcej informacji na temat alternatywnego identyfikatora, zobacz [często zadawane pytania](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname) .

>[!NOTE]
> Po włączeniu funkcji uwierzytelniania przekazywanego trzeba mieć co najmniej jedną zweryfikowaną domenę, aby można było kontynuować pracę w kreatorze.

> [!WARNING]
> Korzystanie z alternatywnego identyfikatora nie jest zgodne ze wszystkimi obciążeniami Microsoft 365. Więcej informacji można znaleźć w temacie [Konfigurowanie alternatywnego identyfikatora logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

### <a name="domain-and-ou-filtering"></a>Filtrowanie domen i jednostek organizacyjnych
Domyślnie wszystkie domeny i jednostki organizacyjne są zsynchronizowane. Jeśli istnieją jakieś domeny lub jednostki organizacyjne, których nie chcesz synchronizować z usługą Azure AD, możesz usunąć zaznaczenie tych domen i jednostek organizacyjnych.  
![Filtrowanie domen i jednostek organizacyjnych](./media/how-to-connect-install-custom/domainoufiltering.png)  
Ta strona kreatora służy do konfigurowania filtrowania opartego na domenie i jednostce organizacyjnej. Jeżeli planujesz wprowadzić zmiany, zobacz [filtrowanie oparte na domenie](how-to-connect-sync-configure-filtering.md#domain-based-filtering) i [filtrowanie oparte na jednostce organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) przed wprowadzeniem tych zmian. Niektóre jednostki organizacyjne są niezbędne do działania i powinny pozostać zaznaczone.

Jeśli używasz filtrowania opartego na jednostce organizacyjnej z programem Azure AD Connect w wersji wcześniejszej niż 1.1.524.0, nowe jednostki organizacyjne dodane później są domyślnie synchronizowane. Aby nowe jednostki organizacyjne nie były synchronizowane, możesz skonfigurować odpowiednie ustawienie po zakończeniu działania kreatora w zakresie [filtrowania opartego na jednostce organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). W przypadku programu Azure AD Connect w wersji 1.1.524.0 lub nowszej można wskazać, czy nowe jednostki organizacyjne mają być synchronizowane.

Jeśli planujesz używać [filtrowania opartego na grupie](#sync-filtering-based-on-groups), upewnij się, że jednostka organizacyjna z grupą jest uwzględniona i nie została odfiltrowana przy użyciu filtrowania opartego na jednostce organizacyjnej. Filtrowanie oparte na jednostce organizacyjnej jest stosowane przed filtrowaniem opartym na grupie.

Istnieje również możliwość, że niektóre domeny są niedostępne z powodu ograniczeń zapory. Te domeny są domyślnie niezaznaczone i wyświetlane jest dla nich ostrzeżenie.  
![Niedostępne domeny](./media/how-to-connect-install-custom/unreachable.png)  
Jeśli takie ostrzeżenie zostanie wyświetlone, sprawdź, czy te domeny są rzeczywiście niedostępne i ostrzeżenie jest oczekiwane.

### <a name="uniquely-identifying-your-users"></a>Unikatowa identyfikacja użytkowników

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Wybieranie sposobu identyfikowania użytkowników w katalogach lokalnych
Funkcja dopasowywania w lasach umożliwia określenie, jak użytkownicy z lasów usługi AD DS są wyświetlani w usłudze Azure AD. Użytkownik może być wyświetlany tylko jeden raz we wszystkich lasach lub mieć kombinację włączonych i wyłączonych kont. W niektórych lasach użytkownik może być także wyświetlany jako kontakt.

![Unikatowe](./media/how-to-connect-install-custom/unique2.png)

| Ustawienie | Opis |
| --- | --- |
| [Obsługiwani użytkownicy są reprezentowani jednokrotnie we wszystkich lasach](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Wszyscy użytkownicy są utworzeni jako pojedyncze obiekty w usłudze Azure AD. Obiekty nie są łączone w magazynie metaverse. |
| [Atrybut poczty](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ta opcja łączy użytkowników i kontakty, jeśli atrybut poczty ma taką samą wartość w różnych lasach. Tej opcji należy użyć w przypadku, gdy kontakty zostały utworzone przy użyciu usługi GALSync. Jeśli ta opcja zostanie wybrana, obiekty użytkownika, których atrybut poczty nie jest wypełniony, nie będą synchronizowane z usługą Azure AD. |
| [Atrybuty ObjectSID i msExchangeMasterAccountSID/msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ta opcja łączy włączonego użytkownika w lesie konta z wyłączonym użytkownikiem w lesie zasobów. W programie Exchange ta konfiguracja jest określana jako połączona skrzynka pocztowa. Ta opcja może być również używana, jeśli używany jest tylko program Lync, a program Exchange nie jest obecny w lesie zasobów. |
| Atrybuty sAMAccountName i MailNickName |Ta opcja łączy atrybuty w przypadku, gdy oczekiwane jest znalezienie identyfikatora logowania dla użytkownika. |
| Określony atrybut |Ta opcja umożliwia wybranie własnego atrybutu. Jeśli ta opcja zostanie wybrana, obiekty użytkownika, których wybrany atrybut nie jest wypełniony, nie będą synchronizowane z usługą Azure AD. **Ograniczenie:** Dla tej opcji dostępne są tylko atrybuty, które można już znaleźć w obiekcie Metaverse. ". |

#### <a name="select-how-users-should-be-identified-with-azure-ad---source-anchor"></a>Wybieranie sposobu identyfikowania użytkowników z usługą Azure AD — zakotwiczenie źródła
Atrybut sourceAnchor jest niezmienialny w okresie istnienia obiektu użytkownika. Jest kluczem podstawowym łączącym użytkownika lokalnego z użytkownikiem w usłudze Azure AD.

| Ustawienie | Opis |
| --- | --- |
| Pozwól, aby platforma Azure zarządzała zakotwiczeniem źródła | Wybierz tę opcję, jeśli chcesz, aby usługa Azure AD wybierała ten atrybut za Ciebie. Jeśli wybierzesz tę opcję, kreator programu Azure AD Connect zastosuje logikę wyboru atrybutu sourceAnchor opisaną w sekcji artykułu [Azure AD Connect: Design concepts - Using ms-DS-ConsistencyGuid as sourceAnchor (Azure AD Connect: zagadnienia dotyczące projektowania — korzystanie z atrybutu ms-DS-ConsistencyGuid jako atrybutu sourceAnchor)](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Kreator informuje użytkownika, który atrybut został wybrany jako atrybut zakotwiczenia źródła po zakończeniu instalacji niestandardowej. |
| Określony atrybut | Wybierz tę opcję, jeśli chcesz określić istniejący atrybut usługi AD jako atrybut sourceAnchor. |

Ze względu na to, że atrybutu nie można zmienić należy zaplanować użycie właściwego atrybutu. Dobrym wyborem jest atrybut objectGUID. Tego atrybutu nie można zmienić, chyba że konto użytkownika jest przenoszone między lasami/domenami. Należy unikać atrybutów, które mogą ulec zmianie po zmianie stanu cywilnego lub zmianie zadań. Nie można używać atrybutów ze znakiem @-sign, więc nie można używać adresu e-mail ani atrybutu userPrincipalName. W tym atrybucie uwzględniana jest również wielkość liter, więc w przypadku przenoszenia obiektu między lasami należy pamiętać o zachowaniu wielkich/małych liter. Atrybuty binarne są zakodowane przy użyciu standardu base64, ale inne typy atrybutów pozostają w stanie niezakodowanym. W scenariuszach federacji i niektórych interfejsach usługi Azure AD ten atrybut nosi również nazwę immutableID. Więcej informacji na temat zakotwiczenia źródła znajduje się w temacie, w którym opisano [zagadnienia dotyczące projektowania](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrowanie synchronizacji na podstawie grup
Funkcja filtrowania grup umożliwia synchronizowanie tylko małego podzbioru obiektów do celów wdrożenia pilotażowego. Aby użyć tej funkcji, należy utworzyć w tym celu grupę w lokalnej usłudze Active Directory. Następnie należy dodać użytkowników i grupy, którzy mają zostać zsynchronizowani z usługą Azure AD jako bezpośredni członkowie. Później można dodawać i usuwać użytkowników tej grupy, aby opracować listę obiektów, które powinny znajdować się w usłudze Azure AD. Wszystkie obiekty przeznaczone do synchronizacji powinny być bezpośrednimi członkami grupy. Wszyscy użytkownicy i wszystkie grupy, kontakty, komputery/urządzenia muszą być bezpośrednimi członkami. Członkostwo grup zagnieżdżonych nie jest rozpoznawane. W przypadku dodania grupy jako członka dodawana jest tylko sama grupa, a nie jej członkowie.

![Filtrowanie synchronizacji](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Ta funkcja służy tylko do obsługi wdrożenia pilotażowego. Nie należy jej używać w pełnej wersji środowiska produkcyjnego.
>
>

W pełnej wersji środowiska produkcyjnego bardzo trudno będzie utrzymać jedną grupę ze wszystkimi obiektami do synchronizacji. Zamiast tego należy użyć jednej z metod opisanych w temacie [Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Funkcje opcjonalne
Na tym ekranie można wybrać funkcje opcjonalne dla określonych scenariuszy.

>[!WARNING]
>Wersje **1.0.8641.0** i starsze programu Azure AD Connect polegają na usłudze Azure Access Control w ramach zapisywania zwrotnego haseł.  Ta usługa zostanie wycofana w dniu **7 listopada 2018 r**.  Jeśli używasz jakiejkolwiek z tych wersji programu Azure AD Connect i masz włączoną funkcję zapisywania zwrotnego haseł, użytkownicy mogą stracić możliwość zmiany lub resetowania haseł po wycofaniu usługi. Zapisywanie zwrotne haseł w tych wersjach programu Azure AD Connect nie będzie obsługiwane.
>
>Aby uzyskać więcej informacji o usłudze Azure Access Control, zobacz [Instrukcje: migrowanie z usługi Azure Access Control Service](../azuread-dev/active-directory-acs-migration.md)
>
>Aby pobrać najnowszą wersję programu Azure AD Connect, kliknij [tutaj](https://www.microsoft.com/download/details.aspx?id=47594).

 ![Funkcje opcjonalne](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Jeśli narzędzie DirSync lub program Azure AD Sync są obecnie aktywne, nie należy aktywować żadnych funkcji zapisywania zwrotnego w programie Azure AD Connect.



| Funkcje opcjonalne | Opis |
| --- | --- |
| Wdrożenie hybrydowe programu Exchange |Funkcja wdrażania hybrydowego programu Exchange umożliwia współistnienie skrzynek pocztowych programu Exchange zarówno lokalnie, jak i w Microsoft 365. Program Azure AD Connect synchronizuje określony zbiór [atrybutów](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) z usługi Azure AD z katalogiem lokalnym. |
| Foldery publiczne poczty programu Exchange | Funkcja Foldery publiczne poczty programu Exchange umożliwia synchronizowanie obiektów z folderu publicznego wykorzystującego pocztę z lokalnej usługi Active Directory do usługi Azure AD. |
| Filtrowanie atrybutów i aplikacji usługi Azure AD |Przez włączenie filtrowania atrybutów i aplikacji usługi Azure AD można dostosować zestaw synchronizowanych atrybutów. Ta opcja dodaje do kreatora dwie dodatkowe strony konfiguracji. Więcej informacji znajduje się w temacie [Filtrowanie atrybutów i aplikacji usługi Azure AD](#azure-ad-app-and-attribute-filtering). |
| Synchronizacja skrótów haseł |Te opcję można włączyć, jeśli jako metodę logowania wybrano federację. Synchronizacja skrótów haseł może być następnie użyta jako opcja tworzenia kopii zapasowych. Dodatkowe informacje znajdują się w temacie [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md). </br></br>W przypadku wybrania uwierzytelniania przekazywanego tę opcję można również włączyć, aby zapewnić obsługę starszych klientów i opcję tworzenia kopii zapasowych. Dodatkowe informacje znajdują się w temacie [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md).|
| Zapisywanie zwrotne haseł |Po włączeniu zapisywania zwrotnego haseł zmiany hasła, które pochodzą z usługi Azure AD, są ponownie zapisywane w katalogu lokalnym. Więcej informacji można znaleźć w temacie [Wprowadzenie do zarządzania hasłami](../authentication/tutorial-enable-sspr.md) |
| Zapisywanie zwrotne grup |Jeśli używasz funkcji **grup Microsoft 365** , możesz mieć te grupy reprezentowane w Active Directory lokalnym. Ta opcja jest dostępna tylko, jeśli w lokalnej usłudze Active Directory jest dostępny program Exchange. Aby uzyskać więcej informacji, zobacz [Azure AD Connect zapisywania zwrotnego grup](how-to-connect-group-writeback.md)|
| Zapisywanie zwrotne urządzeń |Umożliwia zapisywanie zwrotne obiektów urządzenia w usłudze Azure AD do Active Directory lokalnych dla scenariuszy dostępu warunkowego. Więcej informacji znajduje się w temacie [Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect](how-to-connect-device-writeback.md). |
| Synchronizacja atrybutów rozszerzeń katalogów |Po włączeniu synchronizacji atrybutów rozszerzeń katalogów określone atrybuty są synchronizowane z usługą Azure AD. Więcej informacji znajduje się w temacie [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtrowanie atrybutów i aplikacji usługi Azure AD
W celu ograniczenia liczby atrybutów, które mają być synchronizowane z usługą Azure AD, należy najpierw wybrać usługi, które są używane. W przypadku wprowadzania na tej stronie zmian konfiguracji nową usługę należy wybrać jawnie przez ponowne uruchomienie kreatora instalacji.

![Funkcje opcjonalne — aplikacje](./media/how-to-connect-install-custom/azureadapps2.png)

W oparciu o usługi wybrane w poprzednim kroku na tej stronie wyświetlane są wszystkie atrybuty, które są synchronizowane. Ta lista zawiera kombinację wszystkich typów obiektów, które są synchronizowane. Jeśli istnieją jakieś określone atrybuty, które nie wymagają synchronizacji, można je odznaczyć.

![Funkcje opcjonalne — atrybuty](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Usuwanie atrybutów może mieć wpływ na funkcjonalność. Aby poznać najlepsze rozwiązania i zalecenia, zobacz temat [Synchronizowane atrybuty](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>
>

### <a name="directory-extension-attribute-sync"></a>Synchronizacja atrybutów rozszerzeń katalogów
Można rozszerzyć schemat w usłudze Azure AD o atrybuty niestandardowe dodane przez organizację użytkownika lub inne atrybuty w usłudze Active Directory. Aby użyć tej funkcji, wybierz opcję **Synchronizacja atrybutów rozszerzeń katalogu** na stronie **Funkcje opcjonalne**. Na tej stronie można wybrać więcej atrybutów do synchronizacji.

>[!NOTE]
>W polu Dostępne atrybuty rozróżniana jest wielkość liter.

![Rozszerzenia katalogów](./media/how-to-connect-install-custom/extension2.png)

Więcej informacji znajduje się w temacie [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on-sso"></a>Włączanie logowania jednokrotnego
Konfigurowanie logowania jednokrotnego na użytek synchronizacji haseł lub uwierzytelniania przekazywanego to prosty proces, który trzeba wykonać tylko raz dla każdego lasu synchronizowanego w usłudze Azure AD. Konfiguracja obejmuje następujące dwa kroki:

1.  Utworzenie niezbędnego konta komputera w lokalnej usłudze Active Directory.
2.  Skonfigurowanie obsługi logowania jednokrotnego w strefie intranetowej na maszynach klienckich.

#### <a name="create-the-computer-account-in-active-directory"></a>Tworzenie konta komputera w usłudze Active Directory
Dla każdego lasu dodanego za pomocą programu Azure AD Connect trzeba podać poświadczenia administratora domeny, aby konto komputera mogło zostać utworzone w każdym lesie. Poświadczenia są używane tylko w celu utworzenia konta i nie są przechowywane ani używane w kontekście innych operacji. Wystarczy dodać poświadczenia na stronie **Włącz logowanie jednokrotne** kreatora Azure AD Connect, jak pokazano poniżej:

![Włączanie logowania jednokrotnego](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Jeśli w określonym lesie nie chcesz używać logowania jednokrotnego, możesz pominąć ten las.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurowanie strefy intranetowej dla maszyn klienckich
Aby upewnić się, że logowanie klienta odbywa się automatycznie w strefie intranetowej, upewnij się, że adres URL jest częścią strefy intranetowej. Dzięki temu komputer przyłączony do domeny automatycznie wyśle bilet protokołu Kerberos do usługi Azure AD po połączeniu z siecią firmową.
Na komputerze, na którym są zainstalowane narzędzia do zarządzania zasadami grupy:

1.  Otwórz narzędzia do zarządzania zasadami grupy.
2.  Poddaj edycji zasady grupy, które zostaną zastosowane do wszystkich użytkowników. Mogą to być na przykład domyślne zasady domeny.
3.  Przejdź do obszaru **Konfiguracja użytkownika\Szablony administracyjne\Składniki systemu Windows\Internet Explorer\Internetowy panel sterowania\Strona zabezpieczeń** i wybierz pozycję **Lista przypisywanie witryn do stref** (jak na poniższym obrazie).
4.  Włącz zasady, a następnie wprowadź nazwę `https://autologon.microsoftazuread-sso.com` i wartość wartości `1` w oknie dialogowym.
5.  Zawartość okna powinna wyglądać mniej więcej tak:  
![Strefy intranetowe](./media/how-to-connect-install-custom/sitezone.png)

6.  Dwa razy kliknij przycisk **OK**.

## <a name="configuring-federation-with-ad-fs"></a>Konfigurowanie federacji przy użyciu usług AD FS
Konfigurowanie usług AD FS przy użyciu programu Azure AD Connect jest proste i wymaga zaledwie kilku kliknięć. Przed przystąpieniem do konfiguracji potrzebne są następujące elementy:

* Serwer Windows Server 2012 R2 lub nowszy dla serwera federacyjnego z włączonym zarządzaniem zdalnym
* Serwer Windows Server 2012 R2 lub nowszy dla serwera proxy aplikacji internetowej z włączonym zarządzaniem zdalnym
* Certyfikat TLS/SSL dla nazwy usługi federacyjnej, która ma być używana (na przykład sts.contoso.com)

>[!NOTE]
>Można zaktualizować certyfikat protokołu TLS/SSL dla farmy AD FS przy użyciu Azure AD Connect, nawet jeśli nie jest on używany do zarządzania relacją zaufania federacji.

### <a name="ad-fs-configuration-pre-requisites"></a>Wymagania wstępne konfiguracji usług AD FS
Aby skonfigurować farmę usług AD FS przy użyciu programu Azure AD Connect, należy upewnić się, że na serwerach zdalnych jest włączona usługa WinRM. Upewnij się, że ukończono inne zadania określone w [wymaganiach wstępnych federacji](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Ponadto należy zapoznać się z wymaganiami dotyczącymi portów wymienionymi w sekcji [Tabela 3 — program Azure AD Connect i serwery federacyjne/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Tworzenie nowej famy usług AD FS lub korzystanie z istniejącej farmy usług AD FS
Można użyć istniejącej farmy usług AD FS lub zdecydować się na utworzenie nowej farmy usług AD FS. Jeśli zdecydujesz się utworzyć nowe, musisz podać certyfikat TLS/SSL. Jeśli certyfikat TLS/SSL jest chroniony hasłem, zostanie wyświetlony monit o podanie hasła.

![Farma usług AD FS](./media/how-to-connect-install-custom/adfs1.png)

W przypadku decyzji o użyciu istniejącej farmy usług AD FS użytkownik zostaje bezpośrednio przeniesiony do konfigurowania relacji zaufania między usługami AD FS a ekranem usługi Azure AD.

>[!NOTE]
>Program Azure AD Connect może być używany do zarządzania tylko jedną farmą usług AD FS. Jeśli masz istniejącą relację zaufania federacyjnego zaufania z usługą Azure AD skonfigurowaną w wybranej farmie usług AD FS, zaufanie zostanie ponownie utworzone od początku przy użyciu programu Azure AD Connect.

### <a name="specify-the-ad-fs-servers"></a>Określanie serwerów usług AD FS
Należy wprowadzić serwery, na których mają być zainstalowane usługi AD FS. Można dodać jeden serwer lub większą ich liczbę w zależności od tego, jakie są potrzeby związane z planowaniem wydajności. Przed wykonaniem tej konfiguracji należy dołączyć wszystkie serwery usługi AD FS do usługi Active Directory (niewymagane w przypadku serwerów WAP). Firma Microsoft zaleca instalowanie jednego serwera usług AD FS do celów wdrożeń testowych i pilotażowych. Następnie można dodać i wdrożyć więcej serwerów w zależności od potrzeb związanych ze skalowaniem przez ponowne uruchomienie programu Azure AD Connect po wstępnej konfiguracji.

> [!NOTE]
> Przed wykonaniem tej konfiguracji należy upewnić się, że wszystkie serwery zostały dołączone do domeny AD.
>
>

![Serwery usług AD FS](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Określanie serwerów proxy aplikacji internetowych
Należy wprowadzić serwery, które mają służyć jako serwery proxy aplikacji internetowych. Serwer proxy aplikacji internetowej jest wdrażany w strefie DMZ (ukierunkowanej na sieć ekstranet) i obsługuje żądania uwierzytelniania z ekstranetu. Można dodać jeden serwer lub większą ich liczbę w zależności od tego, jakie są potrzeby związane z planowaniem wydajności. Firma Microsoft zaleca instalowanie jednego serwera proxy aplikacji internetowej do celów wdrożeń testowych i pilotażowych. Następnie można dodać i wdrożyć więcej serwerów w zależności od potrzeb związanych ze skalowaniem przez ponowne uruchomienie programu Azure AD Connect po wstępnej konfiguracji. Zaleca się równoważną liczbę serwerów proxy, aby spełnić wymagania uwierzytelniania z sieci intranet.

> [!NOTE]
> <li> Jeśli używane konto nie jest lokalnym kontem administratora na serwerach WAP, zostanie wyświetlony monit o podanie poświadczeń administratora.</li>
> <li> Przed wykonaniem tego kroku upewnij się, że istnieje połączenie HTTP/HTTPS między serwerem programu Azure AD Connect a serwerem proxy aplikacji internetowej.</li>
> <li> Upewnij się, że istnieje połączenie HTTP/HTTPS między serwerem aplikacji internetowej a serwerem usług AD FS, które umożliwi przepływ żądań uwierzytelniania.</li>
>

![Aplikacja internetowa](./media/how-to-connect-install-custom/adfs3.png)

Zostanie wyświetlony monit o podanie poświadczeń, aby serwer aplikacji internetowej mógł nawiązać bezpieczne połączenie z serwerem usług AD FS. Wymagane są poświadczenia lokalnego administratora na serwerze usług AD FS.

![Serwer proxy](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Określanie konta usługi dla usług AD FS
Usługi AD FS wymagają konta usług domeny do uwierzytelniania użytkowników i sprawdzania informacji o użytkownikach w usłudze Active Directory. Obsługiwane są dwa typy kont usługi:

* **Konto usługi zarządzane przez grupę** — wprowadzone w usługach Active Directory Domain Services w systemie Windows Server 2012. Ten typ konta zapewnia usługom, takim jak usługi AD FS, pojedyncze konto bez konieczności regularnego aktualizowania hasła do konta. Opcja ta powinna być używana, jeśli kontrolery domeny systemu Windows Server 2012 znajdują się już w domenie, do której należą serwery usług AD FS.
* **Konto użytkownika domeny** — ten typ konta wymaga podawania hasła i regularnego aktualizowania hasła w przypadku jego zmiany lub wygaśnięcia. Tej opcji należy używać tylko, jeśli w domenie, do której należą serwery usług AD FS, nie ma kontrolerów domeny systemu Windows Server 2012.

Jeśli zostało wybrane konto usługi zarządzane przez grupę i funkcja ta nie była nigdy używana w usłudze Active Directory, zostanie wyświetlony monit o poświadczenia administratora przedsiębiorstwa. Poświadczenia te służą do inicjowania magazynu kluczy i włączania funkcji w usłudze Active Directory.

> [!NOTE]
> Program Azure AD Connect wykonuje sprawdzenie w celu wykrycia, czy usługa AD FS jest już zarejestrowana w domenie jako nazwa SPN.  Usługa AD DS nie zezwala na jednoczesną rejestrację zduplikowanych nazw SPN.  Jeśli zostanie znaleziona zduplikowana nazwa SPN, nie będzie można kontynuować pracy do momentu usunięcia tej nazwy SPN.

![Konto usług AD FS](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Wybór domeny usługi Azure AD, która ma być sfederowana
Ta konfiguracja służy do konfiguracji relacji federacyjnej pomiędzy usługami AD FS a usługą Azure AD. Usługi AD FS są konfigurowane w celu wydawania tokenów zabezpieczających do usługi Azure AD, natomiast usługa Azure AD jest konfigurowana w celu zaufania tokenom pochodzącym z tego określonego wystąpienia usług AD FS. Ta strona umożliwia tylko konfigurowanie pojedynczej domeny podczas instalacji wstępnej. Później można skonfigurować więcej domen przez ponowne uruchomienie programu Azure AD Connect.

![Zrzut ekranu przedstawiający stronę "domena usługi Azure AD".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Weryfikowanie domeny usługi Azure AD wybranej do federacji
Po wybraniu domeny do sfederowania program Azure AD Connect dostarcza niezbędne informacje umożliwiające sprawdzenie niezweryfikowanej domeny. Sposób korzystania z tych informacji opisano w temacie [Dodawanie i weryfikowanie domeny](../fundamentals/add-custom-domain.md).

![Domena usługi Azure AD](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Program AD Connect podejmuje próbę weryfikacji domeny w fazie konfiguracji. W przypadku kontynuowania konfigurowania bez dodania niezbędnych rekordów DNS kreator nie będzie mógł ukończyć konfiguracji.
>
>

## <a name="configuring-federation-with-pingfederate"></a>Konfigurowanie federacji z serwerem PingFederate
Konfigurowanie serwera PingFederate przy użyciu programu Azure AD Connect jest proste i wymaga zaledwie kilku kliknięć. Jednak wymagane jest spełnienie wymienionych niżej wymagań wstępnych.
- Serwer PingFederate 8.4 lub nowszy.  Aby uzyskać więcej informacji [, zobacz serwera pingfederate Integration with Azure Active Directory i Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)
- Certyfikat TLS/SSL dla nazwy usługi federacyjnej, która ma być używana (na przykład sts.contoso.com)

### <a name="verify-the-domain"></a>Weryfikowanie domeny
Po wyborze federacji z serwerem PingFederate zostanie wyświetlony monit z prośbą o weryfikację domeny, która ma zostać sfederowana.  Wybierz domenę z listy rozwijanej.

![Zrzut ekranu przedstawiający "domenę usługi Azure AD" z wybraną przykładową domeną "contoso.com".](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Eksportowanie ustawień serwera PingFederate


Serwer PingFederate musi być skonfigurowany jako serwer federacyjny dla każdej domeny federacyjnej platformy Azure.  Kliknij przycisk Eksportuj ustawienia i udostępnij te informacje administratorowi serwera PingFederate.  Administrator serwera federacyjnego zaktualizuje konfigurację, a następnie poda adres URL i numer portu dla serwera PingFederate, dzięki czemu program Azure AD Connect będzie mógł zweryfikować ustawienia metadanych.  

![Zweryfikuj domenę](./media/how-to-connect-install-custom/ping2.png)

Aby rozwiązać problemy z walidacją, skontaktuj się z administratorem serwera PingFederate.  Poniżej przedstawiono przykład serwera PingFederate, który nie ma prawidłowej relacji zaufania na platformie Azure:

![Zaufanie](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Weryfikowanie łączności federacji
Program Azure AD Connect podejmie próbę walidacji punktów końcowych uwierzytelniania pobranych w poprzednim kroku z metadanych serwera PingFederate.  Na początku program Azure AD Connect spróbuje rozpoznać punkty końcowe przy użyciu lokalnych serwerów DNS.  W dalszej kolejności spróbuje rozpoznać punkty końcowe, korzystając z zewnętrznego dostawcy DNS.  Aby rozwiązać problemy z walidacją, skontaktuj się z administratorem serwera PingFederate.  

![Weryfikowanie łączności](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-login"></a>Weryfikowanie logowania federacyjnego
Na koniec możesz sprawdzić nowo skonfigurowany przepływ logowania federacyjnego, logując się do domeny federacyjnej. Powodzenie tej operacji oznacza, że federacja z serwerem PingFederate została pomyślnie skonfigurowana.
![Weryfikowanie logowania](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Konfigurowanie i weryfikowanie stron
Na tej stronie wykonywane jest konfigurowanie.

> [!NOTE]
> Przed kontynuowaniem instalacji po skonfigurowaniu federacji należy upewnić się, że skonfigurowano [rozpoznawanie nazw dla serwerów federacyjnych](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers).
>
>


![Wszystko gotowe do skonfigurowania](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Tryb przejściowy
Istnieje możliwość skonfigurowania nowego serwera synchronizacji równolegle z trybem przejściowym. Obsługiwany jest tylko jeden serwer synchronizacji wykonujący eksport do jednego katalogu w chmurze. W przypadku przenoszenia z innego serwera, np. takiego, na którym jest uruchomione narzędzie DirSync, można przełączyć program Azure AD Connect w tryb przejściowy. Po włączeniu aparat synchronizacji importuje i synchronizuje dane w zwykły sposób, ale nie eksportuje żadnych elementów do usługi Azure AD lub AD. W trybie przejściowym funkcje synchronizacji haseł i zapisywania zwrotnego haseł są wyłączone.

![Tryb przejściowy](./media/how-to-connect-install-custom/stagingmode.png)

W trybie przejściowym można wprowadzić wymagane zmiany w aparacie synchronizacji i sprawdzić to, co ma być wyeksportowane. Jeśli konfiguracja wygląda dobrze, uruchom ponownie kreatora instalacji i wyłącz tryb przejściowy. Dane z tego serwera zostaną wyeksportowane do usługi Azure AD. Pamiętaj, aby w tym samym czasie wyłączyć drugi serwer, aby aktywnie eksportował tylko jeden serwer.

Więcej informacji znajduje się w temacie [Tryb przejściowy](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Weryfikowanie konfiguracji federacji
Po kliknięciu przycisku Weryfikuj program Azure AD Connect sprawdza ustawienia DNS.

**Sprawdzanie łączności z intranetem**

* Rozpoznawanie federacyjnej nazwy FQDN: program Azure AD Connect sprawdza, czy federacyjna nazwa FQDN może zostać rozpoznana przez serwer DNS w celu zapewnienia łączności. Jeśli program Azure AD Connect nie może rozpoznać nazwy FQDN, weryfikacja zakończy się niepowodzeniem. Upewnij się, że rekord DNS jest obecny dla nazwy FQDN usługi federacyjnej, aby można było pomyślnie ukończyć weryfikację.
* Rekord A systemu DNS: program Azure AD Connect sprawdza, czy istnieje rekord A dla usługi federacyjnej. W przypadku braku rekordu A weryfikacja zakończy się niepowodzeniem. Utwórz rekord A (nie rekord CNAME) dla federacyjnej nazwy FQDN, aby można było pomyślnie ukończyć weryfikację.

**Sprawdzanie łączności z ekstranetem**

* Rozpoznawanie federacyjnej nazwy FQDN: program Azure AD Connect sprawdza, czy federacyjna nazwa FQDN może zostać rozpoznana przez serwer DNS w celu zapewnienia łączności.

![Ukończ](./media/how-to-connect-install-custom/completed.png)

![Weryfikacja](./media/how-to-connect-install-custom/adfs7.png)

Aby sprawdzić poprawność uwierzytelniania na całej trasie, należy ręcznie wykonać przynajmniej jeden z następujących testów:

* Po zakończeniu synchronizacji skorzystaj z dodatkowego zadania weryfikacji logowania federacyjnego w programie Azure AD Connect, aby zweryfikować uwierzytelnianie dla wybranego konta użytkownika lokalnego.
* Sprawdź, czy możesz zalogować się w przeglądarce na komputerze dołączonym do domeny w sieci intranet — połącz się z adresem https://myapps.microsoft.com i sprawdź logowanie na zalogowanym koncie. Wbudowane konto administratora usług AD DS nie jest synchronizowane i nie można używać go do weryfikacji.
* Sprawdź, czy możesz zalogować się na urządzeniu w sieci ekstranet. Z komputera domowego lub urządzenia mobilnego połącz się z adresem https://myapps.microsoft.com i podaj poświadczenia.
* Sprawdź poprawność logowania wzbogaconego klienta. Połącz się z adresem https://testconnectivity.microsoft.com, wybierz kartę usługi **Office 365** i wybierz opcję **Test rejestracji jednokrotnej usługi Office 365**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Poniższa sekcja obejmuje rozwiązywanie problemów i informacje, z których możesz skorzystać w przypadku napotkania problemu podczas instalowania programu Azure AD Connect.

### <a name="the-adsync-database-already-contains-data-and-cannot-be-overwritten"></a>„Baza danych ADSync już zawiera dane i nie można jej zastąpić”
W przypadku niestandardowej instalacji Azure AD Connect i wybrania opcji **Użyj istniejącego serwera SQL** na stronie **Instalowanie wymaganych składników** może wystąpić błąd informujący o tym, że **baza danych ADSync zawiera już dane i nie można jej zastąpić. Usuń istniejącą bazę danych i spróbuj ponownie.**

![Zrzut ekranu przedstawiający stronę "Instalowanie składników wymaganych".](./media/how-to-connect-install-custom/error1.png)

Dzieje się tak, ponieważ na wystąpieniu SQL serwera SQL istnieje już baza danych o nazwie **ADSync**, którą określono w powyższych polach tekstowych.

Ten błąd zazwyczaj występuje po odinstalowaniu program Azure AD Connect.  Baza danych nie zostanie usunięta z programu SQL Server podczas dezinstalacji.

Aby rozwiązać ten problem, najpierw upewnij się, że baza danych **ADSync**, która była używana przez program Azure AD Connect przed dezinstalacją, nie jest już używana.

Następnie zaleca się utworzenie kopii zapasowej bazy danych przed jej usunięciem.

Na koniec należy usunąć bazę danych.  Możesz to zrobić, używając programu **Microsoft SQL Server Management Studio** i nawiązując połączenie z wystąpieniem SQL. Znajdź bazę danych **ADSync**, kliknij ją prawym przyciskiem myszy, a następnie wybierz polecenie **Usuń** z menu kontekstowego.  Następnie kliknij przycisk **OK**, aby usunąć bazę danych.

![Błąd](./media/how-to-connect-install-custom/error2.png)

Po usunięciu bazy danych **ADSync** możesz kliknąć przycisk **Zainstaluj**, aby ponowić próbę instalacji.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu instalacji wyloguj się, a następnie zaloguj się ponownie w systemie Windows przed użyciem narzędzia Synchronization Service Manager lub Edytor reguł synchronizacji.

Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).

Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](how-to-connect-health-sync.md).

Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).