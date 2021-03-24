---
title: 'Azure AD Connect: historia wersji | Microsoft Docs'
description: W tym artykule wymieniono wszystkie wersje Azure AD Connect i Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7269a2435715834a2c1e6723de3fdc6e72eaad5f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955452"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: historia wersji
Zespół Azure Active Directory (Azure AD) regularnie aktualizuje Azure AD Connect za pomocą nowych funkcji i funkcji. Nie wszystkie dodatki są stosowane dla wszystkich odbiorców.

Ten artykuł ma na celu ułatwienie śledzenia wersji, które zostały wydane, oraz zrozumienie, jakie zmiany znajdują się w najnowszej wersji.



Ta tabela zawiera listę powiązanych tematów:

Temat |  Szczegóły
--------- | --------- |
Procedura uaktualniania z programu Azure AD Connect | Różne metody [uaktualniania z poprzedniej wersji do najnowszego](how-to-upgrade-previous-version.md) wydania Azure AD Connect.
Wymagane uprawnienia | Aby uzyskać uprawnienia wymagane do zastosowania aktualizacji, zobacz [konta i uprawnienia](reference-connect-accounts-permissions.md#upgrade).
Pobierz| [Pobierz Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Wydanie nowej wersji Azure AD Connect to proces, który wymaga kilku kroków kontroli jakości, aby zapewnić działanie usługi i przechodząc przez ten proces, numer wersji nowej wersji, a także stan wydania zostanie zaktualizowany w celu odzwierciedlenia najnowszego stanu.
W trakcie tego procesu numer wersji wersji będzie wyświetlany jako "X" w pozycji pomocniczej numer wersji, jak w "1.3. X. 0" — oznacza to, że informacje o wersji w tym dokumencie są prawidłowe dla wszystkich wersji zaczynających się od "1,3". Po sfinalizowaniu procesu wydania numer wersji zostanie zaktualizowany do ostatnio wydanej wersji, a stan wydania zostanie zaktualizowany na "zwolnione do pobrania i Autouaktualnianie".
Nie wszystkie wersje Azure AD Connect będą udostępniane do autouaktualniania. Stan wersji wskazuje, czy wersja jest udostępniona do autouaktualnienia, czy tylko do pobrania. Jeśli automatyczne uaktualnianie zostało włączone na serwerze Azure AD Connect, serwer zostanie automatycznie uaktualniony do najnowszej wersji Azure AD Connect wydanej na potrzeby automatycznego uaktualniania. Należy pamiętać, że nie wszystkie konfiguracje Azure AD Connect mogą być stosowane do autouaktualnienia. 

Aby wyjaśnić użycie autouaktualniania, należy wypchnąć wszystkie ważne aktualizacje i poprawki krytyczne. Nie musi to być Najnowsza wersja, ponieważ nie wszystkie wersje będą wymagane/zawierają poprawkę do krytycznego problemu z zabezpieczeniami (tylko jeden przykład wielu). Problem taki jak w przypadku nowej wersji zapewnianej przez funkcję autoupgrade. Jeśli nie ma takich problemów, nie ma aktualizacji wypychanych przy użyciu funkcji autoupgrade, a ogólnie w przypadku korzystania z najnowszej wersji usługi autoupgrade należy dobrać.
Jeśli jednak chcesz uzyskać wszystkie najnowsze funkcje i aktualizacje, najlepszym sposobem sprawdzenia, czy jest to konieczne, aby sprawdzić Tę stronę i zainstalować ją zgodnie z potrzebami. 

Skorzystaj z tego linku, aby dowiedzieć się więcej na temat [autouaktualniania](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Począwszy od 1 kwietnia 2024, wycofamy wersje Azure AD Connect, które zostały wydane przed 1 maja 2018 wersji 1.1.751.0 i starszych. 
>
> Musisz upewnić się, że korzystasz z najnowszej wersji Azure AD Connect, aby uzyskać optymalne środowisko pomocy technicznej. 
>
>W przypadku korzystania z wycofanej wersji programu Azure AD Connect mogą nie mieć najnowszych poprawek zabezpieczeń, ulepszeń wydajności, narzędzi do rozwiązywania problemów i funkcji diagnostycznych oraz ulepszeń usług, a jeśli będziesz potrzebować pomocy technicznej, firma Microsoft może nie być w stanie zapewnić Ci potrzebom swojej organizacji.
>

>
>Zapoznaj się z [tym artykułem](./how-to-upgrade-previous-version.md) , aby dowiedzieć się więcej o tym, jak uaktualnić Azure AD Connect do najnowszej wersji.
>
>Informacje o historii wersji dotyczące wycofywanych wersji znajdują się w temacie [Azure AD Connect archiwalny historia wersji archiwum](reference-connect-version-history-archive.md)


## <a name="1624"></a>1.6.2.4

>[!NOTE]
> - Ta wersja zostanie udostępniona tylko do pobrania.
> - Uaktualnienie do tej wersji będzie wymagało pełnej synchronizacji ze względu na zmiany reguły synchronizacji.
> - Ta wersja domyślnie AADConnect serwer programu z nowym punktem końcowym v2. Należy zauważyć, że ten punkt końcowy nie jest obsługiwany w niemieckiej chmurze krajowej, w chińskiej chmurze krajowej i w chmurze dla instytucji rządowych USA, a jeśli musisz wdrożyć tę wersję w tych chmurach, musisz wykonać [te instrukcje](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) , aby przełączyć się z powrotem do punktu końcowego w wersji 1. Niewykonanie tej czynności spowoduje błędy synchronizacji.

### <a name="release-status"></a>Stan wydania
3/19/2021: wydano do pobrania

### <a name="functional-changes"></a>Zmiany funkcjonalne

 - Zaktualizowano domyślne reguły synchronizacji w celu ograniczenia członkostwa w grupach zapisanych do członków 50 000.
   - Dodano nowe domyślne reguły synchronizacji na potrzeby ograniczania liczby członkostwa w grupie zapisywanie zwrotne (limit elementu członkowskiego zapisywania zwrotnego z grupą AD) i synchronizowanie grup do Azure Active Directory (poza grupą limitów elementów członkowskich writeup).
   - Dodano atrybut member do reguły "Out to AD-Group SOAInAAD-Exchange" w celu ograniczenia elementów członkowskich w odpisanych grupach do 50 000
 - Zaktualizowano reguły synchronizacji w celu obsługi funkcji zapisywania zwrotnego w grupie v2 — jeśli jest klonowana reguła "in from AAD-Group SOAInAAD" i AADConnect jest uaktualniana.
     -Zaktualizowana reguła zostanie domyślnie wyłączona, więc targetWritebackType będzie mieć wartość null.
     - AADConnect będzie przełączać wszystkie grupy chmury (w tym Azure Active Directory grupy zabezpieczeń z włączoną funkcją zapisywania zwrotnego) jako grupy dystrybucji.
   — Jeśli reguła "Out to AD-Group SOAInAAD" jest sklonowana i AADConnect zostaje uaktualniona.
     - Zaktualizowana reguła zostanie domyślnie wyłączona. Jednak Nowa reguła synchronizacji "Out to AD-Group SOAInAAD-Exchange", która została dodana zostanie włączona.
     - W zależności od sklonowanej reguły synchronizacji niestandardowej AADConnect będzie przepływać atrybuty poczty i programu Exchange.
     - Jeśli sklonowana reguła synchronizacji niestandardowej nie przepływ niektórych atrybutów poczty i programu Exchange, wówczas Nowa reguła synchronizacji programu Exchange doda te atrybuty.
 - Dodano obsługę [synchronizacji skrótów haseł selektywnych](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Dodano nowe [polecenie cmdlet synchronizacji pojedynczego obiektu](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync). Użyj tego polecenia cmdlet, aby rozwiązać problemy z konfiguracją synchronizacji Azure AD Connect. 
 -  Azure AD Connect teraz obsługuje rolę administratora tożsamości hybrydowej do konfigurowania usługi.
 - Zaktualizowano agenta AADConnectHealth do 3.1.83.0
 - Nowa wersja [modułu ADSyncTools PowerShell](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools), która ma kilka nowych lub udoskonalonych poleceń cmdlet. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Zaktualizowano rejestrowanie błędów dla niepowodzeń pozyskiwania tokenu.
 - Zaktualizowano linki "Dowiedz się więcej" na stronie Konfiguracja, aby uzyskać więcej szczegółów dotyczących połączonych informacji.
 - Usunięto kolumnę jawną ze strony wyszukiwania CS w starym interfejsie użytkownika synchronizacji
 - Do przepływu zapisywania zwrotnego grupy Dodano dodatkowy interfejs użytkownika, który monituje użytkownika o poświadczenia lub aby skonfigurować własne uprawnienia przy użyciu modułu ADSyncConfig, jeśli poświadczenia nie zostały jeszcze podane we wcześniejszym kroku.
 - Utwórz na kontrolerze domeny funkcję MSA dla konta usługi ADSync. 
 -  Dodano możliwość ustawiania i pobierania Azure Active Directory zapisywania zwrotnego grup funkcji narzędzia DirSync w ramach istniejących poleceń cmdlet:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Dodano 2 polecenia cmdlet do odczytu wersji interfejsu API AWS
    - Get-ADSyncAADConnectorImportApiVersion — aby pobrać wersję interfejsu API AWS importowania
    - Get-ADSyncAADConnectorExportApiVersion — Aby uzyskać wersję interfejsu API eksportu AWS

 - Zmiany wprowadzone w regułach synchronizacji są teraz śledzone w celu ułatwienia rozwiązywania problemów w usłudze. Polecenie cmdlet "Get-ADSyncRuleAudit" spowoduje pobranie śledzonych zmian.
 - Zaktualizowano polecenie cmdlet Add-ADSyncADDSConnectorAccount w [module ADSyncConfig PowerShell](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) , aby zezwolić użytkownikowi w grupie ADSyncAdmin na zmianę konta łącznika AD DS. 

### <a name="bug-fixes"></a>Poprawki błędów
 - Zaktualizowano wyłączony kolor pierwszego planu, aby spełniał wymagania jasności na białym tle. Dodano dodatkowe warunki dla drzewa nawigacji, aby ustawić kolor tekstu pierwszego planu na biały, gdy zostanie wybrana wyłączona Strona, aby spełnić wymagania dotyczące jasności.
 - Zwiększ poziom szczegółowości dla Set-ADSyncPasswordHashSyncPermissions polecenie cmdlet — zaktualizowany skrypt uprawnień PHS (Set-ADSyncPasswordHashSyncPermissions) w celu uwzględnienia opcjonalnego parametru "ADobjectDN". 
 - Poprawka błędu ułatwień dostępu. Czytnik ekranu może teraz opisać element UX, który przechowuje listę lasów jako "**list lasów**" zamiast "List **list** lasów"
 - Zaktualizowano dane wyjściowe czytnika ekranu dla niektórych elementów w Kreatorze Azure AD Connect. Zaktualizowany kolor aktywowania przycisku w celu spełnienia wymagań dotyczących kontrastu. Zaktualizowano kolor tytułu Synchronization Service Manager w celu spełnienia wymagań kontrastu.
 - Rozwiązano problem dotyczący instalowania AADConnect z wyeksportowanej konfiguracji z atrybutami rozszerzeń niestandardowych — dodano warunek, aby pominąć sprawdzanie atrybutów rozszerzenia w schemacie docelowym podczas stosowania reguły synchronizacji.
 - Odpowiednie uprawnienia są dodawane podczas instalacji, jeśli jest włączona funkcja zapisywania zwrotnego grup.
 - Napraw duplikat domyślnego pierwszeństwa reguły synchronizacji przy imporcie
 - Rozwiązano problem, który spowodował błąd przemieszczania podczas importowania różnicowego interfejsu API w wersji 2 dla obiektu powodującego konflikt, który został naprawiony za pośrednictwem portalu kondycji.
 - Rozwiązano problem w aparacie synchronizacji, który spowodował, że obiekty CS mają niespójny stan łącza
 - Dodano liczniki importu do Get-ADSyncConnectorStatistics danych wyjściowych.
 - Naprawiono usuwanie nieosiągalnych domen (wybrane wcześniej) problemu w niektórych przypadkach narożnych w Kreatorze pass2.
 - Modyfikacja importu i eksportu zasad nie powiedzie się, jeśli reguła niestandardowa ma duplikat pierwszeństwa 
 - Naprawiono usterkę w logice wyboru domeny.
 - Rozwiązuje problem z kompilacją 1.5.18.0, jeśli używasz usługi mS-DS-ConsistencyGuid jako kotwicy źródłowej i Sklonowano element from z reguły sprzężenia AD-Group.
 - Nowe instalacje AADConnect będą używać progu usuwania eksportu przechowywanego w chmurze, jeśli jest dostępny i nie ma innego przekazaną.
 - Rozwiązano problem polegający na tym, że AADConnect nie odczytuje zmian AD displayName urządzeń przyłączonych do hybrydowego

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Stan wydania
07/29/2020: wydano do pobrania

### <a name="functional-changes"></a>Zmiany funkcjonalne
Jest to poprawka do rozwiązania problemu. W tej wersji nie ma żadnych zmian funkcjonalnych.

### <a name="fixed-issues"></a>Naprawione problemy

- Rozwiązano problem polegający na tym, że administrator nie może włączyć "bezproblemowego logowania jednokrotnego", jeśli konto komputera AZUREADSSOACC już istnieje w "Active Directory".
- Rozwiązano problem, który spowodował błąd przemieszczania podczas importowania różnicowego interfejsu API w wersji 2 dla obiektu powodującego konflikt, który został naprawiony za pośrednictwem portalu kondycji.
- Rozwiązano problem z konfiguracją importu/eksportu, gdzie wyłączona reguła niestandardowa została zaimportowana jako włączona.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Stan wydania
07/10/2020: wydano do pobrania

### <a name="functional-changes"></a>Zmiany funkcjonalne
W tej wersji uwzględniono publiczną wersję zapoznawczą funkcji eksportowania konfiguracji istniejącego serwera Azure AD Connect do programu. Plik JSON, którego można następnie użyć podczas instalowania nowego serwera Azure AD Connect, aby utworzyć kopię oryginalnego serwera.

Szczegółowy opis tej nowej funkcji można znaleźć w [tym artykule](./how-to-connect-import-export-config.md) .

### <a name="fixed-issues"></a>Naprawione problemy
- Naprawiono usterkę powodującą fałszywe ostrzeżenie o rozmiarze lokalnego bazy danych w zlokalizowanych kompilacjach podczas uaktualniania.
- Naprawiono usterkę, w której wystąpił błąd w zdarzeniach aplikacji dla nazwy konta i nazwy domeny.
- Naprawiono błąd polegający na tym, że instalacja Azure AD Connect nie powiedzie się na kontrolerze domeny, dając błąd "nie znaleziono elementu członkowskiego".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Stan wydania
05/07/2020: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek rozwiązuje problem polegający na tym, że w interfejsie użytkownika kreatora wybrano niepoprawną niewybrane domeny, jeśli tylko kontenery grandchild zostały wybrane.


>[!NOTE]
>Ta wersja zawiera nowy interfejs API punktu końcowego Azure AD Connect synchronizacji w wersji 2.  Ten nowy punkt końcowy w wersji 2 jest obecnie w publicznej wersji zapoznawczej.  Ta wersja lub nowsza jest wymagana do korzystania z nowego interfejsu API punktu końcowego v2.  Jednak po prostu zainstalowanie tej wersji nie powoduje włączenia punktu końcowego v2. Nadal będziesz korzystać z punktu końcowego V1, chyba że zostanie włączony punkt końcowy v2.  Należy postępować zgodnie z instrukcjami w obszarze [Azure AD Connect Sync Endpoint API (publiczna wersja zapoznawcza)](how-to-connect-sync-endpoint-api-v2.md) , aby włączyć tę funkcję i zalogować się do publicznej wersji zapoznawczej.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Stan wydania
04/23/2020: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek rozwiązuje problem wprowadzony w kompilacji 1.5.20.0, gdzie Administrator dzierżawy z uwierzytelnianiem MFA nie mógł włączyć DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Stan wydania
04/20/2020: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek rozwiązuje problem w 1.5.20.0 kompilacji, jeśli Sklonowano **z reguły dołączania do grupy usługi AD** i nie Sklonowano elementu **from z reguły wspólnych dla grupy AD** .

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Stan wydania
04/09/2020: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
- Ta kompilacja poprawek rozwiązuje problem z kompilacją 1.5.18.0, jeśli włączono funkcję filtrowania grup i używasz usługi mS-DS-ConsistencyGuid jako kotwicy źródłowej.
- Rozwiązano problem w module ADSyncConfig PowerShell, gdzie Wywoływanie polecenia DSACLS użytego we wszystkich poleceniach cmdlet Set-ADSync * spowoduje wystąpienie jednego z następujących błędów:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Jeśli Sklonowano z reguły synchronizacji **dołączania do grupy w usłudze AD** i nie zostały sklonowane **w ramach typowej reguły synchronizacji usługi AD-Group** i planujesz uaktualnienie, wykonaj następujące czynności w ramach uaktualnienia:
> 1. Podczas uaktualniania Usuń zaznaczenie opcji **Rozpocznij proces synchronizacji po zakończeniu konfiguracji**.
> 2. Edytuj regułę synchronizacji sklonowanego połączenia i Dodaj następujące dwa przekształcenia:
>     - Ustaw bezpośredni przepływ `objectGUID` na `sourceAnchorBinary` .
>     - Ustaw przepływ wyrażeń `ConvertToBase64([objectGUID])` na `sourceAnchor` .     
> 3. Włącz usługę Scheduler przy użyciu programu `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Stan wydania
04/02/2020: wydano do pobrania

### <a name="functional-changes-adsyncautoupgrade"></a>Zmiany funkcjonalne ADSyncAutoUpgrade 

- Dodano obsługę funkcji mS-DS-ConsistencyGuid dla obiektów Group. Umożliwia to przenoszenie grup między lasami lub ponowne łączenie grup w usłudze AD z usługą Azure AD, w której zmieniono identyfikator obiektu grupy usługi AD, np. gdy serwer usługi AD zostanie odbudowany po Calamity. Aby uzyskać więcej informacji, zobacz [Przechodzenie między lasami](how-to-connect-migrate-groups.md).
- Atrybut mS-DS-ConsistencyGuid jest ustawiany automatycznie we wszystkich synchronizowanych grupach i nie trzeba wykonywać żadnych czynności w celu włączenia tej funkcji. 
- Usunięto Get-ADSyncRunProfile, ponieważ nie jest już używany. 
- Zmieniono ostrzeżenie widoczne podczas próby użycia konta administratora przedsiębiorstwa lub administratora domeny dla konta łącznika AD DS, aby zapewnić więcej kontekstu. 
- Dodano nowe polecenie cmdlet służące do usuwania obiektów z obszaru łącznika starego narzędzia CSDelete.exe i zostanie ono zastąpione nowym Remove-ADSyncCSObject poleceniem cmdlet. Polecenie cmdlet Remove-ADSyncCSObject pobiera CsObject jako dane wejściowe. Ten obiekt można pobrać przy użyciu polecenia cmdlet Get-ADSyncCSObject.

>[!NOTE]
>Stare narzędzie CSDelete.exe zostało usunięte i zastąpione nowym Remove-ADSyncCSObject poleceniem cmdlet 

### <a name="fixed-issues"></a>Naprawione problemy

- Naprawiono usterkę w selektorze zapisywania zwrotnego lasu/jednostki organizacyjnej na stronie z uruchomioną Azure AD Connect kreatora po wyłączeniu tej funkcji. 
- Wprowadzono nową stronę błędu, która będzie wyświetlana, jeśli brakuje wymaganych wartości rejestru DCOM z nowym łączem pomocy. Informacje są również zapisywane w plikach dziennika. 
- Rozwiązano problem polegający na utworzeniu konta synchronizacji Azure Active Directory, w którym włączenie rozszerzeń katalogów lub PHS może się nie powieść, ponieważ konto nie zostało propagowane we wszystkich replikach usługi przed próbą użycia. 
- Rozwiązano błąd w narzędziu kompresji błędy synchronizacji, które nie obsługują poprawnie znaków dwuskładnikowych. 
- Rozwiązano błąd w ramach autouaktualnienia, który opuścił serwer w stanie wstrzymania usługi Scheduler. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stan wydania
12/9/2019: wydanie do pobrania. Niedostępne przez funkcję autouaktualniania.
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Zaktualizowano synchronizację skrótów haseł dla Azure AD Domain Services, aby poprawnie uwzględnić uzupełnienie w skrótach protokołu Kerberos.  Zapewni to poprawę wydajności podczas synchronizacji haseł z usługi Azure AD do Azure AD Domain Services.
- Dodaliśmy obsługę niezawodnych sesji między agentem uwierzytelniania a usługą Service Bus.
- Ta wersja wymusza protokół TLS 1,2 na potrzeby komunikacji między agentem uwierzytelniania i usługami w chmurze.
- Dodaliśmy pamięć podręczną DNS dla połączeń protokołu WebSocket między agentem uwierzytelniania i usługami w chmurze.
- Dodaliśmy możliwość przekierowania określonego agenta z chmury w celu przetestowania łączności z agentem.

### <a name="fixed-issues"></a>Naprawione problemy
- W wydaniu 1.4.18.0 Wystąpił błąd, gdzie polecenie cmdlet programu PowerShell dla usługi DSSO dotyczyło poświadczeń logowania systemu Windows zamiast poświadczeń administratora podanych podczas korzystania z platformy PS. Z tego powodu nie było możliwe włączenie DSSO w wielu lasach za pomocą interfejsu użytkownika Azure AD Connect. 
- Podjęto poprawkę umożliwiającą jednoczesne włączenie DSSO w każdym lesie za pomocą interfejsu użytkownika Azure AD Connect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stan wydania
11/08/2019: wydano do pobrania. Niedostępne przez funkcję autouaktualniania.

>[!IMPORTANT]
>Ze względu na wewnętrzną zmianę schematu w tej wersji Azure AD Connect, w przypadku zarządzania ustawieniami konfiguracji relacji zaufania AD FS za pomocą programu MSOnline PowerShell należy zaktualizować moduł MSOnline PowerShell do wersji 1.1.183.57 lub nowszej

### <a name="fixed-issues"></a>Naprawione problemy

Ta wersja rozwiązuje problem z istniejącymi urządzeniami hybrydowymi z usługą Azure AD. Ta wersja zawiera nową regułę synchronizacji urządzeń, która rozwiązuje ten problem.
Ta zmiana reguły może spowodować usunięcie przestarzałych urządzeń z usługi Azure AD. Nie jest to przyczyną problemu, ponieważ te obiekty urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. W przypadku niektórych klientów liczba urządzeń, które zostaną usunięte przez tę zmianę reguły, może przekroczyć próg usuwania. Jeśli w usłudze Azure AD jest widoczne usuwanie obiektów urządzeń przekraczających próg usuwania eksportu, zaleca się zezwolenie na przechodzenie przez operacje usuwania. [Jak zezwolić na usuwanie przepływów po przekroczeniu progu usuwania](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stan wydania
9/28/2019: wydano do autouaktualnienia, aby wybrać dzierżawców. Niedostępne do pobrania.

Ta wersja naprawia usterkę, w której niektóre serwery, które zostały automatycznie uaktualnione z poprzedniej wersji do 1.4.18.0 i napotkały problemy z funkcją samoobsługowego resetowania hasła (SSPR) i zapisywania zwrotnego haseł.

### <a name="fixed-issues"></a>Naprawione problemy

W pewnych okolicznościach serwery, które były automatycznie uaktualnione do wersji 1.4.18.0, nie włączyły ponownie funkcji samoobsługowego resetowania hasła i zapisywania zwrotnego haseł po zakończeniu uaktualniania. Ta wersja automatycznego uaktualniania rozwiązuje problem i ponownie włącza funkcję samoobsługowego resetowania hasła i zapisywania zwrotnego haseł.

Rozwiązano błąd w narzędziu kompresji błędy synchronizacji, które nie obsługują poprawnie znaków dwuskładnikowych.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Badamy przypadki, w których niektórzy klienci napotykają problem z istniejącymi przyłączonymi do niej urządzeniami hybrydowymi usługi Azure AD po uaktualnieniu do tej wersji programu Azure AD Connect. Radzimy klientom, którzy utworzyli sprzężenie hybrydowe usługi Azure AD, aby odroczyć uaktualnienie do tej wersji do momentu, w pełni zrozumiałe i skorygowane przyczyny problemu głównego. Więcej informacji będzie można uzyskać tak szybko, jak to możliwe.

>[!IMPORTANT]
>W tej wersji programu Azure AD Connect niektórzy klienci mogą zobaczyć, że niektóre lub wszystkie urządzenia z systemem Windows znikną z usługi Azure AD. Nie jest to przyczyną problemu, ponieważ te tożsamości urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [omówienie Azure AD Connect 1.4. XX. x Device disappearnce](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Stan wydania
9/25/2019: wydano tylko do autouaktualnienia.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Nowe narzędzia do rozwiązywania problemów ułatwiają rozwiązywanie problemów z scenariuszami "użytkownik niesynchronizowany", "Grupa niezsynchronizowana" lub "członek grupy nie jest synchronizowany".
- Dodaj obsługę chmur krajowych w Azure AD Connect skrypt rozwiązywania problemów.
- Klienci powinni mieć świadomość, że przestarzałe punkty końcowe usługi WMI dla MIIS_Service zostały już usunięte. Wszystkie operacje WMI powinny teraz odbywać się za pośrednictwem poleceń cmdlet środowiska PS.
- Poprawa zabezpieczeń przez zresetowanie ograniczonego delegowania obiektu AZUREADSSOACC.
- W przypadku dodawania/edytowania reguły synchronizacji, jeśli istnieją jakiekolwiek atrybuty używane w regule, które znajdują się w schemacie łącznika, ale nie są dodawane do łącznika, atrybuty są automatycznie dodawane do łącznika. Ta sama wartość dotyczy typu obiektu, którego dotyczy reguła. Jeśli wszystkie elementy zostaną dodane do łącznika, łącznik zostanie oznaczony do pełnego importowania w następnym cyklu synchronizacji.
- Korzystanie z administratora przedsiębiorstwa lub domeny jako konta łącznika nie jest już obsługiwane w nowych wdrożeniach Azure AD Connect. Ta wersja nie ma wpływ na bieżące wdrożenia Azure AD Connect przy użyciu konta administratora przedsiębiorstwa lub domeny.
- W Menedżerze synchronizacji do tworzenia/edytowania/usuwania reguły jest uruchamiana pełna synchronizacja. Gdy zostanie uruchomiony pełny import lub pełna synchronizacja, pojawi się okno podręczne.
- Dodano kroki zaradcze dla błędów haseł do strony "łączniki > właściwości > łączności".
- Dodano ostrzeżenie o zaniechaniu dla Menedżera usług synchronizacji na stronie właściwości łącznika. To ostrzeżenie powiadamia użytkownika, że należy wprowadzić zmiany za pomocą Kreatora Azure AD Connect.
- Dodano nowy błąd dotyczący problemów z zasadami haseł użytkownika.
- Zapobiegaj błędom konfiguracji filtrowania grup przez filtry domeny i jednostki organizacyjnej. Filtrowanie grup wyświetli błąd, gdy domena/jednostka organizacyjna wprowadzonej grupy zostanie już odfiltrowana, i uniemożliwić użytkownikowi przechodzenie do przodu do momentu rozwiązania problemu.
- Użytkownicy nie mogą już tworzyć łącznika dla Active Directory Domain Services lub Windows Azure Active Directory w interfejsie użytkownika Synchronization Service Manager.
- Stałe dostępność niestandardowych kontrolek interfejsu użytkownika w Synchronization Service Manager.
- Włączono sześć zadań zarządzania Federacji dla wszystkich metod logowania w Azure AD Connect.  (Wcześniej dla wszystkich logowań jest dostępne tylko zadanie "Aktualizacja AD FS TLS/certyfikat SSL").
- Dodano ostrzeżenie podczas zmiany metody logowania z Federacji do PHS lub PTA, że wszystkie domeny i użytkownicy usługi Azure AD zostaną przekonwertowane na uwierzytelnianie zarządzane.
- Usunięto certyfikaty podpisywania tokenu z zadania "Zresetuj usługę Azure AD i zaufanie AD FS zaufania" i dodano oddzielne podzadanie, aby zaktualizować te certyfikaty.
- Dodano nowe zadanie zarządzania federacyjnego o nazwie "Zarządzanie certyfikatami", które ma podzadania do aktualizowania certyfikatów protokołu TLS lub podpisywania tokenu dla farmy AD FS.
- Dodano nowe zadanie podrzędne zarządzania federacyjnego o nazwie "Określ serwer podstawowy", które umożliwia administratorom określenie nowego serwera podstawowego dla farmy AD FS.
- Dodano nowe zadanie zarządzania federacyjnego o nazwie "Zarządzanie serwerami", które ma podzadania do wdrożenia serwera AD FS, wdrożenia serwera proxy aplikacji sieci Web i określenia serwera podstawowego.
- Dodano nowe zadanie zarządzania federacyjnego o nazwie "Wyświetl konfigurację Federacji", które wyświetla bieżące ustawienia AD FS.  (W związku z tym, że ustawienia AD FS zostały usunięte z strony "Przejrzyj swoje rozwiązanie").

### <a name="fixed-issues"></a>Naprawione problemy
- Rozwiązano problem z błędem synchronizacji dotyczący scenariusza, w którym obiekt użytkownika przeszukiwany przez odpowiadający mu obiekt Contact ma odwołanie do samego siebie (np. użytkownik jest własnym menedżerem).
- Okna podręczne pomocy są teraz widoczne na fokus klawiatury.
- W przypadku autouaktualniania, jeśli jakakolwiek aplikacja powodująca konflikt jest uruchomiona od 6 godzin, należy ją skasować i kontynuować uaktualnianie.
- Ogranicz liczbę atrybutów, które klient może wybrać do 100 dla każdego obiektu podczas wybierania rozszerzeń katalogów. Pozwoli to zapobiec wystąpieniu błędu podczas eksportowania, ponieważ platforma Azure ma maksymalnie 100 atrybutów rozszerzenia dla każdego obiektu.
- Rozwiązano problem polegający na tym, że skrypt łączności usługi AD jest bardziej niezawodny.
- Rozwiązano problem polegający na tym, że Azure AD Connect zainstalować na maszynie przy użyciu istniejących usługi WCF nazwanych potoków bardziej niezawodnie.
- Ulepszona diagnostyka i rozwiązywanie problemów dotyczących zasad grupy, które nie pozwalają na uruchomienie usługi ADSync po jej początkowej instalacji.
- Rozwiązano błąd, gdzie nazwa wyświetlana dla komputera z systemem Windows została niepoprawnie zapisywana.
- Usuń usterkę, w której typ systemu operacyjnego dla komputera z systemem Windows został niepoprawnie zapisany.
- Rozwiązano błąd z nieoczekiwanym synchronizacją komputerów z systemem innym niż Windows 10. Należy zauważyć, że ta zmiana polega na tym, że komputery z systemem innym niż Windows 10, które zostały wcześniej zsynchronizowane, zostaną usunięte. Nie ma to wpływu na wszystkie funkcje, ponieważ synchronizacja komputerów z systemem Windows jest używana tylko w przypadku hybrydowego przyłączania do domeny usługi Azure AD, które działa tylko w przypadku urządzeń z systemem Windows 10.
- Dodano kilka nowych (wewnętrznych) poleceń cmdlet do modułu ADSync PowerShell.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Istnieje znany problem z uaktualnianiem Azure AD Connect ze starszej wersji do 1.3.21.0, gdzie Portal Microsoft 365 nie odzwierciedla zaktualizowanej wersji, nawet jeśli Azure AD Connect uaktualniono pomyślnie.
>
> Aby rozwiązać ten problem, należy zaimportować moduł **AdSync** , a następnie uruchomić `Set-ADSyncDirSyncConfiguration` polecenie cmdlet programu PowerShell na serwerze Azure AD Connect.  Można wykonać następujące czynności:
>
>1. Otwórz program PowerShell w trybie administratora.
>2. Uruchom polecenie `Import-Module "ADSync"`.
>3. Uruchom polecenie `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stan wydania 

05/14/2019: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy 

- Usunięto lukę w zabezpieczeniach, która istnieje w Microsoft Azure Active Directory Connect kompilacja 1.3.20.0.  Ta luka w zabezpieczeniach w pewnych warunkach może pozwolić osobie atakującej na wykonywanie dwóch poleceń cmdlet programu PowerShell w kontekście konta uprzywilejowanego i wykonywanie uprzywilejowanych akcji.  Ta aktualizacja zabezpieczeń rozwiązuje ten problem, wyłączając te polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [Aktualizacja zabezpieczeń](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
