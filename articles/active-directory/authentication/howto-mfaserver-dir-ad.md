---
title: Serwer usługi Azure MFA i Active Directory — Azure Active Directory
description: Opis sposobu integracji serwera Azure Multi-Factor Authentication z usługą Active Directory, w wyniku której możliwa jest synchronizacja katalogów.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b3778ea68edf1fbbb41efb899749e6f35b39bae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742293"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integracja katalogu między serwerem Azure MFA i usługą Active Directory

Sekcja Integracja katalogu serwera usługi Azure MFA umożliwia przeprowadzenie integracji z usługą Active Directory lub innym katalogiem LDAP. Pozwala ona skonfigurować atrybuty w celu ich dostosowania do schematu katalogu oraz ustawić automatyczną synchronizację użytkowników.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać Multi-Factor Authentication usługi Azure AD opartych na chmurze.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="settings"></a>Ustawienia

Domyślnie serwer usługi Azure Multi-Factor Authentication (MFA) jest skonfigurowany pod kątem importowania lub synchronizowania użytkowników z usługi Active Directory.  Karta Integracja katalogu pozwala zastąpić domyślne zachowanie i utworzyć powiązanie z innym katalogiem LDAP, katalogiem ADAM lub określonym kontrolerem domeny usługi Active Directory.  Umożliwia także korzystanie z uwierzytelniania LDAP w odniesieniu do serwera proxy protokołu LDAP lub do powiązania protokołu LDAP jako obiektu docelowego usługi RADIUS, uwierzytelniania wstępnego w ramach uwierzytelniania IIS lub uwierzytelniania podstawowego dla portalu użytkownika.  W poniższej tabeli opisano poszczególne ustawienia.

