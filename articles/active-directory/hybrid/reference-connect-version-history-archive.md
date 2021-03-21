---
title: 'Azure AD Connect: archiwalny historia wersji | Microsoft Docs'
description: W tym artykule wymieniono wszystkie zarchiwizowane wydania Azure AD Connect i Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 859a4f199e65dd0c3aee9424029f6060683d5fbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836091"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: archiwum historii wersji

Zespół Azure Active Directory (Azure AD) regularnie aktualizuje Azure AD Connect za pomocą nowych funkcji i funkcji. Nie wszystkie dodatki są stosowane dla wszystkich odbiorców.

>[!NOTE] 
> Ten artykuł zawiera informacje o wersji dotyczące wszystkich zarchiwizowanych wersji usługi Azure AD — 1.3.20.0 i starszych.  Bieżące wersje można znaleźć w temacie [historia wersji Azure AD Connect](reference-connect-version-history.md)

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Stan wydania 

04/24/2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

- Dodawanie obsługi odświeżania domeny 
- Funkcja folderów publicznych poczty programu Exchange jest dostępna 
- Poprawa obsługi błędów kreatora w przypadku błędów usługi 
- Dodano link ostrzegawczy na Synchronization Service Manager interfejsie użytkownika na stronie właściwości łącznika. 
- Funkcja zapisywania zwrotnego ujednoliconych grup jest teraz ogólnie dostępna 
- Ulepszony komunikat o błędzie SSPR, gdy kontroler domeny nie ma kontrolki LDAP 
- Dodano diagnostykę dla błędów rejestru DCOM podczas instalacji  
- Ulepszone śledzenie błędów usługi PHS RPC 
- Zezwalanie na poświadczenia EA z domeny podrzędnej 
- Zezwalaj na wprowadzanie nazwy bazy danych podczas instalacji (nazwa domyślna: ADSync)
- Uaktualnij do biblioteki ADAL 3.19.8, aby pobrać WS-Trust poprawkę dla polecenia ping i dodać obsługę nowych wystąpień platformy Azure 
- Modyfikuj reguły synchronizacji grupy, aby przepływy samAccountName, DomainNetbios i DomainFQDN do chmury-wymagały oświadczeń 
- Zmodyfikowano obsługę domyślnej reguły synchronizacji — Przeczytaj więcej [tutaj](how-to-connect-fix-default-rules.md).
- Dodano nowego agenta działającego jako usługa systemu Windows. Ten agent o nazwie "Agent administracyjny" umożliwia dokładniejszą diagnostykę zdalną serwera Azure AD Connect, aby ułatwić inżynierom firmy Microsoft Rozwiązywanie problemów podczas otwierania zgłoszenia do pomocy technicznej. Ten agent nie jest zainstalowany i domyślnie włączony.  Aby uzyskać więcej informacji na temat instalowania i włączania agenta, zobacz [co to jest Agent administracyjny Azure AD Connect?](whatis-aadc-admin-agent.md) 
- Zaktualizowano umowę licencyjną użytkownika oprogramowania (EULA) 
- Dodano obsługę autouaktualniania dla wdrożeń, które używają AD FS jako ich typu logowania.  Spowoduje to również usunięcie wymagania dotyczącego aktualizacji AD FS zaufania jednostki uzależnionej usługi Azure AD w ramach procesu uaktualniania. 
- Dodano zadanie zarządzania zaufaniem do usługi Azure AD, które udostępnia dwie opcje: Analiza/aktualizacja zaufania i resetowanie zaufania. 
- AD FS zmieniono zachowanie zaufania jednostki uzależnionej usługi Azure AD, aby zawsze używało przełącznika-SupportMultipleDomain (w tym zaufania i aktualizacji domeny usługi Azure AD). 
- Zmieniono zachowanie usługi Install New AD FS farmie, aby wymagało certyfikatu PFX przez usunięcie opcji korzystania z certyfikatu preinstalowanego.
- Zaktualizowano przepływ pracy AD FS instalacji nowej farmy, tak aby zezwalał tylko na wdrażanie 1 AD FS i 1 serwera WAP.  Wszystkie dodatkowe serwery zostaną wykonane po instalacji początkowej. 

### <a name="fixed-issues"></a>Naprawione problemy 

- Naprawianie logiki reconnect programu SQL dla usługi ADSync 
- Poprawka zezwalająca na czystą instalację przy użyciu pustej bazy danych SQL AOA 
- Napraw skrypt uprawnień PS, aby uściślić uprawnienia GWB 
- Napraw błędy usługi VSS za pomocą LocalDB  
- Usuń mylący komunikat o błędzie, gdy typ obiektu nie należy do zakresu 
- Rozwiązano problem polegający na tym, że instalacja programu Azure AD PowerShell na serwerze może potencjalnie spowodować konflikt zestawu z Azure AD Connect. 
- Stała usterka PHS na serwerze przejściowym, gdy poświadczenia łącznika są aktualizowane w interfejsie użytkownika Synchronization Service Manager. 
- Rozwiązano pewne przecieki pamięci 
- Różne poprawki autouaktualniające 
- Różne poprawki dotyczące eksportu i niepotwierdzonego przetwarzania importu 
- Rozwiązano błąd z obsługą ukośnika odwrotnego w domenie i filtrowaniu jednostek organizacyjnych 
- Rozwiązano problem polegający na tym, że zatrzymanie usługi ADSync trwa dłużej niż 2 minuty i powoduje problem w czasie uaktualnienia. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Stan wydania

12/18/2018: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy

Ta kompilacja aktualizuje łączniki niestandardowe (na przykład ogólny łącznik LDAP i ogólny łącznik SQL) dostarczane z Azure AD Connect. Aby uzyskać więcej informacji na temat odpowiednich łączników, zobacz wersja 1.1.911.0 w [artykule historia wersji łącznika](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Stan wydania
12/11/2018: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek umożliwia użytkownikowi wybranie domeny docelowej w określonym lesie dla kontenera RegisteredDevices podczas włączania funkcji zapisywania zwrotnego urządzeń.  W poprzednich wersjach, które zawierają nowe funkcje opcji urządzeń (1.1.819.0 – 1.2.68.0), lokalizacja kontenera RegisteredDevices została ograniczona do katalogu głównego lasu i nie zezwala na używanie domen podrzędnych.  To ograniczenie jest zaatakowane tylko w nowych wdrożeniach — w przypadku uaktualnień w miejscu.  

Jeśli dowolna kompilacja zawierająca zaktualizowane funkcje opcji urządzenia została wdrożona na nowym serwerze i włączono funkcję zapisywania zwrotnego urządzeń, należy ręcznie określić lokalizację kontenera, jeśli nie ma go w katalogu głównym lasu.  W tym celu należy wyłączyć funkcję zapisywania zwrotnego urządzeń i włączyć ją ponownie, co umożliwi określenie lokalizacji kontenera na stronie "zapisywanie zwrotne" lasu.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Stan wydania 

11/30/2018: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy

Ta kompilacja poprawek rozwiązuje konflikt, w którym może wystąpić błąd uwierzytelniania spowodowany niezależną obecnością modułu Galeria programu PowerShell MSOnline na serwerze synchronizacji.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Stan wydania 

11/19/2018: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy

Ta kompilacja poprawek rozwiązuje regresję w poprzedniej kompilacji, w której zapisywanie zwrotne haseł kończy się niepowodzeniem podczas korzystania z dołączenia kontrolera domeny w systemie Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Stan wydania 

10/25/2018: wydano do pobrania


### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 


- Zmieniono funkcję zapisu z powrotem, aby upewnić się, że hostowana poczta głosowa działa zgodnie z oczekiwaniami.  W niektórych scenariuszach usługa Azure AD zastąpiła zastąpienie atrybutu msExchUcVoicemailSettings podczas zapisywania zwrotnego wartością null.  Usługa Azure AD nie będzie już czyścić wartości lokalnego tego atrybutu, jeśli wartość chmury nie jest ustawiona.
- Dodano diagnostykę w Kreatorze Azure AD Connect w celu zbadania i zidentyfikowania problemów z łącznością z usługą Azure AD. Te same funkcje diagnostyczne można również uruchomić bezpośrednio za pośrednictwem programu PowerShell za pomocą polecenia cmdlet Test-AdSyncAzureServiceConnectivity. 
- Dodano diagnostykę w Kreatorze Azure AD Connect w celu zbadania i zidentyfikowania problemów z łącznością z usługą AD. Te same funkcje diagnostyczne można również uruchomić bezpośrednio za pomocą programu PowerShell przy użyciu funkcji Start-ConnectivityValidation w module ADConnectivityTools PowerShell.  Aby uzyskać więcej informacji [, zobacz co to jest moduł ADConnectivityTool PowerShell?](how-to-connect-adconnectivitytools.md)
- Dodano wstępne Sprawdzanie wersji schematu usługi AD pod kątem dołączania hybrydowego Azure Active Directory i zapisywania urządzeń 
- Zmieniono wyszukiwanie atrybutu strony rozszerzenia katalogu, aby nie uwzględniać wielkości liter.
-   Dodano pełną obsługę protokołu TLS 1,2. Ta wersja obsługuje wszystkie inne wyłączane protokoły i włączana jest tylko protokół TLS 1,2 na komputerze, na którym zainstalowano Azure AD Connect.  Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu TLS 1,2 dla Azure AD Connect](reference-connect-tls-enforcement.md)



### <a name="fixed-issues"></a>Naprawione problemy   

- Naprawiono usterkę, w której Azure AD Connect uaktualnienie nie powiedzie się, jeśli jest używane SQL Always On. 
- Rozwiązano usterkę umożliwiającą prawidłowe analizowanie nazw jednostek organizacyjnych, które zawierają ukośnik. 
- Rozwiązano problem polegający na tym, że uwierzytelnianie Pass-Through będzie wyłączone dla czystej instalacji w trybie przejściowym. 
- Naprawiono usterkę, która uniemożliwiła załadowanie modułu programu PowerShell podczas uruchamiania narzędzi do rozwiązywania problemów 
- Naprawiono usterkę, która spowodowałaby zablokowanie klientom używania wartości liczbowych w pierwszym znaku nazwy hosta. 
- Naprawiono usterkę, w której Azure AD Connect zezwolił na nieprawidłowe partycje i wybór kontenera 
- Naprawiono komunikat o błędzie "nieprawidłowe hasło", gdy jest włączone Logowanie jednokrotne na komputerze. 
- Różne poprawki usterek dotyczące zarządzania zaufaniem AD FS  
- Podczas konfigurowania funkcji zapisywania zwrotnego urządzeń Naprawiono sprawdzenie schematu w celu wyszukania klasy obiektu msDs-DeviceContainer (wprowadzonej w WS2012 R2)


## <a name="118820"></a>1.1.882.0  

9/7/2018: zwolnione do pobrania, nie będzie wydania do autouaktualniania 

### <a name="fixed-issues"></a>Naprawione problemy  

Uaktualnianie Azure AD Connect kończy się niepowodzeniem, jeśli dla usługi ADSync DB skonfigurowano zawsze dostęp do usługi SQL Server. Ta poprawka rozwiązuje ten problem i umożliwia pomyślne uaktualnienie. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Stan wydania

