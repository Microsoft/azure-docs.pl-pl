---
title: 'Azure AD Connect: Historia wersji | Microsoft Docs'
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
ms.openlocfilehash: f67bc46b4f612d3d2f377070d5d8280512e0e3df
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576367"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Version release history (Azure AD Connect: historia wersji)
Zespół Azure Active Directory (Azure AD) regularnie aktualizuje Azure AD Connect o nowe funkcje i funkcje. Nie wszystkie dodatki mają zastosowanie do wszystkich odbiorców.

Ten artykuł ma na celu pomoc w śledzeniu wersji, które zostały wydane, oraz zrozumieniu zmian wprowadzonych w najnowszej wersji.



Ta tabela zawiera listę powiązanych tematów:

Temat |  Szczegóły
--------- | --------- |
Kroki uaktualniania z Azure AD Connect | Różne metody [uaktualniania z poprzedniej wersji](how-to-upgrade-previous-version.md) do najnowszej Azure AD Connect wersji.
Wymagane uprawnienia | Aby uzyskać informacje o uprawnieniach wymaganych do zastosowania aktualizacji, [zobacz accounts and permissions (Konta i uprawnienia).](reference-connect-accounts-permissions.md#upgrade)
Pobierz| [Pobierz Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Wydanie nowej wersji usługi Azure AD Connect jest procesem, który wymaga kilku kroków kontroli jakości w celu zapewnienia funkcjonalności działania usługi. Podczas tego procesu numer wersji nowego wydania oraz stan wydania zostaną zaktualizowane w celu odzwierciedlenia najnowszego stanu.
W trakcie tego procesu numer wersji wydania będzie wyświetlany z wartością "X" w pozycji pomocniczego numeru wydania, jak w wersji "1.3.X.0" — oznacza to, że informacje o wersji w tym dokumencie są prawidłowe dla wszystkich wersji rozpoczynających się od "1.3". Po sfinalizowaniu procesu wydania numer wersji wersji zostanie zaktualizowany do najnowszej wydanej wersji, a stan wydania zostanie zaktualizowany do wersji "Wydano do pobrania i automatycznego uaktualnienia".
Nie wszystkie wersje Azure AD Connect zostaną udostępnione do automatycznego uaktualnienia. Stan wydania będzie wskazywać, czy wydanie ma być dostępne do automatycznego uaktualnienia, czy tylko do pobrania. Jeśli automatyczne uaktualnianie zostało włączone na serwerze Azure AD Connect, ten serwer zostanie automatycznie uaktualniony do najnowszej wersji programu Azure AD Connect wydanej do automatycznego uaktualnienia. Pamiętaj, że nie wszystkie Azure AD Connect kwalifikują się do automatycznego uaktualnienia. 

Aby wyjaśnić użycie automatycznego uaktualniania, należy wypchnąć do Ciebie wszystkie ważne aktualizacje i poprawki krytyczne. Niekoniecznie jest to najnowsza wersja, ponieważ nie wszystkie wersje będą wymagały/zawierały poprawkę krytycznego problemu z zabezpieczeniami (tylko jeden przykład dla wielu). Problem podobny do tego, który zostałby rozwiązany przy użyciu nowej wersji udostępnianej za pośrednictwem automatycznego uaktualnienia. Jeśli nie ma takich problemów, nie ma żadnych aktualizacji wypychanych przy użyciu automatycznego uaktualniania. Ogólnie rzecz biorąc, jeśli używasz najnowszej wersji automatycznego uaktualnienia, warto to zrobić.
Jeśli jednak chcesz korzystać ze wszystkich najnowszych funkcji i aktualizacji, najlepszym sposobem na sprawdzenie, czy istnieją, jest sprawdzenie tej strony i zainstalowanie ich zgodnie z potrzebami. 

Skorzystaj z tego linku, aby dowiedzieć się więcej o [automatycznym uaktualnieniu](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Począwszy od 1 kwietnia 2024 r., wycofamy wersje programu Azure AD Connect wydane przed 1 maja 2018 r. — w wersji 1.1.751.0 i starszych. 
>
> Aby uzyskać optymalne środowisko pomocy technicznej, musisz upewnić się, że używasz najnowszej Azure AD Connect programu . 
>
>Jeśli korzystasz z wycofanej wersji programu Azure AD Connect, możesz nie mieć najnowszych poprawek zabezpieczeń, ulepszeń wydajności, narzędzi do rozwiązywania problemów i ulepszeń usług, a jeśli potrzebujesz pomocy technicznej, możemy nie być w stanie zapewnić Ci poziomu usług wymaganych przez organizację.
>

>
>Zapoznaj się z [tym artykułem,](./how-to-upgrade-previous-version.md) aby dowiedzieć się więcej na temat sposobu uaktualniania Azure AD Connect do najnowszej wersji.
>
>Aby uzyskać informacje o historii wersji wycofanych wersji, [zobacz Azure AD Connect historii wersji](reference-connect-version-history-archive.md)

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> Interfejs API punktu końcowego Azure AD Connect synchronizacji w wersji 2 jest teraz dostępny w tych środowiskach platformy Azure:
> - Komercyjna platforma Azure
> - Chmura Azure w Chinach
> - Chmura platformy Azure dla instytucji rządowych USA Nie będzie dostępna w niemieckiej chmurze platformy Azure

### <a name="release-status"></a>Stan wydania
31.03.2021: wydano tylko do pobrania, niedostępne dla automatycznego uaktualnienia

### <a name="bug-fixes"></a>Poprawki błędów
- W tej wersji naprawiono usterkę w wersji 1.6.2.4, która oznaczała, że po uaktualnieniu do tej wersji funkcja Azure AD Connect Health nie została prawidłowo zarejestrowana i nie działała. Klienci, którzy wdrożyli kompilację 1.6.2.4, są proszeni o zaktualizowanie serwera Azure AD Connect za pomocą tej kompilacji, co spowoduje poprawne zarejestrowanie funkcji Kondycja. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Aktualizacja z 30 marca 2021 r.: wykryliśmy problem w tej kompilacji. Po zainstalowaniu tej kompilacji usługi kondycji nie są rejestrowane. Nie zalecamy instalowania tej kompilacji. Wkrótce wydamy poprawkę.
> Jeśli ta kompilacja została już zainstalowana, możesz ręcznie zarejestrować usługi kondycji przy użyciu polecenia cmdlet , jak pokazano w [tym artykule](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Ta wersja będzie dostępna tylko do pobrania.
> - Uaktualnienie do tej wersji będzie wymagało pełnej synchronizacji ze względu na zmiany reguł synchronizacji.
> - W tej wersji serwer programu AADConnect jest domyślnie domyślny dla nowego punktu końcowego w wersji 2. Należy pamiętać, że ten punkt końcowy nie jest obsługiwany w niemieckiej chmurze krajowej. Jeśli chcesz wdrożyć tę wersję w tym środowisku, postępuj zgodnie z tymi instrukcjami, aby wrócić do punktu końcowego w wersji 1. [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) Niewykonanie tej funkcji spowoduje błędy synchronizacji.

### <a name="release-status"></a>Stan wydania
2021-03-19: wydano do pobrania, niedostępne do automatycznego uaktualnienia

### <a name="functional-changes"></a>Zmiany funkcjonalne

 - Zaktualizowano domyślne reguły synchronizacji, aby ograniczyć członkostwo w grupach z powrotem do 50 000 członków.
   - Dodano nowe domyślne reguły synchronizacji dotyczące ograniczania liczby członkostwa w zapisywaniu zwrotym grup (wychodzące do usługi AD — limit składowych zapisu zwrotnego grup) i synchronizacji grup z grupami Azure Active Directory (wychodzące do usługi AAD — limit członków zapisu grup).
   - Dodano atrybut członka do reguły "Out to AD - Group SOAInAAD - Exchange", aby ograniczyć liczbę elementów członkowskich w grupach zapisywanych wstecz do 50 tys.
 - Zaktualizowano reguły synchronizacji w celu obsługi zapisu zwrotnego grup w wersji 2 — jeśli reguła "In from AAD — Group SOAInAAD" zostanie sklonowana i program AADConnect zostanie uaktualniony.
     — Zaktualizowana reguła zostanie domyślnie wyłączona, więc targetWritebackType będzie mieć wartość null.
     - Usługa AADConnect zapisze zwrotne wszystkie grupy chmury (w tym Azure Active Directory zabezpieczeń włączone do zapisu zwrotnego) jako grupy dystrybucyjne.
   -Jeśli zostanie sklonowana reguła "Out to AD - Group SOAInAAD" (Wychodzące do usługi AD — grupa SOAInAAD), a program AADConnect zostanie uaktualniony.
     - Zaktualizowana reguła zostanie domyślnie wyłączona. Zostanie jednak włączona nowa reguła synchronizacji "Out to AD - Group SOAInAAD - Exchange", która zostanie dodana.
     - W zależności od pierwszeństwa sklonowanej reguły synchronizacji niestandardowej program AADConnect będzie przepływać atrybuty poczty i programu Exchange.
     - Jeśli sklonowana reguła synchronizacji niestandardowej nie przepływa przez niektóre atrybuty poczty e-mail i programu Exchange, nowa reguła synchronizacji programu Exchange doda te atrybuty.
 - Dodano obsługę [selektywnej synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Dodano nowe [polecenie cmdlet single object sync](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync). Użyj tego polecenia cmdlet, aby rozwiązać Azure AD Connect konfiguracji synchronizacji. 
 -  Azure AD Connect obsługuje teraz rolę administratora tożsamości hybrydowej na temat konfigurowania usługi.
 - Zaktualizowano agenta AADConnectHealth do wersji 3.1.83.0
 - Nowa wersja modułu [ADSyncTools programu PowerShell,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools)który zawiera kilka nowych lub ulepszonych poleceń cmdlet. 
 
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
 - Zaktualizowano linki "Dowiedz się więcej" na stronie konfiguracji, aby uzyskać więcej szczegółów na temat połączonych informacji.
 - Usunięto jawną kolumnę ze strony wyszukiwania CS w starym interfejsie użytkownika synchronizacji
 - Do przepływu zapisu zwrotnego grup dodano dodatkowy interfejs użytkownika w celu monitowania użytkownika o poświadczenia lub konfigurowania własnych uprawnień przy użyciu modułu ADSyncConfig, jeśli poświadczenia nie zostały jeszcze podane we wcześniejszym kroku.
 - Automatycznie utwórz konto MSA dla konta usługi ADSync na kontrolerze domeny. 
 -  Dodano możliwość ustawienia i Azure Active Directory funkcji DirSync w wersji 2 w istniejących poleceniach cmdlet:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Dodano 2 polecenia cmdlet do odczytywania wersji interfejsu API usług AWS
    - Get-ADSyncAADConnectorImportApiVersion — aby zaimportować wersję interfejsu API usług AWS
    - Get-ADSyncAADConnectorExportApiVersion — aby wyeksportować wersję interfejsu API platformy AWS

 - Zmiany wprowadzone w zasadach synchronizacji są teraz śledzone, aby pomóc w rozwiązywaniu problemów ze zmianami w usłudze. Polecenie cmdlet "Get-ADSyncRuleAudit" pobierze śledzone zmiany.
 - Zaktualizowano Add-ADSyncADDSConnectorAccount cmdlet w module [ADSyncConfig programu PowerShell,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) aby umożliwić użytkownikowi w grupie ADSyncAdmin zmianę konta AD DS Connector. 

### <a name="bug-fixes"></a>Poprawki błędów
 - Zaktualizowano wyłączony kolor pierwszego planu w celu spełnienia wymagań dotyczących jasności na białym tle. Dodano dodatkowe warunki dla drzewa nawigacji, aby ustawić kolor tekstu pierwszego planu na biały po wybraniu wyłączonej strony w celu spełnienia wymagań dotyczących jasności.
 - Zwiększenie szczegółowości polecenia cmdlet Set-ADSyncPasswordHashSyncPermissions — zaktualizowano skrypt uprawnień PHS (Set-ADSyncPasswordHashSyncPermissions), aby uwzględnić opcjonalny parametr "ADobjectDN". 
 - Poprawka usterki dotyczącej ułatwień dostępu. Czytnik zawartości ekranu opisuje teraz element środowiska użytkownika, który przechowuje listę lasów jako **"Lista** lasów" zamiast **"Lista lasów"**
 - Zaktualizowano dane wyjściowe czytnika zawartości ekranu dla niektórych elementów Azure AD Connect kreatora. Zaktualizowano kolor wskaźnika myszy w celu spełnienia wymagań dotyczących kontrastu. Zaktualizowano Synchronization Service Manager tytułu w celu spełnienia wymagań dotyczących kontrastu.
 - Rozwiązano problem z instalowaniem programu AADConnect z wyeksportowanych konfiguracji mających niestandardowe atrybuty rozszerzenia — dodano warunek pomijania sprawdzania atrybutów rozszerzenia w schemacie docelowym podczas stosowania reguły synchronizacji.
 - Jeśli włączono funkcję zapisu zwrotnego grup, podczas instalacji są dodawane odpowiednie uprawnienia.
 - Naprawienie pierwszeństwa zduplikowanych domyślnych reguł synchronizacji podczas importowania
 - Rozwiązano problem, który powodował błąd przejściowy podczas importowania zmian interfejsu API w wersji 2 dla obiektu powodującego konflikt, który został naprawiony za pośrednictwem portalu kondycji.
 - Rozwiązano problem z aparatem synchronizacji, który powodował, że obiekty CS były w stanie niespójnego połączenia
 - Dodano liczniki importu do Get-ADSyncConnectorStatistics danych wyjściowych.
 - Rozwiązano problem z nieosiąganym co do wyboru domeny (wybranym wcześniej) w niektórych przypadkach rogu podczas pracy z kreatorem pass2.
 - Zmodyfikowano importowanie i eksportowanie zasad, aby nie powiodło się, jeśli reguła niestandardowa ma zduplikowane pierwszeństwo 
 - Usunięto usterkę w logice wyboru domeny.
 - Rozwiązuje problem z kompilacją 1.5.18.0, jeśli używasz reguły mS-DS-ConsistencyGuid jako zakotwiczenia źródła i klonuj regułę In z usługi AD — dołączenie do grupy.
 - Nowe instalacje programu AADConnect będą używać progu usuwania eksportu przechowywanego w chmurze, jeśli jest dostępny i nie ma innego, przekazywanego progu.
 - Rozwiązano problem, który oznaczał, że rozwiązanie AADConnect nie odczytuje zmian displayName usługi AD dla urządzeń przyłączone hybrydowo

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Stan wydania
29.07.2020: wydano do pobrania

### <a name="functional-changes"></a>Zmiany funkcjonalne
Jest to wersja poprawki usterki. W tej wersji nie ma żadnych zmian funkcjonalnych.

### <a name="fixed-issues"></a>Naprawione problemy

- Rozwiązano problem, który oznaczał, że administrator nie może włączyć "bezproblemowego logowania jednokrotnego", jeśli konto komputera AZUREADSSOACC jest już obecne w "usłudze Active Directory".
- Rozwiązano problem, który powodował błąd przejściowy podczas importowania zmian interfejsu API w wersji 2 dla obiektu powodującego konflikt, który został naprawiony za pośrednictwem portalu kondycji.
- Rozwiązano problem w konfiguracji importu/eksportu, który dotyczył importowania wyłączonej reguły niestandardowej jako włączonej.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Stan wydania
2020-07-10: wydano do pobrania

### <a name="functional-changes"></a>Zmiany funkcjonalne
Ta wersja zawiera publiczną wersję zapoznawczą funkcji eksportowania konfiguracji istniejącego serwera Azure AD Connect do systemu . Plik JSON, którego można następnie użyć podczas instalowania nowego serwera Azure AD Connect, aby utworzyć kopię oryginalnego serwera.

Szczegółowy opis tej nowej funkcji można znaleźć w [tym artykule](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Naprawione problemy
- Usunięto usterkę, która oznaczała, że podczas uaktualniania było wyświetlane fałszywe ostrzeżenie o rozmiarze lokalnej bazy danych w zlokalizowanych kompilacjach.
- Usunięto usterkę, która oznaczała, że w zdarzeniach aplikacji wystąpił błąd podczas zamiany nazwy konta/nazwy domeny.
- Naprawiono błąd, który Azure AD Connect nie można zainstalować na kontrolerze domeny, co oznaczało błąd "nie znaleziono członka".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Stan wydania
2020-05-07: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek rozwiązuje problem, który dotyczył nieprawidłowego wybierania niezaznaczanych domen z interfejsu użytkownika kreatora, jeśli wybrano tylko kontenery z systemem grandchild.


>[!NOTE]
>Ta wersja zawiera nowy interfejs API punktu końcowego Azure AD Connect synchronizacji w wersji 2.  Ten nowy punkt końcowy w wersji 2 jest obecnie w publicznej wersji zapoznawczej.  Ta wersja lub nowsza jest wymagana do korzystania z nowego interfejsu API punktu końcowego w wersji 2.  Jednak samo zainstalowanie tej wersji nie powoduje włączenia punktu końcowego w wersji 2. Będziesz nadal używać punktu końcowego w wersji 1, chyba że włączysz punkt końcowy w wersji 2.  Aby włączyć interfejs API punktu końcowego usługi Azure AD Connect w wersji [2 (publiczna](how-to-connect-sync-endpoint-api-v2.md) wersja zapoznawcza), należy go włączyć i wyrazić zgodę na dostęp do publicznej wersji zapoznawczej.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Stan wydania
23.04.2020: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek rozwiązuje problem wprowadzony w kompilacji 1.5.20.0, w którym administrator dzierżawy z usługą MFA nie mógł włączyć logowania jednokrotnego.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Stan wydania
20.04.2020: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
Ta kompilacja poprawek rozwiązuje problem w kompilacji 1.5.20.0, jeśli sklonowana reguła In z usługi **AD** — dołączenie do grupy nie została sklonowana z usługi **AD —** reguła Wspólna grupa.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Stan wydania
2020-04-09: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy
- Ta kompilacja poprawek rozwiązuje problem z kompilacją 1.5.18.0, jeśli włączono funkcję filtrowania grup i używasz elementu mS-DS-ConsistencyGuid jako zakotwiczenia źródła.
- Rozwiązano problem w module ADSyncConfig programu PowerShell, który powodować, że wywołania polecenia DSACLS używanego we wszystkich poleceniach cmdlet uprawnień Set-ADSync* było przyczyną jednego z następujących błędów:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Jeśli sklonujesz regułę synchronizacji In from **AD — Group Join** i nie sklonujesz reguły synchronizacji In from AD — Group Common **Sync** i planujesz uaktualnienie, wykonaj następujące kroki w ramach uaktualniania:
> 1. Podczas uaktualniania usuń zaznaczenie opcji **Rozpocznij proces synchronizacji po zakończeniu konfiguracji.**
> 2. Edytuj sklonowane reguły synchronizacji sprzężenia i dodaj następujące dwa przekształcenia:
>     - Ustaw przepływ bezpośredni `objectGUID` na `sourceAnchorBinary` wartość .
>     - Ustaw przepływ wyrażenia `ConvertToBase64([objectGUID])` na `sourceAnchor` wartość .     
> 3. Włącz harmonogram przy użyciu funkcji `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Stan wydania
2020-04-02: wydano do pobrania

### <a name="functional-changes-adsyncautoupgrade"></a>Zmiany funkcjonalne ADSyncAutoUpgrade 

- Dodano obsługę funkcji mS-DS-ConsistencyGuid dla obiektów grupy. Dzięki temu można przenosić grupy między lasami lub ponownie nawiązywać połączenia z grupami w usłudze AD do usługi Azure AD, gdzie identyfikator objectID grupy usługi AD uległ zmianie, np. gdy serwer usługi AD zostanie ponownie przebudowany po ładzie. Aby uzyskać więcej informacji, [zobacz Przenoszenie grup między lasami.](how-to-connect-migrate-groups.md)
- Atrybut mS-DS-ConsistencyGuid jest automatycznie ustawiany dla wszystkich zsynchronizowanych grup i nie trzeba nic robić, aby włączyć tę funkcję. 
- Usunięto Get-ADSyncRunProfile, ponieważ nie jest już w użyciu. 
- Zmieniono ostrzeżenie wyświetlane podczas próby użycia konta administratora przedsiębiorstwa lub administratora domeny dla konta AD DS łącznika, aby zapewnić więcej kontekstu. 
- Dodano nowe polecenie cmdlet do usuwania obiektów z obszaru łącznika, CSDelete.exe stare narzędzie cmdlet zostało usunięte i zostało zastąpione nowym Remove-ADSyncCSObject cmdlet. Polecenie cmdlet Remove-ADSyncCSObject przyjmuje jako dane wejściowe projekt CsObject. Ten obiekt można pobrać za pomocą Get-ADSyncCSObject cmdlet .

>[!NOTE]
>Stare narzędzie CSDelete.exe zostało usunięte i zastąpione nowym Remove-ADSyncCSObject cmdlet 

### <a name="fixed-issues"></a>Naprawione problemy

- Usunięto usterkę w lesie/selektorze obiektu OU zapisu zwrotnego grupy podczas ponownego Azure AD Connect kreatora po wyłączeniu funkcji. 
- Wprowadzono nową stronę błędu, która będzie wyświetlana, jeśli brakuje wymaganych wartości rejestru DCOM z nowym linkiem pomocy. Informacje są również zapisywane w plikach dziennika. 
- Rozwiązano problem z tworzeniem konta synchronizacji usługi Azure Active Directory, który mógł powodować niepowodzenie włączania rozszerzeń katalogów lub phs, ponieważ konto nie zostało rozpropagowane we wszystkich replikach usługi przed podjęciem próby użycia. 
- Usunięto usterkę w narzędziu kompresji błędów synchronizacji, która nie obsługiła poprawnie znaków zastępczych. 
- Usunięto usterkę w automatycznym uaktualnieniu, która pozostawiała serwer w stanie zawieszenia harmonogramu. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stan wydania
2019-12-09: wydanie do pobrania. Niedostępne za pośrednictwem automatycznego uaktualnienia.
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Zaktualizowaliśmy synchronizację skrótów haseł, aby Azure AD Domain Services prawidłowo uwzględniać uzupełnianie skrótów protokołu Kerberos.  Zapewni to poprawę wydajności podczas synchronizacji haseł z usługi Azure AD do Azure AD Domain Services.
- Dodaliśmy obsługę niezawodnych sesji między agentem uwierzytelniania a magistralą usług.
- Ta wersja wymusza protokoły TLS 1.2 do komunikacji między agentem uwierzytelniania a usługami w chmurze.
- Dodaliśmy pamięć podręczną DNS dla połączeń protokołu WebSocket między agentem uwierzytelniania a usługami w chmurze.
- Dodaliśmy możliwość kierowania określonego agenta z chmury do testowania łączności z agentem.

### <a name="fixed-issues"></a>Naprawione problemy
- W wersji 1.4.18.0 miała miejsce usterka, w przypadku której polecenie cmdlet programu PowerShell dla logowania jednokrotnego używało poświadczeń systemu Windows logowania zamiast poświadczeń administratora podanych podczas uruchamiania narzędzia ps. W związku z tym nie było możliwe włączenie logowania jednokrotnego w wielu lasach za pośrednictwem Azure AD Connect interfejsu użytkownika. 
- W poprawkach w celu włączenia logowania jednokrotnego jednocześnie we wszystkich lasach za pośrednictwem Azure AD Connect interfejsu użytkownika

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stan wydania
2019-11-08: wydano do pobrania. Niedostępne za pośrednictwem automatycznego uaktualnienia.

>[!IMPORTANT]
>Ze względu na wewnętrzną zmianę schematu w tej wersji programu Azure AD Connect, jeśli zarządzasz ustawieniami konfiguracji relacji zaufania usługi AD FS przy użyciu programu MSOnline PowerShell, musisz zaktualizować moduł MSOnline PowerShell do wersji 1.1.183.57 lub wyższej

### <a name="fixed-issues"></a>Naprawione problemy

Ta wersja rozwiązuje problem z istniejącymi urządzeniami przyłączonymi hybrydową do usługi Azure AD. Ta wersja zawiera nową regułę synchronizacji urządzeń, która koryguje ten problem.
Należy pamiętać, że ta zmiana reguły może spowodować usunięcie przestarzałych urządzeń z usługi Azure AD. Nie jest to powód do obaw, ponieważ te obiekty urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. W przypadku niektórych klientów liczba urządzeń, które zostaną usunięte w ramach tej zmiany reguły, może przekroczyć próg usuwania. Jeśli zobaczysz, że usunięcie obiektów urządzeń w usłudze Azure AD przekracza próg usuwania eksportu, zaleca się, aby umożliwić usunięcie. [Jak zezwolić na przepływ usuwania po przekroczeniu progu usuwania](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stan wydania
28.09.2019: wydano polecenie automatycznego uaktualniania w celu wybrania dzierżaw. Niedostępne do pobrania.

Ta wersja usuwa usterkę, która powoduje, że niektóre serwery, które zostały automatycznie uaktualnione z poprzedniej wersji do wersji 1.4.18.0, i wystąpiły problemy z samoobsługowym resetowaniem haseł (SSPR, Self-service password reset) i funkcjami zapisu zwrotnego haseł.

### <a name="fixed-issues"></a>Naprawione problemy

W pewnych okolicznościach serwery, które zostały automatycznie uaktualnione do wersji 1.4.18.0, nie włączyć ponownie samoobsługowego resetowania hasła i funkcji zapisu zwrotnego haseł po zakończeniu uaktualniania. To wydanie automatycznego uaktualnienia rozwiązuje ten problem i ponownie włącza samoobsługowe resetowanie haseł i funkcję zapisu zwrotnego haseł.

Usunięto usterkę w narzędziu kompresji błędów synchronizacji, która nie obsługiła poprawnie znaków zastępczych.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Badamy zdarzenie, w którym niektórzy klienci mają problem z istniejącymi urządzeniami przyłączonymi hybrydową do usługi Azure AD po uaktualnieniu do tej wersji Azure AD Connect. Zalecamy klientom, którzy wdrożyli dołączanie do hybrydowej usługi Azure AD w celu odroczenia uaktualnienia do tej wersji do momentu, gdy główna przyczyna tych problemów zostanie w pełni zrozumiana i rozwiązana. Więcej informacji zostanie podanych jak najszybciej.

>[!IMPORTANT]
>W tej wersji usługi Azure AD Connect niektórzy klienci mogą zobaczyć, że niektóre lub wszystkie ich urządzenia z systemem Windows zniknęły z usługi Azure AD. Nie jest to powód do zaniepokojenia, ponieważ te tożsamości urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. Aby uzyskać więcej informacji, [zobacz Understanding Azure AD Connect 1.4.xx.x device disappearnce](reference-connect-device-disappearance.md) (Opis znikania urządzeń w wersji 1.4.xx.x)


### <a name="release-status"></a>Stan wydania
25.09.2019: wydano tylko do automatycznego uaktualniania.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Nowe narzędzia do rozwiązywania problemów pomagają w rozwiązywaniu problemów ze scenariuszami "użytkownik nie synchronizuje", "nie synchronizuje grupy" lub "członek grupy nie synchronizuje".
- Dodanie obsługi chmur krajowych w skrypcie Azure AD Connect rozwiązywania problemów.
- Klienci powinni być poinformowani, że przestarzałe punkty końcowe usługi WMI MIIS_Service zostały usunięte. Wszystkie operacje usługi WMI powinny być teraz wykonywane za pomocą polecenia cmdlet ps.
- Ulepszenie zabezpieczeń przez zresetowanie ograniczonego delegowania w obiekcie AZUREADSSOACC.
- Jeśli podczas dodawania/edytowania reguły synchronizacji istnieją jakiekolwiek atrybuty używane w regułę, które znajdują się w schemacie łącznika, ale nie zostały dodane do łącznika, atrybuty są automatycznie dodawane do łącznika. To samo dotyczy typu obiektu, na który wpływa reguła. Jeśli do łącznika zostaną dodane jakkolwiek informacje, zostanie on oznaczony do pełnego zaimportowania w następnym cyklu synchronizacji.
- Używanie administratora przedsiębiorstwa lub domeny jako konta łącznika nie jest już obsługiwane w nowych Azure AD Connect wdrożeniach. Bieżąca Azure AD Connect wdrażania przy użyciu administratora przedsiębiorstwa lub domeny, ponieważ ta wersja nie będzie mieć wpływu na konto łącznika.
- W Menedżerze synchronizacji pełna synchronizacja jest uruchamiana przy tworzeniu/edytowaniu/usuwaniu reguł. Zostanie wyświetlone okno podręczne dla każdej zmiany reguły z powiadomieniem użytkownika, czy zostanie uruchomiony pełny import lub pełna synchronizacja.
- Dodano kroki zaradcze dotyczące błędów haseł do > "Łączniki > łączności".
- Dodano ostrzeżenie o cofaniu obsługi menedżera usługi synchronizacji na stronie właściwości łącznika. To ostrzeżenie powiadamia użytkownika, że należy wprowadzić zmiany za pośrednictwem Azure AD Connect kreatora.
- Dodano nowy błąd w przypadku problemów z zasadami haseł użytkownika.
- Zapobiegaj błędnej konfiguracji filtrowania grup według filtrów domeny i nazwy użytkownika. Filtrowanie grup spowoduje wyświetlanie błędu, gdy domena/nazwa OU wprowadzonej grupy jest już odfiltrowana i nie będzie można przenosić użytkownika do momentu rozwiązania problemu.
- Użytkownicy nie mogą już tworzyć łącznika dla Active Directory Domain Services windows Azure Active Directory w interfejsie Synchronization Service Manager użytkownika.
- Poprawiono dostępność niestandardowych kontrolek interfejsu użytkownika w Synchronization Service Manager.
- Włączono sześć zadań zarządzania federacją dla wszystkich metod logowania w Azure AD Connect.  (Wcześniej tylko zadanie "Aktualizuj AD FS TLS/SSL" było dostępne dla wszystkich logowaniu).
- Dodano ostrzeżenie podczas zmiany metody logowania z federacji na PHS lub PTA, że wszystkie domeny i użytkownicy usługi Azure AD zostaną przekonwertowane na uwierzytelnianie zarządzane.
- Usunięto certyfikaty podpisywania tokenu z zadania "Resetuj usługę Azure AD i AD FS zaufania" i dodano oddzielne podsekcję w celu zaktualizowania tych certyfikatów.
- Dodano nowe zadanie zarządzania federacją o nazwie "Zarządzanie certyfikatami", które zawiera zadania podrzędne służące do aktualizowania certyfikatów TLS lub certyfikatów podpisywania tokenów dla AD FS farmy.
- Dodano nowe zadanie podrzędne zarządzania federacją o nazwie "Określ serwer podstawowy", które umożliwia administratorom określenie nowego serwera podstawowego dla AD FS farmy.
- Dodano nowe zadanie zarządzania federacją o nazwie "Zarządzanie serwerami", które ma zadania podrzędne wdrażania serwera AD FS, wdrażania serwera serwer proxy aplikacji Sieci Web i określania serwera podstawowego.
- Dodano nowe zadanie zarządzania federacją o nazwie "Wyświetl konfigurację federacji", które wyświetla bieżące AD FS ustawień.  (Z powodu tego AD FS ustawienia zostały usunięte ze strony "Przeglądanie rozwiązania".

### <a name="fixed-issues"></a>Naprawione problemy
- Rozwiązano problem z błędem synchronizacji w scenariuszu, w którym obiekt użytkownika przejmuje odpowiadający mu obiekt kontaktu, ma odwołanie do siebie (np. użytkownik jest własnym menedżerem).
- Okna podręczne Pomocy są teraz wyświetlane na fokusie klawiatury.
- W przypadku automatycznego uaktualniania, jeśli dowolna aplikacja, w przypadku których występuje konflikt, działa od 6 godzin, należy ją zabicia i kontynuować uaktualnianie.
- Ogranicz liczbę atrybutów, które klient może wybrać, do 100 na obiekt podczas wybierania rozszerzeń katalogów. Zapobiegnie to występowaniu błędu podczas eksportowania, ponieważ platforma Azure ma maksymalnie 100 atrybutów rozszerzeń na obiekt.
- Usunięto usterkę, która sprawiła, że skrypt łączności usługi AD był bardziej niezawodny.
- Usunięto usterkę, która Azure AD Connect na maszynie przy użyciu istniejącej usługi WCF nazwanych potoków, która jest bardziej niezawodna.
- Ulepszona diagnostyka i rozwiązywanie problemów dotyczących zasad grupy, które nie zezwalają na uruchomienie usługi ADSync po początkowym zainstalowaniu.
- Usunięto usterkę, która miała miejsce, gdy nazwa wyświetlana komputera z systemem Windows była niepoprawnie zapisywana.
- Naprawienie usterki w przypadku nieprawidłowego napisania typu systemu operacyjnego dla komputera z systemem Windows.
- Usunięto usterkę, która Windows 10 były nieoczekiwanie synchronizowane. Należy pamiętać, że efektem tej zmiany jest to, że komputery z systemem innych niż Windows 10, które zostały wcześniej zsynchronizowane, zostaną usunięte. Nie ma to wpływu na żadne funkcje, ponieważ synchronizacja komputerów z systemem Windows jest używana tylko w przypadku hybrydowego dołączania do domeny usługi Azure AD, co działa tylko w przypadku urządzeń z systemem Windows 10.
- Dodano kilka nowych (wewnętrznych) poleceń cmdlet do modułu adsync programu PowerShell.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Istnieje znany problem z uaktualnieniem programu Azure AD Connect ze starszej wersji do wersji 1.3.21.0, w którym portal programu Microsoft 365 nie odzwierciedla zaktualizowanej wersji, mimo że program Azure AD Connect uaktualniony pomyślnie.
>
> Aby rozwiązać ten problem, należy zaimportować moduł **AdSync,** a następnie uruchomić polecenie `Set-ADSyncDirSyncConfiguration` cmdlet programu PowerShell na Azure AD Connect serwera.  Możesz wykonać następujące czynności:
>
>1. Otwórz program PowerShell w trybie administratora.
>2. Uruchom polecenie `Import-Module "ADSync"`.
>3. Uruchom polecenie `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stan wydania 

2019-05-14: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy 

- Usunięto lukę w zabezpieczeniach podniesienia uprawnień, która istnieje Microsoft Azure Active Directory Connect kompilacji 1.3.20.0.  W pewnych warunkach ta luka w zabezpieczeniach może umożliwić osobie atakującej wykonanie dwóch poleceń cmdlet programu PowerShell w kontekście konta uprzywilejowanego i wykonywanie uprzywilejowanych akcji.  Ta aktualizacja zabezpieczeń rozwiązuje ten problem, wyłączając te polecenia cmdlet. Aby uzyskać więcej informacji, [zobacz aktualizacja zabezpieczeń](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