![Edytowanie konfiguracji LDAP na serwerze usługi MFA](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Integracja katalogów nie gwarantuje pracy z katalogami innymi niż Active Directory Domain Services.

| Cecha | Opis |
| --- | --- |
| Użyj usługi Active Directory |Wybierz opcję Użyj usługi Active Directory, aby użyć tej usługi do importowania i synchronizacji.  Jest to ustawienie domyślne. <br>Uwaga: aby integracja z usługą Active Directory działała prawidłowo, przyłącz komputer do domeny i zaloguj się przy użyciu konta domeny. |
| Uwzględnij domeny zaufane |Zaznacz pole wyboru **Uwzględnij domeny zaufane**, jeśli chcesz, aby agent podejmował próbę nawiązania połączenia z domenami ustawionymi jako zaufane dla bieżącej domeny lub innej domeny w lesie albo domenami zaufanymi lasu.  Jeśli nie zachodzi importowanie ani synchronizowanie użytkowników z żadnej z zaufanych domen, usuń zaznaczenie pola wyboru, aby poprawić wydajność.  Pole jest domyślnie zaznaczone. |
| Użyj określonej konfiguracji LDAP |Wybierz opcję Użyj LDAP, aby użyć ustawień LDAP określonych dla importowania i synchronizacji. Uwaga: po wybraniu opcji Użyj LDAP interfejs użytkownika odwołuje się do katalogu LDAP, a nie do usługi Active Directory. |
| Przycisk Edytuj |Przycisk Edytuj pozwala modyfikować bieżące ustawienia konfiguracji LDAP. |
| Użyj zapytań dotyczących zakresu atrybutów |Określa, czy należy użyć zapytań dotyczących zakresu atrybutów.  Zapytania dotyczące zakresu atrybutów umożliwiają wydajne wyszukiwanie w katalogu obejmujące kwalifikację rekordów na podstawie wpisów w atrybucie innego rekordu.  Serwer usługi Azure Multi-Factor Authentication wykorzystuje zapytania dotyczące zakresu atrybutów do wydajnego przesyłania zapytań dotyczących użytkowników, którzy są członkami grupy zabezpieczeń.   <br>Uwaga: w określonych przypadkach zapytania dotyczące zakresu atrybutów są obsługiwane, nie powinny jednak być używane.  Na przykład w usłudze Active Directory mogą wystąpić problemy z zapytaniami dotyczącymi zakresu atrybutów, jeśli grupa zabezpieczeń zawiera członków z więcej niż jednej domeny. W takim przypadku usuń zaznaczenie pola wyboru. |

W poniższej tabeli opisano ustawienia konfiguracji LDAP.

| Cecha | Opis |
| --- | --- |
| Serwer |Wprowadź nazwę hosta lub adres IP serwera z uruchomionym katalogiem LDAP.  Serwer zapasowy można także określić osobno, oddzielając go średnikiem. <br>Uwaga: Jeśli typ powiązania to SSL (TLS), wymagana jest w pełni kwalifikowana nazwa hosta. |
| Podstawowa nazwa wyróżniająca |Wprowadź nazwę wyróżniającą obiektu podstawowego katalogu, z którego będą uruchamiane wszystkie zapytania katalogu.  Na przykład: dc=abc,dc=com. |
| Typ powiązania — zapytania |Wybierz odpowiedni typ powiązania do użycia podczas tworzenia powiązań w celu przeprowadzania wyszukiwań w katalogu LDAP.  Ten typ jest wykorzystywany do importowania, synchronizacji oraz rozpoznania nazw użytkowników. <br><br>  Anonimowe — jest tworzone anonimowe powiązanie.  Powiązana nazwa wyróżniająca oraz powiązane hasło nie są używane.  Użycie tej metody jest możliwe tylko wtedy, gdy katalog LDAP umożliwia tworzenie anonimowych powiązań i jeśli uprawnienia zezwalają na przesyłanie zapytań dotyczących odpowiednich rekordów i atrybutów.  <br><br> Proste — powiązana nazwa wyróżniająca oraz powiązane hasło są przekazywane jako zwykły tekst w celu utworzenia powiązania z katalogiem LDAP.  Ta opcja jest przeznaczona do celów testowych. Umożliwia sprawdzenie, czy można połączyć się z serwerem i czy konto powiązania może uzyskać odpowiedni dostęp. Po zainstalowaniu odpowiedniego certyfikatu, należy używać typu SSL.  <br><br> SSL — powiązana nazwa wyróżniająca oraz powiązane hasło są szyfrowane z użyciem protokołu SSL w celu utworzenia powiązania z katalogiem LDAP.  Zainstaluj lokalny certyfikat, któremu ufa katalog LDAP.  <br><br> Windows — powiązana nazwa użytkownika oraz powiązane hasło są używane do bezpiecznego połączenia z kontrolerem domeny usługi Active Directory lub katalogiem ADAM.  Jeśli pole Powiązana nazwa użytkownika pozostanie puste, do powiązania zostanie użyte konto, do którego zalogował się użytkownik. |
| Typ powiązania — uwierzytelnienia |Wybierz typ powiązania odpowiedni do użycia podczas przeprowadzania uwierzytelniania powiązania LDAP.  Zobacz opisy typów powiązań w obszarze Typ powiązania — zapytania.  Dzięki temu możliwe jest na przykład użycie anonimowego powiązania w odniesieniu do zapytań i korzystanie z powiązania SSL w celu zabezpieczenia uwierzytelnień powiązania LDAP. |
| Powiązana nazwa wyróżniająca lub powiązana nazwa użytkownika |Wprowadź nazwę wyróżniającą rekordu użytkownika dla konta, które ma zostać użyte podczas tworzenia powiązania z katalogiem LDAP.<br><br>Powiązana nazwa wyróżniająca jest używana tylko w przypadku wybrania typu powiązania Proste lub SSL.  <br><br>Wprowadź nazwę użytkownika konta systemu Windows, które zostanie użyte podczas tworzenia powiązania z katalogiem LDAP, jeżeli wybrany typ powiązania to Windows.  Jeśli pole pozostanie puste, do powiązania zostanie użyte konto, do którego zalogował się użytkownik. |
| Powiązane hasło |Wprowadź powiązane hasło dla powiązanej nazwy wyróżniającej lub nazwy użytkownika używanej w celu utworzenia powiązania z katalogiem LDAP.  Aby skonfigurować hasło usługi ADSync serwera usługi Multi-Factor Authentication, włącz synchronizację i upewnij się, że usługa jest uruchomiona na komputerze lokalnym.  Hasło jest zapisywane w obszarze Nazwy użytkowników i hasła przechowywane w systemie Windows na koncie, w ramach którego jest uruchomiona usługa ADSync serwera usługi Multi-Factor Authentication.  Hasło jest także zapisywane na koncie, w ramach którego jest uruchomiony interfejs użytkownika serwera usługi Multi-Factor Authentication i na koncie, w ramach którego jest uruchomiony serwer usługi Multi-Factor Authentication.  <br><br>Jako że hasło jest przechowywane wyłącznie w obszarze Nazwy użytkowników i hasła przechowywane w systemie Windows na lokalnym serwerze, powtórz ten krok na każdym serwerze usługi Multi-Factor Authentication wymagającym dostępu do hasła. |
| Limit rozmiaru zapytania |Określ limit rozmiaru dla maksymalnej liczby użytkowników zwracanych przez wyszukiwanie w katalogu.  Ten limit powinien odpowiadać konfiguracji w katalogu LDAP.  W ramach dużych wyszukiwań, w przypadku których stronicowanie nie jest obsługiwane, podczas operacji importowania i synchronizacji są podejmowane próby pobierania użytkowników w partiach.  Jeśli limit rozmiaru określony w tym obszarze jest większy niż limit skonfigurowany w katalogu LDAP, niektórzy użytkownicy mogą zostać pominięci. |
| Przycisk Testuj |Kliknij przycisk **Testuj**, aby przetestować powiązanie z serwerem LDAP.  <br><br>Nie musisz wybierać opcji **Użyj LDAP**, aby przetestować powiązanie. Dzięki temu możliwe jest sprawdzenie powiązania przed zastosowaniem konfiguracji LDAP. |

## <a name="filters"></a>Filtry

Filtry umożliwiają ustawienie kryteriów kwalifikowania rekordów podczas przeprowadzania wyszukiwania w katalogu.  Ustawiając filtr, można określić zakres obiektów, które mają podlegać synchronizacji.  

![Konfigurowanie filtrowania katalogów na serwerze usługi MFA](./media/howto-mfaserver-dir-ad/dirint2.png)

Usługa Azure Multi-Factor Authentication zapewnia dostęp do następujących trzech opcji filtrowania:

* **Filtr kontenera** — pozwala określić kryteria filtru wykorzystywane do kwalifikowania rekordów kontenera podczas przeprowadzania wyszukiwania w katalogu.  W przypadku katalogów Active Directory i ADAM często używane są kryteria filtrowania (|(objectClass=organizationalUnit)(objectClass=container)).  W przypadku innych katalogów LDAP w zależności od schematu katalogu użyj kryteriów filtrowania służących do kwalifikowania poszczególnych typów obiektu kontenera.  <br>Uwaga: jeśli pole pozostanie puste, domyślnie używane są kryteria filtrowania ((objectClass=organizationalUnit)(objectClass=container)).
* **Filtr grupy zabezpieczeń** — pozwala określić kryteria filtru wykorzystywane do kwalifikowania rekordów grupy zabezpieczeń podczas przeprowadzania wyszukiwania w katalogu.  W przypadku katalogów Active Directory i ADAM często używane są kryteria filtrowania (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  W przypadku innych katalogów LDAP w zależności od schematu katalogu użyj kryteriów filtrowania służących do kwalifikowania poszczególnych typów obiektu grupy zabezpieczeń.  <br>Uwaga: jeśli pole pozostanie puste, domyślnie używane są kryteria filtrowania (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).
* **Filtr użytkownika** — pozwala określić kryteria filtru wykorzystywane do kwalifikowania rekordów użytkowników podczas przeprowadzania wyszukiwania w katalogu.  W przypadku katalogów Active Directory i ADAM często używane są kryteria filtrowania (&(objectClass=user)(objectCategory=person)).  W przypadku innych katalogów LDAP w zależności od schematu katalogu użyj kryteriów (objectClass=inetOrgPerson) lub podobnych. <br>Uwaga: jeśli pole pozostanie puste, domyślnie używane są kryteria filtrowania (&(objectCategory=person)(objectClass=user)).

## <a name="attributes"></a>Atrybuty

Atrybuty można w razie potrzeby dostosować pod kątem określonego katalogu.  Pozwala to na dodawanie atrybutów niestandardowych oraz dostosowanie synchronizacji wyłącznie pod kątem określonych atrybutów. Użyj nazwy atrybutu zgodnie z definicją w schemacie katalogu dla wartości każdego pola atrybutu. Poniższa tabela zawiera dodatkowe informacje na temat każdej funkcji.

Atrybuty mogą być wprowadzane ręcznie i nie muszą odpowiadać atrybutom na liście.

![Dostosowywanie atrybutów integracji katalogów na serwerze MFA](./media/howto-mfaserver-dir-ad/dirint3.png)

| Cecha | Opis |
| --- | --- |
| Unikatowy identyfikator |Wprowadź nazwę atrybutu, która pełni rolę unikatowego identyfikatora rekordów kontenera, grupy zabezpieczeń i użytkowników.  W katalogu Active Directory jest to zazwyczaj wartość elementu objectGUID. Inne implementacje katalogu LDAP mogą używać wartości entryUUID lub podobnej.  Wartość domyślna to objectGUID. |
| Typ unikatowego identyfikatora |Wybierz typ atrybutu unikatowego identyfikatora.  W katalogu Active Directory atrybut objectGUID ma typ GUID. Inne implementacje katalogu LDAP mogą używać typu tablicy bajtowej znaków ASCII lub ciągu.  Wartość domyślna to GUID. <br><br>Poprawne ustawienie tego typu jest istotne, ponieważ elementy synchronizacji są przywoływane przy użyciu ich unikatowych identyfikatorów. Typ unikatowego identyfikatora jest używany do bezpośredniego odnajdywania obiektu w katalogu.  Ustawienie typu ciągu w przypadku, gdy wartość jest przechowywana w katalogu jako tablica bajtowa znaków ASCII, uniemożliwia prawidłową synchronizację. |
| Nazwa wyróżniająca |Wprowadź nazwę atrybutu, który zawiera nazwę wyróżniającą każdego z rekordów.  W katalogu Active Directory jest to zazwyczaj atrybut distinguishedName. Inne implementacje katalogu LDAP mogą używać atrybutu entryDN lub podobnego.  Wartość domyślna to distinguishedName. <br><br>Jeśli nie istnieje atrybut zawierający tylko nazwę wyróżniającą, można użyć atrybutu ścieżki AD.  Część "LDAP:// \<server\> /" ścieżki jest automatycznie usuwana, pozostawiając tylko nazwę wyróżniającą obiektu. |
| Nazwa kontenera |Wprowadź nazwę atrybutu, który zawiera nazwę w rekordzie kontenera.  Wartość tego atrybutu jest wyświetlana w hierarchii kontenera podczas importowania pozycji z katalogu Active Directory lub dodawania elementów do synchronizacji.  Wartość domyślna to name. <br><br>Jeśli różne kontenery używają różnych atrybutów dla swoich nazw, rozdziel poszczególne atrybuty nazw kontenera średnikami.  Pierwszy atrybut nazwy kontenera znaleziony w obiekcie kontenera pełni rolę jego nazwy wyświetlanej. |
| Nazwa grupy zabezpieczeń |Wprowadź nazwę atrybutu, który zawiera nazwę w rekordzie grupy zabezpieczeń.  Wartość tego atrybutu jest wyświetlana na liście grupy zabezpieczeń podczas importowania pozycji z katalogu Active Directory lub dodawania elementów do synchronizacji.  Wartość domyślna to name. |
| Nazwa użytkownika |Wprowadź nazwę atrybutu, który zawiera nazwę użytkownika w rekordzie użytkownika.  Wartość tego atrybutu jest używana jako nazwa użytkownika serwera usługi Multi-Factor Authentication.  Drugi atrybut może zostać określony jako atrybut zapasowy względem pierwszego.  Drugi atrybut jest używany tylko wtedy, gdy pierwszy atrybut nie zawiera wartości użytkownika.  Wartości domyślne to userPrincipalName i sAMAccountName. |
| Imię |Wprowadź nazwę atrybutu, który zawiera imię w rekordzie użytkownika.  Wartość domyślna to givenName. |
| Nazwisko |Wprowadź nazwę atrybutu, który zawiera nazwisko w rekordzie użytkownika.  Wartość domyślna to sn. |
| Adres e-mail |Wprowadź nazwę atrybutu, który zawiera adres e-mail w rekordzie użytkownika.  Adres e-mail jest używany do przesłania użytkownikowi powitalnej wiadomości e-mail oraz w celu przesyłania wiadomości dotyczących aktualizacji.  Wartość domyślna to mail. |
| Grupa użytkowników |Wprowadź nazwę atrybutu, który zawiera grupę użytkowników w rekordzie użytkownika.  Grupa użytkowników może służyć do filtrowania użytkowników w agencie i w raportach w portalu zarządzania serwerem usługi Multi-Factor Auth. |
| Opis |Wprowadź nazwę atrybutu, który zawiera opis w rekordzie użytkownika.  Opis jest wykorzystywany wyłącznie do celów wyszukiwania.  Wartością domyślną jest description. |
| Język połączenia telefonicznego |Wprowadź nazwę atrybutu, który zawiera skróconą nazwę języka używanego na potrzeby połączeń głosowych użytkownika. |
| Język wiadomości SMS |Wprowadź nazwę atrybutu, który zawiera skróconą nazwę języka używanego na potrzeby wiadomości SMS użytkownika. |
| Język aplikacji mobilnej |Wprowadź nazwę atrybutu, który zawiera skróconą nazwę języka używanego na potrzeby wiadomości tekstowych aplikacji telefonu użytkownika. |
| Język tokenu OATH |Wprowadź nazwę atrybutu, który zawiera skróconą nazwę języka używanego na potrzeby wiadomości tekstowych tokenu OATH użytkownika. |
| Telefon służbowy |Wprowadź nazwę atrybutu, który zawiera firmowy numer telefonu w rekordzie użytkownika.  Wartość domyślna to telephoneNumber. |
| Telefon domowy |Wprowadź nazwę atrybutu, który zawiera domowy numer telefonu w rekordzie użytkownika.  Wartość domyślna to homePhone. |
| Pager |Wprowadź nazwę atrybutu, który zawiera numer pagera w rekordzie użytkownika.  Wartość domyślna to pager. |
| Telefon komórkowy |Wprowadź nazwę atrybutu, który zawiera numer telefonu komórkowego w rekordzie użytkownika.  Wartość domyślna to mobile. |
| Faks |Wprowadź nazwę atrybutu, który zawiera numer faksu w rekordzie użytkownika.  Wartość domyślna to facsimileTelephoneNumber. |
| Telefon VoIP |Wprowadź nazwę atrybutu, który zawiera numer telefonu VoIP w rekordzie użytkownika.  Wartość domyślna to ipPhone. |
| Niestandardowy |Wprowadź nazwę atrybutu, który zawiera niestandardowy numer telefonu w rekordzie użytkownika.  Pole jest domyślnie puste. |
| Wewnętrzny |Wprowadź nazwę atrybutu, który zawiera numer wewnętrzny w rekordzie użytkownika.  Wartość pola numeru wewnętrznego jest używana wyłącznie dla podstawowego numeru telefonu.  Pole jest domyślnie puste. <br><br>Jeśli nie określono atrybutu Wewnętrzny, numer wewnętrzny można uwzględnić w atrybucie numeru telefonu. W takim przypadku poprzedź rozszerzenie znakiem „x”, aby zostało poprawnie przeanalizowane.  Na przykład wartość 555-123-4567 x890 zostanie zinterpretowana jako numer telefonu 555-123-4567 i numer wewnętrzny 890. |
| Przycisk Przywróć domyślne |Kliknij przycisk **Przywróć domyślne**, aby przywrócić wartości domyślne wszystkich atrybutów.  Ustawienia domyślne powinny działać prawidłowo z normalnym schematem usługi Active Directory lub ADAM. |

Aby edytować atrybuty, kliknij przycisk **Edytuj** na karcie Atrybuty.  Spowoduje to wyświetlenie okna, w którym można edytować atrybuty. Wybierz przycisk **...** obok dowolnego atrybutu, aby otworzyć okno, w którym można określić atrybuty do wyświetlenia.

![Edytowanie mapowania atrybutów katalogu na serwerze usługi MFA](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Synchronizacja

Proces synchronizacji gwarantuje synchronizację bazy danych użytkowników usługi Azure MFA z użytkownikami w katalogu Active Directory lub innym katalogu Lightweight Directory Access Protocol (LDAP). Proces przebiega podobnie do ręcznego importowania użytkowników z katalogu Active Directory, obejmuje jednak także okresowe sondowanie pod kątem użytkowników usługi Active Directory i zmiany w grupie zabezpieczeń, które wymagają przetworzenia.  Ponadto wyłącza lub usuwa on użytkowników, którzy zostali usunięci z kontenera, grupy zabezpieczeń lub katalogu Active Directory.

Multi-Factor Auth ADSync to usługa systemu Windows, która przeprowadza okresowe sondowanie katalogu Active Directory.  Nie należy jej mylić z usługą Azure AD Sync ani Azure AD Connect.  Usługa Multi-Factor Auth ADSync, choć opiera się na podobnym kodzie podstawowym, jest powiązana w sposób wyłączny z serwerem usługi Azure Multi-Factor Authentication.  Usługa jest instalowana w stanie zatrzymania i zostaje uruchomiona przez usługę Multi-Factor Auth Server w momencie określonym podczas konfiguracji.  W przypadku konfiguracji usługi Multi-Factor Auth Server obejmującej wiele serwerów funkcja Multi-Factor Auth ADSync może zostać uruchomiona tylko na jednym serwerze.

Usługa Multi-Factor Auth ADSync wykorzystuje rozszerzenie serwera DirSync LDAP dostarczone przez firmę Microsoft w celu wydajnego sondowania pod kątem zmian.  Ten kontrolny obiekt wywołujący narzędzia DirSync musi mieć uprawnienie „directory get changes” i rozszerzone uprawnienie kontroli dostępu DS-Replication-Get-Changes.  Domyślnie te uprawnienia są przypisane do kont Administrator i LocalSystem na kontrolerach domeny.  Usługa Multi-Factor Auth AdSync jest domyślnie skonfigurowana pod kątem uruchomienia w powiązaniu z kontem LocalSystem.  Dlatego też najprościej jest uruchomić usługę na kontrolerze domeny.  Jeśli usługa zostanie skonfigurowana pod kątem każdorazowej pełnej synchronizacji, może działać jako konto z bardziej ograniczonymi uprawnieniami.  Jest to mniej wydajne rozwiązanie, ale wymaga mniejszych uprawnień konta.

Jeśli katalog LDAP obsługuje narzędzie DirSync i jest skonfigurowany do jego używania, sondowanie w poszukiwaniu zmian w zakresie użytkowników i grup zabezpieczeń będzie działać tak samo, jak w przypadku katalogu Active Directory.  Jeśli katalog LDAP nie obsługuje kontrolki DirSync, podczas każdego cyklu ma miejsce pełna synchronizacja.

![Synchronizacja obiektów katalogu z serwerem MFA](./media/howto-mfaserver-dir-ad/dirint5.png)

Poniższa tabela zawiera dodatkowe informacje na temat każdego ustawienia na karcie Synchronizacja.

| Cecha | Opis |
| --- | --- |
| Włącz synchronizację z usługą Active Directory |W przypadku zaznaczenia tej opcji usługa serwera Multi-Factor Authentication okresowo sonduje pod kątem zmian w katalogu Active Directory. <br><br>Uwaga: zanim usługa Multi-Factor Auth Server zacznie przetwarzać zmiany, należy dodać co najmniej jeden element synchronizacji i wybrać opcję Synchronizuj teraz. |
| Synchronizuj co |Określ interwał czasu oczekiwania usługi Multi-Factor Auth Server między sondowaniem pod kątem zmian oraz przetwarzaniem zmian. <br><br> Uwaga: wskazany interwał to czas, jaki mija przed rozpoczęciem każdego kolejnego cyklu.  Jeśli czas przetwarzania zmian przekracza interwał, nastąpi natychmiastowe rozpoczęcie kolejnego sondowania. |
| Usuń użytkowników, którzy nie są już zarejestrowani w usłudze Active Directory |W przypadku zaznaczenia tego pola usługa Multi-Factor Auth Server przetworzy relikty usuniętego użytkownika usługi Active Directory i spowoduje usunięcie powiązanego użytkownika Multi-Factor Auth Server. |
| Zawsze wykonuj pełną synchronizację |W przypadku zaznaczenia tego pola usługa Multi-Factor Auth Server będzie zawsze wykonywać pełną synchronizację.  Gdy pole opcji nie jest zaznaczone, usługa Multi-Factor Auth Server będzie wykonywać synchronizację przyrostową, sondując tylko użytkowników, w przypadku których nastąpiły zmiany.  Domyślnie pole nie jest zaznaczone. <br><br>Jeśli pole nie jest zaznaczone, serwer usługi Azure MFA wykonuje synchronizację przyrostową tylko wtedy, gdy katalog obsługuje kontrolkę DirSync, a konto tworzące powiązanie z katalogiem ma uprawnienia do przeprowadzania zapytań przyrostowych DirSync.  Jeśli konto nie ma odpowiednich uprawnień lub synchronizacja obejmuje wiele domen, serwer usługi Azure MFA wykonuje pełną synchronizację. |
| Wyłączenie lub usunięcie więcej niż X użytkowników powinno być zatwierdzane przez administratora. |Elementy synchronizacji można skonfigurować pod kątem wyłączania lub usuwania użytkowników, którzy nie są już członkami kontenera lub grupy zabezpieczeń elementu.  Ze względów bezpieczeństwa w sytuacji, gdy liczba użytkowników do wyłączenia lub usunięcia przekracza próg, może być wymagane zatwierdzenie operacji przez administratora.  W przypadku zaznaczenia pola zatwierdzenie jest wymagane dla określonego progu.  Wartość domyślna to 5, natomiast zakres wynosi od 1 do 999. <br><br> Zatwierdzenie jest ułatwione dzięki wysyłce wiadomości e-mail z powiadomieniem do administratorów. Powiadomienie e-mail zawiera instrukcje dotyczące przeglądania i zatwierdzania operacji wyłączania i usuwania użytkowników.  Po uruchomieniu interfejsu użytkownika usługi Multi-Factor Auth Server jest wyświetlany monit o zatwierdzenie. |

Przycisk **Synchronizuj teraz** umożliwia uruchamianie pełnej synchronizacji określonych elementów.  Pełna synchronizacja jest wymagana za każdym razem, gdy elementy synchronizacji są dodawane, modyfikowane, usuwane lub gdy zostaje zmieniona ich kolejność.  Jej przeprowadzenie jest również wymagane przed uruchomieniem usługi Multi-Factor Authentication ADSync, ponieważ ustawia ona punkt początkowy, od którego usługa będzie sondowała pod kątem zmian przyrostowych.  Jeśli wprowadzono zmiany w elementach synchronizacji, ale nie wykonano pełnej synchronizacji, zostanie wyświetlony monit o wybranie pozycji Synchronizuj teraz.

Przycisk **Usuń** pozwala administratorowi usunąć jeden lub więcej elementów z listy elementów synchronizacji usługi Multi-Factor Auth Server.

> [!WARNING]
> Usuniętego elementu synchronizacji nie można odzyskać. Jeśli rekord elementu synchronizacji został usunięty przez pomyłkę, należy dodać go ponownie.

Elementy synchronizacji zostały usunięte z usługi Multi-Factor Auth Server.  Usługa Multi-Factor Auth Server nie będzie już przetwarzać elementów synchronizacji.

Przyciski Przenieś w górę i Przenieś w dół umożliwiają administratorowi zmianę kolejności elementów synchronizacji.  Kolejność jest ważna, ponieważ ten sam użytkownik może być członkiem więcej niż jednego elementu synchronizacji (np. kontenera i grupy zabezpieczeń).  Ustawienia mające zastosowanie do użytkownika podczas synchronizacji będą pochodziły z pierwszego elementu z listy, z którym użytkownik jest skojarzony.  Elementy synchronizacji powinny więc być uporządkowane w kolejności priorytetu.

> [!TIP]
> Po usunięciu elementów synchronizacji należy przeprowadzić pełną synchronizację.  Po określeniu kolejności elementów synchronizacji należy przeprowadzić pełną synchronizację.  Kliknij przycisk **Synchronizuj teraz**, aby przeprowadzić pełną synchronizację.

## <a name="multi-factor-authentication-servers"></a>Serwery Multi-Factor Authentication

Dodatkowe serwery Multi-Factor Authentication można skonfigurować w taki sposób, aby służyć jako zapasowy serwer proxy usługi RADIUS, serwer proxy LDAP lub uwierzytelnianie usług IIS. Konfiguracja synchronizacji jest współdzielona przez wszystkich agentów. Jednak tylko jeden z tych agentów może mieć uruchomioną usługę Multi-Factor Authentication Server. Na tej karcie można wybrać serwer Multi-Factor Authentication, który ma być włączony do synchronizacji.

![Powiązane serwery Multi-Factor Authentication](./media/howto-mfaserver-dir-ad/dirint6.png)