8/21/2018: wydano do pobrania i uaktualnienia autoupgrade. 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Integracja z usługą ping Sfederować w Azure AD Connect jest teraz dostępna do ogólnej dostępności. [Dowiedz się więcej na temat sposobu federacyjnego usługi Azure AD za pomocą polecenia ping Sfederować](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect teraz tworzy kopię zapasową zaufania usługi Azure AD w AD FS za każdym razem, gdy aktualizacja zostanie wprowadzona i zapisuje ją w osobnym pliku do łatwego przywracania, jeśli jest to wymagane. [Dowiedz się więcej na temat nowych funkcji i zarządzania relacjami zaufania usługi Azure AD w Azure AD Connect](./how-to-connect-azure-ad-trust.md).
- Nowe narzędzia do rozwiązywania problemów ułatwiają rozwiązywanie problemów z zmianą podstawowego adresu e-mail i ukrywaniem konta z globalnej listy adresów
- Azure AD Connect zaktualizowano w celu uwzględnienia najnowszego klienta SQL Server 2012 Native
- Po przełączeniu logowania użytkownika do synchronizacji skrótów haseł lub uwierzytelniania przekazywanego w zadaniu "zmiana logowania użytkownika" pole wyboru bezproblemowego pojedynczego Sign-On jest domyślnie włączone.
- Dodano obsługę systemu Windows Server Essentials 2019
- Agent Azure AD Connect Health został zaktualizowany do najnowszej wersji 3.1.7.0
- Jeśli podczas uaktualniania Instalator wykryje zmiany w domyślnych regułach synchronizacji, administrator zostanie poproszony o ostrzeżenie przed zastąpieniem zmodyfikowanych reguł. Umożliwi to użytkownikowi podejmowanie działań naprawczych i późniejsze wznowienie. Stare zachowanie: Jeśli istniała jakakolwiek zmodyfikowana reguła, uaktualnienie ręczne zostało zastąpione tymi regułami bez podawania ostrzeżenia dla użytkownika, a harmonogram synchronizacji został wyłączony bez poinformowania użytkownika. Nowe zachowanie: użytkownik zostanie poproszony o ostrzeżenie przed zastąpieniem zmodyfikowanych reguł synchronizacji. Użytkownik będzie mógł zatrzymać proces uaktualniania i wznowić go później po podjęciu działań naprawczych.
- Zapewnij lepszą obsługę problemu ze zgodnością ze standardem FIPS, dostarczając komunikat o błędzie dotyczący generowania skrótów MD5 w środowisku zgodnym ze standardem FIPS oraz link do dokumentacji, która zapewnia obejście tego problemu.
- Aktualizacja interfejsu użytkownika w celu usprawnienia zadań federacyjnych w kreatorze, które znajdują się teraz w oddzielnej grupie podrzędnej dla Federacji. 
- Wszystkie dodatkowe zadania federacyjne są teraz pogrupowane w jednym podmenu w celu ułatwienia użycia.
- Nowy moduł odnowionych ADSyncConfig Posh (AdSyncConfig. PSM1) z nowymi funkcjami uprawnień usługi AD przenoszony ze starego ADSyncPrep. PSM1 (co może być wkrótce przestarzałe)

### <a name="fixed-issues"></a>Naprawione problemy 

- Rozwiązano problem polegający na tym, że serwer Azure AD Connect będzie wyświetlał duże użycie procesora po uaktualnieniu do programu .NET 4.7.2
- Rozwiązano błąd, który mógłby sporadycznie generować komunikat o błędzie dla nierozwiązanego problemu zakleszczenia SQL
- Rozwiązano kilka problemów dotyczących ułatwień dostępu dla edytora reguł synchronizacji i Service Manager synchronizacji  
- Naprawiono usterkę, w której Azure AD Connect nie może pobrać informacji o ustawieniach rejestru
- Rozwiązano usterkę, która utworzyła problemy, gdy użytkownik przejdzie do przodu/wstecz w Kreatorze
- Naprawiono usterkę, aby zapobiec wystąpieniu błędu z powodu niepoprawnej obsługi wielowątkowości w Kreatorze
- Gdy strona filtrowania synchronizacji grup napotka błąd LDAP podczas rozpoznawania grup zabezpieczeń, Azure AD Connect teraz zwraca wyjątek z pełną dokładnością.  Główna przyczyna wyjątku odwołania jest nadal nieznana i będzie rozwiązywana przez inną usterkę.
-  Rozwiązano problem polegający na tym, że uprawnienia dla kluczy STK i NGC (atrybut MS-DS-KeyCredentialLink dla obiektów użytkownika/urządzenia dla WHfB) nie zostały prawidłowo ustawione.     
- Naprawiono usterkę polegającą na tym, że element "Set-ADSyncRestrictedPermissions" nie został wywołany prawidłowo
-  Dodawanie obsługi uprawnień udzielania na potrzeby zapisywania zwrotnego grup w Kreatorze instalacji usługi Azure ADConnect
- Podczas zmiany metody logowania z synchronizacji skrótów haseł na AD FS synchronizacja skrótów haseł nie została wyłączona.
- Dodano weryfikację adresów IPv6 w konfiguracji AD FS
- Zaktualizowano komunikat z powiadomieniem, aby poinformować o istnieniu istniejącej konfiguracji.
- Nie można wykryć kontenera w niezaufanym lesie przez funkcję zapisywania zwrotnego urządzeń. Ten element został zaktualizowany, aby zapewnić lepszy komunikat o błędzie i link do odpowiedniej dokumentacji
- Zaznaczenie jednostki organizacyjnej, a następnie synchronizacji/zapisu zwrotnego odpowiadającego tej jednostce organizacyjnej daje ogólny błąd synchronizacji. Ten element został zmieniony, aby utworzyć bardziej zrozumiały komunikat o błędzie.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stan wydania

5/14/2018: wydano do autouaktualnienia i pobrania.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

Nowe funkcje i ulepszenia

- Ta wersja obejmuje publiczną wersję zapoznawczą integracji serwera pingfederate w Azure AD Connect. W tej wersji klienci mogą łatwo i niezawodnie konfigurować swoje środowisko Azure Active Directory, aby korzystać z serwera pingfederate jako dostawcy federacyjnego. Aby dowiedzieć się więcej na temat korzystania z tej nowej funkcji, zapoznaj się z naszą [dokumentacją online](plan-connect-user-signin.md#federation-with-pingfederate). 
- Zaktualizowano narzędzie do rozwiązywania problemów z kreatorem Azure AD Connect, gdzie teraz analizuje więcej scenariuszy błędów, takich jak połączone skrzynki pocztowe i grupy dynamiczne usługi AD. Więcej informacji na temat narzędzia do rozwiązywania problemów można znaleźć [tutaj](tshoot-connect-objectsync.md).
- Konfiguracja zapisywania zwrotnego urządzeń jest teraz zarządzana wyłącznie w Kreatorze Azure AD Connect.
- Dodano nowy moduł programu PowerShell o nazwie ADSyncTools. PSM1, który może służyć do rozwiązywania problemów z łącznością SQL i różnych narzędzi do rozwiązywania problemów. Więcej informacji na temat modułu ADSyncTools można znaleźć [tutaj](tshoot-connect-tshoot-sql-connectivity.md). 
- Dodano nowe zadanie dodatkowe "Konfigurowanie opcji urządzenia". Możesz użyć zadania, aby skonfigurować następujące dwie operacje: 
  - **Hybrydowe dołączanie do usługi Azure AD**: Jeśli w środowisku znajduje się lokalna usługa AD i chcesz również skorzystać z możliwości zapewnianych przez Azure Active Directory, możesz zaimplementować urządzenia dołączone do hybrydowej usługi Azure AD. Są to urządzenia dołączone do lokalnej usługi Active Directory oraz usługi Azure Active Directory.
  - **Zapisywanie zwrotne urządzeń**: zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego opartego na urządzeniach do AD FS (2012 R2 lub nowsze) chronionych urządzeń

    >[!NOTE] 
    > - Opcja włączenia funkcji zapisywania zwrotnego urządzeń z opcji dostosowywania synchronizacji będzie wyszarzona. 
    > -  Moduł programu PowerShell dla narzędzia ADPrep jest przestarzały z tą wersją.


### <a name="fixed-issues"></a>Naprawione problemy 

- W tej wersji zaktualizowano instalację SQL Server Express do wersji SQL Server 2012 SP4, która zawiera poprawki kilku luk w zabezpieczeniach.  Więcej informacji na temat SQL Server 2012 SP4 można znaleźć [tutaj](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) .
- Przetwarzanie reguły synchronizacji: reguły synchronizacji dołączania danych wychodzących bez warunku sprzężenia powinny zostać cofnięte, jeśli nadrzędna reguła synchronizacji nie ma już zastosowania
- Do interfejsu użytkownika Synchronization Service Manager i edytora reguł synchronizacji zastosowano kilka poprawek ułatwień dostępu.
- Kreator Azure AD Connect: Wystąpił błąd podczas tworzenia konta łącznika usługi AD, gdy Azure AD Connect znajduje się w grupie roboczej
- Kreator Azure AD Connect: na stronie logowania usługi Azure AD Wyświetl pole wyboru weryfikacji zawsze wtedy, gdy występuje niezgodność w domenach usługi AD i domenach zweryfikowanych przez usługę Azure AD
- Automatyczne uaktualnianie poprawki programu PowerShell w celu ustawienia stanu automatycznej aktualizacji w niektórych przypadkach po próbie automatycznej aktualizacji.
- Kreator Azure AD Connect: Zaktualizowano telemetrię, aby przechwycić poprzednio brakujące informacje
- Kreator Azure AD Connect: następujące zmiany zostały wprowadzone podczas korzystania z zadania **zmiany logowania użytkownika** w celu przełączenia z AD FS na uwierzytelnianie przekazywane:
    - Agent uwierzytelniania przekazywanego jest instalowany na serwerze Azure AD Connect i funkcja uwierzytelniania przekazywanego jest włączona, przed konwersją domen z federacyjnego na zarządzane.
    - Użytkownicy nie są już przekonwertowane z federacyjnego na zarządzane. Konwertowane są tylko domeny.
- Kreator Azure AD Connect: AD FS wyrażenie regularne z wielodomenem nie jest prawidłowe, jeśli nazwa UPN użytkownika ma "aktualizację wyrażenia regularnego znakowego" do obsługi znaków specjalnych
- Kreator Azure AD Connect: Usuń komunikat fałszywe "Skonfiguruj źródłowy atrybut zakotwiczenia", gdy nie zostanie zmieniona 
- Kreator Azure AD Connect: obsługa AD FS dla scenariusza o podwójnej Federacji
- Kreator Azure AD Connect: oświadczenia AD FS nie są aktualizowane dla dodanej domeny podczas konwersji domeny zarządzanej na federacyjnego
- Kreator Azure AD Connect: podczas wykrywania zainstalowanych pakietów znajdziesz przestarzałe produkty z narzędzia DirSync/Azure AD Sync/Azure AD Connect. Podejmiemy teraz próbę odinstalowania starych produktów.
- Kreator Azure AD Connect: poprawianie mapowania komunikatów o błędach w przypadku niepowodzenia instalacji agenta uwierzytelniania przekazującego
- Kreator Azure AD Connect: Usunięto kontener "Configuration" z strony filtrowania jednostki organizacyjnej domeny
- Instalacja aparatu synchronizacji: Usuń niepotrzebną starszą logikę, która czasami nie powiodła się z instalacji MSI przez aparat synchronizacji
- Kreator Azure AD Connect: napraw tekst pomocy podręczny na stronie funkcje opcjonalne na potrzeby synchronizacji skrótów haseł
- Środowisko uruchomieniowe aparatu synchronizacji: napraw scenariusz, w którym obiekt CS ma zaimportowane reguły usuwania i synchronizowania, próbując ponownie zainicjować obsługę administracyjną obiektu.
- Środowisko uruchomieniowe aparatu synchronizacji: Dodaj łącze pomocy do przewodnika rozwiązywania problemów z łącznością online w dzienniku zdarzeń, aby uzyskać Błąd importowania
- Środowisko uruchomieniowe aparatu synchronizacji: zmniejszone użycie pamięci przez harmonogram synchronizacji podczas wyliczania łączników
- Kreator Azure AD Connect: Rozwiązywanie problemu podczas rozwiązywania niestandardowego konta usługi synchronizacji, które nie ma uprawnień do odczytu usługi AD
- Kreator Azure AD Connect: ulepszanie rejestrowania opcji filtrowania domen i jednostek organizacyjnych
- Kreator Azure AD Connect: AD FS Dodawanie domyślnych oświadczeń do zaufania federacji utworzonego dla scenariusza MFA
- Kreator Azure AD Connect: AD FS wdrażania WAP: Dodawanie serwera nie powiedzie się przy użyciu nowego certyfikatu
- Kreator Azure AD Connect: wyjątek DSSO, jeśli onPremCredentials nie są zainicjowane dla domeny 
- Preferencyjnie przepływaj atrybut AD odróżnionyname z obiektu aktywnego użytkownika.
- Rozwiązano błąd kosmetyków był pierwszeństwem pierwszej reguły synchronizacji OOB ustawionym na 99 zamiast 100



## <a name="117510"></a>1.1.751.0
Status 4/12/2018: wydano tylko do pobrania

>[!NOTE]
>Ta wersja jest poprawką dla Azure AD Connect
### <a name="azure-ad-connect-sync"></a>Synchronizacja programu Azure AD Connect
#### <a name="fixed-issues"></a>Naprawione problemy
Rozwiązano problem polegający na tym, że automatyczne odnajdowanie wystąpień platformy Azure dla dzierżawców Chin było sporadyczne niepowodzeniem.  

### <a name="ad-fs-management"></a>Usługi AD FS zarządzania
#### <a name="fixed-issues"></a>Naprawione problemy

Wystąpił problem z logiką ponawiania konfiguracji, która spowodowałaby, że ArgumentException wskazuje, że element o tym samym kluczu został już dodany.  Spowoduje to, że wszystkie operacje ponawiania zakończą się niepowodzeniem.

## <a name="117500"></a>1.1.750.0
Status 3/22/2018: wydano do autouaktualnienia i pobrania.
>[!NOTE]
>Po zakończeniu uaktualniania do tej nowej wersji zostanie automatycznie wyzwolona pełna synchronizacja i pełny import dla łącznika usługi Azure AD oraz pełna synchronizacja łącznika usługi AD. Ponieważ może to potrwać pewien czas, w zależności od rozmiaru środowiska Azure AD Connect upewnij się, że wykonano czynności niezbędne do zapewnienia obsługi tego lub wyłączenia podczas uaktualniania do momentu, gdy okaże się to wygodne.
>[!NOTE]
>"Funkcja autouaktualniania została nieprawidłowo wyłączona dla niektórych dzierżawców, którzy wdrażali kompilacje później niż 1.1.524.0. Aby upewnić się, że wystąpienie Azure AD Connect nadal kwalifikuje się do autouaktualnienia, uruchom następujące polecenie cmdlet programu PowerShell: "Set-ADSyncAutoUpgrade-AutoupGradeState Enabled"

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Naprawione problemy

* Set-ADSyncAutoUpgrade polecenie cmdlet powinno poprzednio blokować autouaktualnienie, jeśli stan autouaktualniania jest ustawiony na zawieszone. Ta funkcja została zmieniona, więc nie blokuje autouaktualniania przyszłych kompilacji.
* Zmieniono opcję strony **logowania użytkownika** "Synchronizacja haseł" na "Synchronizacja skrótów haseł".  Azure AD Connect synchronizuje skróty haseł, a nie hasła, więc jest to zgodne z rzeczywistym użyciem.  Aby uzyskać więcej informacji, zobacz [Implementacja synchronizacji skrótów haseł z synchronizacją Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stan: wydano, aby wybrać klientów

>[!NOTE]
>Po zakończeniu uaktualniania do tej nowej wersji zostanie automatycznie wyzwolona pełna synchronizacja i pełny import dla łącznika usługi Azure AD oraz pełna synchronizacja łącznika usługi AD. Ponieważ może to potrwać pewien czas, w zależności od rozmiaru środowiska Azure AD Connect upewnij się, że wykonano czynności niezbędne do zapewnienia obsługi tego lub wyłączenia podczas uaktualniania do momentu, gdy okaże się to wygodne.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Naprawione problemy
* Naprawianie okna chronometrażu zadań w tle dla strony filtrowania partycji podczas przełączania na następną stronę.

* Rozwiązano błąd, który spowodował naruszenie zasad dostępu podczas akcji niestandardowej ConfigDB.

* Rozwiązano usterkę do odzyskania z limitu czasu połączenia SQL.

* Rozwiązano problem polegający na tym, że certyfikaty z symbolami wieloznacznymi sieci SAN zostały zakończone niepowodzeniem.

* Naprawiono usterkę powodującą miiserver.exe awarię podczas eksportowania łącznika usługi Azure AD.

* Naprawiono usterkę, która powoduje, że podczas uruchamiania Kreatora Azure AD Connect do zmiany konfiguracji zarejestrowano złą próbę hasła.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Dodawanie ustawień prywatności dla Ogólne rozporządzenie o ochronie danych (Rodo).  Aby uzyskać więcej informacji, zobacz [artykuł.](reference-connect-user-privacy.md)

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Telemetria aplikacji — administrator może włączyć/wyłączyć tę klasę danych na

* Dane kondycji usługi Azure AD — administrator musi odwiedzić portal kondycji, aby kontrolować jego ustawienia kondycji.
   Po zmianie zasad usługi Agenty będą je odczytywać i wymuszać.

* Dodano akcje konfiguracji zapisywania i wycofywania urządzenia oraz pasek postępu dla inicjowania strony

* Ulepszona ogólna Diagnostyka z raportem HTML i pełną zbieraniem danych w raporcie ZIP-Text/HTML

* Ulepszona niezawodność autouaktualnienia i dodano dodatkową telemetrię w celu zapewnienia, że kondycja serwera można ustalić

* Ogranicz uprawnienia dostępne dla kont uprzywilejowanych na koncie łącznika usługi AD

  * W przypadku nowych instalacji Kreator ograniczy uprawnienia, które konta uprzywilejowane mają na koncie MSOL po utworzeniu konta MSOL.

Zmiany będą następujące:
1. Instalacje ekspresowe
2. Instalacje niestandardowe z użyciem konta AutoCreate
3. Zmieniono Instalatora, aby nie wymagało uprawnienia administratora w czystej instalacji Azure AD Connect

* Dodano nowe narzędzie do rozwiązywania problemów z synchronizacją dla określonego obiektu. Jest on dostępny w opcji "Rozwiązywanie problemów z synchronizacją obiektów" kreatora Azure AD Connect rozwiązywania problemów z dodatkowym zadaniem. Obecnie narzędzie sprawdza następujące elementy:

  * Niezgodność UserPrincipalName między zsynchronizowanym obiektem użytkownika a kontem użytkownika w dzierżawie usługi Azure AD.
  * Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania domeny
  * Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania jednostki organizacyjnej (OU)

* Dodano nowe narzędzie do synchronizacji bieżącego skrótu hasła przechowywanego w Active Directory lokalnym dla określonego konta użytkownika.

Narzędzie nie wymaga zmiany hasła. Jest on dostępny w opcji "Rozwiązywanie problemów z synchronizacją skrótów haseł" kreatora Azure AD Connect rozwiązywania problemów z dodatkowym zadaniem.






## <a name="116540"></a>1.1.654.0
Stan: 12 grudnia, 2017

>[!NOTE]
>Ta wersja jest poprawką powiązaną z zabezpieczeniami dla Azure AD Connect
### <a name="azure-ad-connect"></a>Azure AD Connect
Dodano ulepszenia do Azure AD Connect wersji 1.1.654.0 (i po), aby upewnić się, że zalecane zmiany uprawnień opisane w sekcji [Zablokuj dostęp do konta AD DS](#lock) są automatycznie stosowane podczas Azure AD Connect tworzenia konta AD DS. 

- Podczas konfigurowania Azure AD Connect administrator może udostępnić istniejące konto AD DS lub pozwolić Azure AD Connect automatycznie utworzyć konto. Zmiany uprawnień są automatycznie stosowane do konta AD DS, które jest tworzone przez Azure AD Connect podczas instalacji. Nie są one stosowane do istniejącego konta AD DS dostarczonego przez administratora instalacji.
- W przypadku klientów, którzy dokonali uaktualnienia ze starszej wersji programu Azure AD Connect do 1.1.654.0 (lub później), zmiany uprawnień nie zostaną zastosowane wstecz do istniejących kont AD DS utworzonych przed uaktualnieniem. Zostaną one zastosowane tylko do nowych kont AD DS utworzonych po uaktualnieniu. Dzieje się tak, gdy dodawane są nowe lasy usługi AD, które mają być synchronizowane z usługą Azure AD.

>[!NOTE]
>Ta wersja usuwa tylko lukę w zabezpieczeniach dla nowych instalacji Azure AD Connect, w których konto usługi jest tworzone przez proces instalacji. W przypadku istniejących instalacji lub w przypadkach, w których konto jest używane samodzielnie, należy się upewnić, że ta usterka nie istnieje.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> Zablokuj dostęp do konta AD DS
Zablokuj dostęp do konta AD DS, implementując następujące zmiany uprawnień w lokalnej usłudze AD:  

*   Wyłącz dziedziczenie dla określonego obiektu
*   Usuń wszystkie wpisy kontroli dostępu do określonego obiektu, z wyjątkiem ACE określonych dla siebie. Chcemy zachować domyślne uprawnienia, które są niezmienione, gdy nastąpi samodzielne.
*   Przypisz te określone uprawnienia:

Typ     | Nazwa                          | Access               | Dotyczy:
---------|-------------------------------|----------------------|--------------|
Zezwalaj    | SYSTEM                        | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Enterprise Admins             | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy domeny                 | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy                | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Wyświetl zawartość        | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Uprawnienia do odczytu     | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Wyświetl zawartość        | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Uprawnienia do odczytu     | Ten obiekt  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skrypt programu PowerShell służący do zaostrzania istniejącego konta usługi

Aby użyć skryptu programu PowerShell do zastosowania tych ustawień, na istniejącym koncie AD DS (Eter udostępniony przez organizację lub utworzony przez poprzednią instalację Azure AD Connect, należy pobrać skrypt z podanego łącza powyżej.

##### <a name="usage"></a>Użycie:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Lokalizacja 

**$ObjectDN** = konto Active Directory, którego uprawnienia należy wzmocnić.

**$Credential** = poświadczenia administratora, które ma niezbędne uprawnienia do ograniczenia uprawnień na koncie $ObjectDN. Te uprawnienia są zwykle przechowywane przez administratora przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowanej nazwy domeny konta administratora, aby uniknąć niepowodzeń wyszukiwania kont. Przykład: contoso. com\admin.

>[!NOTE] 
>$credential. Nazwa użytkownika powinna mieć format FQDN\username. Przykład: contoso. com\admin 
##### <a name="example"></a>Przykład:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Czy ta luka w zabezpieczeniach została użyta do uzyskania nieautoryzowanego dostępu?

Aby sprawdzić, czy ta luka w zabezpieczeniach została użyta w celu naruszenia konfiguracji Azure AD Connect należy zweryfikować datę ostatniego resetowania hasła konta usługi.  Jeśli sygnatura czasowa w nieoczekiwanym czasie, dalsze badanie przez dziennik zdarzeń dla tego zdarzenia resetowania hasła, należy podjąć.

Aby uzyskać więcej informacji, zobacz [Poradnik zabezpieczeń firmy Microsoft 4056318](/security-updates/securityadvisories/2017/4056318)

## <a name="116490"></a>1.1.649.0
Stan: Październik 27 2017

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect autoupgrade.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Rozwiązano problem 
* Rozwiązano problem ze zgodnością wersji między Azure AD Connect i agentem Azure AD Connect Health (na potrzeby synchronizacji). Ten problem dotyczy klientów, którzy wykonują Azure AD Connect uaktualnieniem w miejscu do wersji 1.1.647.0, ale obecnie ma agenta kondycji w wersji 3.0.127.0. Po uaktualnieniu Agent kondycji nie będzie mógł wysyłać danych o kondycji dotyczących usługi Azure AD Connect Synchronization do Usługa kondycji Azure AD. Ta poprawka powoduje zainstalowanie agenta kondycji w wersji 3.0.129.0 podczas Azure AD Connect uaktualnienia w miejscu. Wersja agenta kondycji 3.0.129.0 nie ma problemu ze zgodnością w wersji 1.1.649.0 Azure AD Connect.


## <a name="116470"></a>1.1.647.0
Stan: październik 19 2017

> [!IMPORTANT]
> Istnieje znany problem ze zgodnością między wersjami Azure AD Connect 1.1.647.0 i Azure AD Connect Health Agent (for Sync) w wersji 3.0.127.0. Ten problem uniemożliwia agentowi kondycji wysyłanie danych o kondycji dotyczących usługi synchronizacji Azure AD Connect (w tym błędów synchronizacji obiektów i uruchamiania danych historii) do Usługa kondycji usługi Azure AD. Przed ręcznym uaktualnieniem wdrożenia Azure AD Connect do wersji 1.1.647.0 upewnij się, że bieżąca wersja agenta Azure AD Connect Health zainstalowana na serwerze Azure AD Connect. Możesz to zrobić, przechodząc do *Panelu sterowania → Dodaj Usuń programy* i wyszukaj aplikację *Microsoft Azure AD Connect Health Agent do synchronizacji*. Jeśli jego wersja to 3.0.127.0, zaleca się poczekanie, aż Następna Azure AD Connect wersja będzie dostępna przed uaktualnieniem. Jeśli wersja agenta kondycji nie jest 3.0.127.0, warto kontynuować ręczne uaktualnianie w miejscu. Należy zauważyć, że ten problem nie wpływa na uaktualnienie lub klientów dokonujących nowej instalacji Azure AD Connect.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Naprawione problemy
* Rozwiązano problem z kreatorem *zmiany zadania logowania użytkownika* w Azure AD Connect:

  * Ten problem występuje, gdy istnieje wdrożenie Azure AD Connect z **włączoną** synchronizacją haseł i podjęto próbę ustawienia metody logowania użytkownika jako *uwierzytelniania przekazywanego*. Przed zastosowaniem zmiany Kreator nieprawidłowo wyświetli monit "*Wyłącz synchronizację haseł*". Jednak synchronizacja haseł pozostaje włączona po zastosowaniu zmiany. W przypadku tej poprawki Kreator nie wyświetla już monitu.

  * Po zaprojektowaniu Kreator nie wyłącza synchronizacji haseł podczas aktualizacji metody logowania użytkownika przy użyciu zadania *zmiany użytkownika* . Ma to na celu uniknięcie zakłócenia klientów, którzy chcą zachować synchronizację haseł, nawet jeśli umożliwiają uwierzytelnianie przekazujące lub Federacji jako podstawową metodę logowania użytkownika.

  * Jeśli chcesz wyłączyć synchronizację haseł po aktualizacji metody logowania użytkownika, musisz wykonać zadanie *Dostosuj konfigurację synchronizacji* w kreatorze. Po przejściu do strony *funkcje opcjonalne* Usuń zaznaczenie opcji *Synchronizacja haseł* .

  * Należy zauważyć, że ten sam problem występuje również w przypadku próby włączenia/wyłączenia bezproblemowego logowania jednokrotnego. W każdym przypadku istnieje wdrożenie Azure AD Connect z włączoną synchronizacją haseł, a metoda logowania użytkownika jest już skonfigurowana jako *uwierzytelnianie przekazywane*. Korzystając z zadania *zmiany logowania użytkownika* , spróbuj zaznaczyć/usunąć zaznaczenie opcji *Włącz bezproblemową rejestrację* jednokrotną, gdy metoda logowania użytkownika pozostanie skonfigurowana jako "uwierzytelnianie przekazywane". Przed zastosowaniem zmiany Kreator nieprawidłowo wyświetli monit "*Wyłącz synchronizację haseł*". Jednak synchronizacja haseł pozostaje włączona po zastosowaniu zmiany. W przypadku tej poprawki Kreator nie wyświetla już monitu.

* Rozwiązano problem z kreatorem *zmiany zadania logowania użytkownika* w Azure AD Connect:

  * Ten problem występuje, gdy istnieje już wdrożenie Azure AD Connect z **wyłączonym** synchronizacją haseł i podjęto próbę ustawienia metody logowania użytkownika jako *uwierzytelniania przekazywanego*. Gdy zmiana zostanie zastosowana, Kreator włączy zarówno uwierzytelnianie przekazywane, jak i synchronizację haseł. W przypadku tej poprawki Kreator nie umożliwia już synchronizacji haseł.

  * Wcześniej synchronizacja haseł była warunkiem wstępnym umożliwiającym włączenie uwierzytelniania przekazywanego. Po ustawieniu metody logowania użytkownika jako *uwierzytelniania przekazywanego* Kreator włączy zarówno uwierzytelnianie przekazywane, jak i synchronizację haseł. Ostatnio synchronizacja haseł została usunięta jako wymaganie wstępne. W ramach Azure AD Connect w wersji 1.1.557.0 wprowadzono zmianę Azure AD Connect, aby nie włączać synchronizacji haseł podczas ustawiania metody logowania użytkownika jako *uwierzytelniania przekazywanego*. Jednak zmiana została zastosowana tylko do instalacji Azure AD Connect. W przypadku tej poprawki Ta sama zmiana zostanie również zastosowana do zadania *zmiany logowania użytkownika* .

  * Należy zauważyć, że ten sam problem występuje również w przypadku próby włączenia/wyłączenia bezproblemowego logowania jednokrotnego. W tym celu istnieje już istniejące wdrożenie Azure AD Connect z wyłączonym synchronizacją haseł, a metoda logowania użytkownika została skonfigurowana jako *uwierzytelnianie przekazywane*. Korzystając z zadania *zmiany logowania użytkownika* , spróbuj zaznaczyć/usunąć zaznaczenie opcji *Włącz bezproblemową rejestrację* jednokrotną, gdy metoda logowania użytkownika pozostanie skonfigurowana jako "uwierzytelnianie przekazywane". Gdy zmiana zostanie zastosowana, Kreator włączy synchronizację haseł. W przypadku tej poprawki Kreator nie umożliwia już synchronizacji haseł. 

* Rozwiązano problem, który spowodował, że uaktualnienie Azure AD Connect nie powiodło się z powodu błędu "*nie można uaktualnić usługi synchronizacji*". Ponadto usługa synchronizacji nie może już się zaczynać z powodu błędu zdarzenia "*nie można uruchomić usługi, ponieważ wersja bazy danych jest nowsza niż zainstalowana wersja plików binarnych*". Ten problem występuje, gdy administrator przeprowadzający uaktualnienie nie ma uprawnień administratora systemu do programu SQL Server, który jest używany przez Azure AD Connect. Ta poprawka Azure AD Connect wymaga, aby administrator miał uprawnienia db_owner do bazy danych ADSync podczas uaktualniania.

* Rozwiązano problem z uaktualnieniem Azure AD Connect, którego dotyczą Klienci, którzy włączyli [bezproblemowe logowanie jednokrotne](./how-to-connect-sso.md). Po uaktualnieniu Azure AD Connect bezproblemowe pojedyncze Sign-On nieprawidłowo pojawia się jako wyłączone w Kreatorze Azure AD Connect, mimo że funkcja pozostaje włączona i w pełni funkcjonalna. W przypadku tej poprawki funkcja jest teraz wyświetlana prawidłowo w kreatorze.

* Rozwiązano problem, który spowodował, że Kreator Azure AD Connect, aby zawsze wyświetlał monit "*Konfigurowanie zakotwiczenia źródła*" na stronie *gotowe do skonfigurowania* , nawet jeśli nie wprowadzono żadnych zmian związanych z kotwicą źródła.

* Podczas ręcznego uaktualniania w miejscu Azure AD Connect klient musi podać poświadczenia administratora globalnego odpowiedniej dzierżawy usługi Azure AD. Wcześniej uaktualnienie mogło być kontynuowane, mimo że poświadczenia administratora globalnego należą do innej dzierżawy usługi Azure AD. Po pomyślnym zakończeniu uaktualnienia pewne konfiguracje nie są poprawnie utrwalane po uaktualnieniu. W przypadku tej zmiany Kreator nie zezwala na przeprowadzanie uaktualnienia, jeśli podane poświadczenia nie pasują do dzierżawy usługi Azure AD.

* Usunięto nadmiarową logikę, która niekoniecznie ponownie uruchomiła usługę Azure AD Connect Health na początku uaktualnienia ręcznego.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano logikę, aby uprościć kroki wymagane do skonfigurowania Azure AD Connect z usługą Microsoft Niemcy Cloud. Wcześniej należy zaktualizować określone klucze rejestru na serwerze Azure AD Connect, aby działały prawidłowo w chmurze Microsoft Niemcy, zgodnie z opisem w tym artykule. Teraz Azure AD Connect może automatycznie wykryć, czy dzierżawca znajduje się w chmurze firmy Microsoft (Niemcy) w oparciu o poświadczenia administratora globalnego podane podczas instalacji.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Uwaga: usługa synchronizacji ma interfejs WMI, który umożliwia tworzenie własnych niestandardowych harmonogramów. Ten interfejs jest obecnie przestarzały i zostanie usunięty z przyszłych wersji Azure AD Connect dostarczonego po 30 czerwca 2018. Klienci, którzy chcą dostosować harmonogram synchronizacji, powinni używać [wbudowanego harmonogramu](./how-to-connect-sync-feature-scheduler.md).
#### <a name="fixed-issues"></a>Naprawione problemy
* Gdy Kreator Azure AD Connect tworzy konto łącznika usługi AD wymagane do synchronizacji zmian z Active Directory lokalnych, nie przypisze prawidłowo konta uprawnienia wymaganego do odczytu obiektów PublicFolder. Ten problem dotyczy zarówno instalacji ekspresowej, jak i instalacji niestandardowej. Ta zmiana rozwiązuje ten problem.

* Rozwiązano problem, który spowodował, że strona Rozwiązywanie problemów z kreatorem Azure AD Connect nie jest prawidłowo renderowana dla administratorów z systemem Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* W przypadku rozwiązywania problemów z synchronizacją haseł za pomocą strony Rozwiązywanie problemów z kreatorem Azure AD Connect Strona rozwiązywania problemów zwraca teraz stan specyficzny dla domeny.

* Wcześniej, Jeśli podjęto próbę włączenia synchronizacji skrótów haseł, Azure AD Connect nie sprawdza, czy konto łącznika usługi AD ma wymagane uprawnienia do synchronizowania skrótów haseł z lokalnej usługi AD. Teraz Kreator Azure AD Connect sprawdzi i wyświetli ostrzeżenie, jeśli konto łącznika usługi AD nie ma wystarczających uprawnień.

### <a name="ad-fs-management"></a>Usługi AD FS zarządzania
#### <a name="fixed-issue"></a>Rozwiązano problem 
* Rozwiązano problem związany z korzystaniem z [usługi MS-ds-ConsistencyGuid jako funkcji kotwicy źródłowej](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) . Ten problem ma wpływ na klientów, którzy skonfigurowali *Federacji z AD FS* jako metodę logowania użytkownika. Podczas wykonywania zadania *konfigurowania zakotwiczenia źródła* w Kreatorze Azure AD Connect przełącza do używania * MS-ds-ConsistencyGuid jako atrybutu Source dla immutableId. W ramach tej zmiany Azure AD Connect próbuje zaktualizować reguły dotyczące usługi ImmutableId w programie AD FS. Jednak ten krok nie powiódł się, ponieważ Azure AD Connect nie ma poświadczeń administratora wymaganych do skonfigurowania AD FS. W przypadku tej poprawki Azure AD Connect teraz zostanie wyświetlony komunikat z prośbą o wprowadzenie poświadczeń administratora dla AD FS, gdy zostanie wykonane zadanie *konfigurowania kotwicy źródłowej* .



## <a name="116140"></a>1.1.614.0
Stan: wrzesień 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Znane problemy
* Istnieje znany problem, który powoduje Niepowodzenie uaktualnienia Azure AD Connect z powodu błędu "*nie można uaktualnić usługi synchronizacji*". Ponadto usługa synchronizacji nie może już się zaczynać z powodu błędu zdarzenia "*nie można uruchomić usługi, ponieważ wersja bazy danych jest nowsza niż zainstalowana wersja plików binarnych*". Ten problem występuje, gdy administrator przeprowadzający uaktualnienie nie ma uprawnień administratora systemu do programu SQL Server, który jest używany przez Azure AD Connect. Uprawnienia dbo nie są wystarczające.

* Istnieje znany problem z uaktualnieniem Azure AD Connect, który ma wpływ na klientów, którzy włączyli [bezproblemowe logowanie jednokrotne](how-to-connect-sso.md). Po uaktualnieniu Azure AD Connect funkcja zostanie wyświetlona jako wyłączona w kreatorze, mimo że funkcja pozostanie włączona. Rozwiązanie tego problemu będzie dostępne w przyszłym wydaniu. Klienci, którzy są zainteresowani tym problemem dotyczącym wyświetlania, mogą ręcznie rozwiązać ten problem, włączając bezproblemowe pojedyncze Sign-On w kreatorze.

#### <a name="fixed-issues"></a>Naprawione problemy
* Rozwiązano problem uniemożliwiający Azure AD Connect aktualizacji reguł oświadczeń w lokalnym AD FS podczas włączania funkcji [MS-ds-ConsistencyGuid as Source](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) . Problem występuje, jeśli spróbujesz włączyć funkcję dla istniejącego wdrożenia Azure AD Connect, które ma AD FS skonfigurowany jako metoda logowania. Ten problem występuje, ponieważ Kreator nie monituje o poświadczenia usług ADFS przed próbą zaktualizowania reguł oświadczeń w AD FS.
* Rozwiązano problem, który spowodował niepowodzenie instalacji Azure AD Connect, jeśli lokalny Las usługi AD ma wyłączone NTLM. Problem jest spowodowany tym, że Kreator Azure AD Connect nie udostępnia w pełni kwalifikowanych poświadczeń podczas tworzenia kontekstów zabezpieczeń wymaganych do uwierzytelniania Kerberos. Powoduje to niepowodzenie uwierzytelniania Kerberos i Azure AD Connect kreatora, aby wrócić do korzystania z protokołu NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Naprawione problemy
* Rozwiązano problem polegający na tym, że nie można utworzyć nowej reguły synchronizacji, jeśli atrybut tagu nie jest wypełniony.
* Rozwiązano problem, który spowodował Azure AD Connect łączenia się z lokalną usługą AD na potrzeby synchronizacji haseł przy użyciu protokołu NTLM, chociaż jest dostępny protokół Kerberos. Ten problem występuje, gdy lokalna topologia usługi AD ma jeden lub więcej kontrolerów domeny, które zostały przywrócone z kopii zapasowej.
* Rozwiązano problem, który spowodował, że pełne kroki synchronizacji występują niepotrzebnie po uaktualnieniu. Ogólnie rzecz biorąc, wykonywanie kroków pełnej synchronizacji jest wymagane po uaktualnieniu, jeśli istnieją zmiany w regułach synchronizacji out-of-box. Problem był spowodowany błędem logiki wykrywania zmian, który niepoprawnie wykrył zmianę w przypadku napotkania wyrażenia reguły synchronizacji z znakami nowego wiersza. Znaki nowego wiersza są wstawiane do wyrażenia reguły synchronizacji w celu zwiększenia czytelności.
* Rozwiązano problem, który może spowodować, że serwer Azure AD Connect nie będzie działał prawidłowo po uaktualnieniu automatycznym. Ten problem ma wpływ na serwery Azure AD Connect w wersji wersji 1.1.443.0 (lub starszej). Aby uzyskać szczegółowe informacje o tym problemie, zapoznaj się z artykułem [Azure AD Connect nie działa prawidłowo po uaktualnieniu automatycznym](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Rozwiązano problem, który może spowodować ponowną próbę automatycznego uaktualnienia co 5 minut, gdy wystąpią błędy. Po naprawieniu poprawek automatyczne uaktualnienie ponawia próbę z wycofywaniem z powrotem po wystąpieniu błędów.
* Rozwiązano problem polegający na tym, że w dzienniku zdarzeń aplikacji systemu Windows nieprawidłowo pokazywane jest zdarzenie synchronizacji haseł 611 jako **informacyjne** zamiast **błędu**. Zdarzenie 611 jest generowane, gdy synchronizacja haseł napotyka problem. 
* Rozwiązano problem w Kreatorze Azure AD Connect, który umożliwia włączenie funkcji zapisywania zwrotnego grup bez wybierania jednostki organizacyjnej wymaganej do zapisywania zwrotnego grup.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano zadanie rozwiązywania problemów do kreatora Azure AD Connect w obszarze dodatkowych zadań. Klienci mogą korzystać z tego zadania, aby rozwiązywać problemy związane z synchronizacją haseł i zbierać ogólną diagnostykę. W przyszłości zadanie rozwiązywania problemów zostanie rozszerzone w taki sposób, aby obejmowało inne problemy związane z synchronizacją katalogu.
* Azure AD Connect teraz obsługuje nowy tryb instalacji o nazwie **use istniejąca baza danych**. Ten tryb instalacji umożliwia klientom zainstalowanie Azure AD Connect, która określa istniejącą bazę danych ADSync. Aby uzyskać więcej informacji na temat tej funkcji, zapoznaj się z artykułem [Korzystanie z istniejącej bazy danych](how-to-connect-install-existing-database.md).
* W celu zwiększenia bezpieczeństwa Azure AD Connect teraz używać protokołu TLS 1.2 do nawiązywania połączenia z usługą Azure AD w celu synchronizacji katalogów. Poprzednio wartość domyślna to TLS 1.0.
* Po uruchomieniu Azure AD Connect agenta synchronizacji haseł próbuje nawiązać połączenie z dobrze znanym punktem końcowym usługi Azure AD na potrzeby synchronizacji haseł. Po pomyślnym nawiązaniu połączenia zostanie on przekierowany do punktu końcowego określonego dla regionu. Wcześniej Agent synchronizacji haseł buforuje punkt końcowy specyficzny dla regionu, dopóki nie zostanie ponownie uruchomiony. Teraz Agent czyści pamięć podręczną i ponowi próbę z dobrze znanym punktem końcowym, jeśli napotka problem z połączeniem z punktem końcowym specyficznym dla regionu. Ta zmiana zapewnia, że synchronizacja haseł może przełączeć w tryb failover do innego punktu końcowego określonego dla regionu, gdy punkt końcowy specyficzny dla regionu nie jest już dostępny.
* Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Możesz albo (i) samodzielnie utworzyć konto AD DS i podać swoje poświadczenie, aby Azure AD Connect, lub (II) podać poświadczenia administratora przedsiębiorstwa i Azure AD Connect utworzyć konto AD DS. Wcześniej, (i) jest opcją domyślną w Kreatorze Azure AD Connect. Teraz (II) jest opcją domyślną.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano obsługę Microsoft Azure Government chmury i Microsoft Cloud Niemcy.

### <a name="ad-fs-management"></a>Usługi AD FS zarządzania
#### <a name="fixed-issues"></a>Naprawione problemy
* Polecenie cmdlet Initialize-ADSyncNGCKeysWriteBack w module programu PowerShell do przygotowywania usługi AD nieprawidłowo stosowało listy ACL do kontenera rejestracji urządzeń i w związku z tym dziedziczy tylko istniejące uprawnienia.  Ta aktualizacja została zaktualizowana tak, aby konto usługi synchronizacji miało odpowiednie uprawnienia.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Azure AD Connect sprawdzić, czy zadanie logowania do usługi ADFS zostało zaktualizowane, aby sprawdzać logowania do usługi Microsoft Online, a nie tylko pobieranie tokenu z usług AD FS.
* Podczas konfigurowania nowej farmy usług AD FS przy użyciu Azure AD Connect, Strona z prośbą o poświadczenia usług ADFS została przeniesiona, tak aby była teraz wykonywana, zanim użytkownik zostanie poproszony o dostarczenie serwerów usług AD FS i WAP.  Pozwala to Azure AD Connect sprawdzić, czy określone konto ma odpowiednie uprawnienia.
* Podczas uaktualniania Azure AD Connect nie będziemy już kończyć się niepowodzeniem, jeśli aktualizacja zaufania usług AD FS nie powiedzie się.  Jeśli tak się stanie, zostanie wyświetlony odpowiedni komunikat ostrzegawczy, który należy wykonać w celu zresetowania zaufania za pomocą dodatkowego zadania Azure AD Connect.

### <a name="seamless-single-sign-on"></a>Bezproblemowe pojedyncze Sign-On
#### <a name="fixed-issues"></a>Naprawione problemy
* Rozwiązano problem, który spowodował, że Kreator Azure AD Connect zwrócił błąd, jeśli spróbujesz włączyć [bezproblemowe logowanie jednokrotne](how-to-connect-sso.md). Komunikat o błędzie to *"Konfiguracja agenta uwierzytelniania Microsoft Azure AD nie powiodła się".* Ten problem dotyczy istniejących klientów, którzy mogli ręcznie uaktualnić wersję zapoznawczą agentów uwierzytelniania na potrzeby [uwierzytelniania przekazywanego](how-to-connect-sso.md) na podstawie kroków opisanych w tym [artykule](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stan: Lipiec 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem 

* Rozwiązano problem, który spowodował usunięcie reguły synchronizacji poza Box "do usługi AD-User ImmutableId":

  * Problem występuje, gdy Azure AD Connect jest uaktualniana, lub jeśli *Konfiguracja synchronizacji aktualizacji* opcji zadań w Kreatorze Azure AD Connect służy do aktualizacji konfiguracji synchronizacji Azure AD Connect.

  * Ta reguła synchronizacji dotyczy klientów, którzy włączyli [funkcję zakotwiczenia MS-ds-ConsistencyGuid jako źródło](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Ta funkcja została wprowadzona w wersji 1.1.524.0 i po niej. Po usunięciu reguły synchronizacji Azure AD Connect nie może już wypełnić lokalnego atrybutu usługi AD MS-DS-ConsistencyGuid z wartością atrybutu ObjectGuid. Nie zapobiega to aprowizacji nowych użytkowników w usłudze Azure AD.

  * Poprawka zapewnia, że reguła synchronizacji nie zostanie już usunięta podczas uaktualniania lub podczas zmiany konfiguracji, o ile ta funkcja jest włączona. W przypadku istniejących klientów, których dotyczy ten problem, Poprawka gwarantuje również, że reguła synchronizacji zostanie dodana ponownie po uaktualnieniu do tej wersji programu Azure AD Connect.

* Rozwiązano problem, który powoduje, że reguły synchronizacji out-of-Box mają wartość pierwszeństwa mniejszą niż 100:

  * Ogólnie rzecz biorąc wartości pierwszeństwa 0-99 są zarezerwowane dla reguł synchronizacji niestandardowej. Podczas uaktualniania wartości pierwszeństwa dla reguł synchronizacji out-of-Box są aktualizowane w celu uwzględnienia zmian reguły synchronizacji. Przyczyną tego problemu może być przypisana wartość pierwszeństwa reguł synchronizacji, która jest mniejsza niż 100.

  * Poprawka zapobiega występowaniu problemu podczas uaktualniania. Jednak nie przywraca wartości pierwszeństwa dla istniejących klientów, których dotyczy problem. W przyszłości zostanie udostępniona odrębna poprawka, która będzie pomocna w odniesieniu do przywracania.

* Rozwiązano problem polegający na tym, że [ekran filtrowania domeny i jednostki organizacyjnej](how-to-connect-install-custom.md#domain-and-ou-filtering) w Kreatorze Azure AD Connect wyświetla opcję *Synchronizuj wszystkie domeny i jednostki organizacyjne* jako wybrane, nawet jeśli filtrowanie oparte na jednostce organizacyjnej jest włączone.

*   Rozwiązano problem, który spowodował, że [ekran Konfigurowanie partycji katalogu](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) w Synchronization Service Manager, aby zwrócić błąd, jeśli kliknięto przycisk *Odśwież* . Komunikat o błędzie "wystąpił *błąd podczas odświeżania domen: nie można rzutować obiektu typu" System. Collections. ArrayList "na typ" Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. partitionobject. "* Ten błąd występuje, gdy nowa domena usługi AD została dodana do istniejącego lasu usługi AD i próbujesz zaktualizować Azure AD Connect przy użyciu przycisku Odśwież.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcja automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) została rozszerzona w celu obsługi klientów z następującymi konfiguracjami:
  * Włączono funkcję zapisywania zwrotnego urządzeń.
  * Włączono funkcję zapisywania zwrotnego grup.
  * Instalacja nie jest ustawieniami ekspresowymi ani uaktualnieniem narzędzia DirSync.
  * W magazynie Metaverse znajduje się więcej niż 100 000 obiektów.
  * Nawiązujesz połączenie z więcej niż jednym lasem. Instalacja ekspresowa nawiązuje połączenie tylko z jednym lasem.
  * Konto łącznika usługi AD nie jest już domyślnym kontem MSOL_.
  * Serwer jest ustawiony na tryb przejściowy.
  * Włączono funkcję zapisywania zwrotnego użytkownika.

  >[!NOTE]
  >Rozszerzenie zakresu funkcji automatycznego uaktualniania wpływa na klientów z Azure AD Connect build 1.1.105.0 i po nim. Jeśli nie chcesz, aby serwer Azure AD Connect był automatycznie uaktualniany, musisz uruchomić następujące polecenie cmdlet na serwerze Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Aby uzyskać więcej informacji na temat włączania/wyłączania automatycznego uaktualniania, zapoznaj się z artykułem [Azure AD Connect: automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md).
## <a name="115580"></a>1.1.558.0
Stan: nie zostanie wydana. Zmiany w tej kompilacji są zawarte w wersji 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem 

* Rozwiązano problem polegający na tym, że reguła synchronizacji poza ramką "Out to AD-User ImmutableId" do usunięcia, gdy zostanie zaktualizowana konfiguracja filtrowania opartego na jednostce organizacyjnej. Ta reguła synchronizacji jest wymagana dla [funkcji zakotwiczenia MS-ds-ConsistencyGuid jako źródła](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Rozwiązano problem polegający na tym, że [ekran filtrowania domeny i jednostki organizacyjnej](how-to-connect-install-custom.md#domain-and-ou-filtering) w Kreatorze Azure AD Connect wyświetla opcję *Synchronizuj wszystkie domeny i jednostki organizacyjne* jako wybrane, nawet jeśli filtrowanie oparte na jednostce organizacyjnej jest włączone.

*   Rozwiązano problem, który spowodował, że [ekran Konfigurowanie partycji katalogu](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) w Synchronization Service Manager, aby zwrócić błąd, jeśli kliknięto przycisk *Odśwież* . Komunikat o błędzie "wystąpił *błąd podczas odświeżania domen: nie można rzutować obiektu typu" System. Collections. ArrayList "na typ" Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. partitionobject. "* Ten błąd występuje, gdy nowa domena usługi AD została dodana do istniejącego lasu usługi AD i próbujesz zaktualizować Azure AD Connect przy użyciu przycisku Odśwież.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcja automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) została rozszerzona w celu obsługi klientów z następującymi konfiguracjami:
  * Włączono funkcję zapisywania zwrotnego urządzeń.
  * Włączono funkcję zapisywania zwrotnego grup.
  * Instalacja nie jest ustawieniami ekspresowymi ani uaktualnieniem narzędzia DirSync.
  * W magazynie Metaverse znajduje się więcej niż 100 000 obiektów.
  * Nawiązujesz połączenie z więcej niż jednym lasem. Instalacja ekspresowa nawiązuje połączenie tylko z jednym lasem.
  * Konto łącznika usługi AD nie jest już domyślnym kontem MSOL_.
  * Serwer jest ustawiony na tryb przejściowy.
  * Włączono funkcję zapisywania zwrotnego użytkownika.

  >[!NOTE]
  >Rozszerzenie zakresu funkcji automatycznego uaktualniania wpływa na klientów z Azure AD Connect build 1.1.105.0 i po nim. Jeśli nie chcesz, aby serwer Azure AD Connect był automatycznie uaktualniany, musisz uruchomić następujące polecenie cmdlet na serwerze Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Aby uzyskać więcej informacji na temat włączania/wyłączania automatycznego uaktualniania, zapoznaj się z artykułem [Azure AD Connect: automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md).
## <a name="115570"></a>1.1.557.0
Stan: lipiec 2017

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect autoupgrade.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem 
* Rozwiązano problem z poleceniem cmdlet Initialize-ADSyncDomainJoinedComputerSync, które spowodowało zmianę zweryfikowanej domeny skonfigurowanej w istniejącym obiekcie punktu połączenia usługi, nawet jeśli jest ona nadal prawidłową domeną. Ten problem występuje, gdy dzierżawa usługi Azure AD ma więcej niż jedną zweryfikowaną domenę, której można użyć do skonfigurowania punktu połączenia z usługą.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Zapisywanie zwrotne haseł jest teraz dostępne w wersji zapoznawczej w chmurze Microsoft Azure Government i Microsoft Cloud Niemcy. Aby uzyskać więcej informacji na temat obsługi Azure AD Connect różnych wystąpień usługi, zapoznaj się z artykułem [Azure AD Connect: specjalne zagadnienia dotyczące wystąpień](reference-connect-instances.md).

* Polecenie cmdlet Initialize-ADSyncDomainJoinedComputerSync ma teraz nowy opcjonalny parametr o nazwie AzureADDomain. Ten parametr umożliwia określenie, która zweryfikowana domena ma być używana do konfigurowania punktu połączenia z usługą.

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Nazwa agenta wymaganego do uwierzytelniania przekazywanego została zmieniona z *łącznika serwera proxy aplikacji Microsoft Azure AD* na *Microsoft Azure AD Połącz agenta uwierzytelniania*.

* Włączenie uwierzytelniania przekazywanego nie powoduje już domyślnego synchronizowania skrótów haseł.


## <a name="115530"></a>1.1.553.0
Stan: czerwiec 2017

> [!IMPORTANT]
> W tej kompilacji wprowadzono zmiany reguł schematu i synchronizacji. Usługa synchronizacji Azure AD Connect będzie wyzwalać pełne kroki importowania i pełnej synchronizacji po uaktualnieniu. Szczegółowe informacje o zmianach zostały opisane poniżej. Aby tymczasowo odroczyć pełne kroki importowania i pełnej synchronizacji po uaktualnieniu, zapoznaj się z artykułem [jak odroczyć pełną synchronizację po uaktualnieniu](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>
### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Znany problem
* Występuje problem, który ma wpływ na klientów, którzy korzystają z [filtrowania opartego na jednostce organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) za pomocą synchronizacji Azure AD Connect. Po przejściu do [strony filtrowania domeny i jednostki organizacyjnej](how-to-connect-install-custom.md#domain-and-ou-filtering) w Kreatorze Azure AD Connect jest oczekiwane następujące zachowanie:
  * Jeśli filtrowanie na podstawie jednostki organizacyjnej jest włączone, zaznaczona jest opcja **Synchronizuj wybrane domeny i jednostki organizacyjne** .
  * W przeciwnym razie zaznaczona jest opcja **Synchronizuj wszystkie domeny i jednostki organizacyjne** .

Występuje problem polegający na tym, że **opcja Synchronizuj wszystkie domeny i jednostki organizacyjne** jest zawsze zaznaczona po uruchomieniu kreatora.  Dzieje się tak nawet wtedy, gdy wcześniej skonfigurowano filtrowanie na podstawie jednostki organizacyjnej. Przed zapisaniem zmian w konfiguracji Azure AD Connect upewnij się, że wybrano **opcję Synchronizuj wybrane domeny i jednostki organizacyjne** i upewnij się, że wszystkie jednostki organizacyjne, które wymagają synchronizacji, są włączone ponownie. W przeciwnym razie filtrowanie na podstawie jednostki organizacyjnej będzie wyłączone.

#### <a name="fixed-issues"></a>Naprawione problemy

* Rozwiązano problem z funkcją zapisywania zwrotnego haseł, która umożliwia administratorowi usługi Azure AD Resetowanie hasła do lokalnego konta użytkownika uprzywilejowanego usługi AD. Ten problem występuje, gdy Azure AD Connect przyznano uprawnienia resetowania hasła na koncie uprzywilejowanym. Ten problem został rozwiązany w tej wersji programu Azure AD Connect przez nieumożliwienie administratorowi usługi Azure AD resetowania hasła do dowolnego lokalnego konta użytkownika, które ma uprzywilejowane konto usługi AD, chyba że administrator jest właścicielem tego konta. Więcej informacji można znaleźć w [dokumencie Security Advisory 4033453](/security-updates/SecurityAdvisories/2017/4033453).

* Rozwiązano problem związany z funkcją [zakotwiczenia usługi MS-ds-ConsistencyGuid jako źródła,](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) w której Azure AD Connect nie ma zapisywania zwrotnego w lokalnym atrybucie AD MS-ds-ConsistencyGuid. Ten problem występuje, gdy w Azure AD Connect istnieje wiele lokalnych lasów usługi AD, a w *wielu katalogach jest wybrana opcja tożsamości użytkowników* . Gdy taka konfiguracja jest używana, wynikowe reguły synchronizacji nie wypełniają atrybutu sourceAnchorBinary w obiekcie Metaverse. Atrybut sourceAnchorBinary jest używany jako atrybut źródłowy dla atrybutu MS-DS-ConsistencyGuid. W związku z tym nie występuje Zapis zwrotny do atrybutu MS-DSConsistencyGuid. Aby rozwiązać ten problem, następujące reguły synchronizacji zostały zaktualizowane, aby upewnić się, że atrybut sourceAnchorBinary w obiekcie Metaverse jest zawsze wypełniony:
  * W programie z poziomu usługi AD — klasa InetOrgPerson AccountEnabled.xml
  * W programie z poziomu usługi AD — klasa InetOrgPerson Common.xml
  * W programie z usługi AD AccountEnabled.xml użytkownika
  * W programie z usługi AD Common.xml użytkownika
  * W programie z poziomu usługi AD-User Join SOAInAAD.xml

* Wcześniej, nawet jeśli funkcja [zakotwiczenia MS-ds-ConsistencyGuid as Source](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) nie jest włączona, reguła synchronizacji "do usługi AD – użytkownik ImmutableId" jest nadal dodawana do Azure AD Connect. Efekt jest niegroźny i nie powoduje zapisywania zwrotnego atrybutu MS-DS-ConsistencyGuid. Aby uniknąć nieporozumień, dodano logikę, aby upewnić się, że reguła synchronizacji jest dodawana tylko wtedy, gdy ta funkcja jest włączona.

* Rozwiązano problem, który spowodował niepowodzenie synchronizacji skrótów haseł i zdarzenia błędu 611. Ten problem występuje, gdy co najmniej jeden kontroler domeny został usunięty z lokalnej usługi AD. Na koniec każdego cyklu synchronizacji haseł plik cookie synchronizacji wystawiony przez lokalną usługi AD zawiera identyfikatory wywołań usuniętych kontrolerów domeny z wartością USN (numer sekwencji aktualizacji) 0. Menedżer synchronizacji haseł nie może utrwalać pliku cookie synchronizacji zawierającego wartość numeru USN 0 i kończy się niepowodzeniem z powodu błędu 611. W następnym cyklu synchronizacji Menedżer synchronizacji haseł ponownie używa ostatniego utrwalonego pliku cookie synchronizacji, który nie zawiera wartości USN równej 0. Powoduje to, że zmiany hasła zostaną ponownie zsynchronizowane. W przypadku tej poprawki Menedżer synchronizacji haseł zachowuje plik cookie synchronizacji prawidłowo.

* Wcześniej, nawet jeśli automatyczne uaktualnianie zostało wyłączone za pomocą polecenia cmdlet Set-ADSyncAutoUpgrade, proces uaktualniania automatycznego kontynuuje sprawdzanie dostępności uaktualnienia i polega na pobraniu instalatora, aby honorować wyłączenie. W przypadku tej poprawki proces automatycznego uaktualniania nie sprawdza już okresowo uaktualniania. Poprawka jest automatycznie stosowana, gdy Instalator uaktualnienia dla tej Azure AD Connect wersji zostanie wykonany raz.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Wcześniej funkcja [kotwicy MS-ds-ConsistencyGuid as Source](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) była dostępna tylko dla nowych wdrożeń. Teraz jest dostępny dla istniejących wdrożeń. Więcej szczegółów:
  * Aby uzyskać dostęp do tej funkcji, uruchom Kreatora Azure AD Connect i wybierz opcję *Aktualizuj zakotwiczenie źródła* .
  * Ta opcja jest widoczna tylko dla istniejących wdrożeń, które używają parametru objectGuid jako atrybutu sourceAnchor.
  * Podczas konfigurowania opcji Kreator sprawdza poprawność stanu atrybutu MS-DS-ConsistencyGuid w lokalnym Active Directory. Jeśli atrybut nie jest skonfigurowany w żadnym obiekcie użytkownika w katalogu, Kreator użyje właściwości MS-DS-ConsistencyGuid jako atrybutu sourceAnchor. Jeśli atrybut jest skonfigurowany w co najmniej jednym obiekcie użytkownika w katalogu, Kreator końcowy ten atrybut jest używany przez inne aplikacje i nie jest odpowiedni jako atrybut sourceAnchor i nie zezwala na przechodzenie przez zakotwiczenie źródłowe. Jeśli masz pewność, że atrybut nie jest używany przez istniejące aplikacje, musisz skontaktować się z pomocą techniczną, aby uzyskać informacje na temat sposobu pomijania tego błędu.

* Specyficzne dla atrybutu **userCertificate** obiektów urządzeń, Azure AD Connect teraz szuka wartości certyfikatów wymaganych do [połączenia urządzeń przyłączonych do domeny z usługą Azure AD dla systemu Windows 10](../devices/hybrid-azuread-join-plan.md) i wyfiltruje resztę przed synchronizacją do usługi Azure AD. Aby włączyć to zachowanie, Zaktualizowano regułę synchronizacji out-of-Box do usługi AAD-Device Join SOAInAD ".

* Azure AD Connect teraz obsługuje Stornowanie atrybutu **cloudPublicDelegates** usługi Exchange Online do lokalnego atrybutu AD **publicDelegates** . Dzięki temu scenariusz, w którym można przyznać dostęp do skrzynki pocztowej usługi Exchange Online użytkownikom z lokalną skrzynek pocztowych programu Exchange. Aby można było obsłużyć tę funkcję, dodano nową regułę synchronizacji poza Box "do usługi AD — funkcja zapisywania zwrotnego hybrydowego programu Exchange PublicDelegates". Ta reguła synchronizacji jest dodawana tylko do Azure AD Connect, gdy włączono funkcję hybrydową programu Exchange.

* Azure AD Connect teraz obsługuje synchronizowanie atrybutu **altRecipient** z usługi Azure AD. Aby obsłużyć tę zmianę, należy zaktualizować reguły synchronizacji poza ramką w celu uwzględnienia wymaganego przepływu atrybutów:
  * W programie z usługi AD — wymiana użytkownika
  * Do usługi AAD — ExchangeOnline użytkownika

* Atrybut **cloudSOAExchMailbox** w obiekcie Metaverse wskazuje, czy dany użytkownik ma skrzynkę pocztową usługi Exchange Online. Jego definicja została zaktualizowana w celu uwzględnienia dodatkowych RecipientDisplayTypes usługi Exchange Online jako takich urządzeń i skrzynek pocztowych w sali konferencyjnej. Aby włączyć tę zmianę, należy zaktualizować definicję atrybutu cloudSOAExchMailbox, który znajduje się w regule synchronizacji poza ramką "w usłudze AAD — dane hybrydowe programu Exchange użytkownika".

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ... do następujących:

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* Dodano następujący zestaw funkcji zgodnych z X509Certificate2 na potrzeby tworzenia wyrażeń reguł synchronizacji w celu obsługi wartości certyfikatów w atrybucie userCertificate:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * Iscert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Wybierz pozycję
  * CertKeyAlgorithmParams
  * CertHashString
  * Lokalizacja
  * With

* Wprowadzono następujące zmiany schematu, które umożliwiają klientom tworzenie niestandardowych reguł synchronizacji w celu przepływu zasad sAMAccountName, domainNetBios i domainFQDN dla obiektów grup, a także nazwy odróżnionyname dla obiektów użytkownika:

  * Następujące atrybuty zostały dodane do schematu MV:
    * Grupa: AccountName
    * Grupa: domainNetBios
    * Grupa: domainFQDN
    * Osoba: wyróżniająca

  * Następujące atrybuty zostały dodane do schematu łącznika usługi Azure AD:
    * Grupa: nazwy pospolitej onpremisessamaccountname
    * Grupa: system Biosname
    * Grupa: NazwaDomenyDNS
    * Użytkownik: OnPremisesDistinguishedName

* Skrypt poleceń cmdlet ADSyncDomainJoinedComputerSync ma teraz nowy opcjonalny parametr o nazwie AzureEnvironment. Ten parametr służy do określania regionu, w którym jest hostowana odpowiednia dzierżawa Azure Active Directory. Prawidłowe wartości to:
  * AzureCloud (domyślnie)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* Zaktualizowano Edytor reguł synchronizacji w celu użycia sprzężenia (zamiast aprowizacji) jako wartości domyślnej typu łącza podczas tworzenia reguły synchronizacji.

### <a name="ad-fs-management"></a>Zarządzanie AD FS

#### <a name="issues-fixed"></a>Rozwiązano problemy

* Następujące adresy URL to nowe WS-Federation punkty końcowe wprowadzone przez usługę Azure AD w celu zwiększenia odporności na awarie uwierzytelniania i zostaną dodane do lokalnej konfiguracji zaufania jednostki odpowiedzi AD FSowej:
  * https: \/ /ESTS.Login.microsoftonline.com/login.SRF
  * https: \/ /stamp2.Login.microsoftonline.com/login.SRF
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Rozwiązano problem, który spowodował AD FS wygenerowanie nieprawidłowej wartości dla IssuerID. Ten problem występuje w przypadku wielu zweryfikowanych domen w dzierżawie usługi Azure AD, a sufiks domeny atrybutu userPrincipalName używany do generowania żądania IssuerID ma co najmniej 3 poziomy głębokie (na przykład johndoe@us.contoso.com ). Problem został rozwiązany przez zaktualizowanie wyrażenia regularnego używanego przez reguły dotyczące roszczeń.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Wcześniej funkcja zarządzania certyfikatami usług AD FS udostępniona przez Azure AD Connect może być używana tylko z farmami usług ADFS zarządzanymi za pomocą usługi Azure AD Connect. Teraz można użyć tej funkcji z farmą usług AD FS, które nie są zarządzane za pomocą Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Wydanie: 2017 maja

> [!IMPORTANT]
> W tej kompilacji wprowadzono zmiany reguł schematu i synchronizacji. Usługa synchronizacji Azure AD Connect będzie wyzwalać pełne kroki importowania i pełnej synchronizacji po uaktualnieniu. Szczegółowe informacje o zmianach zostały opisane poniżej.
>
>
**Rozwiązano problemy:**

Synchronizacja programu Azure AD Connect

* Rozwiązano problem powodujący automatyczne uaktualnienie na serwerze Azure AD Connect, nawet jeśli klient wyłączył tę funkcję przy użyciu polecenia cmdlet Set-ADSyncAutoUpgrade. W przypadku tej poprawki proces automatycznego uaktualniania na serwerze nadal sprawdza okres uaktualniania, ale pobrany Instalator przestrzega konfiguracji automatycznego uaktualniania.
* Podczas uaktualniania w miejscu usługi DirSync usługa Azure AD Connect tworzy konto usług Azure AD, które będzie używane przez łącznik usługi Azure AD do synchronizowania z usługą Azure AD. Po utworzeniu konta Azure AD Connect uwierzytelniać się w usłudze Azure AD przy użyciu konta. Czasami uwierzytelnianie kończy się niepowodzeniem z powodu przejściowych problemów, które z kolei powodują, że uaktualnienie narzędzia DirSync w miejscu nie powiedzie się z powodu błędu *"Wystąpił błąd podczas wykonywania AAD Sync konfigurowania zadania: AADSTS50034: Aby zalogować się do tej aplikacji, należy dodać konto do katalogu xxx.onmicrosoft.com".* Aby zwiększyć odporność na uaktualnienie narzędzia DirSync, Azure AD Connect teraz ponawia próbę wykonania kroku uwierzytelniania.
* Wystąpił problem z kompilacją 443, który powoduje pomyślne uaktualnienie narzędzia DirSync w miejscu, ale nie są tworzone profile wymagane do synchronizacji katalogów. Logika korygująca jest zawarta w tej kompilacji Azure AD Connect. Podczas uaktualniania klienta do tej kompilacji Azure AD Connect wykrywa brakujące profile uruchamiania i tworzy je.
* Rozwiązano problem powodujący niepowodzenie uruchomienia procesu synchronizacji haseł z IDENTYFIKATORem zdarzenia 6900 i błędem *"element o tym samym kluczu został już dodany"*. Ten problem występuje w przypadku aktualizowania konfiguracji filtrowania jednostki organizacyjnej w celu uwzględnienia partycji konfiguracji usługi AD. Aby rozwiązać ten problem, proces synchronizacji haseł synchronizuje teraz zmiany haseł tylko z partycji domeny usługi AD. Partycje nienależące do domeny, takie jak partycja konfiguracji, są pomijane.
* Podczas instalacji ekspresowej Program Azure AD Connect tworzy lokalne konto AD DS, które będzie używane przez łącznik usługi AD do komunikowania się z lokalną usługą AD. Wcześniej konto jest tworzone z flagą PASSWD_NOTREQD ustawioną dla atrybutu Kontrola konta użytkownika, a na koncie zostanie ustawione hasło losowe. Teraz Azure AD Connect jawnie usuwa flagę PASSWD_NOTREQD po ustawieniu hasła na koncie.
* Rozwiązano problem polegający na tym, że uaktualnienie narzędzia DirSync nie powiodło się z powodu błędu *"zakleszczenie wystąpił w programie SQL Server, który próbuje uzyskać blokadę aplikacji"* , gdy atrybut mailNickname jest znaleziony w lokalnym schemacie usługi AD, ale nie jest powiązany z klasą obiektu użytkownika usługi AD.
* Rozwiązano problem powodujący automatyczne wyłączenie funkcji zapisywania zwrotnego urządzeń, gdy administrator zaktualizuje Azure AD Connect konfigurację synchronizacji za pomocą Kreatora Azure AD Connect. Ten problem jest spowodowany przez kreatora wykonującego sprawdzanie wymagań wstępnych dla istniejącej konfiguracji zapisywania zwrotnego urządzeń w lokalnej usłudze AD i sprawdzanie kończy się niepowodzeniem. Poprawka służy do pomijania sprawdzenia, czy funkcja zapisywania zwrotnego urządzeń została już włączona.
* Aby skonfigurować filtrowanie jednostek organizacyjnych, można użyć Kreatora Azure AD Connect lub Synchronization Service Manager. Wcześniej w przypadku konfigurowania filtrowania jednostki organizacyjnej za pomocą Kreatora Azure AD Connect nowe jednostki organizacyjne utworzone później są uwzględniane w przypadku synchronizacji katalogów. Jeśli nie chcesz uwzględniać nowych jednostek organizacyjnych, musisz skonfigurować filtrowanie jednostek organizacyjnych przy użyciu Synchronization Service Manager. Teraz można osiągnąć takie samo zachowanie przy użyciu kreatora Azure AD Connect.
* Rozwiązano problem polegający na tym, że procedury składowane wymagane przez Azure AD Connect zostaną utworzone w schemacie instalacji administratora, a nie w schemacie dbo.
* Rozwiązano problem powodujący pominięcie atrybutu TrackingId zwróconego przez usługę Azure AD w dziennikach zdarzeń serwera Azure AD Connect. Ten problem występuje, gdy Azure AD Connect otrzymuje komunikat przekierowania z usługi Azure AD, a Azure AD Connect nie może nawiązać połączenia z podanym punktem końcowym. TrackingId jest używany przez inżynierów pomocy technicznej do skorelowania z dziennikami po stronie usługi podczas rozwiązywania problemów.
* Gdy Azure AD Connect otrzymuje błąd LargeObject z usługi Azure AD, Azure AD Connect generuje zdarzenie z EventID 6941 i komunikat *"obiekt aprowizacji jest zbyt duży. Przytnij liczbę wartości atrybutów dla tego obiektu ".* W tym samym czasie Azure AD Connect również generuje mylące zdarzenie z EventID 6900 i komunikatem *"Microsoft. online. ProvisionRetryException: nie można skomunikować się z usługą Windows Azure Active Directory."* Aby zminimalizować pomyłkę, Azure AD Connect nie generować już ostatniego zdarzenia, gdy zostanie odebrany błąd Dużegoobject.
* Rozwiązano problem, który powoduje, że Synchronization Service Manager przestaje odpowiadać podczas próby zaktualizowania konfiguracji dla ogólnego łącznika LDAP.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Zmiany reguły synchronizacji — zaimplementowano następujące zmiany reguły synchronizacji:
  * Zaktualizowano domyślny zestaw reguł synchronizacji, aby nie eksportować atrybutów **userCertificate** i **userSMIMECertificate** , jeśli atrybuty mają więcej niż 15 wartości.
  * Atrybuty AD **IDPracownika** i **msExchBypassModerationLink** są teraz dołączone do domyślnego zestawu reguł synchronizacji.
  * **Zdjęcie** atrybutu usługi AD zostało usunięte z domyślnego zestawu reguł synchronizacji.
  * Dodano **preferredDataLocation** do schematu Metaverse i schematu łącznika usługi Azure AD. Klienci, którzy chcą zaktualizować dowolne atrybuty w usłudze Azure AD, mogą zaimplementować niestandardowe reguły synchronizacji, aby to zrobić. 
  * Dodano **użytkownika** do schematu Metaverse i schematu łącznika usługi Azure AD. Klienci, którzy chcą zaktualizować dowolne atrybuty w usłudze Azure AD, mogą zaimplementować niestandardowe reguły synchronizacji, aby to zrobić.

* Azure AD Connect teraz automatycznie włącza użycie atrybutu ConsistencyGuid jako atrybutu zakotwiczenia źródła dla lokalnych obiektów usługi AD. Ponadto Azure AD Connect wypełnia atrybut ConsistencyGuid wartością atrybutu objectGuid, jeśli jest pusty. Ta funkcja ma zastosowanie tylko do nowego wdrożenia. Aby dowiedzieć się więcej o tej funkcji, zapoznaj się z sekcją artykułu [Azure AD Connect: koncepcje dotyczące projektowania — korzystając z MS-ds-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Dodano nowe polecenie cmdlet rozwiązywania problemów Invoke-ADSyncDiagnostics, aby pomóc w zdiagnozowaniu problemów związanych z synchronizacją skrótów haseł. Aby uzyskać informacje o korzystaniu z polecenia cmdlet, zobacz artykuł [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect teraz obsługuje synchronizowanie obiektów folderów publicznych Mail-Enabled z lokalnej usługi AD do usługi Azure AD. Funkcję można włączyć za pomocą Kreatora Azure AD Connect w obszarze funkcje opcjonalne. Aby dowiedzieć się więcej na temat tej funkcji, zapoznaj się z artykułem [Obsługa blokowania na podstawie katalogów pakietu Office 365 dla folderów publicznych włączonych w ramach poczty lokalnej](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218).
* Azure AD Connect wymaga konta AD DS do synchronizowania z lokalnej usługi AD. Wcześniej, jeśli zainstalowano Azure AD Connect przy użyciu trybu Express, można podać poświadczenia konta administratora przedsiębiorstwa i Azure AD Connect utworzyć konto AD DS wymagane. Jednak w przypadku instalacji niestandardowej i dodania lasów do istniejącego wdrożenia wymagane jest podanie konta AD DS. Teraz można także podać poświadczenia konta administratora przedsiębiorstwa podczas instalacji niestandardowej i pozwolić Azure AD Connect utworzyć wymagane konto AD DS.
* Azure AD Connect teraz obsługuje program SQL AOA. Przed zainstalowaniem Azure AD Connect należy włączyć funkcję SQL AOA. Podczas instalacji program Azure AD Connect wykrywa, czy podane wystąpienie SQL jest włączone dla usługi SQL AOA, czy nie. Jeśli jest włączona funkcja SQL AOA, Azure AD Connect więcej, jeśli SQL AOA jest skonfigurowany do korzystania z replikacji synchronicznej lub asynchronicznej. Podczas konfigurowania odbiornika grupy dostępności zaleca się ustawienie wartości 0 dla właściwości RegisterAllProvidersIP. To zalecenie wynika z faktu, że Azure AD Connect obecnie używa SQL Native Client do nawiązywania połączenia z serwerem SQL, a SQL Native Client nie obsługuje użycia właściwości MultiSubNetFailover.
* Jeśli używasz LocalDB jako bazy danych dla serwera Azure AD Connect i osiągnął limit 10 GB, usługa synchronizacji nie zostanie już uruchomiona. Wcześniej należy wykonać operację ShrinkDatabase na LocalDB w celu odszukania wystarczającej ilości miejsca w bazie danych, aby można było uruchomić usługę synchronizacji. Następnie można użyć Synchronization Service Manager, aby usunąć historię przebiegu w celu Odbierz więcej przestrzeni bazy danych. Teraz można użyć polecenia cmdlet Start-ADSyncPurgeRunHistory, aby przeczyścić dane historii uruchamiania z LocalDB w celu Odbierz miejsce na bazę danych. Dodatkowo to polecenie cmdlet obsługuje tryb offline (przez określenie parametru-offline), którego można użyć, gdy usługa synchronizacji nie jest uruchomiona. Uwaga: tryb offline można używać tylko wtedy, gdy usługa synchronizacji nie jest uruchomiona, a użyta baza danych jest LocalDB.
* Aby zmniejszyć ilość wymaganego miejsca do magazynowania, Azure AD Connect teraz kompresuje szczegóły błędu synchronizacji przed ich zapisaniem w bazach danych LocalDB/SQL. Podczas uaktualniania ze starszej wersji Azure AD Connect do tej wersji, Azure AD Connect wykonuje jednorazową kompresję istniejących szczegółów błędu synchronizacji.
* Wcześniej, po zaktualizowaniu konfiguracji filtrowania jednostek organizacyjnych, należy ręcznie uruchomić pełny import, aby upewnić się, że istniejące obiekty są prawidłowo dołączone/wykluczone z synchronizacji katalogów. Teraz Azure AD Connect automatycznie wyzwala pełny import podczas następnego cyklu synchronizacji. Ponadto pełny import jest stosowany tylko do łączników usługi AD, których dotyczy ta aktualizacja. Uwaga: to ulepszenie dotyczy aktualizacji filtrowania jednostek organizacyjnych, które są wykonywane tylko przy użyciu kreatora Azure AD Connect. Nie ma zastosowania do aktualizacji filtrowania jednostki organizacyjnej wykonanej przy użyciu Synchronization Service Manager.
* Wcześniej filtrowanie oparte na grupach obsługuje tylko użytkowników, grupy i obiekty kontaktów. Teraz filtrowanie oparte na grupach obsługuje również obiekty komputerów.
* Wcześniej można usunąć dane obszaru łącznika bez wyłączania usługi Azure AD Connect Sync Scheduler. Teraz Synchronization Service Manager blokuje usuwanie danych obszaru łącznika, jeśli wykryje, że harmonogram jest włączony. Ponadto zostanie zwrócone ostrzeżenie informujące klientów o potencjalnym utracie danych, jeśli dane dotyczące miejsca łącznika zostaną usunięte.
* Wcześniej należy wyłączyć transkrypcję programu PowerShell dla Kreatora Azure AD Connect, aby działał poprawnie. Ten problem został częściowo rozwiązany. Można włączyć transkrypcję programu PowerShell, jeśli używasz Kreatora Azure AD Connect do zarządzania konfiguracją synchronizacji. Aby zarządzać konfiguracją usług ADFS, należy wyłączyć Azure AD Connect transkrypcję programu PowerShell.



## <a name="114860"></a>1.1.486.0
Opublikowano: kwiecień 2017

**Rozwiązano problemy:**
* Rozwiązano problem polegający na tym, że Azure AD Connect nie zostanie pomyślnie zainstalowana w zlokalizowanej wersji systemu Windows Server.

## <a name="114840"></a>1.1.484.0
Opublikowano: kwiecień 2017

**Znane problemy:**

* Ta wersja Azure AD Connect nie zostanie pomyślnie zainstalowana, jeśli spełnione są następujące warunki:
   1. Wykonujesz uaktualnienie z narzędzia DirSync w miejscu lub nową instalację Azure AD Connect.
   2. Używasz zlokalizowanej wersji systemu Windows Server, w której nazwa wbudowanej grupy administratorów na serwerze nie jest "Administratorzy".
   3. Korzystasz z domyślnego SQL Server 2012 Express LocalDB z systemem Azure AD Connect zamiast udostępniać własny pełny kod SQL.

**Rozwiązano problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem polegający na tym, że harmonogram synchronizacji pomija cały krok synchronizacji, jeśli co najmniej jeden łącznik nie ma profilu uruchamiania dla tego kroku synchronizacji. Na przykład ręcznie dodano łącznik przy użyciu Synchronization Service Manager bez tworzenia dla niego profilu przebiegu importu różnicowego. Ta poprawka zapewnia, że harmonogram synchronizacji kontynuuje wykonywanie importu różnicowego dla innych łączników.
* Rozwiązano problem polegający na tym, że usługa synchronizacji natychmiast zatrzyma przetwarzanie profilu przebiegu, gdy wystąpi problem z jednym z kroków uruchomienia. Ta poprawka zapewnia, że usługa synchronizacji pomija ten krok uruchamiania i kontynuuje przetwarzanie reszty. Na przykład masz profil przebiegu importu różnicowego dla łącznika usługi AD z wieloma krokami uruchamiania (jeden dla każdej lokalnej domeny usługi AD). Usługa synchronizacji uruchomi Importowanie różnicowe z innymi domenami usługi AD, nawet jeśli jedna z nich ma problemy z łącznością sieciową.
* Rozwiązano problem powodujący pominięcie aktualizacji łącznika usługi Azure AD podczas automatycznego uaktualniania.
* Rozwiązano problem, który powoduje, że Azure AD Connect nieprawidłowo ustalić, czy serwer jest kontrolerem domeny podczas instalacji, co z kolei powoduje Niepowodzenie uaktualnienia narzędzia DirSync.
* Rozwiązano problem polegający na tym, że uaktualnienie narzędzia DirSync w miejscu nie powoduje utworzenia żadnego profilu uruchamiania dla łącznika usługi Azure AD.
* Rozwiązano problem polegający na tym, że interfejs użytkownika Synchronization Service Manager przestaje odpowiadać podczas próby skonfigurowania ogólnego łącznika LDAP.

Zarządzanie AD FS
* Rozwiązano problem polegający na tym, że Kreator Azure AD Connect kończy się niepowodzeniem, jeśli węzeł podstawowy AD FS został przeniesiony na inny serwer.

Logowanie jednokrotne na komputerze
* Rozwiązano problem w Kreatorze Azure AD Connect, w którym ekran Sign-In nie pozwala na włączenie funkcji logowania jednokrotnego na komputerze, jeśli wybrano opcję Synchronizacja haseł jako Sign-In opcji podczas nowej instalacji.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Azure AD Connect Sync obsługuje teraz użycie konta usługi wirtualnej, zarządzanego konta usługi i konta usługi zarządzanego przez grupę jako konta usługi. Dotyczy to tylko nowej instalacji Azure AD Connect. Podczas instalowania Azure AD Connect:
    * Domyślnie Kreator Azure AD Connect utworzy konto usługi wirtualnej i użyje go jako konta usługi.
    * Jeśli instalujesz program na kontrolerze domeny, Azure AD Connect powraca do poprzedniego zachowania, w którym zostanie utworzone konto użytkownika domeny i użyje go jako konta usługi.
    * Zachowanie domyślne można przesłonić, podając jedną z następujących wartości:
      * Konto usługi zarządzane przez grupę
      * Zarządzane konto usługi
      * Konto użytkownika domeny
      * Konto użytkownika lokalnego
* Wcześniej, po uaktualnieniu do nowej kompilacji Azure AD Connect zawierającej łączniki aktualizacja lub synchronizacja reguł, Azure AD Connect wyzwoli pełny cykl synchronizacji. Teraz Azure AD Connect selektywnie wyzwalać pełny krok importowania tylko dla łączników z aktualizacją, a krok pełnej synchronizacji dotyczy tylko łączników z regułą synchronizacji.
* Wcześniej próg usuwania eksportu dotyczy wyłącznie eksportów wyzwalanych za pośrednictwem harmonogramu synchronizacji. Teraz funkcja zostaje rozszerzona, aby uwzględnić eksporty ręcznie wyzwalane przez klienta przy użyciu Synchronization Service Manager.
* W ramach dzierżawy usługi Azure AD istnieje konfiguracja usługi, która wskazuje, czy funkcja synchronizacji haseł jest włączona dla Twojej dzierżawy. Wcześniej konfiguracja usługi może być niepoprawnie skonfigurowana przez Azure AD Connect, gdy istnieje aktywny i przejściowy serwer. Teraz Azure AD Connect podejmie próbę zachowania spójności konfiguracji usługi tylko z aktywnym serwerem Azure AD Connect.
* Kreator Azure AD Connect wykrywa teraz i zwraca ostrzeżenie, jeśli lokalna usługa AD nie ma włączonego Kosza usługi AD.
* Wcześniej eksport do usługi Azure AD kończy się niepowodzeniem, jeśli łączny rozmiar obiektów w partii przekracza określony próg. Teraz usługa synchronizacji podejmie ponowną próbę wysłania obiektów w oddzielnych mniejszych partiach, jeśli wystąpił problem.
* Aplikacja do zarządzania kluczami usługi synchronizacji została usunięta z menu Start systemu Windows. Zarządzanie kluczami szyfrowania będzie nadal obsługiwane za pośrednictwem interfejsu wiersza polecenia przy użyciu miiskmu.exe. Aby uzyskać informacje dotyczące zarządzania kluczem szyfrowania, zapoznaj się z artykułem " [porzucanie klucza szyfrowania Azure AD Connect Sync](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key)".
* Wcześniej w przypadku zmiany hasła do konta usługi synchronizacji Azure AD Connect nie będzie można uruchomić usługi synchronizacji, dopóki nie zostanie porzucony klucz szyfrowania i ponownie zainicjowano hasło konta usługi synchronizacji Azure AD Connect. Teraz ten proces nie jest już wymagany.

Logowanie jednokrotne na komputerze

* Kreator Azure AD Connect nie wymaga już otwarcia portu 9090 w sieci podczas konfigurowania uwierzytelniania przekazywanego i logowania jednokrotnego na pulpicie. Wymagany jest tylko port 443.

## <a name="114430"></a>wersji 1.1.443.0
Wydanie: marzec 2017

**Rozwiązano problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem, który powoduje niepowodzenie działania kreatora Azure AD Connect, jeśli nazwa wyświetlana łącznika usługi Azure AD nie zawiera początkowej domeny onmicrosoft.com przypisanej do dzierżawy usługi Azure AD.
* Rozwiązano problem, który powoduje niepowodzenie działania kreatora Azure AD Connect podczas tworzenia połączenia z usługą SQL Database, gdy hasło konta usługi synchronizacji zawiera znaki specjalne, takie jak apostrof, dwukropek i spacja.
* Rozwiązano problem, który powoduje, że wystąpił błąd "obraz ma kotwicę inną niż obraz" na serwerze Azure AD Connect w trybie przejściowym po tymczasowym wykluczeniu lokalnego obiektu usługi AD z synchronizacji, a następnie dołączeniu go ponownie do synchronizacji.
* Rozwiązano problem, który powoduje błąd "obiekt znajdujący się w nazwie wyróżniającej jest Fantomem" na serwerze Azure AD Connect w trybie przejściowym po tymczasowym wykluczeniu lokalnego obiektu usługi AD z synchronizacji, a następnie dołączeniu go ponownie do synchronizacji.

Zarządzanie AD FS
* Rozwiązano problem polegający na tym, że Kreator Azure AD Connect nie zaktualizuje AD FS konfiguracji i nie ustawił odpowiednich oświadczeń dla zaufania jednostki uzależnionej po skonfigurowaniu identyfikatora logowania alternatywnego.
* Rozwiązano problem polegający na tym, że Kreator Azure AD Connect nie może prawidłowo obsłużyć serwerów AD FS, których konta usług są skonfigurowane przy użyciu formatu userPrincipalName zamiast formatu sAMAccountName.

Uwierzytelnianie przekazywane
* Rozwiązano problem powodujący niepowodzenie działania kreatora Azure AD Connect w przypadku wybrania uwierzytelniania przekazywanego, ale rejestracja łącznika nie powiedzie się.
* Rozwiązano problem, który powoduje, że Kreator Azure AD Connect obejść sprawdzanie poprawności przy użyciu metody logowania wybranej podczas włączania funkcji logowania jednokrotnego na komputerze.

Resetowanie hasła
* Rozwiązano problem, który może spowodować, że serwer usługi Azure Azure AD Connect nie spróbuje ponownie nawiązać połączenia, jeśli połączenie zostało przerwane przez zaporę lub serwer proxy.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Polecenie cmdlet Get-ADSyncScheduler teraz zwraca nową właściwość Boolean o nazwie SyncCycleInProgress. Jeśli zwracana wartość to true, oznacza to, że cykl synchronizacji jest zaplanowany w toku.
* Folder docelowy do przechowywania Azure AD Connect instalacji i dzienników instalacji został przeniesiony z%localappdata%\AADConnect do%programdata%\AADConnect w celu usprawnienia ułatwienia dostępu do plików dziennika.

Zarządzanie AD FS
* Dodano obsługę aktualizowania certyfikatu TLS/SSL farmy AD FS.
* Dodano obsługę zarządzania AD FS 2016.
* Podczas instalacji AD FS możesz teraz określić istniejące gMSA (konto usługi zarządzane przez grupę).
* Teraz można skonfigurować SHA-256 jako algorytm wyznaczania wartości skrótu podpisu dla zaufania jednostki uzależnionej usługi Azure AD.

Resetowanie hasła
* Wprowadzono ulepszenia umożliwiające produktowi działanie w środowiskach z bardziej rygorystycznymi regułami zapory.
* Zwiększona niezawodność połączeń do Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Wydanie: grudzień 2016

**Rozwiązano problem:**

* Rozwiązano problem polegający na tym, że w tej kompilacji brakuje reguły issuerid dla Active Directory Federation Services (AD FS).

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect autoupgrade.
## <a name="113710"></a>1.1.371.0
Wydanie: grudzień 2016

**Znany problem:**

* W tej kompilacji brakuje reguły issuerid dla AD FS. Reguła żądania issuerid jest wymagana, jeśli federowanie wiele domen z Azure Active Directory (Azure AD). Jeśli używasz Azure AD Connect do zarządzania wdrożeniem lokalnym AD FS, uaktualnienie do tej kompilacji spowoduje usunięcie istniejącej reguły issuerid z konfiguracji AD FS. Problem można obejść, dodając regułę "issuerid" po zakończeniu instalacji/uaktualniania. Aby uzyskać szczegółowe informacje na temat dodawania reguły "issuerid", zapoznaj się z tym artykułem na temat [obsługi wielu domen w usłudze federowanie z usługą Azure AD](how-to-connect-install-multiple-domains.md).

**Rozwiązano problem:**

* Jeśli port 9090 nie jest otwarty dla połączenia wychodzącego, instalacja Azure AD Connect lub uaktualnienie nie powiedzie się.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect autoupgrade.
## <a name="113700"></a>1.1.370.0
Wydanie: grudzień 2016

**Znane problemy:**

* W tej kompilacji brakuje reguły issuerid dla AD FS. Reguła żądania issuerid jest wymagana, jeśli federowanie wiele domen z usługą Azure AD. Jeśli używasz Azure AD Connect do zarządzania wdrożeniem lokalnym AD FS, uaktualnienie do tej kompilacji spowoduje usunięcie istniejącej reguły issuerid z konfiguracji AD FS. Aby obejść ten problem, Dodaj regułę "issuerid" po zakończeniu instalacji/uaktualniania. Aby uzyskać szczegółowe informacje na temat dodawania reguły roszczeń issuerid, zapoznaj się z tym artykułem na temat [obsługi usługi Azure AD w wielu domenach](how-to-connect-install-multiple-domains.md).
* Aby ukończyć instalację, port 9090 musi być otwarty w ruchu wychodzącym.

**Nowe funkcje:**

* Uwierzytelnianie przekazywane (wersja zapoznawcza).

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect autoupgrade.
## <a name="113430"></a>1.1.343.0
Wydanie: listopad 2016

**Znany problem:**

* W tej kompilacji brakuje reguły issuerid dla AD FS. Reguła żądania issuerid jest wymagana, jeśli federowanie wiele domen z usługą Azure AD. Jeśli używasz Azure AD Connect do zarządzania wdrożeniem lokalnym AD FS, uaktualnienie do tej kompilacji spowoduje usunięcie istniejącej reguły issuerid z konfiguracji AD FS. Aby obejść ten problem, Dodaj regułę "issuerid" po zakończeniu instalacji/uaktualniania. Aby uzyskać szczegółowe informacje na temat dodawania reguły roszczeń issuerid, zapoznaj się z tym artykułem na temat [obsługi usługi Azure AD w wielu domenach](how-to-connect-install-multiple-domains.md).

**Rozwiązano problemy:**

* Czasami zainstalowanie Azure AD Connect nie powiedzie się, ponieważ nie można utworzyć konta usługi lokalnej, którego hasło spełnia poziom złożoności określony przez zasady haseł w organizacji.
* Rozwiązano problem polegający na tym, że reguły dołączania nie są ponownie oceniane, gdy obiekt w przestrzeni łącznika jednocześnie staje się poza zakresem dla jednej reguły sprzężenia i staje się zakresem innym. Taka sytuacja może wystąpić, jeśli masz co najmniej dwie reguły sprzężenia, których warunki sprzężenia wykluczają się wzajemnie.
* Rozwiązano problem polegający na tym, że reguły synchronizacji ruchu przychodzącego (z usługi Azure AD), które nie zawierają reguł sprzężenia, nie są przetwarzane, jeśli mają niższe wartości pierwszeństwa niż te zawierające reguły sprzężenia.

**Ulepszenia:**

* Dodano obsługę instalowania Azure AD Connect w systemie Windows Server 2016 Standard lub nowszym.
* Dodano obsługę SQL Server 2016 jako zdalnej bazy danych dla Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Wydanie: sierpień 2016

**Rozwiązano problemy:**

* Zmiany interwału synchronizacji nie są wykonywane do momentu zakończenia następnego cyklu synchronizacji.
* Kreator Azure AD Connect nie akceptuje konta usługi Azure AD, którego nazwa użytkownika zaczyna się od znaku podkreślenia ( \_ ).
* Kreator Azure AD Connect nie może uwierzytelnić konta usługi Azure AD, jeśli hasło konta zawiera zbyt wiele znaków specjalnych. Komunikat o błędzie "nie można zweryfikować poświadczeń. Wystąpił nieoczekiwany błąd ". .
* Odinstalowanie serwera tymczasowego powoduje wyłączenie synchronizacji haseł w dzierżawie usługi Azure AD i powoduje niepowodzenie synchronizacji haseł z aktywnym serwerem.
* Synchronizacja haseł kończy się niepowodzeniem w przypadku nietypowych przypadków, gdy użytkownik nie przechowuje skrótu hasła.
* Gdy na serwerze Azure AD Connect jest włączony tryb przejściowy, funkcja zapisywania zwrotnego haseł nie jest tymczasowo wyłączona.
* Kreator Azure AD Connect nie pokazuje rzeczywistej synchronizacji haseł i konfiguracji zapisywania zwrotnego haseł, gdy serwer jest w trybie przejściowym. Są one zawsze wyświetlane jako wyłączone.
* Zmiany konfiguracji synchronizacji haseł i zapisywania zwrotnego haseł nie są utrwalane przez kreatora Azure AD Connect, gdy serwer jest w trybie przejściowym.

**Ulepszenia:**

* Zaktualizowano polecenie cmdlet Start-ADSyncSyncCycle, aby wskazać, czy można pomyślnie uruchomić nowy cykl synchronizacji, czy nie.
* Dodano polecenie cmdlet Stop-ADSyncSyncCycle, aby przerwać cykl synchronizacji i operację, które są obecnie w toku.
* Zaktualizowano polecenie cmdlet Stop-ADSyncScheduler, aby przerwać cykl synchronizacji i operację, które są obecnie w toku.
* Podczas konfigurowania [rozszerzeń katalogów](how-to-connect-sync-feature-directory-extensions.md) w Kreatorze Azure AD Connect można teraz wybrać atrybut usługi Azure AD typu "teletex String".

## <a name="111890"></a>1.1.189.0
Wydanie: czerwiec 2016

**Rozwiązano problemy i ulepszenia:**

* Azure AD Connect można teraz zainstalować na serwerze zgodnym ze standardem FIPS.
  * W przypadku synchronizacji haseł Zobacz [Synchronizacja skrótów haseł i FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Rozwiązano problem polegający na tym, że nie można rozpoznać nazwy NetBIOS do nazwy FQDN w łączniku Active Directory.

## <a name="111800"></a>1.1.180.0
Wydanie: 2016 maja

**Nowe funkcje:**

* Ostrzega i pomaga weryfikować domeny, jeśli nie zostało to zrobione przed uruchomieniem Azure AD Connect.
* Dodano obsługę [Microsoft Cloud Niemiec](reference-connect-instances.md#microsoft-cloud-germany).
* Dodano obsługę najnowszej Microsoft Azure Government infrastruktury [chmurowej](reference-connect-instances.md#microsoft-azure-government) z nowymi wymaganiami dotyczącymi adresów URL.

**Rozwiązano problemy i ulepszenia:**

* Dodano filtrowanie do edytora reguł synchronizacji, aby ułatwić znajdowanie reguł synchronizacji.
* Zwiększona wydajność podczas usuwania obszaru łącznika.
* Rozwiązano problem, gdy ten sam obiekt został usunięty i dodany do tego samego przebiegu (nazywanego usuwaniem/dodawaniem).
* Wyłączona reguła synchronizacji nie pozwala już ponownie włączyć uwzględnionych obiektów i atrybutów podczas uaktualniania lub odświeżania schematu katalogu.

## <a name="111300"></a>1.1.130.0
Opublikowano: kwiecień 2016

**Nowe funkcje:**

* Dodano obsługę atrybutów wielowartościowych do [rozszerzeń katalogów](how-to-connect-sync-feature-directory-extensions.md).
* Dodano obsługę większej liczby wariantów konfiguracji, które mają być uważane za zakwalifikowanie [się do uaktualnienia](how-to-connect-install-automatic-upgrade.md) .
* Dodano kilka poleceń cmdlet dla [harmonogramu niestandardowego](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Wydanie: marzec 2016

**Rozwiązano problemy:**

* Upewnij się, że nie można użyć instalacji ekspresowej w systemie Windows Server 2008 (pre-R2), ponieważ synchronizacja haseł nie jest obsługiwana w tym systemie operacyjnym.
* Uaktualnianie z narzędzia DirSync z konfiguracją filtru niestandardowego nie zadziałało zgodnie z oczekiwaniami.
* Podczas uaktualniania do nowszej wersji i nie ma zmian w konfiguracji, nie należy planować pełnego importu/synchronizacji.

## <a name="111100"></a>1.1.110.0
Wydanie: luty 2016

**Rozwiązano problemy:**

* Uaktualnianie z wcześniejszych wersji nie działa, jeśli instalacja nie znajduje się w domyślnym folderze C:\Program Files.
* Po zainstalowaniu i wyczyszczeniu **procedury uruchamiania procesu synchronizacji** na końcu Kreatora instalacji, uruchomienie Kreatora instalacji powoduje, że harmonogram nie zostanie włączony.
* Harmonogram nie działa zgodnie z oczekiwaniami na serwerach, w których format daty/godziny US-EN nie jest używany. Zostanie również zablokowany, `Get-ADSyncScheduler` aby zwracał poprawne czasy.
* Jeśli zainstalowano wcześniejszą wersję Azure AD Connect z AD FS jako opcję logowania i uaktualnieniem, nie można ponownie uruchomić Kreatora instalacji.

## <a name="111050"></a>1.1.105.0
Wydanie: luty 2016

**Nowe funkcje:**

* Funkcja [automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) dla klientów ustawień ekspresowych.
* Obsługa administratora globalnego za pomocą usługi Azure AD Multi-Factor Authentication i Privileged Identity Management w Kreatorze instalacji.
  * Musisz zezwolić serwerowi proxy na Zezwalanie na ruch do https://secure.aadcdn.microsoftonline-p.com , jeśli używasz Multi-Factor Authentication.
  * Musisz dodać https://secure.aadcdn.microsoftonline-p.com do listy zaufanych witryn, aby Multi-Factor Authentication prawidłowo zadziałała.
* Zezwól na zmianę metody logowania użytkownika po początkowej instalacji.
* Zezwalaj na [filtrowanie domen i jednostek organizacyjnych](how-to-connect-install-custom.md#domain-and-ou-filtering) w Kreatorze instalacji. Umożliwia to również łączenie z lasami, w których nie wszystkie domeny są dostępne.
* Usługa [Scheduler](how-to-connect-sync-feature-scheduler.md) jest wbudowana w aparat synchronizacji.

**Funkcje promowane z wersji zapoznawczej do GA:**

* [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md).
* [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md).

**Nowe funkcje w wersji zapoznawczej:**

* Nowy domyślny interwał cyklu synchronizacji to 30 minut. Używane przez trzy godziny dla wszystkich wcześniejszych wersji. Dodaje obsługę zmiany zachowania usługi [Scheduler](how-to-connect-sync-feature-scheduler.md) .

**Rozwiązano problemy:**

* Strona Weryfikowanie domen DNS nie zawsze rozpoznaje domen.
* Wyświetlany jest komunikat z prośbą o poświadczenia administratora domeny podczas konfigurowania AD FS.
* Lokalne konta usługi AD nie są rozpoznawane przez Kreatora instalacji, jeśli znajdują się w domenie z innym drzewem DNS niż domena główna.

## <a name="1091310"></a>1.0.9131.0
Wydanie: grudzień 2015

**Rozwiązano problemy:**

* Synchronizacja haseł może nie działać w przypadku zmiany haseł w programie Active Directory Domain Services (AD DS), ale działa po ustawieniu hasła.
* W przypadku serwera proxy uwierzytelnianie w usłudze Azure AD może zakończyć się niepowodzeniem podczas instalacji lub Jeśli uaktualnienie zostało anulowane na stronie konfiguracji.
* Aktualizowanie z wcześniejszej wersji Azure AD Connect z wystąpieniem pełnej SQL Server kończy się niepowodzeniem, jeśli nie jesteś administratorem systemu SQL Server.
* Aktualizacja z wcześniejszej wersji Azure AD Connect ze zdalną SQL Server pokazuje błąd "nie można uzyskać dostępu do bazy danych SQL ADSync".

## <a name="1091250"></a>1.0.9125.0
Wydanie: listopad 2015

**Nowe funkcje:**

* Można ponownie skonfigurować AD FS do zaufania usługi Azure AD.
* Można odświeżyć schemat Active Directory i ponownie wygenerować reguły synchronizacji.
* Można wyłączyć regułę synchronizacji.
* Można zdefiniować "AuthoritativeNull" jako nowy literał w regule synchronizacji.

**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md).
* Obsługa synchronizacji haseł [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) .

**Nowy obsługiwany scenariusz:**

* Obsługuje wiele lokalnych organizacji programu Exchange. Aby uzyskać więcej informacji, zobacz [wdrażanie hybrydowe z wieloma lasami Active Directory](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Rozwiązano problemy:**

* Problemy z synchronizacją hasła:
  * Obiekt przeniesiony z poza zakresem do zakresu w zakresie nie zostanie zsynchronizowany z jego hasłem. Obejmuje to zarówno filtrowanie jednostek organizacyjnych, jak i atrybutów.
  * Wybranie nowej jednostki organizacyjnej do uwzględnienia w synchronizacji nie wymaga pełnej synchronizacji haseł.
  * Gdy wyłączony użytkownik jest włączony, hasło nie jest synchronizowane.
  * Kolejka ponawiania hasła jest nieskończona, a poprzedni limit 5 000 obiektów do wycofania został usunięty.
* Nie można nawiązać połączenia z Active Directory przy użyciu poziomu funkcjonalności lasu systemu Windows Server 2016.
* Nie można zmienić grupy, która jest używana do filtrowania grup po początkowej instalacji.
* Program nie tworzy już nowego profilu użytkownika na serwerze Azure AD Connect dla każdego użytkownika wykonującego zmianę hasła przy włączonej funkcji zapisywania zwrotnego haseł.
* Nie można użyć wartości długich liczb całkowitych w zakresach reguł synchronizacji.
* Pole wyboru "zapisywanie zwrotne urządzeń" pozostanie wyłączone, jeśli istnieją nieosiągalne kontrolery domeny.

## <a name="1086670"></a>1.0.8667.0
Wydanie: Sierpień 2015

**Nowe funkcje:**

* Kreator instalacji Azure AD Connect jest teraz zlokalizowany dla wszystkich języków systemu Windows Server.
* Dodano obsługę odblokowywania konta podczas korzystania z funkcji zarządzania hasłami usługi Azure AD.

**Rozwiązano problemy:**

* Kreator instalacji Azure AD Connect ulega awarii, jeśli inny użytkownik kontynuuje instalację zamiast osoby, która najpierw uruchomiła instalację.
* Jeśli poprzednia Dezinstalacja Azure AD Connect nie uda się odinstalować Azure AD Connect synchronizacji, nie jest możliwe ponowne zainstalowanie.
* Nie można zainstalować Azure AD Connect przy użyciu instalacji ekspresowej, jeśli użytkownik nie znajduje się w domenie głównej lasu lub jest używana wersja Active Directory w języku angielskim.
* Jeśli nie można rozpoznać nazwy FQDN konta użytkownika Active Directory, zostanie wyświetlony mylący komunikat o błędzie "nie powiodło się zatwierdzenie schematu".
* Jeśli konto używane na Active Directory łącznik zostanie zmienione poza kreatorem, kreator zakończy się niepowodzeniem w kolejnych uruchomieniach.
* Nie można zainstalować programu Azure AD Connect Czasami na kontrolerze domeny.
* Nie można włączyć i wyłączyć "tryb przejściowy", jeśli dodano atrybuty rozszerzenia.
* Zapisywanie zwrotne haseł w niektórych konfiguracjach nie powiodło się z powodu nieprawidłowego hasła na łączniku Active Directory.
* Nie można uaktualnić narzędzia DirSync, jeśli nazwa wyróżniająca (DN) jest używana w filtrowaniu atrybutów.
* Nadmierne użycie procesora podczas korzystania z funkcji resetowania hasła.

**Usunięte funkcje w wersji zapoznawczej:**

* [Zapis zwrotny użytkownika](how-to-connect-preview.md#user-writeback) funkcji w wersji zapoznawczej został tymczasowo usunięty na podstawie opinii naszych klientów w wersji zapoznawczej. Zostanie ona dodana ponownie później po rozpoczęciu podanej opinii.

## <a name="1086410"></a>1.0.8641.0
Wydanie: czerwiec 2015

**Początkowa wersja Azure AD Connect.**

Zmieniono nazwę z Azure AD Sync na Azure AD Connect.

**Nowe funkcje:**

* Instalacja [ustawień ekspresowych](how-to-connect-install-express.md)
* Można [skonfigurować AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Można [uaktualnić z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md)
* [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Wprowadzony [tryb przejściowy](how-to-connect-sync-staging-server.md)

**Nowe funkcje w wersji zapoznawczej:**

* [Zapisywanie zwrotne użytkownika](how-to-connect-preview.md#user-writeback)
* [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)
* [Rozszerzenia katalogów](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Wydanie: 2015 maja

**Nowe wymaganie:**

* Azure AD Sync teraz wymaga zainstalowania .NET Framework wersji 4.5.1.

**Rozwiązano problemy:**

* Zapisywanie zwrotne haseł z usługi Azure AD kończy się niepowodzeniem z błędem łączności Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Opublikowano: kwiecień 2015

**Rozwiązano problemy i ulepszenia:**

* Łącznik Active Directory nie przetwarza prawidłowo usuwania, jeśli kosz jest włączony i istnieje wiele domen w lesie.
* Ulepszono wydajność operacji importowania dla łącznika Azure Active Directory.
* Gdy grupa przekroczyła limit członkostwa (domyślnie limit jest ustawiony na 50 000 obiektów), Grupa została usunięta w Azure Active Directory. Po wprowadzeniu nowego zachowania Grupa nie zostanie usunięta, zostanie zgłoszony błąd i nowe zmiany członkostwa nie zostaną wyeksportowane.
* Nie można zainicjować obsługi nowego obiektu, jeśli w przestrzeni łącznika istnieje już przemieszczony element Delete o tej samej nazwie DN.
* Niektóre obiekty są oznaczane jako przeznaczone do synchronizacji podczas synchronizacji różnicowej, nawet jeśli nie ma zmian przemieszczonych w obiekcie.
* Wymuszanie synchronizacji haseł spowoduje również usunięcie listy preferowanych kontrolerów domeny.
* CSExportAnalyzer ma problemy z niektórymi Stanami obiektów.

**Nowe funkcje:**

* Sprzężenie może teraz łączyć się z "dowolnym" typem obiektu w MV.

## <a name="104850222"></a>1.0.485.0222
Wydanie: luty 2015

**Ulepszenia:**

* Ulepszona wydajność importowania.

**Rozwiązano problemy:**

* Synchronizacja haseł honoruje atrybut cloudFiltered używany przez filtrowanie atrybutów. Filtrowane obiekty nie są już objęte zakresem synchronizacji haseł.
* W rzadkich sytuacjach, gdy topologia ma wiele kontrolerów domeny, synchronizacja haseł nie działa.
* "Zatrzymany serwer" podczas importowania z łącznika usługi Azure AD po włączeniu zarządzania urządzeniami w usłudze Azure AD/Intune.
* Przyłączanie obcych podmiotów zabezpieczeń (FSPs) z wielu domen w tym samym lesie powoduje błąd niejednoznacznego sprzężenia.

## <a name="104751202"></a>1.0.475.1202
Wydanie: grudzień 2014

**Nowe funkcje:**

* Synchronizacja haseł z filtrowaniem opartym na atrybutach jest teraz obsługiwana. Aby uzyskać więcej informacji, zobacz [Synchronizacja haseł z filtrowaniem](how-to-connect-sync-configure-filtering.md).
* Atrybut MS-DS-ExternalDirectoryObjectID jest zapisywana z powrotem do Active Directory. Ta funkcja dodaje obsługę aplikacji Microsoft 365. Używa OAuth2 do uzyskiwania dostępu do online i lokalnych skrzynek pocztowych w hybrydowym wdrożeniu programu Exchange.

**Naprawione problemy z uaktualnianiem:**

* Na serwerze jest dostępna nowsza wersja Asystenta logowania.
* Niestandardowa ścieżka instalacji została użyta do zainstalowania Azure AD Sync.
* Nieprawidłowe kryterium sprzężenia niestandardowego blokuje uaktualnienie.

**Inne poprawki:**

* Naprawiono Szablony pakietu Office Pro Plus.
* Naprawione problemy z instalacją powodowane przez nazwy użytkowników, które zaczynają się od kreski.
* Naprawiono utratę ustawienia sourceAnchor podczas kolejnego uruchamiania Kreatora instalacji.
* Stałe śledzenie ETW na potrzeby synchronizacji haseł.

## <a name="104701023"></a>1.0.470.1023
Wydanie: październik 2014

**Nowe funkcje:**

* Synchronizacja haseł z wielu Active Directory lokalnych do usługi Azure AD.
* Zlokalizowany interfejs użytkownika instalacji dla wszystkich języków systemu Windows Server.

**Uaktualnianie z AADSync 1,0 GA**

Jeśli masz już zainstalowaną Azure AD Sync, istnieje jeden dodatkowy krok, który trzeba wykonać w przypadku zmiany którejkolwiek z reguł synchronizacji poza ramką. Po uaktualnieniu do wersji 1.0.470.1023 reguły synchronizacji, które zostały zmodyfikowane, są zduplikowane. Dla każdej zmodyfikowanej reguły synchronizacji wykonaj następujące czynności:

1. Znajdź zmodyfikowaną regułę synchronizacji i zanotuj zmiany.
1. Usuń regułę synchronizacji.
1. Znajdź nową regułę synchronizacji utworzoną przez Azure AD Sync a następnie ponownie Zastosuj zmiany.

**Uprawnienia dla konta Active Directory**

Konto Active Directory musi mieć przyznane dodatkowe uprawnienia, aby mieć możliwość odczytywania skrótów haseł z Active Directory. Uprawnienia do przyznania są nazywane "Replikowanie zmian w katalogu" i "Replikowanie zmian katalogów wszystkie". Oba uprawnienia są wymagane, aby można było odczytać skróty haseł.

## <a name="104190911"></a>1.0.419.0911
Wydanie: wrzesień 2014

**Początkowa wersja Azure AD Sync.**

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).