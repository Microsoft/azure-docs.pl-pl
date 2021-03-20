---
title: Dostosowywanie instalacji Azure Active Directory Connect
description: W tym artykule wyjaśniono opcje instalacji niestandardowej dla Azure AD Connect. Korzystając z tych instrukcji, można zainstalować usługę Active Directory za pośrednictwem programu Azure AD Connect.
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
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92096355"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Niestandardowa instalacja Azure Active Directory Connect
Użyj *ustawień niestandardowych* w programie Azure Active Directory (Azure AD) Connect, jeśli chcesz uzyskać więcej opcji instalacji. Użyj tych ustawień, na przykład jeśli masz wiele lasów lub chcesz skonfigurować funkcje opcjonalne. Użyj ustawień niestandardowych we wszystkich przypadkach, gdy [Instalacja ekspresowa](how-to-connect-install-express.md) nie spełnia wymagań dotyczących wdrożenia lub topologii.

Wymagania wstępne:
- [Pobierz Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Wykonaj kroki wymagań wstępnych w [Azure AD Connect: Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md). 
- Upewnij się, że masz konta opisane w [Azure AD Connect konta i uprawnienia](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Ustawienia instalacji niestandardowej 

Aby skonfigurować instalację niestandardową dla Azure AD Connect, przejdź do stron kreatora, które opisano w poniższych sekcjach.

### <a name="express-settings"></a>Ustawienia ekspresowe
Na stronie **Ustawienia ekspresowe** wybierz pozycję **Dostosuj** , aby rozpocząć instalację dostosowanego ustawienia.  Pozostała część tego artykułu przeprowadzi Cię przez proces instalacji niestandardowej. Skorzystaj z poniższych linków, aby szybko przejść do informacji dotyczących określonej strony:

- [Wymagane składniki](#install-required-components)
- [Logowanie użytkownika](#user-sign-in)
- [Nawiązywanie połączenia z usługą Azure AD](#connect-to-azure-ad)
- [Synchronizacja](#sync-pages)

### <a name="install-required-components"></a>Instalacja wymaganych składników
Po zainstalowaniu usług synchronizacji można pozostawić niewybraną sekcję konfiguracji opcjonalnej. Azure AD Connect automatycznie konfiguruje wszystko. Konfiguruje wystąpienie SQL Server 2012 Express LocalDB, tworzy odpowiednie grupy i przypisuje uprawnienia. Jeśli chcesz zmienić ustawienia domyślne, wyczyść odpowiednie pola.  Poniższa tabela zawiera podsumowanie tych opcji i zawiera linki do dodatkowych informacji. 

![Zrzut ekranu przedstawiający opcjonalne wybory dla wymaganych składników instalacji w programie Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Konfiguracja opcjonalna | Opis |
| --- | --- |
|Określ niestandardową lokalizację instalacji| Umożliwia zmianę domyślnej ścieżki instalacji Azure AD Connect.|
| Użyj istniejącego serwera SQL Server |Pozwala określić nazwę SQL Server i nazwę wystąpienia. Wybierz tę opcję, jeśli masz już serwer bazy danych, którego chcesz użyć. W polu **Nazwa wystąpienia** wprowadź nazwę wystąpienia, przecinek i numer portu, jeśli wystąpienie SQL Server nie ma włączonej funkcji przeglądania.  Następnie określ nazwę bazy danych Azure AD Connect.  Twoje uprawnienia SQL określają, czy można utworzyć nową bazę danych, czy administrator SQL musi utworzyć bazę danych z wyprzedzeniem.  Jeśli masz uprawnienia administratora SQL Server (SA), zobacz [instalowanie Azure AD Connect przy użyciu istniejącej bazy danych](how-to-connect-install-existing-database.md).  Jeśli masz uprawnienia delegowane (DBO), zobacz [instalowanie Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md). |
| Użyj istniejącego konta usługi |Domyślnie usługa Azure AD Connect udostępnia wirtualne konto usługi dla usług synchronizacji. Jeśli używasz zdalnego wystąpienia SQL Server lub używasz serwera proxy wymagającego uwierzytelniania, możesz użyć *zarządzanego konta usługi* lub konta usługi chronionego hasłem w domenie. W takich przypadkach Wprowadź konto, którego chcesz użyć. Aby uruchomić instalację, musisz być skojarzeniem zabezpieczeń w programie SQL, aby można było utworzyć poświadczenia logowania dla konta usługi. Aby uzyskać więcej informacji, zobacz [Azure AD Connect kont i uprawnień](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Przy użyciu najnowszej kompilacji administrator programu SQL Server może teraz zainicjować obsługę administracyjną bazy danych poza pasmem. Następnie administrator Azure AD Connect może zainstalować go z prawami właściciela bazy danych.  Aby uzyskać więcej informacji, zobacz [instalowanie Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).|
| Określ niestandardowe grupy synchronizacji |Domyślnie podczas instalowania usług synchronizacji program Azure AD Connect tworzy cztery grupy, które są lokalne dla serwera. Te grupy to Administratorzy, operatorzy, przeglądanie i resetowanie hasła. Możesz tu określić własne grupy. Grupy muszą być lokalne na serwerze. Nie mogą znajdować się w domenie. |
|Importuj ustawienia synchronizacji (wersja zapoznawcza)|Umożliwia importowanie ustawień z innych wersji Azure AD Connect.  Aby uzyskać więcej informacji, zobacz [Importowanie i eksportowanie ustawień konfiguracji Azure AD Connect](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Logowanie użytkowników
Po zainstalowaniu wymaganych składników wybierz metodę logowania jednokrotnego dla użytkowników. W poniższej tabeli przedstawiono krótkie informacje o dostępnych opcjach. Pełny opis metod logowania znajduje się w temacie [Logowanie użytkowników](plan-connect-user-signin.md).

![Zrzut ekranu przedstawiający stronę "Logowanie użytkownika". Wybrana jest opcja "Synchronizacja skrótów haseł".](./media/how-to-connect-install-custom/usersignin4.png)

| Opcja logowania jednokrotnego | Opis |
| --- | --- |
| Synchronizacja skrótów haseł |Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej. Hasła użytkowników są synchronizowane z usługą Azure AD jako skrót hasła. Uwierzytelnianie odbywa się w chmurze. Aby uzyskać więcej informacji, zobacz [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md). |
|Uwierzytelnianie przekazywane|Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej.  Hasła użytkowników są weryfikowane przez przekazanie do lokalnego kontrolera domeny Active Directory.
| Federacja z usługami AD FS |Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej.  Użytkownicy są przekierowywani do lokalnego wystąpienia usług Azure Directory Federation Services (AD FS), aby się zalogować. Uwierzytelnianie odbywa się lokalnie. |
| Federacja z serwerem PingFederate|Użytkownicy mogą logować się do usług w chmurze firmy Microsoft, takich jak Microsoft 365, przy użyciu tego samego hasła, które są używane w sieci lokalnej.  Użytkownicy są przekierowywani do lokalnego wystąpienia serwera pingfederate, aby się zalogować. Uwierzytelnianie odbywa się lokalnie. |
| Nie konfiguruj |Nie zainstalowano ani nie skonfigurowano funkcji logowania użytkownika. Wybierz tę opcję, jeśli masz już serwer federacyjny innej firmy lub inne rozwiązanie. |
|Włącz logowanie jednokrotne|Ta opcja jest dostępna zarówno w przypadku synchronizacji skrótów haseł, jak i uwierzytelniania przekazywanego. Udostępnia ona funkcję logowania jednokrotnego dla użytkowników klasycznych w sieciach firmowych. Aby uzyskać więcej informacji, zobacz [Logowanie jednokrotne](how-to-connect-sso.md). </br></br>**Uwaga:** W przypadku klientów AD FS ta opcja jest niedostępna. AD FS już oferuje ten sam poziom logowania jednokrotnego.</br>

### <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD
Na stronie **Połącz z usługą Azure AD** Wprowadź konto administratora globalnego i hasło. Jeśli na poprzedniej stronie została wybrana **Federacja z AD FS** , nie Zaloguj się przy użyciu konta należącego do domeny, którą planujesz włączyć dla Federacji. 

Możesz chcieć użyć konta w domyślnej domenie *onmicrosoft.com* , która jest dostarczana z dzierżawą usługi Azure AD. To konto jest używane tylko do tworzenia konta usługi w usłudze Azure AD. Nie jest on używany po zakończeniu instalacji.
  
![Zrzut ekranu przedstawiający stronę "łączenie z usługą Azure AD".](./media/how-to-connect-install-custom/connectaad.png)

Jeśli konto administratora globalnego ma włączone uwierzytelnianie wieloskładnikowe, należy ponownie podać hasło w oknie logowania i należy wykonać wyzwanie uwierzytelniania wieloskładnikowego. Wyzwaniem może być kod weryfikacyjny lub połączenie telefoniczne.  

![Zrzut ekranu przedstawiający stronę "łączenie z usługą Azure AD". Pole uwierzytelniania wieloskładnikowego poprosi użytkownika o kod.](./media/how-to-connect-install-custom/connectaadmfa.png)

W przypadku konta administratora globalnego może być również włączona funkcja [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) .

Jeśli wystąpi błąd lub problemy z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Synchronizuj strony

Poniższe sekcje zawierają opis stron w sekcji **Synchronizacja** .

### <a name="connect-your-directories"></a>Podłączanie katalogów
Aby nawiązać połączenie z usługą Active Directory Domain Services (Azure AD DS), Azure AD Connect musi mieć nazwę lasu i poświadczenia konta, które ma wystarczające uprawnienia.

![Zrzut ekranu przedstawiający stronę "łączenie katalogów".](./media/how-to-connect-install-custom/connectdir01.png)

Po wprowadzeniu nazwy lasu i wybraniu opcji  **Dodaj katalog** zostanie wyświetlone okno. W poniższej tabeli opisano opcje.

| Opcja | Opis |
| --- | --- |
| Utwórz nowe konto | Utwórz konto usługi Azure AD DS, które Azure AD Connect musi nawiązać połączenie z lasem Active Directory podczas synchronizacji katalogów. Po wybraniu tej opcji wprowadź nazwę użytkownika i hasło dla konta administratora przedsiębiorstwa.  Azure AD Connect używa dostarczonego konta administratora przedsiębiorstwa do utworzenia wymaganego konta usługi Azure AD DS. Możesz wprowadzić domenę w formacie NetBIOS lub FQDN. Oznacza to, że należy wprowadzić *FABRIKAM\administrator* lub *FABRIKAM. com\administrator*. |
| Użyj istniejącego konta | Podaj istniejące konto usługi Azure AD DS, za pomocą którego Azure AD Connect można nawiązać połączenie z lasem Active Directory podczas synchronizacji katalogów. Możesz wprowadzić domenę w formacie NetBIOS lub FQDN. Oznacza to, że należy wprowadzić *FABRIKAM\syncuser* lub *FABRIKAM. com\syncuser*. To konto może być kontem zwykłego użytkownika, ponieważ wymaga tylko domyślnych uprawnień do odczytu. Jednak w zależności od danego scenariusza może być konieczne wykonanie dodatkowych uprawnień. Aby uzyskać więcej informacji, zobacz [Azure AD Connect kont i uprawnień](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Zrzut ekranu przedstawiający stronę "łączenie katalogu" i okno konta lasu D, w którym można utworzyć nowe konto lub użyć istniejącego konta.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> W przypadku kompilacji 1.4.18.0 nie można użyć konta administratora przedsiębiorstwa lub administratora domeny jako konta łącznika usługi Azure AD DS. W przypadku wybrania opcji **Użyj istniejącego konta** w przypadku próby wprowadzenia konta administratora przedsiębiorstwa lub konta administratora domeny zostanie wyświetlony następujący komunikat o błędzie: "używanie konta przedsiębiorstwa lub administratora domeny dla konta lasu usługi AD jest niedozwolone. Zezwól Azure AD Connect utworzyć konta lub określ konto synchronizacji z odpowiednimi uprawnieniami ".
>

### <a name="azure-ad-sign-in-configuration"></a>Konfiguracja logowania się w usłudze Azure AD
Na stronie **Konfiguracja logowania za pomocą usługi Azure AD** Przejrzyj domeny głównej nazwy użytkownika (UPN) w lokalnej AD DS platformy Azure. Te domeny nazw UPN zostały zweryfikowane w usłudze Azure AD. Na tej stronie można skonfigurować atrybut do użycia dla elementu userPrincipalName.

![Zrzut ekranu przedstawiający niezweryfikowane domeny na stronie "Konfiguracja logowania na platformie Azure".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Przejrzyj każdą domenę, która jest oznaczona jako **niedodana** lub **nie została zweryfikowana**. Upewnij się, że używane domeny zostały zweryfikowane w usłudze Azure AD. Po zweryfikowaniu domen wybierz ikonę odświeżania cyklicznego. Aby uzyskać więcej informacji, zobacz [Dodawanie i Weryfikowanie domeny](../fundamentals/add-custom-domain.md).

Użytkownicy używają atrybutu *userPrincipalName* podczas logowania do usługi Azure AD i Microsoft 365. Przed zsynchronizowaniem użytkowników usługa Azure AD powinna zweryfikować domeny, znane także jako sufiks głównej nazwy użytkownika. Firma Microsoft zaleca zachowanie domyślnego atrybutu userPrincipalName. 

Jeśli atrybut userPrincipalName jest nonroutable i nie można go zweryfikować, możesz wybrać inny atrybut. Możesz na przykład wybrać pozycję Poczta e-mail jako atrybut, który zawiera identyfikator logowania. Jeśli używasz atrybutu innego niż userPrincipalName, jest on znany jako *alternatywny identyfikator*. 

Wartość atrybutu alternatywny identyfikator musi być zgodna ze standardem RFC 822. Można użyć alternatywnego identyfikatora z synchronizacją skrótów haseł, uwierzytelniania przekazywanego i Federacji. W Active Directory atrybut nie może być zdefiniowany jako wielowartościowy, nawet jeśli ma tylko jedną wartość. Aby uzyskać więcej informacji o IDENTYFIKATORze alternatywnym, zobacz [uwierzytelnianie przekazywane: często zadawane pytania](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Po włączeniu uwierzytelniania przekazywanego należy mieć co najmniej jedną zweryfikowaną domenę, aby kontynuować proces instalacji niestandardowej.

> [!WARNING]
> Identyfikatory alternatywne nie są zgodne ze wszystkimi obciążeniami Microsoft 365. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnych identyfikatorów logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Filtrowanie domen i jednostek organizacyjnych
Domyślnie są synchronizowane wszystkie domeny i jednostki organizacyjne (OU). Jeśli nie chcesz synchronizować niektórych domen lub jednostek organizacyjnych z usługą Azure AD, możesz wyczyścić odpowiednie opcje.  

![Zrzut ekranu przedstawiający stronę filtrowania domen i O.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Ta strona służy do konfigurowania filtrowania opartego na domenie i jednostce organizacyjnej. Jeśli planujesz wprowadzić zmiany, zobacz [filtrowanie oparte na domenie](how-to-connect-sync-configure-filtering.md#domain-based-filtering) i [filtrowanie oparte na jednostce organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Niektóre jednostki organizacyjne są niezbędne do działania, dlatego należy pozostawić je wybrane.

Jeśli używasz filtrowania opartego na jednostce organizacyjnej w wersji Azure AD Connect starszej niż 1.1.524.0, nowe jednostki organizacyjne są domyślnie synchronizowane. Jeśli nie chcesz synchronizować nowych jednostek organizacyjnych, możesz dostosować zachowanie domyślne po kroku [filtrowania na podstawie jednostki organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) . W przypadku Azure AD Connect 1.1.524.0 lub nowszych można wskazać, czy chcesz synchronizować nowe jednostki organizacyjne.

Jeśli planujesz użycie [filtrowania opartego na grupach](#sync-filtering-based-on-groups), upewnij się, że jednostka organizacyjna z grupą jest dołączona i nie jest filtrowana przy użyciu filtrowania jednostek organizacyjnych. Filtrowanie jednostek organizacyjnych jest oceniane przed oceną filtrowania opartego na grupach.

Istnieje również możliwość, że niektóre domeny są nieosiągalne ze względu na ograniczenia zapory. Te domeny są domyślnie niewybrane i są wyświetlane ostrzeżenie.  

![Zrzut ekranu przedstawiający nieosiągalne domeny.](./media/how-to-connect-install-custom/unreachable.png)  

Jeśli widzisz to ostrzeżenie, upewnij się, że te domeny są rzeczywiście nieosiągalne i że jest oczekiwane ostrzeżenie.

### <a name="uniquely-identifying-your-users"></a>Unikatowa identyfikacja użytkowników

Na stronie **Identyfikowanie użytkowników** wybierz sposób identyfikowania użytkowników w katalogach lokalnych i identyfikowanie ich przy użyciu atrybutu sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Wybieranie sposobu identyfikowania użytkowników w katalogach lokalnych
Korzystając z funkcji *dopasowywania w lasach* , można zdefiniować sposób, w jaki użytkownicy z lasów platformy Azure AD DS są reprezentowani w usłudze Azure AD. Użytkownik może być reprezentowany tylko raz we wszystkich lasach lub może mieć kombinację włączonych i wyłączonych kont. W niektórych lasach użytkownik może być także wyświetlany jako kontakt.

![Zrzut ekranu przedstawiający stronę, na której można jednoznacznie identyfikować użytkowników.](./media/how-to-connect-install-custom/unique2.png)

| Ustawienie | Opis |
| --- | --- |
| [Użytkownicy są reprezentowani tylko raz we wszystkich lasach](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Wszyscy użytkownicy są utworzeni jako pojedyncze obiekty w usłudze Azure AD. Obiekty nie są przyłączone do magazynu Metaverse. |
| [Atrybut poczty](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ta opcja łączy użytkowników i kontakty, jeśli atrybut poczty ma taką samą wartość w różnych lasach. Użyj tej opcji, jeśli kontakty zostały utworzone przy użyciu GALSync. W przypadku wybrania tej opcji obiekty użytkownika, których atrybut poczty nie zostanie wypełniona, nie są synchronizowane z usługą Azure AD. |
| [Atrybuty ObjectSID i msExchangeMasterAccountSID/msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ta opcja łączy włączonego użytkownika w lesie konta z wyłączonym użytkownikiem w lesie zasobów. W programie Exchange ta konfiguracja jest określana jako połączona skrzynka pocztowa. Możesz użyć tej opcji, jeśli używasz tylko programu Lync, a program Exchange nie znajduje się w lesie zasobów. |
| Atrybuty SAMAccountName i MailNickName |Ta opcja dołącza do atrybutów, w których oczekiwany jest identyfikator logowania użytkownika. |
| Wybieranie określonego atrybutu |Ta opcja umożliwia wybranie własnego atrybutu. W przypadku wybrania tej opcji obiekty użytkownika, których atrybut (selected) nie zostanie wypełniona, nie są synchronizowane z usługą Azure AD. **Ograniczenie:** Dla tej opcji dostępne są tylko atrybuty, które znajdują się już w magazynie Metaverse. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Wybierz sposób identyfikowania użytkowników przy użyciu kotwicy źródłowej
Atrybut *sourceAnchor* jest niezmienny w okresie istnienia obiektu użytkownika. Jest to klucz podstawowy, który łączy użytkownika lokalnego z użytkownikiem w usłudze Azure AD.

| Ustawienie | Opis |
| --- | --- |
| Pozwól, aby platforma Azure zarządzała zakotwiczeniem źródła | Wybierz tę opcję, jeśli chcesz, aby usługa Azure AD wybierała ten atrybut za Ciebie. W przypadku wybrania tej opcji Azure AD Connect stosuje logikę wyboru atrybutów sourceAnchor, która została opisana w temacie [using MS-ds-ConsistencyGuid as sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Po zakończeniu instalacji niestandardowej zobaczysz, który atrybut został wybrany jako atrybut sourceAnchor. |
| Wybieranie określonego atrybutu | Wybierz tę opcję, jeśli chcesz określić istniejący atrybut AD jako atrybut sourceAnchor. |

Ponieważ nie można zmienić atrybutu sourceAnchor, należy wybrać odpowiedni atrybut. Dobrym wyborem jest atrybut objectGUID. Ten atrybut nie jest zmieniany, chyba że konto użytkownika jest przenoszone między lasami lub domenami. Nie wybieraj atrybutów, które mogą ulec zmianie, gdy osoba cywilnego lub zmieni przydziały. 

Nie można używać atrybutów, które zawierają znak (@), więc nie można używać poczty e-mail i userPrincipalName. W atrybucie uwzględniana jest wielkość liter, dlatego podczas przenoszenia obiektu między lasami Pamiętaj, aby zachować wielkie i małe litery. Atrybuty binarne są kodowane algorytmem Base64, ale inne typy atrybutów pozostają w niezakodowanym stanie. 

W scenariuszach federacyjnych i w niektórych interfejsach usługi Azure AD atrybut sourceAnchor jest również znany jako *immutableID*. 

Aby uzyskać więcej informacji na temat kotwicy źródłowej, zobacz [koncepcje projektowania](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrowanie synchronizacji na podstawie grup
Funkcja filtrowania w grupach pozwala synchronizować tylko niewielki podzbiór obiektów na potrzeby pilotażu. Aby użyć tej funkcji, należy utworzyć w tym celu grupę w lokalnym wystąpieniu Active Directory. Następnie należy dodać użytkowników i grupy, którzy mają zostać zsynchronizowani z usługą Azure AD jako bezpośredni członkowie. Możesz później dodać użytkowników lub usunąć użytkowników z tej grupy, aby zachować listę obiektów, które powinny być obecne w usłudze Azure AD. 

Wszystkie obiekty, które mają być synchronizowane, muszą być bezpośrednimi członkami grupy. Wszyscy użytkownicy, grupy, kontakty i komputery lub urządzenia muszą być bezpośrednimi członkami. Członkostwo w grupach zagnieżdżonych nie jest rozpoznawane. Po dodaniu grupy jako członka jest dodawana tylko sama Grupa. Jego członkowie nie są dodawani.

![Zrzut ekranu przedstawiający stronę, na której można wybrać sposób filtrowania użytkowników i urządzeń.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Ta funkcja jest przeznaczona do obsługi tylko wdrożenia pilotażowego. Nie należy używać go w pełnym wdrożeniu produkcyjnym.
>

W pełnym wdrożeniu produkcyjnym trudno jest zachować pojedynczą grupę i wszystkie jej obiekty do synchronizacji. Zamiast funkcji filtrowania w grupach należy użyć jednej z metod opisanych w temacie [Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Funkcje opcjonalne
Na następnej stronie możesz wybrać opcjonalne funkcje dla danego scenariusza.

>[!WARNING]
>Azure AD Connect wersje 1.0.8641.0 i wcześniejsze korzystają z usługi Azure Access Control Service na potrzeby zapisywania zwrotnego haseł.  Ta usługa została wycofana w dniu 7 listopada 2018.  Jeśli używasz dowolnej z tych wersji Azure AD Connect i włączono funkcję zapisywania zwrotnego haseł, użytkownicy mogą utracić możliwość zmiany lub resetowania haseł, gdy usługa zostanie wycofana. Te wersje Azure AD Connect nie obsługują funkcji zapisywania zwrotnego haseł.
>
>Aby uzyskać więcej informacji, zobacz [Migrowanie z usługi Azure Access Control Service](../azuread-dev/active-directory-acs-migration.md).
>
>Jeśli chcesz użyć funkcji zapisywania zwrotnego haseł, Pobierz [najnowszą wersję Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Zrzut ekranu przedstawiający stronę "funkcje opcjonalne".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Jeśli Azure AD Sync lub synchronizacja bezpośrednia (DirSync) jest aktywna, nie Uaktywniaj żadnych funkcji zapisywania zwrotnego w programie Azure AD Connect.



| Funkcje opcjonalne | Opis |
| --- | --- |
| Wdrożenie hybrydowe programu Exchange |Funkcja wdrażania hybrydowego programu Exchange umożliwia współistnienie skrzynek pocztowych programu Exchange zarówno lokalnie, jak i w Microsoft 365. Azure AD Connect synchronizuje określony zestaw [atrybutów](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) z usługi Azure AD z powrotem w katalogu lokalnym. |
| Foldery publiczne poczty programu Exchange | Funkcja folderów publicznych poczty programu Exchange umożliwia synchronizowanie obiektów folderów publicznych z włączoną obsługą poczty z lokalnego wystąpienia Active Directory w usłudze Azure AD. |
| Filtrowanie atrybutów i aplikacji usługi Azure AD |Przez włączenie filtrowania atrybutów i aplikacji usługi Azure AD można dostosować zestaw synchronizowanych atrybutów. Ta opcja dodaje do kreatora dwie dodatkowe strony konfiguracji. Więcej informacji znajduje się w temacie [Filtrowanie atrybutów i aplikacji usługi Azure AD](#azure-ad-app-and-attribute-filtering). |
| Synchronizacja skrótów haseł |W przypadku wybrania Federacji jako rozwiązania do logowania można włączyć synchronizację skrótów haseł. Następnie można użyć go jako opcji tworzenia kopii zapasowej.  </br></br>W przypadku wybrania uwierzytelniania przekazywanego można włączyć tę opcję, aby zapewnić obsługę starszych klientów i utworzyć kopię zapasową.</br></br> Aby uzyskać więcej informacji, zobacz [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md).|
| Zapisywanie zwrotne haseł |Użyj tej opcji, aby upewnić się, że zmiany hasła pochodzące z usługi Azure AD są zapisywane z powrotem w katalogu lokalnym. Więcej informacji można znaleźć w temacie [Wprowadzenie do zarządzania hasłami](../authentication/tutorial-enable-sspr.md) |
| Zapisywanie zwrotne grup |Jeśli używasz grup Microsoft 365, możesz reprezentować grupy w lokalnym wystąpieniu Active Directory. Ta opcja jest dostępna tylko w przypadku programu Exchange w lokalnym wystąpieniu Active Directory. Aby uzyskać więcej informacji, zobacz [Azure AD Connect zapisywania zwrotnego grup](how-to-connect-group-writeback.md).|
| Zapisywanie zwrotne urządzeń |W przypadku scenariuszy dostępu warunkowego należy użyć tej opcji, aby zapisać obiekty urządzenia w usłudze Azure AD do lokalnego wystąpienia Active Directory. Więcej informacji znajduje się w temacie [Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect](how-to-connect-device-writeback.md). |
| Synchronizacja atrybutów rozszerzeń katalogów |Wybierz tę opcję, aby zsynchronizować określone atrybuty z usługą Azure AD. Więcej informacji znajduje się w temacie [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtrowanie atrybutów i aplikacji usługi Azure AD
Jeśli chcesz ograniczyć atrybuty, które mają być synchronizowane z usługą Azure AD, Zacznij od wybrania usług, których używasz. Jeśli zmienisz wybrane opcje na tej stronie, musisz jawnie wybrać nową usługę przez ponowną pracę Kreatora instalacji.

![Zrzut ekranu przedstawiający opcjonalne funkcje usługi Azure A D Apps.](./media/how-to-connect-install-custom/azureadapps2.png)

Na podstawie usług wybranych w poprzednim kroku na tej stronie są wyświetlane wszystkie synchronizowane atrybuty. Ta lista jest kombinacją wszystkich typów obiektów, które są synchronizowane. Jeśli potrzebujesz pewnych atrybutów, które pozostaną niezsynchronizowane, możesz usunąć zaznaczenie z tych atrybutów.

![Zrzut ekranu przedstawiający opcjonalne funkcje atrybutów platformy Azure A D.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Usuwanie atrybutów może mieć wpływ na funkcjonalność. Aby uzyskać najlepsze rozwiązania i zalecenia, zobacz [atrybuty do synchronizowania](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Synchronizacja atrybutów rozszerzeń katalogów
Można zwiększyć schemat w usłudze Azure AD przy użyciu atrybutów niestandardowych, które zostały dodane przez organizację lub przy użyciu innych atrybutów w Active Directory. Aby użyć tej funkcji, na stronie **funkcje opcjonalne** wybierz pozycję **Synchronizacja atrybutów rozszerzenia katalogu**. Na stronie **rozszerzenia katalogów** można wybrać więcej atrybutów do zsynchronizowania.

>[!NOTE]
>W polu **dostępne atrybuty** jest rozróżniana wielkość liter.

![Zrzut ekranu przedstawiający stronę "rozszerzenia katalogów".](./media/how-to-connect-install-custom/extension2.png)

Więcej informacji znajduje się w temacie [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Włączanie logowania jednokrotnego
Na stronie **Logowanie** jednokrotne Skonfiguruj Logowanie jednokrotne do użycia z synchronizacją haseł lub uwierzytelnianiem przekazującym. Ten krok należy wykonać raz dla każdego lasu, który jest synchronizowany z usługą Azure AD. Konfiguracja obejmuje dwa kroki:

1.  Utwórz wymagane konto komputera w lokalnym wystąpieniu Active Directory.
2.  Konfigurowanie strefy intranetowej na komputerach klienckich w celu obsługi logowania jednokrotnego.

#### <a name="create-the-computer-account-in-active-directory"></a>Tworzenie konta komputera w usłudze Active Directory
Dla każdego lasu dodanego w Azure AD Connect należy podać poświadczenia administratora domeny, aby konto komputera mogło zostać utworzone w każdym lesie. Poświadczenia są używane tylko do tworzenia konta. Nie są one przechowywane ani używane do żadnej innej operacji. Dodaj poświadczenia na stronie **Włącz logowanie jednokrotne** , jak pokazano na poniższej ilustracji.

![Zrzut ekranu przedstawiający stronę "Włącz logowanie jednokrotne". Poświadczenia lasu są dodawane.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Możesz pominąć lasy, w których nie chcesz korzystać z logowania jednokrotnego.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurowanie strefy intranetowej dla maszyn klienckich
Aby upewnić się, że klient automatycznie zaloguje się w strefie intranetowej, upewnij się, że adres URL jest częścią strefy intranetowej. Ten krok zapewnia, że komputer przyłączony do domeny automatycznie wyśle bilet protokołu Kerberos do usługi Azure AD, gdy jest on połączony z siecią firmową.

Na komputerze, na którym są zasady grupy narzędzia do zarządzania:

1.  Otwórz zasady grupy narzędzia do zarządzania.
2.  Edytuj zasady grupy, które będą stosowane do wszystkich użytkowników. Na przykład domyślne zasady domeny.
3.  Przejdź do pozycji **Konfiguracja użytkownika**  >  **Szablony administracyjne**  >  **składniki systemu Windows** internetowe  >  **Internet Explorer**  >  **Web Panel sterowania** Internet  >  . Następnie wybierz pozycję **lokacja do przypisywania stref**.
4.  Włącz zasady. Następnie w oknie dialogowym wprowadź nazwę wartości `https://autologon.microsoftazuread-sso.com` i wartość `1` . Twoja konfiguracja powinna wyglądać jak na poniższej ilustracji.
  
    ![Zrzut ekranu przedstawiający strefy intranetowe.](./media/how-to-connect-install-custom/sitezone.png)

6.  Kliknij **przycisk OK** dwa razy.

## <a name="configuring-federation-with-ad-fs"></a>Konfigurowanie federacji przy użyciu usług AD FS
AD FS można skonfigurować za pomocą Azure AD Connect w zaledwie kilka kliknięć. Przed rozpoczęciem należy:

* System Windows Server 2012 R2 lub nowszy dla serwera federacyjnego. Zdalne zarządzanie powinno być włączone.
* System Windows Server 2012 R2 lub nowszy dla serwera proxy aplikacji sieci Web. Zdalne zarządzanie powinno być włączone.
* Certyfikat TLS/SSL dla nazwy usługi federacyjnej, która ma być używana (na przykład sts.contoso.com).

>[!NOTE]
>Można zaktualizować certyfikat protokołu TLS/SSL dla farmy AD FS przy użyciu Azure AD Connect, nawet jeśli nie jest on używany do zarządzania relacją zaufania federacji.

### <a name="ad-fs-configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji AD FS
Aby skonfigurować farmę AD FS przy użyciu Azure AD Connect, upewnij się, że na serwerach zdalnych jest włączona usługa WinRM. Upewnij się, że zostały wykonane inne zadania w sekcji [wymagania wstępne dotyczące Federacji](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Upewnij się również, że spełnione są wymagania dotyczące portów wymienione w tabeli [Azure AD Connect i serwerów federacyjnych/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) .

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Tworzenie nowej famy usług AD FS lub korzystanie z istniejącej farmy usług AD FS
Możesz użyć istniejącej farmy AD FS lub utworzyć nową. Jeśli zdecydujesz się utworzyć nowe, musisz podać certyfikat TLS/SSL. Jeśli certyfikat TLS/SSL jest chroniony hasłem, zostanie wyświetlony monit o podanie hasła.

![Zrzut ekranu przedstawiający stronę "A D" farmy S](./media/how-to-connect-install-custom/adfs1.png)

Jeśli zdecydujesz się używać istniejącej farmy AD FS, zobaczysz stronę, na której można skonfigurować relację zaufania między AD FS i usługą Azure AD.

>[!NOTE]
>Azure AD Connect można użyć do zarządzania tylko jedną farmą AD FS. Jeśli istnieje relacja zaufania federacji, w której skonfigurowano usługę Azure AD na wybranej farmie AD FS, Azure AD Connect ponownego tworzenia zaufania od podstaw.

### <a name="specify-the-ad-fs-servers"></a>Określanie serwerów usług AD FS
Określ serwery, na których chcesz zainstalować AD FS. Można dodać jeden lub więcej serwerów, w zależności od potrzeb związanych z pojemnością. Przed skonfigurowaniem tej konfiguracji należy dołączyć wszystkie serwery AD FS do Active Directory. Ten krok nie jest wymagany w przypadku serwerów proxy aplikacji sieci Web. 

Firma Microsoft zaleca instalowanie jednego serwera usług AD FS do celów wdrożeń testowych i pilotażowych. Po wstępnej konfiguracji można dodać i wdrożyć więcej serwerów, aby spełnić potrzeby skalowania, ponownie uruchamiając Azure AD Connect.

> [!NOTE]
> Przed skonfigurowaniem tej konfiguracji upewnij się, że wszystkie serwery są przyłączone do domeny usługi Azure AD.
>


![Zrzut ekranu przedstawiający stronę "serwery federacyjne".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Określanie serwerów proxy aplikacji internetowych
Określ serwery proxy aplikacji sieci Web. Serwer proxy aplikacji sieci Web jest wdrażany w sieci obwodowej, w ramach ekstranetu. Obsługuje ona żądania uwierzytelniania z ekstranetu. Można dodać jeden lub więcej serwerów, w zależności od potrzeb związanych z pojemnością. 

Firma Microsoft zaleca instalowanie jednego serwera proxy aplikacji sieci Web na potrzeby wdrożeń testowych i pilotażowych. Po wstępnej konfiguracji można dodać i wdrożyć więcej serwerów, aby spełnić potrzeby skalowania, ponownie uruchamiając Azure AD Connect. Zalecamy, aby do uwierzytelniania z intranetu była równoważna liczba serwerów proxy.

> [!NOTE]
> - Jeśli używane konto nie jest kontem administratora lokalnego na serwerach proxy aplikacji sieci Web, zostanie wyświetlony monit o podanie poświadczeń administratora.
> - Przed określeniem serwerów proxy aplikacji sieci Web upewnij się, że istnieje połączenie HTTP/HTTPS między serwerem Azure AD Connect a serwerem proxy aplikacji sieci Web.
> - Upewnij się, że istnieje połączenie HTTP/HTTPS między serwerem aplikacji sieci Web a serwerem AD FS, aby umożliwić przesyłanie żądań uwierzytelniania do przepływu.
>


![Zrzut ekranu przedstawiający stronę serwery proxy aplikacji sieci Web.](./media/how-to-connect-install-custom/adfs3.png)

Zostanie wyświetlony monit o wprowadzenie poświadczeń, aby serwer aplikacji sieci Web mógł nawiązać bezpieczne połączenie z serwerem AD FS. Te poświadczenia muszą być dla konta administratora lokalnego na serwerze AD FS.

![Zrzut ekranu przedstawiający stronę "poświadczenia". Poświadczenia administratora są wprowadzane w polu Nazwa użytkownika i hasło.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Określanie konta usługi dla usług AD FS
Usługa AD FS wymaga konta usługi domeny do uwierzytelniania użytkowników i wyszukiwania informacji o użytkowniku w Active Directory. Obsługiwane są dwa typy kont usługi:

* **Konto usługi zarządzane przez grupę**: ten typ konta został wprowadzony do AD DS przez system Windows Server 2012. Ten typ konta zapewnia usługi, takie jak AD FS. Jest to pojedyncze konto, w którym nie trzeba regularnie aktualizować hasła. Opcja ta powinna być używana, jeśli kontrolery domeny systemu Windows Server 2012 znajdują się już w domenie, do której należą serwery usług AD FS.
* **Konto użytkownika domeny**: ten typ konta wymaga podania hasła i regularnego aktualizowania go po jego wygaśnięciu. Tej opcji należy używać tylko wtedy, gdy nie masz kontrolerów domeny systemu Windows Server 2012 w domenie, do której należą serwery AD FS.

W przypadku wybrania opcji **Utwórz konto usługi zarządzanej przez grupę** , a ta funkcja nie została nigdy użyta w Active Directory, wprowadź poświadczenia administratora przedsiębiorstwa. Poświadczenia te służą do inicjowania magazynu kluczy i włączania funkcji w usłudze Active Directory.

> [!NOTE]
> Azure AD Connect sprawdza, czy Usługa AD FS została już zarejestrowana jako główna nazwa usługi (SPN) w domenie.  Usługa Azure AD DS nie zezwala na rejestrację zduplikowanych nazw SPN w tym samym czasie.  Jeśli zostanie znaleziona zduplikowana nazwa SPN, nie można kontynuować pracy do momentu usunięcia nazwy SPN.

![Zrzut ekranu przedstawiający stronę "konto usługi D F S".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Wybierz domenę usługi Azure AD, która ma zostać sfederować
Na stronie **domena usługi Azure AD** można skonfigurować relację federacji między AD FS i usługą Azure AD. Tutaj można skonfigurować AD FS w celu zapewnienia tokenów zabezpieczających w usłudze Azure AD. Należy również skonfigurować usługę Azure AD, aby ufać tokenom z tego wystąpienia AD FS. 

Na tej stronie można skonfigurować tylko jedną domenę w początkowej instalacji. Później można skonfigurować więcej domen przez ponowne uruchomienie programu Azure AD Connect.

![Zrzut ekranu przedstawiający stronę "Azure A D".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Weryfikowanie domeny usługi Azure AD wybranej do federacji
Po wybraniu domeny, która ma zostać sfederować, Azure AD Connect zawiera informacje, których można użyć do zweryfikowania niezweryfikowanej domeny. Aby uzyskać więcej informacji, zobacz [Dodawanie i Weryfikowanie domeny](../fundamentals/add-custom-domain.md).

![Zrzut ekranu przedstawiający stronę "Azure A D", w tym informacje, których można użyć do zweryfikowania domeny.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect próbuje zweryfikować domenę na etapie konfiguracji. Jeśli nie dodasz wymaganych rekordów systemu nazw domen (DNS), nie można ukończyć konfigurowania.
>


## <a name="configuring-federation-with-pingfederate"></a>Konfigurowanie federacji z serwerem PingFederate
Serwera pingfederate można skonfigurować za pomocą Azure AD Connect w zaledwie kilka kliknięć. Wymagane są następujące wymagania wstępne:
- Serwera pingfederate 8,4 lub nowszy.  Aby uzyskać więcej informacji, zobacz [serwera pingfederate Integration with Azure Active Directory i Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Certyfikat TLS/SSL dla nazwy usługi federacyjnej, która ma być używana (na przykład sts.contoso.com).

### <a name="verify-the-domain"></a>Weryfikowanie domeny
Po wybraniu opcji konfigurowania Federacji przy użyciu serwera pingfederate zostanie wyświetlony monit o zweryfikowanie domeny, która ma być sfederować.  Wybierz domenę z menu rozwijanego.

![Zrzut ekranu przedstawiający stronę "Azure A D". Wybrano przykładową domenę "contoso.com".](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Eksportowanie ustawień serwera PingFederate


Skonfiguruj serwera pingfederate jako serwer federacyjny dla każdej federacyjnej domeny platformy Azure.  Wybierz pozycję **Eksportuj ustawienia** , aby udostępnić te informacje administratorowi serwera pingfederate.  Administrator serwera federacyjnego aktualizuje konfigurację, a następnie udostępnia adres URL i numer portu serwera serwera pingfederate, tak aby Azure AD Connect mógł zweryfikować ustawienia metadanych.  

![Zrzut ekranu przedstawiający stronę "Ustawienia serwera pingfederate". Przycisk "Eksportuj ustawienia" pojawia się w górnej części strony.](./media/how-to-connect-install-custom/ping2.png)

Aby rozwiązać problemy z walidacją, skontaktuj się z administratorem serwera PingFederate.  Na poniższej ilustracji przedstawiono informacje o serwerze serwera pingfederate, który nie ma prawidłowej relacji zaufania z platformą Azure.

![Zrzut ekranu przedstawiający informacje o serwerze: znaleziono serwer serwera pingfederate, ale nie ma połączenia z dostawcą usług dla platformy Azure lub jest ono wyłączone.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Weryfikowanie łączności federacji
Azure AD Connect próbuje sprawdzić poprawność punktów końcowych uwierzytelniania pobieranych z metadanych serwera pingfederate w poprzednim kroku.  Azure AD Connect pierwszej próbie rozpoznania punktów końcowych przy użyciu lokalnych serwerów DNS.  Następnie próbuje rozpoznać punkty końcowe przy użyciu zewnętrznego dostawcy DNS.  Aby rozwiązać problemy z walidacją, skontaktuj się z administratorem serwera PingFederate.  

![Zrzut ekranu przedstawiający stronę "Weryfikowanie łączności".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Weryfikuj logowanie Federacji
Na koniec możesz sprawdzić nowo skonfigurowany przepływ logowania federacyjnego, logując się do domeny federacyjnej. Jeśli logowanie powiedzie się, pomyślnie skonfigurowano Federacji z serwera pingfederate.

![Zrzut ekranu przedstawiający stronę "Weryfikuj dane logowania federacyjnego". Komunikat u dołu wskazuje na pomyślne zalogowanie się.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Konfigurowanie i weryfikowanie stron
Konfiguracja odbywa się na stronie **Konfigurowanie** .

> [!NOTE]
> Jeśli skonfigurowano Federacji, przed kontynuowaniem instalacji upewnij się, że skonfigurowano również [rozpoznawanie nazw dla serwerów federacyjnych](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) .
>



![Zrzut ekranu przedstawiający stronę "gotowe do skonfigurowania".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Użyj trybu przejściowego
Istnieje możliwość skonfigurowania nowego serwera synchronizacji równolegle z trybem przejściowym. Jeśli chcesz użyć tej instalacji, tylko jeden serwer synchronizacji może eksportować do jednego katalogu w chmurze. Jeśli jednak chcesz przenieść z innego serwera, na przykład na serwerze z uruchomioną narzędziem DirSync, możesz włączyć Azure AD Connect w trybie przejściowym. 

Po włączeniu konfiguracji przemieszczania aparat synchronizacji importuje i synchronizuje dane jako normalne. Ale nie eksportuje żadnych danych do usługi Azure AD lub Active Directory. W trybie przejściowym funkcja synchronizacji haseł i funkcji zapisywania zwrotnego haseł jest wyłączona.

![Zrzut ekranu przedstawiający opcję Włącz tryb przejściowy.](./media/how-to-connect-install-custom/stagingmode.png)

W trybie przejściowym można wprowadzić wymagane zmiany w aparacie synchronizacji i przejrzeć elementy, które zostaną wyeksportowane. Jeśli konfiguracja wygląda dobrze, uruchom ponownie kreatora instalacji i wyłącz tryb przejściowy. 

Dane są teraz eksportowane do usługi Azure AD z serwera. Pamiętaj, aby w tym samym czasie wyłączyć drugi serwer, aby aktywnie eksportował tylko jeden serwer.

Więcej informacji znajduje się w temacie [Tryb przejściowy](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Weryfikowanie konfiguracji federacji
Azure AD Connect sprawdza ustawienia DNS po wybraniu przycisku **Weryfikuj** . Sprawdza następujące ustawienia:

* **Łączność intranetowa**
    * Rozpoznawanie nazwy FQDN Federacji: Azure AD Connect sprawdza, czy serwer DNS może rozpoznać nazwę FQDN Federacji w celu zapewnienia łączności. Jeśli Azure AD Connect nie może rozpoznać nazwy FQDN, weryfikacja nie powiedzie się. Aby ukończyć weryfikację, upewnij się, że rekord DNS jest obecny dla nazwy FQDN usługi federacyjnej.
    * Rekord A systemu DNS: Azure AD Connect sprawdza, czy usługa federacyjna ma rekord A. W przypadku braku rekordu A weryfikacja nie powiedzie się. Aby ukończyć weryfikację, Utwórz rekord A (nie rekord CNAME) dla federacyjnej nazwy FQDN.
* **Łączność Ekstranetowa**
    * Rozpoznawanie nazwy FQDN Federacji: Azure AD Connect sprawdza, czy serwer DNS może rozpoznać nazwę FQDN Federacji w celu zapewnienia łączności.

      ![Zrzut ekranu przedstawiający stronę "Instalacja ukończona".](./media/how-to-connect-install-custom/completed.png)

      ![Zrzut ekranu przedstawiający stronę "Instalacja ukończona". Zostanie wyświetlony komunikat z informacją, że konfiguracja intranetu została zweryfikowana.](./media/how-to-connect-install-custom/adfs7.png)

Aby zweryfikować kompleksowe uwierzytelnianie, ręcznie wykonaj co najmniej jeden z następujących testów:

* Po zakończeniu synchronizacji w Azure AD Connect Użyj zadania **Weryfikuj zalogowanie federacyjne** jako dodatkowe, aby zweryfikować uwierzytelnianie dla wybranego konta użytkownika lokalnego.
* Na komputerze przyłączonym do domeny w intranecie upewnij się, że możesz zalogować się z przeglądarki. Połącz z https://myapps.microsoft.com . Następnie zaloguj się przy użyciu swojego konta zalogowanego. Wbudowane konto administratora AD DS platformy Azure nie jest zsynchronizowane i nie można go użyć do weryfikacji.
* Upewnij się, że możesz zalogować się z urządzenia w ekstranecie. Na komputerze, na którym znajduje się Strona główna lub urządzenie przenośne, Połącz się z usługą https://myapps.microsoft.com . Wprowadź swoje poświadczenia.
* Sprawdź poprawność logowania wzbogaconego klienta. Połącz z https://testconnectivity.microsoft.com . Następnie wybierz opcję **pakiet Office 365**  >  **Office 365 Single Sign-On test**.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Ta sekcja zawiera informacje dotyczące rozwiązywania problemów, których można użyć w przypadku wystąpienia problemu podczas instalowania Azure AD Connect.

Po dostosowaniu instalacji Azure AD Connect, na stronie **Zainstaluj wymagane składniki** można wybrać opcję **Użyj istniejącej SQL Server**. Może zostać wyświetlony następujący błąd: "baza danych ADSync już zawiera dane i nie można jej zastąpić. Usuń istniejącą bazę danych i spróbuj ponownie. "

![Zrzut ekranu przedstawiający stronę "Instalowanie składników wymaganych". W dolnej części strony pojawi się błąd.](./media/how-to-connect-install-custom/error1.png)

Ten błąd jest wyświetlany, ponieważ baza danych o nazwie *ADSync* już istnieje w wystąpieniu SQL o SQL Server określonym przez użytkownika.

Ten błąd jest zazwyczaj wyświetlany po odinstalowaniu Azure AD Connect.  Baza danych nie jest usuwana z komputera, na którym działa SQL Server podczas odinstalowywania Azure AD Connect.

Aby rozwiązać ten problem:

1. Sprawdź bazę danych ADSync, która Azure AD Connect używana przed odinstalowaniem. Upewnij się, że baza danych nie jest już używana.

2. Wykonaj kopię zapasową bazy danych programu.

3. Usuń bazę danych:
    1. Użyj **Management Studio Microsoft SQL Server** , aby nawiązać połączenie z wystąpieniem programu SQL Server. 
    1. Znajdź bazę danych **ADSync** i kliknij ją prawym przyciskiem myszy.
    1. W menu kontekstowym wybierz pozycję **Usuń**.
    1. Wybierz **przycisk OK** , aby usunąć bazę danych.

![Zrzut ekranu przedstawiający Management Studio Microsoft SQL Server. Wybrano synchronizację D.](./media/how-to-connect-install-custom/error2.png)

Po usunięciu bazy danych ADSync wybierz pozycję **Zainstaluj** , aby ponowić próbę instalacji.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu instalacji wyloguj się z systemu Windows. Następnie zaloguj się ponownie przed użyciem Synchronization Service Manager lub edytora reguł synchronizacji.

Teraz, gdy zainstalowano Azure AD Connect, można [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).

Aby uzyskać więcej informacji na temat funkcji, które można włączyć podczas instalacji, zobacz [zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) i [Azure AD Connect Health](how-to-connect-health-sync.md).

Aby uzyskać więcej informacji na temat innych typowych tematów, zobacz [Azure AD Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md) i [integrowanie tożsamości lokalnych z usługą Azure AD](whatis-hybrid-identity.md).
