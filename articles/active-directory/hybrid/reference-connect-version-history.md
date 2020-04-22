---
title: 'Usługa Azure AD Connect: historia wersji | Dokumenty firmy Microsoft'
description: W tym artykule wymieniono wszystkie wersje usługi Azure AD Connect i usługi Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/21/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a03a03557fbb2e71ff79ff42fd9d9c72cd5907c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770503"
---
# <a name="azure-ad-connect-version-release-history"></a>Usługa Azure AD Connect: historia wersji
Zespół usługi Azure Active Directory (Azure AD) regularnie aktualizuje usługę Azure AD Connect o nowe funkcje i funkcje. Nie wszystkie dodatki mają zastosowanie do wszystkich odbiorców.

Ten artykuł ma na celu pomóc śledzić wersje, które zostały wydane i zrozumieć, jakie zmiany są w najnowszej wersji.

Ta tabela zawiera listę powiązanych tematów:

Temat |  Szczegóły
--------- | --------- |
Kroki uaktualniania z usługi Azure AD Connect | Różne metody [uaktualniania z poprzedniej wersji do najnowszej](how-to-upgrade-previous-version.md) wersji usługi Azure AD Connect.
Wymagane uprawnienia | Aby uzyskać uprawnienia wymagane do zastosowania aktualizacji, zobacz [konta i uprawnienia](reference-connect-accounts-permissions.md#upgrade).
Pobierz| [Pobierz usługę Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Zwolnienie nowej wersji usługi Azure AD Connect jest procesem, który wymaga kilku kroków kontroli jakości, aby zapewnić funkcjonalność operacji usługi, a podczas przechodzenia przez ten proces numer wersji nowej wersji, a także stan wersji zostaną zaktualizowane w celu odzwierciedlenia najnowszego stanu.
Podczas gdy przechodzimy przez ten proces, numer wersji wydania będzie wyświetlany z "X" w pozycji numeru wydania pomocniczego, jak w "1.3.X.0" - oznacza to, że informacje o wersji w tym dokumencie są ważne dla wszystkich wersji zaczynających się od "1.3.". Jak tylko zakończymy proces wydania, numer wersji wydania zostanie zaktualizowany do ostatnio wydanej wersji, a stan wydania zostanie zaktualizowany do "Wydany do pobrania i automatycznego uaktualnienia".
Nie wszystkie wersje usługi Azure AD Connect zostaną udostępnione do automatycznego uaktualnienia. Stan wersji wskazuje, czy wersja jest dostępna do automatycznego uaktualnienia, czy tylko do pobrania. Jeśli automatyczne uaktualnianie zostało włączone na serwerze usługi Azure AD Connect, serwer ten automatycznie uaktualni do najnowszej wersji usługi Azure AD Connect wydanej do automatycznego uaktualnienia. Należy zauważyć, że nie wszystkie konfiguracje usługi Azure AD Connect kwalifikują się do automatycznego uaktualnienia. Kliknij ten link, aby dowiedzieć się więcej o [automatycznym uaktualnieniu](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Począwszy od 1 listopada 2020 r. rozpoczniemy implementację procesu deprekalacji, w ramach którego wersje usługi Azure AD Connect wydane ponad 18 miesięcy temu zostaną przestarzałe. W tym czasie rozpoczniemy ten proces, przestarzałe wszystkie wersje usługi Azure AD Connect w wersji 1.3.20.0 (która została wydana 24.04.2019) i starszych, a my przystąpimy do oceny wycofania starszych wersji usługi Azure AD Connect za każdym razem, gdy nowa wersja zostanie wydana.
>
> Musisz upewnić się, że korzystasz z najnowszej wersji usługi Azure AD Connect, aby uzyskać optymalną pomoc techniczną. 
>
>Jeśli uruchomisz przestarzałą wersję usługi Azure AD Connect, możesz nie mieć najnowszych poprawek zabezpieczeń, ulepszeń wydajności, narzędzi do rozwiązywania problemów i diagnostyki oraz ulepszeń usług, a jeśli potrzebujesz pomocy technicznej, możemy nie być w stanie zapewnić Ci poziomu usług, których potrzebuje twoja organizacja.
>
>Jeśli włączono usługę Azure AD Connect do synchronizacji, wkrótce automatycznie zaczniesz otrzymywać powiadomienia o kondycji, które ostrzegają o nadchodzących przestarzałych powiadomieniach po uruchomieniu jednej ze starszych wersji.
>
>Zapoznaj się z [tym artykułem,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version) aby dowiedzieć się więcej na temat uaktualniania usługi Azure AD Connect do najnowszej wersji.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Stan wydania
20.04.2020: Premiera do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy
Ta kompilacja poprawek rozwiązuje problem w kompilacji 1.5.20.0, jeśli sklonowałeś regułę **In from AD — Group Join** i nie sklonowałeś reguły In from AD — Group **Common.**

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Stan wydania
04/09/2020: Wydany do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy
Ta kompilacja poprawek rozwiązuje problem z kompilacją 1.5.18.0, jeśli masz włączoną funkcję filtrowania grup i używasz mS-DS-ConsistencyGuid jako kotwicy źródłowej.

> [!IMPORTANT]
> Jeśli sklonowałeś regułę synchronizacji **In from AD — Group Join** i nie sklonowałeś reguły synchronizacji In from AD — Group **Common** sync i planujesz uaktualnienie, wykonaj następujące czynności w ramach uaktualnienia:
> 1. Podczas uaktualniania odznacz opcję **Rozpocznij proces synchronizacji po zakończeniu konfiguracji**.
> 2. Edytuj regułę synchronizacji sklonowanego sprzężenia i dodaj następujące dwie przekształcenia:
>     - Ustaw przepływ `objectGUID` `sourceAnchorBinary`bezpośredni do .
>     - Ustaw przepływ `ConvertToBase64([objectGUID])` `sourceAnchor`wyrażenia na .     
> 3. Włącz harmonogram za `Set-ADSyncScheduler -SyncCycleEnabled $true`pomocą programu .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Stan wydania
04/02/2020: Wydany do pobrania

### <a name="functional-changes-adsyncautoupgrade"></a>Zmiany funkcjonalne ADSyncAutoUpgrade 

- Dodano obsługę funkcji mS-DS-ConsistencyGuid dla obiektów grupy. Dzięki temu można przenosić grupy między lasami lub ponownie połączyć grupy w usłudze AD do usługi Azure AD, gdzie obiekt grupy usługi ADid został zmieniony, na przykład, gdy serwer usługi AD jest przebudowywany po katastrofie. Aby uzyskać więcej informacji, zobacz [Przenoszenie grup między lasami](how-to-connect-migrate-groups.md).
- Atrybut mS-DS-ConsistencyGuid jest automatycznie ustawiany na wszystkich zsynchronizowanych grupach i nie trzeba nic robić, aby włączyć tę funkcję. 
- Usunięto Get-ADSyncRunProfile, ponieważ nie jest już używany. 
- Zmieniono ostrzeżenie widoczne podczas próby użycia konta administratora przedsiębiorstwa lub administratora domeny dla konta łącznika usług AD DS w celu zapewnienia większego kontekstu. 
- Dodano nowe polecenie cmdlet w celu usunięcia obiektów z obszaru łącznika stare narzędzie CSDelete.exe jest usuwane i jest zastępowane nowym poleceniem cmdlet Remove-ADSyncCSObject. Polecenie cmdlet Remove-ADSyncCSObject przyjmuje polecenie CsObject jako dane wejściowe. Ten obiekt można pobrać za pomocą polecenia cmdlet Get-ADSyncCSObject.

>[!NOTE]
>Stare narzędzie CSDelete.exe zostało usunięte i zastąpione nowym poleceniem cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Rozwiązane problemy

- Naprawiono błąd w selektorze lasu/operacji organizacyjnej grupy podczas ponownego uruchamiania kreatora usługi Azure AD Connect po wyłączeniu tej funkcji. 
- Wprowadzono nową stronę błędu, która będzie wyświetlana, jeśli brakuje wymaganych wartości rejestru DCOM z nowym łączem pomocy. Informacje są również zapisywane w plikach dziennika. 
- Rozwiązano problem z utworzeniem konta synchronizacji usługi Azure Active Directory, w którym włączanie rozszerzeń katalogów lub usług PHS mogło zakończyć się niepowodzeniem, ponieważ konto nie propagowane we wszystkich replikach usługi przed próbą użycia. 
- Naprawiono błąd w narzędziu kompresji błędów synchronizacji, który nie obsługiwał poprawnie znaków zastępczych. 
- Naprawiono błąd w automatycznym uaktualnieniu, który pozostawił serwer w stanie zawieszenia harmonogramu. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stan wydania
12/9/2019: Wydanie do pobrania. Niedostępne w trybie automatycznego uaktualniania.
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Zaktualizowaliśmy synchronizację skrótów haseł dla usług domenowych usługi Azure AD, aby poprawnie uwzględnić dopełnienie skrótów w skrótach protokołu Kerberos.  Zapewni to poprawę wydajności podczas synchronizacji haseł z usługi AAD do usług domenowych usługi Azure AD Domain Services.
- Dodaliśmy obsługę niezawodnych sesji między agentem uwierzytelniania a magistrali usług.
- Ta wersja wymusza TLS 1.2 do komunikacji między agentem uwierzytelniania i usług w chmurze.
- Dodaliśmy pamięć podręczną DNS dla połączeń websocket między agentem uwierzytelniania a usługami w chmurze.
- Dodaliśmy możliwość kierowania określonego agenta z chmury do testowania łączności agenta.

### <a name="fixed-issues"></a>Rozwiązane problemy
- Wersja 1.4.18.0 miała błąd, w którym polecenie cmdlet programu PowerShell dla dsso używało poświadczeń systemu windows logowania zamiast poświadczeń administratora podanych podczas uruchamiania ps. W rezultacie nie było możliwe włączenie dsso w wielu lasach za pośrednictwem interfejsu użytkownika AADConnect. 
- Poprawka została włączona do jednoczesnego włączania DSSO we wszystkich lasach za pośrednictwem interfejsu użytkownika AADConnect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stan wydania
11/08/2019: Wydany do pobrania. Niedostępne w trybie automatycznego uaktualniania.

>[!IMPORTANT]
>Ze względu na wewnętrzną zmianę schematu w tej wersji usługi Azure AD Connect, jeśli zarządzasz ustawieniami konfiguracji relacji zaufania usług AD FS przy użyciu programu MSOnline PowerShell, należy zaktualizować moduł programu MSOnline PowerShell do wersji 1.1.183.57 lub nowszej

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta wersja rozwiązuje problem z istniejącymi urządzeniami przyłączanych do usługi Azure AD. Ta wersja zawiera nową regułę synchronizacji urządzeń, która poprawia ten problem.
Należy zauważyć, że ta zmiana reguły może spowodować usunięcie przestarzałych urządzeń z usługi Azure AD. Nie jest to powodem do niepokoju, ponieważ te obiekty urządzenia nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. W przypadku niektórych klientów liczba urządzeń, które zostaną usunięte za pomocą tej zmiany reguły, może przekroczyć próg usunięcia. Jeśli zostanie wyświetlone usunięcie obiektów urządzenia w usłudze Azure AD przekraczających próg usuwania eksportu, zaleca się, aby umożliwić usunięcie przejść. [Jak zezwolić na przepływ usuwania po przekroczeniu progu usunięcia](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stan wydania
28.09.2019: Wydany do automatycznego uaktualnienia do wybranych dzierżawców. Niedostępne do pobrania.

Ta wersja rozwiązuje błąd, w którym niektóre serwery, które zostały automatycznie uaktualnione z poprzedniej wersji do 1.4.18.0 i wystąpiły problemy z samoobsługowym resetowaniem haseł (SSPR) i zapisywaniem hasła.

### <a name="fixed-issues"></a>Rozwiązane problemy

W pewnych okolicznościach serwery, które zostały automatycznie uaktualnione do wersji 1.4.18.0, nie włączały ponownie samoobsługowego resetowania hasła i zapisywania hasła po zakończeniu uaktualniania. Ta wersja automatycznego uaktualnienia rozwiązuje ten problem i ponownie włącza samoobsługowe resetowanie hasła i zapisywanie hasła.

Naprawiliśmy błąd w narzędziu kompresji błędów synchronizacji, który nie obsługiwał poprawnie znaków zastępczych.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Badamy incydent, w którym niektórzy klienci występują problem z istniejącymi urządzeniami hybrydowej usługi Azure AD przyłączone po uaktualnieniu do tej wersji usługi Azure AD Connect. Radzimy klientom, którzy wdrożyli sprzężenie usługi Hybrid Azure AD, aby odroczyli uaktualnienie do tej wersji, dopóki główna przyczyna tych problemów nie zostaną w pełni zrozumiane i złagodzone. Więcej informacji zostanie dostarczonych tak szybko, jak to możliwe.

>[!IMPORTANT]
>W tej wersji usługi Azure AD Connect niektórzy klienci mogą zobaczyć niektóre lub wszystkie ich urządzenia z systemem Windows znikają z usługi Azure AD. Nie jest to powód do niepokoju, ponieważ te tożsamości urządzeń nie są używane przez usługę Azure AD podczas autoryzacji dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Opis zniknięcia urządzenia usługi Azure AD Connect 1.4.xx.x](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Stan wydania
25.09.2019: Wydany tylko do automatycznego uaktualnienia.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Nowe narzędzia do rozwiązywania problemów pomagają rozwiązywać problemy ze scenariuszami "użytkownik nie synchronizuje", "grupa nie synchronizuje" lub "członek grupy nie synchronizuje".
- Dodawanie obsługi chmur krajowych w skrypcie rozwiązywania problemów z systemem AAD Connect 
- Klienci powinni zostać poinformowani, że przestarzałe punkty końcowe WMI dla MIIS_Service zostały usunięte. Wszystkie operacje WMI powinny być teraz wykonywane za pomocą poleceń cmdlet PS.
- Poprawa zabezpieczeń przez zresetowanie ograniczonego delegowania na obiekt AZUREADSSOACC
- Podczas dodawania/edytowania reguły synchronizacji, jeśli w regule są używane atrybuty, które znajdują się w schemacie łącznika, ale nie zostały dodane do łącznika, atrybuty są automatycznie dodawane do łącznika. To samo dotyczy typu obiektu, którego dotyczy reguła. Jeśli cokolwiek zostanie dodane do łącznika, łącznik zostanie oznaczony do pełnego zaimportowania w następnym cyklu synchronizacji.
- Używanie administratora przedsiębiorstwa lub domeny jako konta łącznika nie jest już obsługiwane w nowych wdrożeniach usługi Azure AD Connect. Ta wersja nie będzie miała wpływu na bieżące wdrożenia usługi AAD Connect przy użyciu administratora przedsiębiorstwa lub domeny, ponieważ ta wersja nie będzie miała wpływu na konto łącznika.
- W Menedżerze synchronizacji pełna synchronizacja jest uruchamiana podczas tworzenia/edycji/usuwania reguł. Pojawi się okno podręczne na każdej zmianie reguły powiadamiającej użytkownika, jeśli zostanie uruchomiony pełny import lub pełna synchronizacja.
- Dodano kroki ograniczające zagrożenie dla błędów hasła na stronie "łączniki > właściwości > łączności".
- Dodano ostrzeżenie o usunięciu menedżera usługi synchronizacji na stronie właściwości łącznika. To ostrzeżenie powiadamia użytkownika, że zmiany powinny być wprowadzane za pośrednictwem kreatora usługi Azure AD Connect.
- Dodano nowy błąd w przypadku problemów z zasadami haseł użytkownika.
- Zapobiegaj błędnej konfiguracji filtrowania grup według filtrów domeny i oiom. Filtrowanie grup spowoduje wyświetlenie błędu, gdy domena/ou wprowadzonej grupy jest już odfiltrowana i uniemożliwi użytkownikowi przejście do przodu, dopóki problem nie zostanie rozwiązany.
- Użytkownicy nie mogą już tworzyć łącznika dla Usług domenowych Active Directory ani usługi Windows Azure Active Directory w interfejsie użytkownika menedżera usług synchronizacji.
- Poprawiono dostępność niestandardowych formantów interfejsu użytkownika w Menedżerze usług synchronizacji.
- Włączono sześć zadań zarządzania federacją dla wszystkich metod logowania w usłudze Azure AD Connect.  (Wcześniej tylko zadanie "Aktualizuj certyfikat TLS/SSL usługi AD FS" było dostępne dla wszystkich logów).
- Dodano ostrzeżenie podczas zmiany metody logowania z federacji na PHS lub PTA, że wszystkie domeny usługi Azure AD i użytkownicy zostaną przekonwertowane na uwierzytelnianie zarządzane.
- Usunięto certyfikaty podpisywania tokenów z zadania "Resetowanie zaufania usług Ad i AD FS" i dodano oddzielne zadanie podrzędne w celu zaktualizowania tych certyfikatów.
- Dodano nowe zadanie zarządzania federacją o nazwie "Zarządzaj certyfikatami", które ma podzadanie do aktualizowania certyfikatów TLS lub podpisywania tokenów dla farmy usług AD FS.
- Dodano nowe zadanie podrzędne zarządzania federacją o nazwie "Określ serwer podstawowy", które umożliwia administratorom określenie nowego serwera podstawowego dla farmy usług AD FS.
- Dodano nowe zadanie zarządzania federacją o nazwie "Zarządzaj serwerami", które ma podzadanie do wdrożenia serwera usług AD FS, wdrożenia serwera proxy aplikacji sieci Web i określenia serwera podstawowego.
- Dodano nowe zadanie zarządzania federacją o nazwie "Wyświetl konfigurację federacji", które wyświetla bieżące ustawienia usług AD FS.  (Z powodu tego dodatku ustawienia usług AD FS zostały usunięte ze strony "Przejrzyj rozwiązanie").

### <a name="fixed-issues"></a>Rozwiązane problemy
- Rozwiązano problem z błędem synchronizacji w scenariuszu, w którym obiekt użytkownika przejmujący odpowiedni obiekt kontaktowy ma samooceny (np. użytkownik jest ich własnym menedżerem).
- Wyskakujące okienka pomocy są teraz wyświetlane na podstawie fokusu klawiatury.
- W przypadku automatycznego uaktualniania, jeśli jakakolwiek aplikacja powodująca konflikt jest uruchomiona od 6 godzin, zabij ją i kontynuuj uaktualnienie.
- Ogranicz liczbę atrybutów, które klient może wybrać do 100 na obiekt podczas wybierania rozszerzeń katalogów. Zapobiegnie to wystąpieniu błędu podczas eksportowania, ponieważ platforma Azure ma maksymalnie 100 atrybutów rozszerzenia na obiekt.
- Naprawiono błąd, który powodował, że skrypt łączności usługi AD był bardziej niezawodny
- Naprawiono błąd, aby AADConnect zainstalować na komputerze przy użyciu istniejącej usługi Named Pipes WCF bardziej niezawodne.
- Ulepszona diagnostyka i rozwiązywanie problemów wokół zasad grupy, które nie zezwalają na uruchomienie usługi ADSync po początkowym zainstalowaniu.
- Naprawiono błąd, który powodował nieprawidłowe zapisanie nazwy ekranu komputera z systemem Windows.
- Napraw błąd polegający na niepoprawnym zapisaniu typu systemu operacyjnego dla komputera z systemem Windows.
- Naprawiono błąd, który powodował nieoczekiwaną synchronizację komputerów z systemem windows 10. Należy zauważyć, że skutkiem tej zmiany jest to, że komputery inne niż Windows-10, które były wcześniej zsynchronizowane, zostaną usunięte. Nie ma to wpływu na żadne funkcje, ponieważ synchronizacja komputerów z systemem Windows jest używana tylko w przypadku sprzężenia domeny usługi Azure Hybrid, która działa tylko dla urządzeń z systemem Windows-10.
- Dodano kilka nowych (wewnętrznych) poleceń cmdlet do modułu programu ADSync PowerShell.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Znany jest problem z uaktualnieniem usługi Azure AD Connect z wcześniejszej wersji do wersji 1.3.21.0, w którym portal O365 nie odzwierciedla zaktualizowanej wersji, mimo że usługa Azure AD Connect została pomyślnie uaktualniona.
>
> Aby rozwiązać ten problem, należy zaimportować`Set-ADSyncDirSyncConfiguration` moduł **AdSync,** a następnie uruchomić polecenie cmdlet programu PowerShell na serwerze usługi Azure AD Connect.  Można wykonać następujące kroki:
>
>1. Otwórz program PowerShell w trybie administracyjnym.
>2. Uruchom polecenie `Import-Module "ADSync"`.
>3. Uruchom polecenie `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stan wydania 

14.05.2019: Premiera do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy 

- Naprawiono błąd dotyczący luki w zabezpieczeniach uprawnień występującej w kompilacji 1.3.20.0 usługi Microsoft Azure Active Directory Connect.  Ta luka w zabezpieczeniach, pod pewnymi warunkami, może umożliwić osobie atakującej wykonanie dwóch poleceń cmdlet programu PowerShell w kontekście konta uprzywilejowanego i wykonanie akcji uprzywilejowanych.  Ta aktualizacja zabezpieczeń rozwiązuje ten problem, wyłączając te polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [aktualizacja zabezpieczeń](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Stan wydania 

24.04.2019: Premiera do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

- Dodawanie obsługi odświeżania domeny 
- Funkcja Foldery publiczne programu Exchange Mail przechodzi ga 
- Usprawnij obsługę błędów kreatora w przypadku awarii usługi 
- Dodano łącze ostrzegawcze w interfejsie użytkownika menedżera usług synchronizacji na stronie właściwości łącznika. 
- Funkcja ujednoliconych grup Writeback jest teraz GA 
- Ulepszony komunikat o błędzie SSPR, gdy w kontrolerze domeny brakuje kontrolki LDAP 
- Dodano diagnostykę błędów rejestru DCOM podczas instalacji  
- Lepsze śledzenie błędów PHS RPC 
- Zezwalaj na credy EA z domeny podrzędnej 
- Zezwalaj na wprowadzanie nazwy bazy danych podczas instalacji (domyślna nazwa ADSync)
- Uaktualnij do usługi ADAL 3.19.8, aby odebrać poprawkę WS-Trust dla pingu i dodać obsługę nowych wystąpień platformy Azure 
- Modyfikowanie reguł synchronizacji grup, aby przepływać samAccountName, DomainNetbios i DomainFQDN do chmury - potrzebne do oświadczeń 
- Zmodyfikowana domyślna obsługa reguł synchronizacji — więcej [tutaj](how-to-connect-fix-default-rules.md).
- Dodano nowego agenta działającego jako usługa systemu Windows. Ten agent o nazwie "Agent administracyjny" umożliwia głębszą zdalną diagnostykę serwera usługi Azure AD Connect, aby ułatwić inżynierom microsoftowi rozwiązywanie problemów podczas otwierania przypadku pomocy technicznej. Ten agent nie jest zainstalowany i domyślnie włączony.  Aby uzyskać więcej informacji na temat instalowania i włączania agenta, zobacz [Co to jest agent administracyjny usługi Azure AD Connect?](whatis-aadc-admin-agent.md). 
- Zaktualizowano umowę licencyjną użytkownika końcowego (EULA) 
- Dodano obsługę automatycznego uaktualniania dla wdrożeń, które używają usług AD FS jako swojego typu logowania.  Spowoduje to również usunięcie wymogu aktualizowania zaufania jednostki uzależnionej usługi AD azure usługi AD w ramach procesu uaktualniania. 
- Dodano zadanie zarządzania zaufaniem usługi Azure AD, które udostępnia dwie opcje: analizuj/aktualizuj zaufanie i resetuj zaufanie. 
- Zmieniono zachowanie zaufania jednostki uzależniającej usługi AD Azure ad, tak aby zawsze używał przełącznika -SupportMultipleDomain (obejmuje zaufanie i aktualizacje domeny usługi Azure AD). 
- Zmieniono zachowanie farmy instalowanych nowych usług AD FS, tak aby wymagało certyfikatu .pfx, usuwając opcję używania wstępnie zainstalowanego certyfikatu.
- Zaktualizowano nowy przepływ pracy farmy usług AD FS, tak aby umożliwiał on wdrażanie tylko 1 usług AD FS i 1 serwera WAP.  Wszystkie dodatkowe serwery zostaną wykonane po wstępnej instalacji. 

### <a name="fixed-issues"></a>Rozwiązane problemy 

- Naprawianie logiki ponownego łączenia SQL dla usługi ADSync 
- Poprawka umożliwiająca czystą instalację przy użyciu pustej bazy danych SQL AOA 
- Napraw skrypt uprawnień ps, aby zawęzić uprawnienia GWB 
- Napraw błędy usługi VSS w localdb  
- Naprawianie wprowadzającego w błąd komunikatu o błędzie, gdy typ obiektu nie znajduje się w zakresie 
- Naprawiono błąd, który powodował, że instalacja programu Azure AD PowerShell na serwerze może potencjalnie spowodować konflikt zestawu z usługą Azure AD Connect. 
- Naprawiono błąd PHS na serwerze przejściowym, gdy poświadczenia łącznika są aktualizowane w interfejsie użytkownika programu Synchronizacja menedżera usług. 
- Naprawiono niektóre przecieki pamięci 
- Różne poprawki autoupgrade 
- Różne poprawki do eksportu i niepotwierdzonego przetwarzania importu 
- Naprawiono błąd podczas obsługi ukośnika odwrotnego w filtrowaniu domeny i ou 
- Rozwiązano problem polegający na tym, że usługa ADSync zatrzymała się o więcej niż 2 minuty i wystąpiła problem w czasie uaktualniania. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Stan wydania

12/18/2018: Wydany do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta kompilacja aktualizuje niestandardowe łączniki (na przykład ogólny łącznik LDAP i ogólny łącznik SQL) dostarczane z usługą Azure AD Connect. Aby uzyskać więcej informacji na temat odpowiednich łączników, zobacz wersję 1.1.911.0 w [historii wersji łącznika](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Stan wydania
12/11/2018: Wydany do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy
Ta kompilacja poprawek umożliwia użytkownikowi wybranie domeny docelowej w określonym lesie dla kontenera RegisteredDevices podczas włączania zapisywania zwrotnego urządzenia.  W poprzednich wersjach, które zawierają nowe funkcje Opcje urządzenia (1.1.819.0 – 1.2.68.0), lokalizacja kontenera RegisteredDevices była ograniczona do katalogu głównego lasu i nie zezwalała na domeny podrzędne.  To ograniczenie objawiało się tylko w nowych wdrożeniach — uaktualnienia w miejscu pozostały nienaruszone.  

Jeśli dowolna kompilacja zawierająca zaktualizowaną funkcję Opcje urządzenia została wdrożona na nowym serwerze i zapisu zwrotnego urządzenia została włączona, należy ręcznie określić lokalizację kontenera, jeśli nie chcesz go w katalogu głównym lasu.  Aby to zrobić, należy wyłączyć zapisywanie zwrotne urządzenia i ponownie włączyć go, co pozwoli określić lokalizację kontenera na stronie "Las stortowania".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Stan wydania 

30.11.2018: Wydany do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta kompilacja poprawek rozwiązuje konflikt, w którym może wystąpić błąd uwierzytelniania spowodowany niezależną obecnością modułu galerii programu MSOnline PowerShell na serwerze synchronizacji.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Stan wydania 

11/19/2018: Wydany do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta kompilacja poprawek rozwiązuje regresję w poprzedniej kompilacji, w której zapisywanie hasła kończy się niepowodzeniem podczas korzystania z kontrolera domeny ADDS w systemie Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Stan wydania 

25.10.2018: wydany do pobrania

 
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 


- Zmieniono funkcjonalność odpisu atrybutu, aby upewnić się, że hostowana poczta głosowa działa zgodnie z oczekiwaniami.  W niektórych scenariuszach usługa Azure AD nadpisywała atrybut msExchUcVoicemailSettings podczas odpisu z wartością null.  Usługa Azure AD nie będzie już czyścić wartość lokalną tego atrybutu, jeśli wartość chmury nie jest ustawiona.
- Dodano diagnostykę w kreatorze usługi Azure AD Connect w celu zbadania i zidentyfikowania problemów z łącznością z usługą Azure AD. Te same diagnostyki można również uruchomić bezpośrednio za pośrednictwem programu PowerShell przy użyciu polecenia cmdlet Test- AdSyncAzureServiceConnectivity. 
- Dodano diagnostykę w kreatorze usługi Azure AD Connect w celu zbadania i zidentyfikowania problemów z łącznością z usługą AD. Te same diagnostyki można również uruchomić bezpośrednio za pośrednictwem programu PowerShell przy użyciu funkcji Start-ConnectivityValidation w module ADConnectivityTools PowerShell.  Aby uzyskać więcej informacji, zobacz [Co to jest moduł ADConnectivityTool PowerShell?](how-to-connect-adconnectivitytools.md)
- Dodano wersję schematu usługi AD, aby sprawdzić wstępnie sprzężenie hybrydowe usługi Azure Active Directory i odpisu zapisu urządzenia 
- Zmieniono wyszukiwanie atrybutów strony rozszerzenia katalogu na niewrażliwe na wielkość liter.
-   Dodano pełną obsługę TLS 1.2. Ta wersja obsługuje wszystkie inne protokoły są wyłączone i tylko TLS 1.2 jest włączony na komputerze, na którym jest zainstalowany usługi Azure AD Connect.  Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu TLS 1.2 dla usługi Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Rozwiązane problemy   

- Naprawiono błąd, który powodował niepowodzenie uaktualniania usługi Azure AD Connect, jeśli używany był program SQL Always On. 
- Naprawiono błąd, aby poprawnie przeanalizować nazwy OU, które zawierają ukośnik do przodu. 
- Naprawiono błąd, który powodował, że uwierzytelnianie przekazywane było wyłączone dla czystej instalacji w trybie przejściowym. 
- Naprawiono błąd, który uniemożliwiał załadowanie modułu programu PowerShell podczas uruchamiania narzędzi do rozwiązywania problemów 
- Naprawiono błąd, który blokował klientom używanie wartości liczbowych w pierwszym znaku nazwy hosta. 
- Naprawiono błąd, który powodował, że usługa Azure AD Connect zezwalała na nieprawidłowe partycje i wybór kontenera 
- Naprawiono komunikat o błędzie "Nieprawidłowe hasło", gdy funkcja logowania sytego pulpitu jest włączona. 
- Różne poprawki błędów dla zarządzania zaufaniem usług AD FS  
- Podczas konfigurowania zapisu urządzenia - poprawiono sprawdzanie schematu w celu wyszukania klasy obiektu msDs-DeviceContainer (wprowadzonego na WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: wydany do pobrania, nie zostanie wydany do automatycznego uaktualnienia 

### <a name="fixed-issues"></a>Rozwiązane problemy  

Uaktualnienie usługi Azure AD Connect kończy się niepowodzeniem, jeśli dostępność programu SQL Always On jest skonfigurowana dla bazy danych ADSync. Ta poprawka rozwiązuje ten problem i umożliwia uaktualnienie, aby odnieść sukces. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Stan wydania

21.08.2018: Wydany do pobrania i automatycznego uaktualnienia. 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Integracja ping federacji w usłudze Azure AD Connect jest teraz dostępna dla ogólnej dostępności. [Dowiedz się więcej o tym, jak sfederować usługę Azure AD za pomocą usługi Ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Usługa Azure AD Connect tworzy teraz kopię zapasową zaufania usługi Azure AD w usługach AD FS za każdym razem, gdy zostanie dokonana aktualizacja, i przechowuje ją w oddzielnym pliku w celu łatwego przywracania w razie potrzeby. [Dowiedz się więcej o nowych funkcjach i zarządzaniu zaufaniem usługi Azure AD w usłudze Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Nowe narzędzia do rozwiązywania problemów pomagają w rozwiązywaniu problemów ze zmianą podstawowego adresu e-mail i ukrywaniem konta z globalnej listy adresów
- Usługa Azure AD Connect została zaktualizowana w celu uwzględnienia najnowszego klienta macierzystego programu SQL Server 2012
- Po przełączeniu logowania użytkownika do synchronizacji skrótów haseł lub uwierzytelniania przekazywania w zadaniu "Zmień logowanie użytkownika" pole wyboru Bezproblemowe logowanie jednokrotne jest domyślnie włączone.
- Dodano obsługę systemu Windows Server Essentials 2019
- Agent usługi Azure AD Connect Health został zaktualizowany do najnowszej wersji 3.1.7.0
- Podczas uaktualniania, jeśli instalator wykryje zmiany w domyślnych regułach synchronizacji, administrator zostanie poproszony o ostrzeżenie przed zastąpieniem zmodyfikowanych reguł. Umożliwi to użytkownikowi podjęcie działań naprawczych i wznowienie później. Stare zachowanie: Jeśli nie było żadnych zmodyfikowanych out-of-box reguły następnie ręczne uaktualnienie było zastąpienie tych reguł bez podawania ostrzeżenia dla użytkownika i harmonogram synchronizacji został wyłączony bez informowania użytkownika. Nowe zachowanie: Użytkownik zostanie poproszony o ostrzeżenie przed zastąpieniem zmodyfikowanych reguł synchronizacji out-of-box. Użytkownik będzie miał możliwość zatrzymania procesu uaktualniania i wznowienia później po podjęciu działań naprawczych.
- Zapewnij lepszą obsługę problemu ze zgodnością FIPS, zapewniając komunikat o błędzie dla generowania skrótów MD5 w środowisku zgodnym ze standardem FIPS oraz łącze do dokumentacji, która zapewnia obejście tego problemu.
- Aktualizacja interfejsu użytkownika w celu poprawy zadań federacji w kreatorze, które są teraz w ramach oddzielnej podgrupy dla federacji. 
- Wszystkie dodatkowe zadania federacji są teraz grupowane w jednym podmenu w celu ułatwienia użycia.
- Nowy odnowiony moduł ADSyncConfig Posh (AdSyncConfig.psm1) z nowymi funkcjami uprawnień usługi AD przeniesiony ze starego pliku ADSyncPrep.psm1 (który może zostać wkrótce przestarzały)

### <a name="fixed-issues"></a>Rozwiązane problemy 

- Naprawiono błąd, który powodował, że serwer AAD Connect wykazywał wysokie użycie procesora po uaktualnieniu do platformy .NET 4.7.2
- Naprawiono błąd, który sporadycznie powodował, że pojawiał się komunikat o błędzie dla problemu z automatycznie rozwiązanym zakleszczeniem SQL
- Naprawiono kilka problemów z dostępnością edytora reguł synchronizacji i Menedżera usługi synchronizacji  
- Naprawiono błąd, który powodował, że usługa Azure AD Connect nie mogła uzyskać informacji o ustawieniach rejestru
- Naprawiono błąd, który powodował problemy, gdy użytkownik przechodzi do przodu/do tyłu w kreatorze
- Naprawiono błąd, aby zapobiec wystąpieniu błędu z powodu nieprawidłowego przekazywania wielu wątków w kreatorze
- Gdy strona filtrowania synchronizacji grupowej napotka błąd LDAP podczas rozpoznawania grup zabezpieczeń, usługa Azure AD Connect zwraca teraz wyjątek z pełną wiernością.  Główna przyczyna wyjątku odwołania jest nadal nieznana i zostanie rozwiązana przez inny błąd.
-  Naprawiono błąd, który powodował, że uprawnienia do kluczy STK i NGC (atrybut ms-DS-KeyCredentialLink w obiektach użytkownika/urządzenia dla WHfB) nie były poprawnie ustawione.     
- Naprawiono błąd, który powodował, że "Set-ADSyncRestrictedPermissions" nie było wywoływane poprawnie
-  Dodawanie obsługi uprawnień do udzielania uprawnień w kreatorze instalacji grupy Writeback w kreatorze instalacji AADConnect
- Podczas zmiany metody logowania z synchronizacji skrótów haseł do usług AD FS synchronizacja skrótów haseł nie została wyłączona.
- Dodano weryfikację adresów IPv6 w konfiguracji usług AD FS
- Zaktualizowano komunikat powiadomienia, aby poinformować, że istnieje istniejąca konfiguracja.
- Zapisywanie zwrotne urządzenia nie wykrywa kontenera w niezaufanym lesie. Zostało to zaktualizowane w celu zapewnienia lepszego komunikatu o błędzie i łącza do odpowiedniej dokumentacji
- Usuwanie zaznaczania interfejsu organizacyjnego, a następnie synchronizacja/stornia odpowiadające tej ou daje błąd synchronizacji ogólnej. Zostało to zmienione, aby utworzyć bardziej zrozumiały komunikat o błędzie.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stan wydania

5/14/2018: Wydany do automatycznego uaktualniania i pobierania.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

Nowe funkcje i ulepszenia

- Ta wersja zawiera publiczną wersję zapoznawczą integracji PingFederate w usłudze Azure AD Connect. Dzięki tej wersji klienci mogą łatwo i niezawodnie skonfigurować swoje środowisko usługi Azure Active Directory, aby wykorzystać pingfederate jako dostawcę federacji. Aby dowiedzieć się więcej o tym, jak korzystać z tej nowej funkcji, odwiedź naszą [dokumentację online.](plan-connect-user-signin.md#federation-with-pingfederate) 
- Zaktualizowano narzędzie do rozwiązywania problemów z kreatorem usługi Azure AD Connect, w którym analizuje teraz więcej scenariuszy błędów, takich jak połączone skrzynki pocztowe i grupy dynamiczne usługi AD. Przeczytaj więcej o narzędziu do rozwiązywania problemów [tutaj](tshoot-connect-objectsync.md).
- Konfiguracja stornia zwrotnego urządzenia jest teraz zarządzana wyłącznie w ramach Kreatora usługi Azure AD Connect.
- Dodano nowy moduł programu PowerShell o nazwie ADSyncTools.psm1, który może służyć do rozwiązywania problemów z łącznością SQL i różnych innych narzędzi do rozwiązywania problemów. Przeczytaj więcej o module ADSyncTools [tutaj](tshoot-connect-tshoot-sql-connectivity.md). 
- Dodano nowe dodatkowe zadanie "Konfigurowanie opcji urządzenia". Za pomocą zadania można skonfigurować następujące dwie operacje: 
  - **Sprzężenie usługi AD hybrydowej:** Jeśli twoje środowisko ma lokalną usługę AD i chcesz również korzystać z możliwości oferowanych przez usługę Azure Active Directory, możesz zaimplementować hybrydowe urządzenia przyłączone do usługi Azure AD. Są to urządzenia dołączone do lokalnej usługi Active Directory oraz usługi Azure Active Directory.
  - **Zapisywanie zwrotne urządzenia:** Zapisywanie zwrotne urządzenia służy do włączania dostępu warunkowego na podstawie urządzeń do urządzeń chronionych usługami AD FS (2012 R2 lub nowszych)

    >[!NOTE] 
    > - Opcja włączenia zapisywania zwrotnego urządzenia z opcji Dostosowywania synchronizacji zostanie wyszarzona. 
    > -  Moduł programu PowerShell dla programu ADPrep jest przestarzały w tej wersji.



### <a name="fixed-issues"></a>Rozwiązane problemy 

- Ta wersja aktualizuje instalację programu SQL Server Express do dodatku SP4 dla programu SQL Server 2012, która zawiera między innymi poprawki dotyczące kilku luk w zabezpieczeniach.  Zobacz [tutaj,](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) aby uzyskać więcej informacji na temat dodatku SP4 dla programu SQL Server 2012.
- Przetwarzanie reguł synchronizacji: reguły synchronizacji sprzężenia wychodzącego bez warunku sprzężenia powinny zostać ścięte, jeśli nadrzędna reguła synchronizacji nie ma już zastosowania
- W interfejsie użytkownika programu Synchronizacja i Edytorze reguł synchronizacji zastosowano kilka poprawek ułatwień dostępu
- Kreator usługi Azure AD Connect: błąd podczas tworzenia konta łącznika usługi AD, gdy usługa Azure AD Connect znajduje się w grupie roboczej
- Kreator połączenia usługi Azure AD: na stronie logowania usługi Azure AD wyświetl pole wyboru weryfikacji za każdym razem, gdy występuje niezgodność w domenach usług AD i domenach zweryfikowanych w usłudze Azure AD
- Automatyczne uaktualnianie poprawki programu PowerShell, aby ustawić stan automatycznego uaktualniania poprawnie w niektórych przypadkach po próbie automatycznego uaktualnienia.
- Kreator usługi Azure AD Connect: Zaktualizowano dane telemetryczne w celu przechwycenia wcześniej brakujących informacji
- Kreator usługi Azure AD Connect: Następujące zmiany zostały wprowadzone podczas korzystania z zadania **zmiany logowania użytkownika,** aby przełączyć się z usług AD FS do uwierzytelniania przekazywanego:
    - Agent uwierzytelniania przekazywania jest zainstalowany na serwerze usługi Azure AD Connect, a funkcja uwierzytelniania przekazywania jest włączona, zanim przekonwertujemy domeny z federacyjnego na zarządzane.
    - Użytkownicy nie są już konwertowani z federacyjnego na zarządzany. Konwertowane są tylko domeny.
- Kreator połączenia usługi Azure AD Connect: Usługa AD FS Multi Domain Regex nie jest poprawna, gdy nazwa UPN użytkownika ma ' specjalną aktualizację znaku Regex do obsługi znaków specjalnych
- Kreator usługi Azure AD Connect: usuń fałszywy komunikat "Konfigurowanie atrybutu zakotwiczenia źródła", gdy nie ma zmian 
- Kreator usługi Azure AD Connect: obsługa usług AD FS dla scenariusza podwójnej federacji
- Kreator połączenia usługi Azure AD: Oświadczenia usług AD FS nie są aktualizowane dla dodanej domeny podczas konwertowania domeny zarządzanej na sfederowane
- Kreator usługi Azure AD Connect: Podczas wykrywania zainstalowanych pakietów znajdujemy przestarzałe produkty związane z dirsync/Azure AD Sync/Azure AD Connect. Teraz spróbujemy odinstalować przestarzałe produkty.
- Kreator usługi Azure AD Connect: poprawne mapowanie komunikatów o błędach, gdy instalacja agenta uwierzytelniania przekazywanego nie powiedzie się
- Kreator usługi Azure AD Connect: Usunięto kontener "Konfiguracja" ze strony filtrowania operacji organizacyjnej domeny
- Instalacja aparatu synchronizacji: usuń niepotrzebną starszą logikę, która od czasu do czasu nie powiodła się z instalacji msi z aparatu synchronizacji
- Kreator łączy usługi Azure AD Connect: Napraw tekst pomocy podręcznej na stronie Funkcje opcjonalne dla synchronizacji skrótów haseł
- Środowisko uruchomieniowe aparatu synchronizacji: Napraw scenariusz, w którym obiekt CS ma zaimportowane delete i reguły synchronizacji próbują ponownie aprowizować obiekt.
- Środowisko uruchomieniowe aparatu synchronizacji: dodaj łącze pomocy dla przewodnika rozwiązywania problemów z łącznością online do dziennika zdarzeń dla błędu importu
- Środowisko uruchomieniowe aparatu synchronizacji: mniejsze użycie harmonogramu synchronizacji podczas wyliczania łączników
- Kreator usługi Azure AD Connect: rozwiązywanie problemu z niestandardowym kontem usługi synchronizacji, które nie ma uprawnień do odczytu usługi AD
- Kreator połączenia usługi Azure AD: usprawnianie rejestrowania opcji filtrowania domeny i usługi organizacyjnej
- Kreator połączenia usługi Azure AD: adfs Dodaj oświadczenia domyślne do zaufania federacji utworzonego dla scenariusza usługi MFA
- Kreator połączenia usługi Azure AD Connect: Wdrażanie wap w usłudze AD FS: dodawanie serwera nie może używać nowego certyfikatu
- Kreator usługi Azure AD Connect: wyjątek DSSO, gdy program OnPremCredentials nie jest inicjowany dla domeny 
- Preferencyjnie przepływ ad distinguishedName atrybut z active user obiektu.
- Naprawiono błąd kosmetyczny, że pierwszeństwo pierwszej reguły synchronizacji OOB zostało ustawione na 99 zamiast 100.



## <a name="117510"></a>1.1.751.0
Stan 4/12/2018: Wydany tylko do pobrania

>[!NOTE]
>Ta wersja jest poprawką dla usługi Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Synchronizacja programu Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
Naprawiono błąd, ponieważ automatyczne odnajdowanie wystąpienia platformy Azure dla dzierżaw internetowych czasami nie powodował awarii.  

### <a name="ad-fs-management"></a>Usługi AD FS zarządzania
#### <a name="fixed-issues"></a>Rozwiązane problemy

Wystąpił problem w logice ponawiania konfiguracji, który spowodowałby argumentException stwierdzający, że "element z tym samym kluczem został już dodany."  Spowodowałoby to, że wszystkie operacje ponawiania nie powiodły się.

## <a name="117500"></a>1.1.750.0
Stan 22.03.2018: Wydany do automatycznego uaktualniania i pobierania.
>[!NOTE]
>Po zakończeniu uaktualnienia do tej nowej wersji automatycznie wyzwoli pełną synchronizację i pełne importowanie łącznika usługi Azure AD i pełną synchronizację łącznika usługi AD. Ponieważ może to zająć trochę czasu, w zależności od rozmiaru środowiska usługi Azure AD Connect, upewnij się, że podjęto niezbędne kroki w celu obsługi tego lub wstrzymać uaktualnianie, dopóki nie znajdziesz dogodnego momentu, aby to zrobić.

>[!NOTE]
>"Funkcja AutoUpgrade została niepoprawnie wyłączona dla niektórych dzierżawców, którzy wdrożyli kompilacje później niż 1.1.524.0. Aby upewnić się, że wystąpienie usługi Azure AD Connect nadal kwalifikuje się do automatycznego uaktualniania, uruchom następujące polecenie cmdlet programu PowerShell: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy

* Polecenie cmdlet Set-ADSyncAutoUpgrade wcześniej blokowało autoupgrade, jeśli stan automatycznego uaktualniania jest ustawiony na Zawieszony. Ta funkcja została zmieniona, więc nie blokuje AutoUpgrade przyszłych kompilacji.
* Zmieniono opcję strony **logowania użytkownika** "Synchronizacja haseł" na "Synchronizacja skrótów haseł".  Usługa Azure AD Connect synchronizuje skróty haseł, a nie hasła, więc jest to zgodne z tym, co faktycznie występuje.  Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stan: Wydany dla wybranych odbiorców

>[!NOTE]
>Po zakończeniu uaktualnienia do tej nowej wersji automatycznie wyzwoli pełną synchronizację i pełne importowanie łącznika usługi Azure AD i pełną synchronizację łącznika usługi AD. Ponieważ może to zająć trochę czasu, w zależności od rozmiaru środowiska usługi Azure AD Connect, upewnij się, że podjęto niezbędne kroki w celu obsługi tego lub wstrzymać uaktualnianie, dopóki nie znajdziesz dogodnego momentu, aby to zrobić.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Napraw okno chronometrażu w zadaniach w tle dla strony filtrowania partycji podczas przełączania do następnej strony.

* Naprawiono błąd, który powodował naruszenie zasad dostępu podczas akcji niestandardowej configdb.

* Naprawiono błąd, aby odzyskać z limitu czasu połączenia SQL.

* Naprawiono błąd, który powodował, że certyfikaty z symbolami wieloznaczymi sieci SAN nie powiodły się podczas sprawdzania wstępnego.

* Naprawiono błąd, który powodował awarię programu miiserver.exe podczas eksportowania łącznika usługi Azure AD.

* Naprawiono błąd, który powodował, że podczas uruchamiania kreatora usługi Azure AD Connect podczas uruchamiania kreatora usługi Azure AD Connect była rejestrowana próba zalogowania się do usługi Azure AD Connect.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Dodawanie ustawień prywatności dla ogólnego rozporządzenia o ochronie danych (RODO).  Aby uzyskać więcej informacji, zobacz artykuł [tutaj](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria aplikacji - administrator może przełączać tę klasę danych przy woli

* Dane usługi Azure AD Health — administrator musi odwiedzić portal kondycji, aby kontrolować swoje ustawienia kondycji.
   Po zmianie zasad usługi agenci będą je odczytywać i wymuszać.

* Dodano akcje konfiguracji zapisu wstecznego urządzenia i pasek postępu do inicjowania strony

* Ulepszona diagnostyka ogólna z raportem HTML i pełnym zbieraniem danych w raporcie ZIP-Text / HTML

* Poprawiono niezawodność automatycznej aktualizacji i dodano dodatkowe dane telemetryczne, aby zapewnić możliwość ustalenia kondycji serwera

* Ograniczanie uprawnień dostępnych dla kont uprzywilejowanych na koncie łącznika usługi AD

  * W przypadku nowych instalacji kreator ograniczy uprawnienia, które konta uprzywilejowane mają na koncie MSOL po utworzeniu konta MSOL.

Zmiany będą miały na celu:
1. Instalacje ekspresowe
2. Instalacje niestandardowe z kontem Auto-Create
3. Zmieniono instalator, aby nie wymagał uprawnień SA podczas czystej instalacji usługi Azure AD Connect

* Dodano nowe narzędzie do rozwiązywania problemów z synchronizacją dla określonego obiektu. Jest on dostępny w opcji "Rozwiązywanie problemów z synchronizacją obiektów" kreatora azure ad connect Rozwiązywanie problemów z dodatkowymi zadaniami. Obecnie narzędzie sprawdza, co następuje:

  * Niezgodność userPrincipalName między zsynchronizowanym obiektem użytkownika a kontem użytkownika w dzierżawie usługi Azure AD.
  * Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania domeny
  * Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania jednostki organizacyjnej

* Dodano nowe narzędzie do synchronizowania bieżącego skrótu hasła przechowywanego w lokalnej usłudze Active Directory dla określonego konta użytkownika.

Narzędzie nie wymaga zmiany hasła. Jest on dostępny w obszarze "Rozwiązywanie problemów z synchronizacją hash synchronizacji haseł" kreatora usługi Azure AD Connect Rozwiązywanie problemów z dodatkowymi zadaniami.






## <a name="116540"></a>1.1.654.0
Status: 12 grudnia 2017

>[!NOTE]
>Ta wersja jest poprawką związaną z zabezpieczeniami dla usługi Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Dodano ulepszenie do usługi Azure AD Connect w wersji 1.1.654.0 (i później), aby upewnić się, że zalecane zmiany uprawnień opisane w sekcji [Blokowanie dostępu do konta usług AD DS](#lock) są automatycznie stosowane podczas tworzenia konta usług AD DS usługi Azure AD. 

- Podczas konfigurowania usługi Azure AD Connect administrator instalacji może podać istniejące konto usług AD DS lub pozwolić usłudze Azure AD Connect automatycznie utworzyć konto. Zmiany uprawnień są automatycznie stosowane do konta usług AD DS, które jest tworzone przez usługę Azure AD Connect podczas instalacji. Nie są one stosowane do istniejącego konta usług AD DS udostępnianego przez administratora instalacji.
- W przypadku klientów, którzy uaktualnili ze starszej wersji usługi Azure AD Connect do wersji 1.1.654.0 (lub później), zmiany uprawnień nie będą stosowane z mocą wsteczną do istniejących kont usług AD DS utworzonych przed uaktualnieniem. Będą one stosowane tylko do nowych kont usług AD DS utworzonych po uaktualnieniu. Dzieje się tak podczas dodawania nowych lasów usługi AD do synchronizacji z usługą Azure AD.

>[!NOTE]
>Ta wersja usuwa tylko lukę w zabezpieczeniach dla nowych instalacji usługi Azure AD Connect, w których konto usługi jest tworzone przez proces instalacji. W przypadku istniejących instalacji lub w przypadkach, gdy użytkownik samodzielnie podasz konto, należy upewnić się, że luka ta nie istnieje.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>Blokowanie dostępu do konta usług AD DS
Zablokuj dostęp do konta usług AD DS, implementując następujące zmiany uprawnień w lokalnej układzie AD:  

*   Wyłącz dziedziczenie określonego obiektu
*   Usuń wszystkie wpisy ACE na określonym obiekcie, z wyjątkiem obiektów ACE specyficznych dla SELF. Chcemy zachować domyślne uprawnienia nienaruszone, jeśli chodzi o SELF.
*   Przypisz te określone uprawnienia:

Typ     | Nazwa                          | Dostęp               | Dotyczy:
---------|-------------------------------|----------------------|--------------|
Zezwalaj    | SYSTEM                        | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Enterprise Admins             | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy domeny                 | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy                | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Zawartość listy        | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Uprawnienia do odczytu     | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Zawartość listy        | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Uprawnienia do odczytu     | Ten obiekt  |

Aby dokręcić ustawienia konta usług AD DS, można uruchomić [ten skrypt programu PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Skrypt programu PowerShell przypisze wyżej wymienione uprawnienia do konta usług AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skrypt programu PowerShell w celu dokręcenia istniejącego konta usługi

Aby użyć skryptu programu PowerShell, aby zastosować te ustawienia, do istniejącego konta USŁUG AD DS (eter dostarczony przez organizację lub utworzony przez poprzednią instalację usługi Azure AD Connect, pobierz skrypt z podanego łącza powyżej.

##### <a name="usage"></a>Użycie:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Lokalizacja 

**$ObjectDN** = konto usługi Active Directory, którego uprawnienia muszą zostać zaostrzone.

**$Credential** = poświadczenia administratora, który ma uprawnienia niezbędne do ograniczenia uprawnień na koncie $ObjectDN. Te uprawnienia są zazwyczaj przechowywane przez administratora przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowanej nazwy domeny konta administratora, aby uniknąć błędów wyszukiwania kont. Przykład: contoso.com\admin.

>[!NOTE] 
>$credential. Nazwa użytkownika powinna być w formacie FQDN\username. Przykład: contoso.com\admin 

##### <a name="example"></a>Przykład:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Czy ta luka została wykorzystana do uzyskania nieautoryzowanego dostępu?

Aby sprawdzić, czy ta luka została wykorzystana do naruszenia konfiguracji usługi Azure AD Connect, należy sprawdzić datę ostatniego zresetowania hasła konta usługi.  Jeśli sygnatura czasowa w nieoczekiwane, dalsze badania, za pośrednictwem dziennika zdarzeń, dla tego zdarzenia resetowania hasła, powinny być podjęte.

Aby uzyskać więcej informacji, zobacz [Poradnik zabezpieczeń firmy Microsoft 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Stan: 27 października 2017

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji automatycznego uaktualniania usługi Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Rozwiązano problem 
* Rozwiązaliśmy problem ze zgodnością wersji między usługą Azure AD Connect a agentem kondycji usługi Azure AD Connect (do synchronizacji). Ten problem dotyczy klientów, którzy wykonują uaktualnienie usługi Azure AD Connect w miejscu do wersji 1.1.647.0, ale obecnie ma agenta kondycji w wersji 3.0.127.0. Po uaktualnieniu agent kondycji nie może już wysyłać danych o kondycji usługi Azure AD Connect Synchronizacja do usługi Azure AD Health Service. Dzięki tej poprawce agent kondycji w wersji 3.0.129.0 jest instalowany podczas uaktualniania usługi Azure AD Connect w miejscu. Usługa Health Agent w wersji 3.0.129.0 nie ma problemu ze zgodnością z usługą Azure AD Connect w wersji 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stan: 19 października 2017

> [!IMPORTANT]
> Istnieje znany problem ze zgodnością między usługą Azure AD Connect w wersji 1.1.647.0 i agentem kondycji usługi Azure AD Connect (do synchronizacji) w wersji 3.0.127.0. Ten problem uniemożliwia agentowi kondycji wysyłanie danych kondycji dotyczących usługi synchronizacji usługi Azure AD Connect (w tym błędów synchronizacji obiektów i uruchamiania danych historii) do usługi Azure AD Health Service. Przed ręcznym uaktualnieniem wdrożenia usługi Azure AD Connect do wersji 1.1.647.0 należy sprawdzić bieżącą wersję agenta kondycji usługi Azure AD Connect zainstalowanego na serwerze usługi Azure AD Connect. Możesz to zrobić, przechodząc do *Panelu sterowania → Dodaj usuń programy* i poszukaj aplikacji Microsoft Azure AD Connect Health Agent for *Sync*. Jeśli jego wersja jest 3.0.127.0, zaleca się, aby poczekać na następną wersję usługi Azure AD Connect, które mają być dostępne przed uaktualnieniem. Jeśli wersja agenta kondycji nie jest 3.0.127.0, jest w porządku, aby kontynuować ręczne, w miejscu uaktualnienia. Należy zauważyć, że ten problem nie ma wpływu na uaktualnianie huśtawka lub klientów, którzy wykonują nową instalację usługi Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązaliśmy problem z zadaniem *zmiany logowania użytkownika w* kreatorze usługi Azure AD Connect:

  * Ten problem występuje, gdy masz istniejące wdrożenie usługi Azure AD Connect z **włączoną**synchronizacją haseł i próbujesz ustawić metodę logowania użytkownika jako *uwierzytelnianie przekazywane.* Przed zastosowaniem zmiany kreator niepoprawnie wyświetla monit "*Wyłącz synchronizację haseł*". Jednak synchronizacja haseł pozostaje włączona po zastosowaniu zmiany. Dzięki tej poprawce kreator nie wyświetla już monitu.

  * Zgodnie z projektem kreator nie wyłącza synchronizacji haseł podczas aktualizowania metody logowania użytkownika przy użyciu zadania *zmień logowanie użytkownika.* Ma to na celu uniknięcie zakłóceń dla klientów, którzy chcą zachować synchronizację haseł, nawet jeśli włączają uwierzytelnianie przekazywane lub federację jako podstawową metodę logowania użytkownika.
  
  * Jeśli chcesz wyłączyć synchronizację haseł po zaktualizowaniu metody logowania użytkownika, musisz wykonać zadanie *Dostosuj konfigurację synchronizacji* w kreatorze. Po przejściu do strony *Funkcje opcjonalne* wyeznacz opcję *Synchronizacja haseł.*
  
  * Należy zauważyć, że ten sam problem występuje również w przypadku próby włączenia/wyłączenia bezproblemowego logowania jednokrotnego. W szczególności masz istniejące wdrożenie usługi Azure AD Connect z włączoną synchronizacją haseł, a metoda logowania użytkownika jest już skonfigurowana jako *uwierzytelnianie przekazywane.* Za pomocą zadania *Zmień logowanie użytkownika* spróbuj zaznaczyć/odznaczyć opcję *Włącz bezproblemowe logowanie jednokrotne,* podczas gdy metoda logowania użytkownika pozostaje skonfigurowana jako "Uwierzytelnianie przekazywane". Przed zastosowaniem zmiany kreator niepoprawnie wyświetla monit "*Wyłącz synchronizację haseł*". Jednak synchronizacja haseł pozostaje włączona po zastosowaniu zmiany. Dzięki tej poprawce kreator nie wyświetla już monitu.

* Rozwiązaliśmy problem z zadaniem *zmiany logowania użytkownika w* kreatorze usługi Azure AD Connect:

  * Ten problem występuje, gdy masz istniejące wdrożenie usługi Azure AD Connect z **synchronizacją**haseł wyłączone i próbujesz ustawić metodę logowania użytkownika jako *uwierzytelnianie przekazywane.* Po zastosowaniu zmiany kreator włącza zarówno uwierzytelnianie przekazywane, jak i synchronizację haseł. Dzięki tej poprawce kreator nie włącza już synchronizacji haseł.

  * Wcześniej synchronizacja haseł była warunkiem wstępnym włączenia uwierzytelniania przekazywanego. Po ustawieniu metody logowania użytkownika jako *uwierzytelniania przekazywanego*kreator włączy zarówno uwierzytelnianie przekazywane, jak i synchronizację haseł. Ostatnio synchronizacja haseł została usunięta jako warunek wstępny. W ramach usługi Azure AD Connect w wersji 1.1.557.0 wybrano zmianę w usłudze Azure AD Connect, aby nie włączać synchronizacji haseł po ustawieniu metody logowania użytkownika jako *uwierzytelniania przekazywanego.* Jednak zmiana została zastosowana tylko do instalacji usługi Azure AD Connect. Za pomocą tej poprawki ta sama zmiana jest również stosowana do zadania *logowania użytkownika Zmień.*
  
  * Należy zauważyć, że ten sam problem występuje również w przypadku próby włączenia/wyłączenia bezproblemowego logowania jednokrotnego. W szczególności masz istniejące wdrożenie usługi Azure AD Connect z synchronizacją haseł wyłączone, a metoda logowania użytkownika jest już skonfigurowana jako *uwierzytelnianie przekazywane.* Za pomocą zadania *Zmień logowanie użytkownika* spróbuj zaznaczyć/odznaczyć opcję *Włącz bezproblemowe logowanie jednokrotne,* podczas gdy metoda logowania użytkownika pozostaje skonfigurowana jako "Uwierzytelnianie przekazywane". Po zastosowaniu zmiany kreator włącza synchronizację haseł. Dzięki tej poprawce kreator nie włącza już synchronizacji haseł. 

* Rozwiązaliśmy problem, który powodował, że uaktualnianie usługi Azure AD Connect nie powiodło się z powodu błędu "*Nie można uaktualnić usługi synchronizacji*". Ponadto usługa synchronizacji nie może już rozpoczynać się od błędu zdarzenia "*Usługa nie może się uruchomić, ponieważ wersja bazy danych jest nowsza niż zainstalowana wersja plików binarnych*". Ten problem występuje, gdy administrator wykonujący uaktualnienie nie ma uprawnień sysadmin do serwera SQL, który jest używany przez usługę Azure AD Connect. Dzięki tej poprawce usługa Azure AD Connect wymaga tylko administratora, aby mieć db_owner uprawnienia do bazy danych ADSync podczas uaktualniania.

* Rozwiązano problem z uaktualnieniem usługi Azure AD Connect, który dotyczył klientów, którzy włączyli [bezproblemowe logowanie jednokrotne.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) Po uaktualnieniu usługi Azure AD Connect bezproblemowe logowanie jednokrotne niepoprawnie pojawia się jako wyłączone w kreatorze usługi Azure AD Connect, mimo że funkcja pozostaje włączona i w pełni funkcjonalna. Dzięki tej poprawce funkcja jest teraz wyświetlana poprawnie, zgodnie z włączoną funkcją kreatora.

* Rozwiązano problem, który powodował, że kreator usługi Azure AD Connect zawsze wyświetlał monit "*Konfigurowanie kotwicy źródła*" na stronie *Gotowe do skonfigurowania,* nawet jeśli nie wprowadzono żadnych zmian związanych z kotwicą źródła.

* Podczas wykonywania ręcznego uaktualniania w miejscu usługi Azure AD Connect, klient jest zobowiązany do podania poświadczeń administratora globalnego odpowiedniej dzierżawy usługi Azure AD. Wcześniej uaktualnianie można było kontynuować, nawet jeśli poświadczenia administratora globalnego należały do innej dzierżawy usługi Azure AD. Chociaż uaktualnienie wydaje się zakończyć pomyślnie, niektóre konfiguracje nie są poprawnie utrwalone z uaktualnienia. Dzięki tej zmianie kreator uniemożliwia kontynuowanie uaktualnienia, jeśli podane poświadczenia nie są zgodne z dzierżawą usługi Azure AD.

* Usunięto nadmiarową logikę, która niepotrzebnie ponownie uruchomiła usługę Azure AD Connect Health na początku ręcznego uaktualniania.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano logikę, aby uprościć kroki wymagane do skonfigurowania usługi Azure AD Connect w chmurze Microsoft Germany Cloud. Wcześniej wymagane jest zaktualizowanie określonych kluczy rejestru na serwerze usługi Azure AD Connect, aby działały poprawnie z usługą Microsoft Germany Cloud, zgodnie z opisem w tym artykule. Teraz usługa Azure AD Connect może automatycznie wykryć, czy dzierżawa znajduje się w chmurze Microsoft Germany cloud na podstawie poświadczeń administratora globalnego podanych podczas instalacji.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Uwaga: Usługa synchronizacji ma interfejs WMI, który umożliwia tworzenie własnego harmonogramu niestandardowego. Ten interfejs jest teraz przestarzały i zostanie usunięty z przyszłych wersji usługi Azure AD Connect dostarczonych po 30 czerwca 2018 r. Klienci, którzy chcą dostosować harmonogram synchronizacji, powinni użyć [wbudowanego harmonogramu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Rozwiązane problemy
* Gdy kreator usługi Azure AD Connect tworzy konto programu AD Connector wymagane do synchronizowania zmian z lokalnej usługi Active Directory, nie przypisuje poprawnie konta uprawnienia wymaganego do odczytu obiektów PublicFolder. Ten problem dotyczy zarówno instalacji ekspresowej, jak i instalacji niestandardowej. Ta zmiana rozwiązuje problem.

* Rozwiązał błąd, który powodował, że strona rozwiązywania problemów z kreatorem usługi Azure AD Connect nie była poprawnie renderowana dla administratorów z systemem Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Podczas rozwiązywania problemów z synchronizacją haseł przy użyciu strony rozwiązywania problemów z kreatorem usługi Azure AD Connect strona rozwiązywania problemów zwraca teraz stan specyficzny dla domeny.

* Wcześniej, jeśli próbowano włączyć synchronizację skrótów haseł, usługa Azure AD Connect nie sprawdza, czy konto łącznika usługi AD ma wymagane uprawnienia do synchronizowania skrótów haseł z lokalnej usługi AD. Teraz kreator usługi Azure AD Connect zweryfikuje i ostrzeże, jeśli konto programu AD Connector nie ma wystarczających uprawnień.

### <a name="ad-fs-management"></a>Usługi AD FS zarządzania
#### <a name="fixed-issue"></a>Rozwiązano problem 
* Naprawiono błąd związany z używaniem [funkcji ms-DS-ConsistencyGuid jako źródła.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) Ten problem dotyczy klientów, którzy skonfigurowali *federację z usługą AD FS* jako metodę logowania użytkownika. Podczas wykonywania *zadania Konfiguruj kotwicę źródła* w kreatorze usługa Azure AD Connect przełącza się na używanie *ms-DS-ConsistencyGuid jako atrybutu źródłowego dla identyfikatora niezmiennego. W ramach tej zmiany usługa Azure AD Connect próbuje zaktualizować reguły oświadczeń dla immutableid w usługach AD FS. Jednak ten krok nie powiódł się, ponieważ usługa Azure AD Connect nie miała poświadczeń administratora wymaganych do skonfigurowania usług AD FS. Dzięki tej poprawce usługa Azure AD Connect monituje teraz o wprowadzenie poświadczeń administratora dla usług AD FS podczas wykonywania zadania *Konfigurowanie kotwicy źródłowej.*



## <a name="116140"></a>1.1.614.0
Status: Wrzesień 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Znane problemy
* Istnieje znany problem, który powoduje, że uaktualnienie usługi Azure AD Connect kończy się niepowodzeniem z powodu błędu *"Nie można uaktualnić usługi synchronizacji".* Ponadto usługa synchronizacji nie może już rozpoczynać się od błędu zdarzenia "*Usługa nie może się uruchomić, ponieważ wersja bazy danych jest nowsza niż zainstalowana wersja plików binarnych*". Ten problem występuje, gdy administrator wykonujący uaktualnienie nie ma uprawnień sysadmin do serwera SQL, który jest używany przez usługę Azure AD Connect. Uprawnienia Dbo nie są wystarczające.

* Istnieje znany problem z uaktualnieniem usługi Azure AD Connect, który dotyczy klientów, którzy włączyli [bezproblemowe logowanie jednokrotne.](how-to-connect-sso.md) Po uaktualnieniu usługi Azure AD Connect funkcja jest wyświetlana jako wyłączona w kreatorze, nawet jeśli funkcja pozostaje włączona. Poprawka dotycząca tego problemu zostanie udostępnina w przyszłej wersji. Klienci, którzy są zaniepokojeni tym problemem z wyświetlaczem, mogą go ręcznie rozwiązać, włączając bezproblemowe logowanie jednokrotne w kreatorze.

#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem, który uniemożliwiał usłudze Azure AD Connect aktualizowanie reguł oświadczeń w lokalnych usługach AD FS, jednocześnie włączając funkcję [ms-DS-ConsistencyGuid jako funkcję zakotwiczenia źródła.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) Ten problem występuje, jeśli spróbujesz włączyć funkcję dla istniejącego wdrożenia usługi Azure AD Connect, który ma usługi AD FS skonfigurowane jako metoda logowania. Ten problem występuje, ponieważ kreator nie monituje o poświadczenia usługi ADFS przed próbą zaktualizowania reguł oświadczeń w usłudze AD FS.
* Rozwiązać problemu, który powodował, że instalacja usługi Azure AD Connect nie powiodła się, jeśli lokalny las usługi AD ma wyłączono ntlm. Problem jest spowodowany tym, że kreator usługi Azure AD Connect nie zapewnia w pełni kwalifikowanych poświadczeń podczas tworzenia kontekstów zabezpieczeń wymaganych do uwierzytelniania Kerberos. Powoduje to, że uwierzytelnianie Kerberos zakończy się niepowodzeniem, a kreator usługi Azure AD Connect powróci do korzystania z usługi NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązaliśmy problem polegający na tym, że nie można utworzyć nowej reguły synchronizacji, jeśli atrybut Tag nie jest wypełniony.
* Rozwiązano problem, który powodował, że usługa Azure AD Connect łączyła się z lokalną usługą AD w celu synchronizacji haseł przy użyciu usługi NTLM, mimo że protokół Kerberos jest dostępny. Ten problem występuje, jeśli lokalna topologia usługi AD ma jeden lub więcej kontrolerów domeny, które zostały przywrócone z kopii zapasowej.
* Naprawiono błąd, który powodował, że po uaktualnieniu niepotrzebnie występowały pełne kroki synchronizacji. Ogólnie rzecz biorąc, po uaktualnieniu wymagane jest przeprowadzenie pełnych kroków synchronizacji, jeśli zostaną wprowadzone zmiany w regułach synchronizacji po wyjęciu z pudełka. Problem był spowodowany błędem w logice wykrywania zmian, który niepoprawnie wykrył zmianę podczas napotkania wyrażenia reguły synchronizacji ze znakami nowego narzędzia. Znaki nowego słowa są wstawiane do wyrażenia reguły synchronizacji w celu zwiększenia czytelności.
* Rozwiązaliśmy problem, który mógł powodować, że serwer usługi Azure AD Connect nie działał poprawnie po automatycznym uaktualnieniu. Ten problem dotyczy serwerów usługi Azure AD Connect w wersji 1.1.443.0 (lub wcześniejszych). Aby uzyskać szczegółowe informacje na temat problemu, zapoznaj się z artykułem [Usługa Azure AD Connect nie działa poprawnie po automatycznym uaktualnieniu](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Naprawiono błąd, który mógł powodować, że automatyczne uaktualnianie było ponawiane co 5 minut w przypadku napotkania błędów. Z poprawką automatyczne uaktualnianie ponawia ponowne prób z wykładniczym wycofywaniem, gdy wystąpią błędy.
* Rozwiązaliśmy problem polegający na tym, że zdarzenie synchronizacji haseł 611 jest niepoprawnie wyświetlane w dziennikach zdarzeń aplikacji systemu Windows jako **informacje** zamiast **błędu**. Zdarzenie 611 jest generowany za każdym razem, gdy synchronizacja hasła napotka problem. 
* Rozwiązano problem w kreatorze usługi Azure AD Connect, który umożliwiał włączenie funkcji stornia grupowego bez wybierania operacji organizacyjnej wymaganej do zapisywania grup.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano zadanie rozwiązywania problemów do kreatora usługi Azure AD Connect w obszarze Zadania dodatkowe. Klienci mogą korzystać z tego zadania, aby rozwiązywać problemy związane z synchronizacją haseł i zbierać ogólne diagnostyki. W przyszłości zadanie Rozwiązywanie problemów zostanie rozszerzone o inne problemy związane z synchronizacją katalogów.
* Usługa Azure AD Connect obsługuje teraz nowy tryb instalacji o nazwie **Użyj istniejącej bazy danych**. Ten tryb instalacji umożliwia klientom zainstalowanie usługi Azure AD Connect, która określa istniejącą bazę danych usługi ADSync. Aby uzyskać więcej informacji na temat tej funkcji, zobacz artykuł [Korzystanie z istniejącej bazy danych](how-to-connect-install-existing-database.md).
* Aby zwiększyć bezpieczeństwo, usługa Azure AD Connect domyślnie używa teraz protokołu TLS1.2 do łączenia się z usługą Azure AD w celu synchronizacji katalogów. Wcześniej wartość domyślna to TLS1.0.
* Po uruchomieniu agenta synchronizacji haseł usługi Azure AD Connect próbuje połączyć się z dobrze znanym punktem końcowym usługi Azure AD w celu synchronizacji haseł. Po pomyślnym połączeniu jest przekierowywane do punktu końcowego specyficzne dla regionu. Wcześniej agent synchronizacji haseł buforuje punkt końcowy specyficzny dla regionu, dopóki nie zostanie ponownie uruchomiony. Teraz agent czyści pamięci podręcznej i ponownych prób z dobrze znanego punktu końcowego, jeśli napotka problem połączenia z punktu końcowego specyficzne dla regionu. Ta zmiana gwarantuje, że synchronizacja haseł może przejść w tryb failover do innego punktu końcowego specyficzne dla regionu, gdy buforowane punktu końcowego specyficzne dla regionu nie jest już dostępna.
* Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Możesz (i) samodzielnie utworzyć konto usług AD DS i podać jego poświadczenia do usługi Azure AD Connect lub (ii) podać poświadczenia administratora przedsiębiorstwa i umożliwić usłudze Azure AD Connect utworzenie konta usług AD DS. Wcześniej (i) jest domyślną opcją w kreatorze usługi Azure AD Connect. Teraz (ii) jest opcją domyślną.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano obsługę platformy Microsoft Azure Government Cloud i Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Usługi AD FS zarządzania
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Polecenie cmdlet Initialize-ADSyncNGCKeysWriteBack w module programu PowerShell prep usługi AD niepoprawnie stosował listy ACL do kontenera rejestracji urządzenia i w związku z tym dziedziczyło tylko istniejące uprawnienia.  Ta została zaktualizowana, aby konto usługi synchronizacji miało poprawne uprawnienia.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Zadanie AAD Connect Verify ADFS Login zostało zaktualizowane w taki sposób, że weryfikuje dane logowania w witrynie Microsoft Online, a nie tylko pobieranie tokenów z usługi ADFS.
* Podczas konfigurowania nowej farmy usług ADFS przy użyciu usługi AAD Connect strona z prośbą o poświadczenia usługi ADFS została przeniesiona, tak aby teraz wystąpiła, zanim użytkownik zostanie poproszony o dostarczenie serwerów ADFS i WAP.  Dzięki temu AAD Connect sprawdzić, czy określone konto ma poprawne uprawnienia.
* Podczas uaktualniania usługi AAD Connect uaktualnienie nie zakończy się niepowodzeniem, jeśli nie można zaktualizować usługi ADFS AAD Trust.  Jeśli tak się stanie, użytkownik zostanie wyświetlony odpowiedni komunikat ostrzegawczy i należy przystąpić do resetowania zaufania za pośrednictwem AAD Connect dodatkowe zadanie.

### <a name="seamless-single-sign-on"></a>Bezproblemowe logowanie jednokrotne
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązaliśmy problem, który powodował, że kreator usługi Azure AD Connect zwracał błąd, gdy próbowałeś włączyć [bezproblemowe logowanie jednokrotne](how-to-connect-sso.md). Komunikat o błędzie *"Konfiguracja agenta uwierzytelniania usługi Microsoft Azure AD Connect nie powiodła się".* Ten problem dotyczy istniejących klientów, którzy ręcznie uaktualnili wersję zapoznawczą agentów uwierzytelniania do [uwierzytelniania przekazywanego](how-to-connect-sso.md) na podstawie kroków opisanych w tym [artykule](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stan: 23 lipca 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem 

* Naprawiono błąd, który powodował usunięcie reguły synchronizacji "Out to AD - User ImmutableId":

  * Ten problem występuje, gdy usługa Azure AD Connect jest uaktualniany lub gdy opcja zadania *Aktualizacja konfiguracji synchronizacji* w kreatorze usługi Azure AD Connect jest używana do aktualizowania konfiguracji synchronizacji usługi Azure AD Connect.
  
  * Ta reguła synchronizacji ma zastosowanie do klientów, którzy włączyli [funkcję ms-DS-ConsistencyGuid jako funkcję Zakotwiczenia źródła](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Ta funkcja została wprowadzona w wersji 1.1.524.0 i później. Po usunięciu reguły synchronizacji usługa Azure AD Connect nie może już wypełniać lokalnego atrybutu AD ms-DS-ConsistencyGuid wartością atrybutu ObjectGuid. Nie uniemożliwia nowych użytkowników z aprowizacji do usługi Azure AD.
  
  * Poprawka gwarantuje, że reguła synchronizacji nie będzie już usuwana podczas uaktualniania lub podczas zmiany konfiguracji, o ile funkcja jest włączona. Dla istniejących klientów, których dotyczy ten problem, poprawka zapewnia również, że reguła synchronizacji jest dodawany z powrotem po uaktualnieniu do tej wersji usługi Azure AD Connect.

* Naprawiono błąd, który powodował, że reguły synchronizacji po wyjęte z pudełka miały wartość pierwszeństwa mniejszą niż 100:

  * Ogólnie rzecz biorąc wartości pierwszeństwa 0 - 99 są zarezerwowane dla reguł synchronizacji niestandardowej. Podczas uaktualniania wartości pierwszeństwa dla gotowych reguł synchronizacji są aktualizowane w celu uwzględnienia zmian reguł synchronizacji. Z powodu tego problemu regułom synchronizacji po wyjęciu z pudełka może być przypisana wartość pierwszeństwa, która jest mniejsza niż 100.
  
  * Poprawka zapobiega występowaniu problemu podczas uaktualniania. Jednak nie przywraca wartości pierwszeństwa dla istniejących klientów, których dotyczy problem. W przyszłości zostanie dostarczona osobna poprawka, która pomoże w przywróceniu.

* Rozwiązano problem polegający na tym, że [ekran filtrowania domeny i jednostka organizacyjna](how-to-connect-install-custom.md#domain-and-ou-filtering) w kreatorze usługi Azure AD Connect wyświetlał opcję *Synchronizuj wszystkie domeny i procesory organizacyjne* zgodnie z wybraną opcją, nawet jeśli filtrowanie oparte na usłudzeze organizacyjnej jest włączone.

*   Naprawiono błąd, który powodował, że [ekran Konfigurowanie partycji katalogowych](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) w Menedżerze usług synchronizacji zwracał błąd po kliknięciu przycisku *Odśwież.* Komunikat o błędzie *brzmi "Wystąpił błąd podczas odświeżania domen: nie można rzutować obiektu typu "System.Collections.ArrayList" w celu wpisywania "Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject".* Ten błąd występuje, gdy nowa domena usługi AD została dodana do istniejącego lasu usługi AD i próbujesz zaktualizować usługę Azure AD Connect za pomocą przycisku Odśwież.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcja automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) została rozszerzona, aby obsługiwać klientów w następujących konfiguracjach:
  * Włączono funkcję stornia zwrotnego urządzenia.
  * Włączono funkcję stornia grupy.
  * Instalacja nie jest ustawieniami ekspresowymi ani uaktualnianiem DirSync.
  * Masz ponad 100.000 obiektów w metaverse.
  * Łączysz się z więcej niż jednym lasem. Konfiguracja ekspresowa łączy się tylko z jednym lasem.
  * Konto programu AD Connector nie jest już domyślnym kontem MSOL_.
  * Serwer jest ustawiony na tryb przejściowy.
  * Włączono funkcję stornia zwrotnego użytkownika.
  
  >[!NOTE]
  >Rozszerzenie zakresu funkcji automatycznego uaktualniania wpływa na klientów korzystających z kompilacji usługi Azure AD Connect 1.1.105.0 i później. Jeśli serwer usługi Azure AD Connect nie ma być automatycznie uaktualniany, należy uruchomić następujące polecenie `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`cmdlet na serwerze usługi Azure AD Connect: . Aby uzyskać więcej informacji na temat włączania/wyłączania automatycznego uaktualniania, zobacz artykuł [Azure AD Connect: Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stan: Nie zostanie zwolniony. Zmiany w tej kompilacji są zawarte w wersji 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem 

* Naprawiono błąd, który powodował, że reguła synchronizacji out-of-box "Out to AD — User ImmutableId" została usunięta po zaktualizowaniu konfiguracji filtrowania opartej na ui. Ta reguła synchronizacji jest wymagana dla [funkcji ms-DS-ConsistencyGuid jako źródło zakotwiczenia](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Rozwiązano problem polegający na tym, że [ekran filtrowania domeny i jednostka organizacyjna](how-to-connect-install-custom.md#domain-and-ou-filtering) w kreatorze usługi Azure AD Connect wyświetlał opcję *Synchronizuj wszystkie domeny i procesory organizacyjne* zgodnie z wybraną opcją, nawet jeśli filtrowanie oparte na usłudzeze organizacyjnej jest włączone.

*   Naprawiono błąd, który powodował, że [ekran Konfigurowanie partycji katalogowych](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) w Menedżerze usług synchronizacji zwracał błąd po kliknięciu przycisku *Odśwież.* Komunikat o błędzie *brzmi "Wystąpił błąd podczas odświeżania domen: nie można rzutować obiektu typu "System.Collections.ArrayList" w celu wpisywania "Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject".* Ten błąd występuje, gdy nowa domena usługi AD została dodana do istniejącego lasu usługi AD i próbujesz zaktualizować usługę Azure AD Connect za pomocą przycisku Odśwież.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcja automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) została rozszerzona, aby obsługiwać klientów w następujących konfiguracjach:
  * Włączono funkcję stornia zwrotnego urządzenia.
  * Włączono funkcję stornia grupy.
  * Instalacja nie jest ustawieniami ekspresowymi ani uaktualnianiem DirSync.
  * Masz ponad 100.000 obiektów w metaverse.
  * Łączysz się z więcej niż jednym lasem. Konfiguracja ekspresowa łączy się tylko z jednym lasem.
  * Konto programu AD Connector nie jest już domyślnym kontem MSOL_.
  * Serwer jest ustawiony na tryb przejściowy.
  * Włączono funkcję stornia zwrotnego użytkownika.
  
  >[!NOTE]
  >Rozszerzenie zakresu funkcji automatycznego uaktualniania wpływa na klientów korzystających z kompilacji usługi Azure AD Connect 1.1.105.0 i później. Jeśli serwer usługi Azure AD Connect nie ma być automatycznie uaktualniany, należy uruchomić następujące polecenie `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`cmdlet na serwerze usługi Azure AD Connect: . Aby uzyskać więcej informacji na temat włączania/wyłączania automatycznego uaktualniania, zobacz artykuł [Azure AD Connect: Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stan: lipiec 2017 r.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji automatycznego uaktualniania usługi Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem 
* Rozwiązano problem z poleceniem cmdlet Initialize-ADSyncDomainJoinedComputerSync, który powodował, że zweryfikowana domena skonfigurowana w istniejącym obiekcie punktu połączenia usługi została zmieniona, nawet jeśli nadal jest prawidłową domeną. Ten problem występuje, gdy dzierżawa usługi Azure AD ma więcej niż jedną zweryfikowaną domenę, która może służyć do konfigurowania punktu połączenia usługi.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Funkcja zapisywania haseł jest teraz dostępna w wersji zapoznawczej w chmurze Microsoft Azure Dla Instytucji Rządowych i usłudze Microsoft Cloud Germany. Aby uzyskać więcej informacji na temat obsługi usługi Azure AD Connect dla różnych wystąpień usługi, zobacz artykuł [Usługi Azure AD Connect: Uwagi specjalne dla wystąpień](reference-connect-instances.md).

* Polecenie cmdlet Initialize-ADSyncDomainJoinedComputerSync ma teraz nowy parametr opcjonalny o nazwie AzureADDomain. Ten parametr umożliwia określenie, która zweryfikowana domena ma być używana do konfigurowania punktu połączenia usługi.

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Nazwa agenta wymaganego do uwierzytelniania przekazywanego została zmieniona z *łącznika serwera proxy aplikacji usługi Microsoft Azure AD* na Agent *uwierzytelniania Microsoft Azure AD Connect*.

* Włączenie uwierzytelniania przekazywanego nie umożliwia już domyślnej synchronizacji skrótów haseł.


## <a name="115530"></a>1.1.553.0
Stan: czerwiec 2017 r.

> [!IMPORTANT]
> Istnieją zmiany reguł schematu i synchronizacji wprowadzone w tej kompilacji. Usługa synchronizacji usługi Azure AD Connect wyzwoli po uaktualnieniu kroki pełnego importu i pełnej synchronizacji. Szczegóły zmian są opisane poniżej. Aby tymczasowo odroczyć kroki pełnego importu i pełnej synchronizacji po uaktualnieniu, zapoznaj się z artykułem [Jak odroczyć pełną synchronizację po uaktualnieniu](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Znany problem
* Istnieje problem, który dotyczy klientów korzystających z [filtrowania opartego na usłudze OU](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) z synchronizacją usługi Azure AD Connect. Po przejściu do [strony Filtrowanie domeny i najuszczącej w](how-to-connect-install-custom.md#domain-and-ou-filtering) kreatorze usługi Azure AD Connect oczekuje się następującego zachowania:
  * Jeśli filtrowanie oparte na uył. **Sync selected domains and OUs**
  * W przeciwnym razie zostanie wybrana opcja **Synchronizuj wszystkie domeny i OUS.**

Problem, który powstaje jest, że **synchronizacja wszystkich domen i ous opcja** jest zawsze zaznaczona po uruchomieniu Kreatora.  Dzieje się tak, nawet jeśli filtrowanie oparte na ujach było wcześniej skonfigurowane. Przed zapisaniem zmian konfiguracji połączenia usługi AAD upewnij się, że **wybrano opcję Synchronizuj wybrane domeny i nazwy użytkownika,** a następnie upewnij się, że wszystkie procesory organizacyjne, które muszą być synchronizowane, są ponownie włączone. W przeciwnym razie filtrowanie oparte na ujach zostanie wyłączone.

#### <a name="fixed-issues"></a>Rozwiązane problemy

* Rozwiązaliśmy problem z zapisywaniem hasła, który umożliwiał administratorowi usługi Azure AD zresetowanie hasła lokalnego konta użytkownika uprzywilejowanego usługi AD. Ten problem występuje, gdy usługa Azure AD Connect ma przyznane uprawnienie Resetowanie hasła za pomocą konta uprzywilejowanego. Problem został rozwiązany w tej wersji usługi Azure AD Connect, nie zezwalając administratorowi usługi Azure AD na zresetowanie hasła dowolnego lokalnego konta użytkownika uprzywilejowanego usługi AD, chyba że administrator jest właścicielem tego konta. Więcej informacji można znaleźć w [poradniku bezpieczeństwa 4033453](https://technet.microsoft.com/library/security/4033453).

* Rozwiązaliśmy problem związany z funkcją [ms-DS-ConsistencyGuid jako źródło zakotwiczenie,](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) w której usługa Azure AD Connect nie odpisywać zwrotnie do lokalnego atrybutu AD ms-DS-ConsistencyGuid. Ten problem występuje, gdy istnieje wiele lokalnych lasów usługi AD dodane do usługi Azure AD Connect i *tożsamości użytkownika istnieje w wielu katalogach opcja* jest zaznaczona. Gdy taka konfiguracja jest używana, reguły synchronizacji wynikowej nie wypełniają sourceAnchorBinary atrybut w Metaverse. Atrybut sourceAnchorBinary jest używany jako atrybut źródłowy atrybutu ms-DS-ConsistencyGuid. W rezultacie writeback do ms-DSConsistencyGuid atrybut nie występuje. Aby rozwiązać ten problem, następujące reguły synchronizacji zostały zaktualizowane, aby upewnić się, że sourceAnchorBinary atrybut w Metaverse jest zawsze wypełniane:
  * W od AD - InetOrgPerson AccountEnabled.xml
  * W od AD - InetOrgPerson Common.xml
  * W u usługi AD — konto użytkownika.xml
  * W u z usługi AD — użytkownik Common.xml
  * In from AD — użytkownik Dołącz do pliku SOAInAAD.xml

* Wcześniej, nawet jeśli funkcja [ms-DS-ConsistencyGuid jako źródło kotwicy](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) nie jest włączona, reguła synchronizacji "Out to AD — User ImmutableId" jest nadal dodawana do usługi Azure AD Connect. Efekt jest łagodny i nie powoduje storcowania atrybutu ms-DS-ConsistencyGuid. Aby uniknąć nieporozumień, logika została dodana, aby upewnić się, że reguła synchronizacji jest dodawany tylko wtedy, gdy funkcja jest włączona.

* Naprawiono błąd, który powodował niepowodzenie synchronizacji skrótów haseł ze zdarzeniem błędu 611. Ten problem występuje po usunięciu co najmniej jednego kontrolera domeny z lokalnej usługi AD. Po zakończeniu każdego cyklu synchronizacji haseł plik cookie synchronizacji wystawiony przez lokalną usługę AD zawiera identyfikatory wywołania usuniętych kontrolerów domeny o wartości 0 numerów USN (Update Sequence Number). Menedżer synchronizacji haseł nie może utrwalić pliku cookie synchronizacji zawierającego wartość USN 0 i kończy się niepowodzeniem ze zdarzeniem błędu 611. Podczas następnego cyklu synchronizacji Menedżer synchronizacji haseł ponownie używa ostatniego utrwalonego pliku cookie synchronizacji, który nie zawiera wartości USN 0. Powoduje to, że te same zmiany hasła mają być ponownie zsynchronizowane. Dzięki tej poprawce Menedżer synchronizacji haseł poprawnie upiera się w pliku cookie synchronizacji.

* Wcześniej, nawet jeśli automatyczne uaktualnianie zostało wyłączone przy użyciu polecenia cmdlet Set-ADSyncAutoUpgrade, proces automatycznego uaktualniania jest okresowo sprawdzany pod kątem uaktualnienia i polega na pobranym instalatorze w celu uszanowania wyłączenia. Dzięki tej poprawce proces automatycznego uaktualniania nie sprawdza już okresowo uaktualniania. Poprawka jest automatycznie stosowana, gdy instalator uaktualnienia dla tej wersji usługi Azure AD Connect jest wykonywany raz.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Wcześniej funkcja [ms-DS-ConsistencyGuid jako źródło była](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) dostępna tylko dla nowych wdrożeń. Teraz jest dostępna dla istniejących wdrożeń. Więcej szczegółów:
  * Aby uzyskać dostęp do tej funkcji, uruchom kreatora usługi Azure AD Connect i wybierz opcję *Zakotwiczenie źródła aktualizacji.*
  * Ta opcja jest widoczna tylko dla istniejących wdrożeń, które używają objectGuid jako sourceAnchor atrybut.
  * Podczas konfigurowania tej opcji kreator sprawdza poprawność stanu atrybutu ms-DS-ConsistencyGuid w lokalnej usłudze Active Directory. Jeśli atrybut nie jest skonfigurowany na żaden obiekt użytkownika w katalogu, kreator używa ms-DS-ConsistencyGuid jako sourceAnchor atrybut. Jeśli atrybut jest skonfigurowany na jeden lub więcej obiektów użytkownika w katalogu, kreator stwierdza, że atrybut jest używany przez inne aplikacje i nie jest odpowiedni jako sourceAnchor atrybut i nie pozwala na przejście do zmiany source anchor. Jeśli masz pewność, że atrybut nie jest używany przez istniejące aplikacje, należy skontaktować się z pomocą techniczną, aby uzyskać informacje na temat sposobu pomijania błędu.

* Specyficzne dla **userCertificate** atrybut na urządzeniach obiektów, Usługa Azure AD Connect teraz wyszukuje wartości certyfikatów wymaganych [do łączenia urządzeń przyłączonych do domeny do usługi Azure AD dla systemu Windows 10 środowiska](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) i odfiltrowywa resztę przed synchronizacją z usługą Azure AD. Aby włączyć to zachowanie, została zaktualizowana reguła synchronizacji out-of-box "Out to AAD - Device Join SOAInAD".

* Usługa Azure AD Connect obsługuje teraz stornowanie atrybutu **CloudPublicDelegates** usługi Exchange Online do lokalnego atrybutu AD **publicDelegates.** Dzięki temu scenariusz, w którym skrzynki pocztowej usługi Exchange Online mogą być przyznawane SendOnBehalfTo praw do użytkowników z lokalnej skrzynki pocztowej programu Exchange. Aby obsługiwać tę funkcję, dodano nową regułę synchronizacji out-of-box "Out to AD — User Exchange Hybrid PublicDelegates writeback". Ta reguła synchronizacji jest dodawana do usługi Azure AD Connect tylko wtedy, gdy funkcja hybrydowa programu Exchange jest włączona.

* Usługa Azure AD Connect obsługuje teraz synchronizowanie atrybutu **altRecipient** z usługi Azure AD. Aby wesprzeć tę zmianę, zaktualizowano następujące reguły synchronizacji po wyjęciu z pola wyboru, aby uwzględnić wymagany przepływ atrybutów:
  * In from AD — User Exchange
  * Wyjście do AAD – User ExchangeOnline
  
* Atrybut **cloudSOAExchMailbox** w Metaverse wskazuje, czy dany użytkownik ma skrzynkę pocztową usługi Exchange Online, czy nie. Jego definicja została zaktualizowana w celu uwzględnienia dodatkowych adresatów programu Exchange OnlineDisplayTypes jako takich skrzynek pocztowych sprzętu i sali konferencyjnej. Aby włączyć tę zmianę, definicja atrybutu cloudSOAExchMailbox, który znajduje się w obszarze out-of-box sync rule "In from AAD – User Exchange Hybrid", została zaktualizowana z:

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

* Dodano następujący zestaw funkcji zgodnych z certyfikatem X509Certificate2 do tworzenia wyrażeń reguły synchronizacji do obsługi wartości certyfikatów w atrybucie userCertificate:

    ||||
    | --- | --- | --- |
    |CertyfikatSubject|CertyfikatIssuer|CertKeyAlgorithm (Polski)|
    |Nazwa IDN certSubject|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|Iscert ( Iscert )|
    |CertFriendlyName (Nazwa)|CertThumbprint (Drukarka certThumbprint)|CertExtensionOids (Objawy certExtensionOids)|
    |CertFormat|CertNotAfter (Po|CertPublicKeyOid (Polski)|
    |Numer certSerialnumer|CertNotBefore|CertPublicKeyParametersOid|
    |Wersja cert|CertSignatureAlgorithmOid|Wybierz|
    |CertKeyAlgorithmParams|CertHashString (CertHashString)|Lokalizacja|
    |||With|

* Wprowadzono następujące zmiany schematu, aby umożliwić klientom tworzenie niestandardowych reguł synchronizacji do przepływu sAMAccountName, domainNetBios i domainFQDN dla obiektów grupy, a także distinguishedName for User objects:

  * Do schematu MV dodano następujące atrybuty:
    * Grupa: AccountName
    * Grupa: domainNetBios
    * Grupa: domainFQDN
    * Osoba: distinguishedName

  * Następujące atrybuty zostały dodane do schematu usługi Azure AD Connector:
    * Grupa: OnPremisesSamAccountName
    * Grupa: NetBiosName
    * Grupa: DnsDomainName
    * Użytkownik: OnPremisesDistinguishedName

* Skrypt cmdlet ADSyncDomainJoinedComputerSync ma teraz nowy parametr opcjonalny o nazwie AzureEnvironment. Parametr jest używany do określenia regionu, w którym znajduje się odpowiednia dzierżawa usługi Azure Active Directory. Prawidłowe wartości to:
  * AzureCloud (domyślnie)
  * AzureChinaCloud
  * AzureGermanyCloud
  * Rząd USA
 
* Zaktualizowano Edytor reguł synchronizacji, aby używać funkcji Dołącz (zamiast aprowizować) jako domyślnej wartości typu łącza podczas tworzenia reguł synchronizacji.

### <a name="ad-fs-management"></a>Zarządzanie usługami AD FS

#### <a name="issues-fixed"></a>Naprawiono problemy

* Następujące adresy URL to nowe punkty końcowe federacji WS wprowadzone przez usługę Azure AD w celu zwiększenia odporności na awarię uwierzytelniania i zostaną dodane do lokalnej konfiguracji zaufania strony odpowiadającej usługi AD FS:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Naprawiono błąd, który powodował, że usługi AD FS wygenerowały niepoprawną wartość oświadczenia dla identyfikatora wystawcy. Ten problem występuje, jeśli istnieje wiele zweryfikowanych domen w dzierżawie usługi Azure AD, a sufiks domeny atrybutu userPrincipalName używanego johndoe@us.contoso.comdo generowania oświadczenia Identyfikator wystawcy ma co najmniej 3 poziomy głębokości (na przykład ). Problem został rozwiązany przez aktualizację wyrażenia regularnego używanego przez reguły oświadczeń.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Wcześniej funkcja zarządzania certyfikatami usługi ADFS udostępniana przez usługę Azure AD Connect może być używana tylko w farmach usług ADFS zarządzanych za pośrednictwem usługi Azure AD Connect. Teraz można użyć tej funkcji w farmach usługi ADFS, które nie są zarządzane przy użyciu usługi Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Data premiery: maj 2017 r.

> [!IMPORTANT]
> Istnieją zmiany reguł schematu i synchronizacji wprowadzone w tej kompilacji. Usługa synchronizacji usługi Azure AD Connect wyzwoli po uaktualnieniu kroki pełnego importu i pełnej synchronizacji. Szczegóły zmian są opisane poniżej.
>
>

**Naprawiono problemy:**

Synchronizacja programu Azure AD Connect

* Rozwiązano problem, który powodował, że automatyczne uaktualnianie występowało na serwerze usługi Azure AD Connect, nawet jeśli klient wyłączył tę funkcję przy użyciu polecenia cmdlet Set-ADSyncAutoUpgrade. Dzięki tej poprawce proces automatycznego uaktualniania na serwerze nadal okresowo sprawdza dostępność uaktualnienia, ale pobrany instalator honoruje konfigurację automatycznego uaktualniania.
* Podczas uaktualniania programu DirSync w miejscu usługa Azure AD Connect tworzy konto usługi Azure AD, które ma być używane przez łącznik usługi Azure AD do synchronizacji z usługą Azure AD. Po utworzeniu konta usługa Azure AD Connect uwierzytelnia się za pomocą usługi Azure AD przy użyciu konta. Czasami uwierzytelnianie kończy się niepowodzeniem z powodu przejściowych problemów, co z kolei powoduje niepowodzenie uaktualnienia programu DirSync w miejscu z błędem *"Wystąpił błąd podczas wykonywania zadania Konfigurowanie synchronizacji AAD: AADSTS50034: Aby zalogować się do tej aplikacji, konto musi zostać dodane do katalogu xxx.onmicrosoft.com."* Aby zwiększyć odporność uaktualnienia DirSync, usługa Azure AD Connect ponowiła teraz ponowny ponawianie kroku uwierzytelniania.
* Wystąpił problem z kompilacją 443, który powoduje, że uaktualnienie w miejscu DirSync zakończy się pomyślnie, ale nie są tworzone profile wymagane do synchronizacji katalogów. Logika uzdrawiania jest uwzględniona w tej kompilacji usługi Azure AD Connect. Po uaktualnieniu klienta do tej kompilacji usługi Azure AD Connect wykrywa brakujące profile uruchamiania i tworzy je.
* Naprawiono błąd, który powodował, że proces synchronizacji haseł nie powodował rozpoczęcia procesu zdarzenia 6900 i błędu *"Element z tym samym kluczem został już dodany".* Ten problem występuje, jeśli aktualizacja konfiguracji filtrowania o usłudze operacyjnej w celu uwzględnienia partycji konfiguracji usługi AD. Aby rozwiązać ten problem, proces synchronizacji haseł synchronizuje teraz zmiany hasła tylko z partycji domeny usługi AD. Partycje inne niż domeny, takie jak partycja konfiguracji, są pomijane.
* Podczas instalacji ekspresowej usługa Azure AD Connect tworzy lokalne konto usług AD DS, które ma być używane przez łącznik usługi AD do komunikowania się z lokalną usługą AD. Wcześniej konto jest tworzone z flagą PASSWD_NOTREQD ustawioną w atrybucie Kontrola konta użytkownika i na koncie jest ustawiane losowe hasło. Teraz usługa Azure AD Connect jawnie usuwa flagę PASSWD_NOTREQD po ustawieniu hasła na koncie.
* Rozwiązano problem, który powodował, że uaktualnienie dirsync nie powiodło się z *błędem "wystąpił zakleszczenie w programie SQL Server, który próbuje uzyskać blokadę aplikacji",* gdy atrybut mailNickname znajduje się w lokalnym schemacie ad, ale nie jest związany z klasą obiektu użytkownika usługi AD.
* Rozwiązaliśmy problem, który powodował, że funkcja zapisywania zwrotnego urządzenia była automatycznie wyłączona, gdy administrator aktualizuje konfigurację synchronizacji usługi Azure AD Connect za pomocą kreatora Usługi Azure AD Connect. Ten problem jest spowodowany przez kreatora wykonywania wstępnego sprawdzania istniejącej konfiguracji stornowania zwrotnego urządzenia w lokalnej u klienta, a sprawdzanie nie powiedzie się. Poprawka polega na pominięciu sprawdzenia, czy zapisywanie zwrotne urządzenia jest już wcześniej włączone.
* Aby skonfigurować filtrowanie w usłudze Operacyjnej, można użyć kreatora usługi Azure AD Connect lub menedżera usługi synchronizacji. Wcześniej, jeśli używasz kreatora usługi Azure AD Connect do konfigurowania filtrowania ou, nowe procesory organizacyjne utworzone później są uwzględniane do synchronizacji katalogów. Jeśli nie chcesz, aby nowe procesory organizacyjne były uwzględniane, należy skonfigurować filtrowanie w usłudze organizacyjnej przy użyciu Menedżera usług synchronizacji. Teraz można osiągnąć to samo zachowanie za pomocą kreatora usługi Azure AD Connect.
* Rozwiązać problemu, który powodował, że procedury przechowywane wymagane przez usługę Azure AD Connect były tworzone w schemacie instalacyjnego administratora, a nie w schemacie dbo.
* Rozwiązaliśmy problem, który powodował, że atrybut TrackingId zwracany przez usługę Azure AD został pominięty w dziennikach zdarzeń serwera AAD Connect Server. Ten problem występuje, jeśli usługa Azure AD Connect odbiera komunikat przekierowania z usługi Azure AD i usługi Azure AD Connect nie może połączyć się z dostarczonym punktem końcowym. TrackingId jest używany przez inżynierów pomocy technicznej do skorelowania z dzienników po stronie usługi podczas rozwiązywania problemów.
* Gdy usługa Azure AD Connect odbiera błąd LargeObject z usługi Azure AD Connect, usługa Azure AD Connect generuje zdarzenie o identyfikatorze zdarzenia 6941 i komunikat *"Obiekt aprowizacji jest za duży. Przytnij liczbę wartości atrybutów w tym obiekcie."* W tym samym czasie usługa Azure AD Connect generuje również mylące zdarzenie z identyfikatorem zdarzenia 6900 i *komunikatem "Microsoft.Online.Coexistence.ProvisionRetryException: Nie można komunikować się z usługą Windows Azure Active Directory".* Aby zminimalizować zamieszanie, usługa Azure AD Connect nie generuje już tego ostatniego zdarzenia po odebraniu błędu LargeObject.
* Rozwiązaliśmy problem, który powodował, że program Menedżer usług synchronizacji przestał odpowiadać podczas próby zaktualizowania konfiguracji ogólnego łącznika LDAP.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Zmiany reguł synchronizacji — zaimplementowano następujące zmiany reguł synchronizacji:
  * Zaktualizowano domyślną regułę synchronizacji ustawioną na nieeksportowanie atrybutów **userCertificate** i **userSMIMECertificate,** jeśli atrybuty mają więcej niż 15 wartości.
  * Atrybuty usługi **AD employeeID** i **msExchBypassModerationLink** są teraz uwzględniane w domyślnym zestawie reguł synchronizacji.
  * **Zdjęcie** atrybutu usługi AD zostało usunięte z domyślnego zestawu reguł synchronizacji.
  * Dodano **preferowaną** lokalizację danych do schematu Metaverse i schematu łącznika AAD. Klienci, którzy chcą zaktualizować te atrybuty w usłudze Azure AD, mogą zaimplementować reguły synchronizacji niestandardowej w tym celu. 
  * Dodano **userType** do schematu Metaverse i schematu łącznika AAD. Klienci, którzy chcą zaktualizować te atrybuty w usłudze Azure AD, mogą zaimplementować reguły synchronizacji niestandardowej w tym celu.

* Usługa Azure AD Connect automatycznie włącza teraz użycie atrybutu ConsistencyGuid jako atrybutu Zakotwiczenie źródła dla lokalnych obiektów usługi AD. Ponadto usługa Azure AD Connect wypełnia atrybut ConsistencyGuid z objectGuid wartość atrybutu, jeśli jest pusta. Ta funkcja ma zastosowanie tylko do nowego wdrożenia. Aby dowiedzieć się więcej o tej funkcji, zapoznaj się z sekcją artykułu [Usługi Azure AD Connect: Pojęcia dotyczące projektowania — używanie ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Dodano nowe narzędzie cmdlet Invoke-ADSyncDiagnostics w celu zdiagnozowania problemów związanych z synchronizacją haszyszu haseł. Aby uzyskać informacje dotyczące korzystania z polecenia cmdlet, zobacz artykuł [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją usługi Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* Usługa Azure AD Connect obsługuje teraz synchronizowanie obiektów folderów publicznych z lokalnej usługi AD na usługę Azure AD. Funkcję można włączyć za pomocą kreatora usługi Azure AD Connect w obszarze Funkcje opcjonalne. Aby dowiedzieć się więcej o tej funkcji, zapoznaj się z artykułem [Obsługa blokowania krawędzi opartych na usłudze Office 365 directory dla lokalnych folderów publicznych z włączoną funkcją poczty](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Usługa Azure AD Connect wymaga konta usług AD DS do synchronizowania z lokalnej usługi AD. Wcześniej, jeśli zainstalowano usługę Azure AD Connect w trybie ekspresowym, można podać poświadczenia konta administratora przedsiębiorstwa i usługi Azure AD Connect utworzy wymagane konto usług AD DS. Jednak w przypadku instalacji niestandardowej i dodawania lasów do istniejącego wdrożenia wymagane było podanie konta usług AD DS. Teraz masz również możliwość podania poświadczeń konta administratora przedsiębiorstwa podczas instalacji niestandardowej i umożliwienia usługi Azure AD Connect utworzenia wymaganego konta usług AD DS.
* Usługa Azure AD Connect obsługuje teraz usługę SQL AOA. Przed zainstalowaniem usługi Azure AD Connect należy włączyć usługę SQL AOA. Podczas instalacji usługa Azure AD Connect wykrywa, czy podane wystąpienie SQL jest włączone dla usługi SQL AOA, czy nie. Jeśli usługa SQL AOA jest włączona, usługa Azure AD Connect dodatkowo określa, czy usługa SQL AOA jest skonfigurowana do używania replikacji synchronicznego lub replikacji asynchronicznego. Podczas konfigurowania odbiornika grupy dostępności zaleca się ustawienie właściwości RegisterAllProvidersIP na 0. To zalecenie jest, ponieważ usługa Azure AD Connect obecnie używa klienta macierzystego SQL do łączenia się z klientem macierzystym SQL i SQL nie obsługuje korzystania z Właściwości MultiSubNetFailover.
* Jeśli używasz LocalDB jako bazy danych dla serwera usługi Azure AD Connect i osiągnął limit rozmiaru 10 GB, usługa synchronizacji nie jest już uruchamiana. Wcześniej należy wykonać ShrinkDatabase operacji na LocalDB odzyskać wystarczającą ilość miejsca w bazie danych dla usługi synchronizacji, aby uruchomić. Po tym, można użyć Menedżera usług synchronizacji, aby usunąć historię uruchamiania, aby odzyskać więcej miejsca w db. Teraz można użyć polecenia cmdlet Start-ADSyncPurgeRunHistory do przeczyszczenia danych historii uruchamiania z localdb w celu odzyskania miejsca w bazy danych. Ponadto to polecenie cmdlet obsługuje tryb offline (przez określenie parametru -offline), który może być używany, gdy usługa synchronizacji nie jest uruchomiona. Uwaga: Tryb offline może być używany tylko wtedy, gdy usługa synchronizacji nie jest uruchomiona, a używana baza danych to LocalDB.
* Aby zmniejszyć ilość wymaganego miejsca do magazynowania, usługa Azure AD Connect kompresuje teraz szczegóły błędu synchronizacji przed zapisaniem ich w bazach danych LocalDB/SQL. Podczas uaktualniania ze starszej wersji usługi Azure AD Connect do tej wersji usługa Azure AD Connect wykonuje jednorazową kompresję na podstawie istniejących szczegółów błędu synchronizacji.
* Wcześniej, po zaktualizowaniu konfiguracji filtrowania instalacji organizacyjnej, należy ręcznie uruchomić pełny import, aby upewnić się, że istniejące obiekty są poprawnie uwzględnione/wykluczone z synchronizacji katalogów. Teraz usługa Azure AD Connect automatycznie wyzwala pełny import podczas następnego cyklu synchronizacji. Ponadto pełne importowanie jest stosowane tylko do łączników usługi AD, których dotyczy aktualizacja. Uwaga: to ulepszenie ma zastosowanie do aktualizacji filtrowania w usłudze Organizacyjnej wprowadzonych tylko za pomocą kreatora usługi Azure AD Connect. Nie ma zastosowania do aktualizacji filtrowania o usłudze operacyjnej dokonanej przy użyciu Menedżera usług synchronizacji.
* Wcześniej filtrowanie oparte na grupach obsługuje tylko obiekty Użytkownicy, Grupy i Kontakt. Teraz filtrowanie oparte na grupach obsługuje również obiekty komputer.
* Wcześniej można usunąć dane obszaru łącznika bez wyłączania harmonogramu synchronizacji usługi Azure AD Connect. Teraz Menedżer usługi synchronizacji blokuje usuwanie danych spacji łącznika, jeśli wykryje, że harmonogram jest włączony. Ponadto zwracane jest ostrzeżenie informujące klientów o potencjalnej utracie danych w przypadku usunięcia danych przestrzeni łącznika.
* Wcześniej należy wyłączyć transkrypcję programu PowerShell dla kreatora usługi Azure AD Connect, aby działał poprawnie. Ten problem został częściowo rozwiązany. Transkrypcji programu PowerShell można włączyć, jeśli używasz kreatora usługi Azure AD Connect do zarządzania konfiguracją synchronizacji. Transkrypcja programu PowerShell musi zostać wyłączone, jeśli do zarządzania konfiguracją usługi ADFS jest używany kreator usługi Azure AD Connect.



## <a name="114860"></a>1.1.486.0
Data premiery: kwiecień 2017 r.

**Naprawiono problemy:**
* Rozwiązano problem polegający na tym, że usługa Azure AD Connect nie została pomyślnie zainstalowana w zlokalizowanej wersji systemu Windows Server.

## <a name="114840"></a>1.1.484.0
Data premiery: kwiecień 2017 r.

**Znane problemy:**

* Ta wersja usługi Azure AD Connect nie zostanie pomyślnie zainstalowana, jeśli spełnione są następujące warunki:
   1. Wykonujesz uaktualnienie DirSync w miejscu lub nową instalację usługi Azure AD Connect.
   2. Używasz zlokalizowanej wersji systemu Windows Server, w której nazwa wbudowanej grupy Administrator na serwerze nie jest "Administratorzy".
   3. Używasz domyślnego programu SQL Server 2012 Express LocalDB zainstalowanego z usługą Azure AD Connect zamiast dostarczania własnego pełnego sql.

**Naprawiono problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem polegający na tym, że harmonogram synchronizacji pomijał cały krok synchronizacji, jeśli w profilu uruchamiania dla tego kroku synchronizacji brakuje co najmniej jednego łącznika. Na przykład łącznik został ręcznie dodany przy użyciu Menedżera usług synchronizacji bez tworzenia dla niego profilu uruchamiania importu delta. Ta poprawka gwarantuje, że harmonogram synchronizacji kontynuuje uruchamianie importu delta dla innych łączników.
* Rozwiązaliśmy problem polegający na tym, że usługa synchronizacji natychmiast przestała przetwarzać profil uruchamiania, gdy napotka on problem z jednym z kroków uruchamiania. Ta poprawka gwarantuje, że usługa synchronizacji pomija ten krok uruchamiania i kontynuuje przetwarzanie pozostałych. Na przykład masz profil uruchamiania importu delta dla łącznika usługi AD z wieloma krokami uruchamiania (po jednym dla każdej lokalnej domeny usługi AD). Usługa synchronizacji uruchomi import delta z innymi domenami usługi AD, nawet jeśli jedna z nich ma problemy z łącznością sieciową.
* Rozwiązać problemu, który powodował, że aktualizacja programu Azure AD Connector została pominięta podczas automatycznego uaktualniania.
* Rozwiązano problem, który powodował, że usługa Azure AD Connect niepoprawnie określała, czy serwer jest kontrolerem domeny podczas instalacji, co z kolei powoduje niepowodzenie uaktualnienia programu DirSync.
* Rozwiązano problem, który powodował, że uaktualnienie w miejscu dirsync nie tworzył żadnego profilu uruchamiania dla usługi Azure AD Connector.
* Rozwiązano problem polegający na tym, że interfejs użytkownika programu Synchronizacja programu Service Manager przestał odpowiadać podczas próby skonfigurowania ogólnego łącznika LDAP.

Zarządzanie usługami AD FS
* Rozwiązano problem polegający na tym, że kreator usługi Azure AD Connect nie powiódł się, jeśli węzeł podstawowy usług AD FS został przeniesiony na inny serwer.

SSO do pulpitu
* Rozwiązaliśmy problem w kreatorze usługi Azure AD Connect, w którym ekran logowania nie umożliwia włączania funkcji logowania logowania do pulpitu, jeśli podczas nowej instalacji wybrano opcję synchronizacji haseł.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Usługa Azure AD Connect Sync obsługuje teraz korzystanie z konta usługi wirtualnej, konta usługi zarządzanej i konta usługi zarządzanej grupy jako konta usługi. Dotyczy to tylko nowej instalacji usługi Azure AD Connect. Podczas instalowania usługi Azure AD Connect:
    * Domyślnie kreator usługi Azure AD Connect utworzy konto usługi wirtualnej i użyje go jako swojego konta usługi.
    * Jeśli instalujesz na kontrolerze domeny, usługa Azure AD Connect powróci do poprzedniego zachowania, w którym utworzy konto użytkownika domeny i użyje go jako konta usługi.
    * Zachowanie domyślne można zastąpić, podając jedną z następujących opcji:
      * Konto usługi zarządzanej w grupie
      * Konto usługi zarządzanej
      * Konto użytkownika domeny
      * Lokalne konto użytkownika
* Wcześniej, jeśli uaktualnisz do nowej kompilacji usługi Azure AD Connect zawierającej zmiany reguł łączników lub synchronizacji, usługa Azure AD Connect wyzwoli pełny cykl synchronizacji. Teraz usługa Azure AD Connect selektywnie wyzwala krok pełnego importu tylko dla łączników z aktualizacją i krok pełnej synchronizacji tylko dla łączników ze zmianami reguł synchronizacji.
* Wcześniej próg usuwania eksportu ma zastosowanie tylko do eksportu, które są wyzwalane za pośrednictwem harmonogramu synchronizacji. Teraz funkcja jest rozszerzana o eksporty ręcznie wyzwalane przez klienta przy użyciu Menedżera usług synchronizacji.
* W dzierżawie usługi Azure AD istnieje konfiguracja usługi, która wskazuje, czy funkcja synchronizacji haseł jest włączona dla dzierżawy, czy nie. Wcześniej konfiguracja usługi jest łatwa do niepoprawnej konfiguracji przez usługę Azure AD Connect, gdy masz aktywny i serwer przejściowy. Teraz usługa Azure AD Connect spróbuje zachować spójność konfiguracji usługi tylko z aktywnym serwerem usługi Azure AD Connect.
* Kreator usługi Azure AD Connect wykrywa i zwraca ostrzeżenie, jeśli lokalna usługa AD nie ma włączonego Kosza usługi AD.
* Poprzednio Export to Azure AD upotrzymał limit czasu i kończy się niepowodzeniem, jeśli łączny rozmiar obiektów w partii przekracza określony próg. Teraz usługa synchronizacji będzie ponownie próbował ponownie wystawić obiekty w oddzielnych, mniejszych partii, jeśli problem zostanie napotkany.
* Aplikacja Zarządzanie kluczami usługi synchronizacji została usunięta z menu Start systemu Windows. Zarządzanie kluczem szyfrowania będzie nadal obsługiwane za pośrednictwem interfejsu wiersza polecenia za pomocą miiskmu.exe. Aby uzyskać informacje dotyczące zarządzania kluczem szyfrowania, zobacz artykuł [Porzucenie klucza szyfrowania usługi Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key).
* Wcześniej, jeśli zmienisz hasło konta usługi synchronizacji usługi Azure AD Connect, usługa synchronizacji nie będzie mogła poprawnie uruchomić, dopóki nie porzucisz klucza szyfrowania i nie ponownie zrezygnowano z hasła do konta usługi synchronizacji usługi Azure AD Connect. Teraz ten proces nie jest już wymagany.

SSO do pulpitu

* Kreator usługi Azure AD Connect nie wymaga już otwarcia portu 9090 w sieci podczas konfigurowania uwierzytelniania przekazywanego i poświadczenia para. Wymagany jest tylko port 443. 

## <a name="114430"></a>1.1.443.0
Data premiery: marzec 2017 r.

**Naprawiono problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązał błąd, który powodował niepowodzenie kreatora usługi Azure AD Connect, jeśli nazwa wyświetlana programu Azure AD Connector nie zawiera początkowej domeny onmicrosoft.com przypisanej do dzierżawy usługi Azure AD.
* Rozwiązaliśmy problem, który powodował, że kreator usługi Azure AD Connect nie powodował niepowodzenia podczas nawiązywania połączenia z bazą danych SQL, gdy hasło konta usługi synchronizacji zawiera znaki specjalne, takie jak apostrof, dwukropek i spacja.
* Rozwiązaliśmy problem, który powodował, że błąd "Obraz ma kotwicę inną niż obraz" występuje na serwerze usługi Azure AD Connect w trybie przejściowym, po tymczasowym wykluczeniu lokalnego obiektu usługi AD z synchronizacji, a następnie dołączonym go ponownie do synchronizacji.
* Rozwiązaliśmy problem, który powodował, że błąd "Obiekt znajdujący się przez DN jest fantomem" wystąpił na serwerze usługi Azure AD Connect w trybie przejściowym, po tymczasowym wykluczeniu lokalnego obiektu usługi AD z synchronizacji, a następnie uwzględnieniu go ponownie w celu synchronizacji.

Zarządzanie usługami AD FS
* Rozwiązano problem polegający na tym, że kreator usługi Azure AD Connect nie aktualizował konfiguracji usług AD FS i ustawiał odpowiednie oświadczenia dotyczące zaufania jednostki uzależnianej po skonfigurowaniu alternatywnego identyfikatora logowania.
* Rozwiązano problem polegający na tym, że kreator usługi Azure AD Connect nie mógł poprawnie obsługiwać serwerów usług AD FS, których konta usług są skonfigurowane przy użyciu formatu userPrincipalName zamiast formatu sAMAccountName.

Uwierzytelnianie przekazywane
* Rozwiązaliśmy problem, który powodował, że kreator usługi Azure AD Connect kończył się niepowodzeniem, jeśli zostanie wybrana opcja Przeładuj uwierzytelnianie, ale rejestracja jego łącznika nie powiedzie się.
* Rozwiązano błąd, który powodował, że kreator usługi Azure AD Connect pomijał sprawdzanie poprawności metody logowania wybranej po włączeniu funkcji logowania do logowania pulpitu.

Resetowanie hasła
* Rozwiązano problem, który mógł powodować, że serwer usługi Azure AAD Connect nie próbował ponownie połączyć się, jeśli połączenie zostało zabite przez zaporę lub serwer proxy.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Polecenie cmdlet Get-ADSyncScheduler zwraca teraz nową właściwość logiczną o nazwie SyncCycleInProgress. Jeśli zwracana wartość jest true, oznacza to, że jest zaplanowane cyklu synchronizacji w toku.
* Folder docelowy służący do przechowywania dzienników instalacji i instalacji usługi Azure AD Connect został przeniesiony z %localappdata%\AADConnect do %programdata%\AADConnect w celu poprawy dostępności plików dziennika.

Zarządzanie usługami AD FS
* Dodano obsługę aktualizacji certyfikatu TLS/SSL farmy usług AD FS.
* Dodano obsługę zarządzania usługami AD FS 2016.
* Podczas instalacji usług AD FS można teraz określić istniejącą usługę gMSA (grupowe konto usługi zarządzanej).
* Teraz można skonfigurować SHA-256 jako algorytm mieszania podpisu dla zaufania jednostki uzależniającej usługi Azure AD.

Resetowanie hasła
* Wprowadzono ulepszenia umożliwiające działanie produktu w środowiskach z bardziej rygorystycznymi regułami zapory.
* Zwiększona niezawodność połączeń z usługą Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Data premiery: grudzień 2016 r.

**Naprawiono błąd:**

* Rozwiązało problem polegający na tym, że w tej kompilacji brak była reguły oświadczeń o problemie dla usług federacyjnych Active Directory (AD FS).

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji automatycznego uaktualniania usługi Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Data premiery: grudzień 2016 r.

**Znany problem:**

* W tej kompilacji brakuje reguły oświadczeń issuerid dla usług AD FS. Reguła oświadczenia issuerid jest wymagana, jeśli federujesz wiele domen za pomocą usługi Azure Active Directory (Azure AD). Jeśli używasz usługi Azure AD Connect do zarządzania lokalnym wdrożeniem usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczeń issuerid z konfiguracji usług AD FS. Można obejść ten problem, dodając regułę oświadczenia issuerid po instalacji/uaktualnienia. Aby uzyskać szczegółowe informacje na temat dodawania reguły oświadczeń issuerid, zapoznaj się z tym artykułem na [temat obsługi wielu domen do federowania za pomocą usługi Azure AD](how-to-connect-install-multiple-domains.md).

**Naprawiono błąd:**

* Jeśli port 9090 nie jest otwarty dla połączenia wychodzącego, instalacja lub uaktualnienie usługi Azure AD Connect kończy się niepowodzeniem.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji automatycznego uaktualniania usługi Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Data premiery: grudzień 2016 r.

**Znane problemy:**

* W tej kompilacji brakuje reguły oświadczeń issuerid dla usług AD FS. Reguła oświadczenia issuerid jest wymagana, jeśli federating wielu domen z usługi Azure AD. Jeśli używasz usługi Azure AD Connect do zarządzania lokalnym wdrożeniem usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczeń issuerid z konfiguracji usług AD FS. Można obejść ten problem, dodając regułę oświadczenia issuerid po instalacji/uaktualnieniu. Szczegółowe informacje na temat dodawania reguły oświadczeń o problemach można znaleźć w tym artykule na [temat obsługi wielu domen w celu federowania za pomocą usługi Azure AD.](how-to-connect-install-multiple-domains.md)
* Port 9090 musi być otwarty wychodzący, aby zakończyć instalację.

**Nowe funkcje:**

* Uwierzytelnianie przekazywane (wersja zapoznawcza).

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji automatycznego uaktualniania usługi Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Data premiery: listopad 2016 r.

**Znany problem:**

* W tej kompilacji brakuje reguły oświadczeń issuerid dla usług AD FS. Reguła oświadczenia issuerid jest wymagana, jeśli federating wielu domen z usługi Azure AD. Jeśli używasz usługi Azure AD Connect do zarządzania lokalnym wdrożeniem usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczeń issuerid z konfiguracji usług AD FS. Można obejść ten problem, dodając regułę oświadczenia issuerid po instalacji/uaktualnieniu. Szczegółowe informacje na temat dodawania reguły oświadczeń o problemach można znaleźć w tym artykule na [temat obsługi wielu domen w celu federowania za pomocą usługi Azure AD.](how-to-connect-install-multiple-domains.md)

**Naprawiono problemy:**

* Czasami instalacja usługi Azure AD Connect kończy się niepowodzeniem, ponieważ nie można utworzyć konta usługi lokalnej, którego hasło spełnia poziom złożoności określony przez zasady haseł organizacji.
* Rozwiązaliśmy problem polegający na tym, że reguły sprzężenia nie były ponownie dokonywanych, gdy obiekt w przestrzeni łącznika jednocześnie staje się poza zakresem dla jednej reguły sprzężenia i staje się w zakresie dla innej. Może się tak zdarzyć, jeśli masz dwie lub więcej reguł sprzężenia, których warunki sprzężenia wzajemnie się wykluczają.
* Rozwiązano problem polegający na tym, że reguły synchronizacji przychodzącej (z usługi Azure AD), które nie zawierają reguł sprzężenia, nie są przetwarzane, jeśli mają niższe wartości pierwszeństwa niż te zawierające reguły sprzężenia.

**Ulepszenia:**

* Dodano obsługę instalowania usługi Azure AD Connect w systemie Windows Server 2016 Standard lub nowszym.
* Dodano obsługę używania programu SQL Server 2016 jako zdalnej bazy danych dla usługi Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Opublikowano: sierpień 2016

**Naprawiono problemy:**

* Zmiany interwału synchronizacji mają miejsce dopiero po zakończeniu następnego cyklu synchronizacji.
* Kreator usługi Azure AD Connect nie akceptuje konta usługi Azure\_AD, którego nazwa użytkownika zaczyna się od podkreślenia ( ).
* Kreator usługi Azure AD Connect nie może uwierzytelnić konta usługi Azure AD, jeśli hasło do konta zawiera zbyt wiele znaków specjalnych. Komunikat o błędzie "Nie można sprawdzić poprawności poświadczeń. Wystąpił nieoczekiwany błąd." zostanie zwrócona.
* Odinstalowanie serwera przemieszczania wyłącza synchronizację haseł w dzierżawie usługi Azure AD i powoduje niepowodzenie synchronizacji haseł z aktywnym serwerem.
* Synchronizacja haseł kończy się niepowodzeniem w niezbyt często, gdy nie ma skrótu hasła przechowywanego na użytkowniku.
* Gdy serwer usługi Azure AD Connect jest włączony w trybie przejściowym, zapisywanie zwrotne haseł nie jest tymczasowo wyłączone.
* Kreator usługi Azure AD Connect nie pokazuje rzeczywistej synchronizacji haseł i konfiguracji zapisywania zwrotnego hasła, gdy serwer jest w trybie przejściowym. Zawsze pokazuje je jako wyłączone.
* Zmiany konfiguracji synchronizacji haseł i zapisywania haseł nie są zachowywane przez kreatora usługi Azure AD Connect, gdy serwer jest w trybie przejściowym.

**Ulepszenia:**

* Zaktualizowano polecenie cmdlet Start-ADSyncSyncCycle, aby wskazać, czy jest w stanie pomyślnie rozpocząć nowy cykl synchronizacji, czy nie.
* Dodano polecenie cmdlet Stop-ADSyncSyncCycle, aby zakończyć cykl synchronizacji i działanie, które są obecnie w toku.
* Zaktualizowano polecenie cmdlet Stop-ADSyncScheduler, aby zakończyć cykl synchronizacji i działanie, które są obecnie w toku.
* Podczas konfigurowania [rozszerzeń katalogów](how-to-connect-sync-feature-directory-extensions.md) w kreatorze usługi Azure AD Connect można teraz wybrać atrybut usługi Azure AD typu "Ciąg Teletex".

## <a name="111890"></a>1.1.189.0
Data premiery: czerwiec 2016 r.

**Naprawiono problemy i ulepszenia:**

* Usługę Azure AD Connect można teraz zainstalować na serwerze zgodnym ze standardem FIPS.
  * Aby zapoznać się z synchronizacją haseł, zobacz [Synchronizacja skrótów haseł i FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Rozwiązano problem polegający na tym, że nie można było rozpoznać nazwy NetBIOS z nazwą FQDN w łączniku usługi Active Directory.

## <a name="111800"></a>1.1.180.0
Data premiery: maj 2016 r.

**Nowe funkcje:**

* Ostrzega i pomaga zweryfikować domeny, jeśli nie zrobiłeś tego przed uruchomieniem usługi Azure AD Connect.
* Dodano obsługę usługi [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Dodano obsługę najnowszej infrastruktury [chmury platformy Microsoft Azure dla instytucji rządowych](reference-connect-instances.md#microsoft-azure-government) z nowymi wymaganiami dotyczącymi adresów URL.

**Naprawiono problemy i ulepszenia:**

* Dodano filtrowanie do Edytora reguł synchronizacji, aby ułatwić znajdowanie reguł synchronizacji.
* Poprawiono wydajność podczas usuwania miejsca na łączniki.
* Naprawiono błąd, który powodował, że ten sam obiekt został usunięty i dodany w tym samym przebiegu (o nazwie delete/add).
* Wyłączona reguła synchronizacji nie włącza już ponownie dołączonych obiektów i atrybutów podczas uaktualniania lub odświeżania schematu katalogu.

## <a name="111300"></a>1.1.130.0
Data premiery: kwiecień 2016 r.

**Nowe funkcje:**

* Dodano obsługę atrybutów o wielu wartościach w [rozszerzeniach katalogów](how-to-connect-sync-feature-directory-extensions.md).
* Dodano obsługę większej liczby odmian konfiguracji [dla automatycznego uaktualniania,](how-to-connect-install-automatic-upgrade.md) które można uznać za kwalifikujące się do uaktualnienia.
* Dodano kilka poleceń cmdlet dla [niestandardowego harmonogramu](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Data premiery: marzec 2016 r.

**Naprawiono problemy:**

* Upewnij się, że instalacji ekspresowej nie można używać w systemie Windows Server 2008 (pre-R2), ponieważ synchronizacja haseł nie jest obsługiwana w tym systemie operacyjnym.
* Uaktualnienie z programu DirSync przy obliczu niestandardowej konfiguracji filtra nie działało zgodnie z oczekiwaniami.
* Podczas uaktualniania do nowszej wersji i nie ma żadnych zmian w konfiguracji, pełne importowanie/synchronizacja nie powinny być zaplanowane.

## <a name="111100"></a>1.1.110.0
Data premiery: luty 2016

**Naprawiono problemy:**

* Uaktualnienie z wcześniejszych wersji nie działa, jeśli instalacja nie znajduje się w domyślnym folderze C:\Program Files.
* Jeśli zainstalujesz i wyczyść **Rozpocznij proces synchronizacji** na końcu kreatora instalacji, uruchomienie kreatora instalacji po raz drugi nie spowoduje włączenia harmonogramu.
* Harmonogram nie działa zgodnie z oczekiwaniami na serwerach, na których nie jest używany format daty/godziny w USA. Zablokuje `Get-ADSyncScheduler` również zwracanie prawidłowych czasów.
* Jeśli zainstalowano wcześniejszą wersję usługi Azure AD Connect z usługą AD FS jako opcję logowania i uaktualnienie, nie można ponownie uruchomić kreatora instalacji.

## <a name="111050"></a>1.1.105.0
Data premiery: luty 2016

**Nowe funkcje:**

* [Funkcja automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) dla klientów ustawień ekspresowych.
* Obsługa administratora globalnego przy użyciu uwierzytelniania wieloskładnikowego platformy Azure i zarządzania tożsamościami uprzywilejowanymi w kreatorze instalacji.
  * Należy zezwolić serwerowi proxy na https://secure.aadcdn.microsoftonline-p.com zezwalanie na ruch, jeśli używasz uwierzytelniania wieloskładnikowego.
  * Aby uwierzytelnianie https://secure.aadcdn.microsoftonline-p.com wieloskładnikowe było poprawne, należy dodać do listy zaufanych witryn uwierzytelnianie wieloskładnikowe.
* Zezwalaj na zmianę metody logowania użytkownika po wstępnej instalacji.
* Zezwalaj na [filtrowanie domeny i instalacji](how-to-connect-install-custom.md#domain-and-ou-filtering) w kreatorze instalacji. Umożliwia to również łączenie się z lasami, w których nie wszystkie domeny są dostępne.
* [Harmonogram](how-to-connect-sync-feature-scheduler.md) jest wbudowany w aparat synchronizacji.

**Funkcje promowane z wersji zapoznawczej do ga:**

* [Zapisywanie zwrotne urządzenia](how-to-connect-device-writeback.md).
* [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md).

**Nowe funkcje podglądu:**

* Nowy domyślny interwał cyklu synchronizacji wynosi 30 minut. Kiedyś trzy godziny dla wszystkich wcześniejszych wydań. Dodaje obsługę, aby zmienić zachowanie [harmonogramu.](how-to-connect-sync-feature-scheduler.md)

**Naprawiono problemy:**

* Strona sprawdź domeny DNS nie zawsze rozpoznawała domeny.
* Monity o podanie poświadczeń administratora domeny podczas konfigurowania usług AD FS.
* Lokalne konta usługi AD nie są rozpoznawane przez kreatora instalacji, jeśli znajdują się w domenie z innym drzewem DNS niż domena główna.

## <a name="1091310"></a>1.0.9131.0
Data premiery: grudzień 2015 r.

**Naprawiono problemy:**

* Synchronizacja haseł może nie działać po zmianie haseł w Usługach domenowych Active Directory (AD DS), ale działa po ustawieniu hasła.
* Jeśli masz serwer proxy, uwierzytelnianie w usłudze Azure AD może zakończyć się niepowodzeniem podczas instalacji lub jeśli uaktualnienie zostanie anulowane na stronie konfiguracji.
* Aktualizowanie z poprzedniej wersji usługi Azure AD Connect z pełnym wystąpieniem programu SQL Server kończy się niepowodzeniem, jeśli nie jesteś administratorem systemu PROGRAMU SQL Server (SA).
* Aktualizacja z poprzedniej wersji usługi Azure AD Connect za pomocą zdalnego programu SQL Server pokazuje błąd "Nie można uzyskać dostępu do bazy danych SQL usługi ADSync".

## <a name="1091250"></a>1.0.9125.0
Data premiery: listopad 2015 r.

**Nowe funkcje:**

* Można ponownie skonfigurować usługi AD FS do zaufania usługi Azure AD.
* Można odświeżyć schemat usługi Active Directory i ponownie wygenerować reguły synchronizacji.
* Można wyłączyć regułę synchronizacji.
* Można zdefiniować "AuthoritativeNull" jako nowy literał w regule synchronizacji.

**Nowe funkcje podglądu:**

* [Kondycja usługi Azure AD Connect do synchronizacji](how-to-connect-health-sync.md).
* Obsługa synchronizacji haseł [usług domenowych usługi Azure AD.](../user-help/active-directory-passwords-update-your-own-password.md)

**Nowy obsługiwany scenariusz:**

* Obsługuje wiele lokalnych organizacji programu Exchange. Aby uzyskać więcej informacji, zobacz [Wdrożenia hybrydowe z wieloma lasami usługi Active Directory](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Naprawiono problemy:**

* Problemy z synchronizacją haseł:
  * Obiekt przeniesiony z poza zakresem do zakresu nie będzie miał hasła zsynchronizowane. Obejmuje to zarówno OU i filtrowanie atrybutów.
  * Wybranie nowej usługi organizacyjnej do uwzględnienia w synchronizacji nie wymaga pełnej synchronizacji hasła.
  * Gdy wyłączony użytkownik jest włączony hasło nie jest synchronizowane.
  * Kolejka ponownych prób hasła jest nieskończona, a poprzedni limit 5000 obiektów do wycofania został usunięty.
* Nie można połączyć się z usługą Active Directory na poziomie funkcjonalności lasu w systemie Windows Server 2016.
* Nie można zmienić grupy używanej do filtrowania grup po instalacji początkowej.
* Nie tworzy już nowego profilu użytkownika na serwerze usługi Azure AD Connect dla każdego użytkownika, który zmienia hasło z włączoną funkcją zapisywania zwrotnego hasła.
* Nie można używać wartości długich całkowitych w zakresach reguł synchronizacji.
* Pole wyboru "zapisywanie zwrotne urządzenia" pozostaje wyłączone, jeśli istnieją nieosiągalne kontrolery domeny.

## <a name="1086670"></a>1.0.8667.0
Opublikowano: sierpień 2015

**Nowe funkcje:**

* Kreator instalacji usługi Azure AD Connect jest teraz zlokalizowany we wszystkich językach systemu Windows Server.
* Dodano obsługę odblokowywania konta podczas korzystania z zarządzania hasłami usługi Azure AD.

**Naprawiono problemy:**

* Kreator instalacji usługi Azure AD Connect ulega awarii, jeśli inny użytkownik kontynuuje instalację, a nie osoba, która po raz pierwszy rozpoczęła instalację.
* Jeśli poprzednia dezinstalacja usługi Azure AD Connect nie może wyleciać synchronizacji usługi Azure AD Connect w sposób czysty, nie można ponownie zainstalować.
* Nie można zainstalować usługi Azure AD Connect przy użyciu instalacji ekspresowej, jeśli użytkownik nie znajduje się w domenie głównej lasu lub jeśli używana jest wersja nieanglojęzyczna usługi Active Directory.
* Jeśli nie można rozpoznać nazwy FQDN konta użytkownika usługi Active Directory, wyświetlany jest wprowadzający w błąd komunikat o błędzie "Nie można zatwierdzić schematu".
* Jeśli konto używane w łączniku usługi Active Directory zostanie zmienione poza kreatorem, kreator ulegnie awarii w kolejnych biegach.
* Usługa Azure AD Connect czasami nie można zainstalować na kontrolerze domeny.
* Nie można włączyć i wyłączyć "Tryb przemieszczania", jeśli dodano atrybuty rozszerzenia.
* Zapisywanie zwrotne hasła kończy się niepowodzeniem w niektórych konfiguracjach z powodu złego hasła w łączniku usługi Active Directory.
* Nie można uaktualnić programu DirSync, jeśli w filtrowaniu atrybutów jest używana nazwa wyróżniająca (DN).
* Nadmierne użycie procesora CPU podczas resetowania hasła.

**Usunięto funkcje podglądu:**

* Funkcja podglądu [Odpis użytkownika](how-to-connect-preview.md#user-writeback) została tymczasowo usunięta na podstawie opinii naszych klientów w wersji zapoznawczej. Zostanie on dodany ponownie później po skierowaniu dostarczonych opinii.

## <a name="1086410"></a>1.0.8641.0
Data premiery: czerwiec 2015 r.

**Początkowa wersja usługi Azure AD Connect.**

Zmieniono nazwę z usługi Azure AD Sync na usługę Azure AD Connect.

**Nowe funkcje:**

* [Instalacja ustawień ekspresowych](how-to-connect-install-express.md)
* Można [skonfigurować usługi AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Można [uaktualnić z DirSync](how-to-dirsync-upgrade-get-started.md)
* [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Wprowadzony [tryb przemieszczania](how-to-connect-sync-staging-server.md)

**Nowe funkcje podglądu:**

* [Odpisy użytkownika](how-to-connect-preview.md#user-writeback)
* [Zapisywanie zwrotne grup](how-to-connect-preview.md#group-writeback)
* [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)
* [Rozszerzenia katalogów](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Data premiery: maj 2015 r.

**Nowe wymagania:**

* Usługa Azure AD Sync wymaga teraz zainstalowania programu .NET Framework w wersji 4.5.1.

**Naprawiono problemy:**

* Zapisywanie zwrotne hasła z usługi Azure AD kończy się niepowodzeniem z błędem łączności usługi Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Data premiery: kwiecień 2015 r.

**Naprawiono problemy i ulepszenia:**

* Łącznik usługi Active Directory nie przetwarza usuwania poprawnie, jeśli kosz jest włączony i istnieje wiele domen w lesie.
* Poprawiono wydajność operacji importowania dla łącznika usługi Azure Active Directory.
* Gdy grupa przekroczyła limit członkostwa (domyślnie limit jest ustawiony na 50 000 obiektów), grupa została usunięta w usłudze Azure Active Directory. W przypadku nowego zachowania grupa nie jest usuwana, zgłaszany jest błąd, a nowe zmiany członkostwa nie są eksportowane.
* Nie można aprowizować nowego obiektu, jeśli w przestrzeni łącznika znajduje się już nieumieszczona liczba usuwanych z tą samą demy.
* Niektóre obiekty są oznaczone do synchronizacji podczas synchronizacji różnicowej, mimo że nie ma żadnych zmian przemieszczanych na obiekcie.
* Wymuszanie synchronizacji haseł powoduje również usunięcie preferowanej listy kontrolerów domeny.
* CSExportAnalyzer ma problemy z niektórymi stanami obiektów.

**Nowe funkcje:**

* Sprzężenie może teraz łączyć się z typem obiektu "ANY" w MV.

## <a name="104850222"></a>1.0.485.0222
Data premiery: luty 2015

**Ulepszenia:**

* Poprawiono wydajność importu.

**Naprawiono problemy:**

* Password Sync honoruje atrybut cloudFiltered używany przez filtrowanie atrybutów. Filtrowane obiekty nie są już w zakresie synchronizacji haseł.
* W rzadkich sytuacjach, gdy topologia miała wiele kontrolerów domeny, synchronizacja haseł nie działa.
* "Zatrzymany serwer" podczas importowania z usługi Azure AD Connector po włączeniu zarządzania urządzeniami w usłudze Azure AD/Intune.
* Dołączanie do podmiotów zabezpieczeń obcych (FSPs) z wielu domen w tym samym lesie powoduje niejednoznaczny błąd sprzężenia.

## <a name="104751202"></a>1.0.475.1202
Data premiery: grudzień 2014 r.

**Nowe funkcje:**

* Synchronizacja haseł z filtrem opartym na atrybutach jest teraz obsługiwana. Aby uzyskać więcej informacji, zobacz [Synchronizacja haseł z filtrem](how-to-connect-sync-configure-filtering.md).
* Atrybut ms-DS-ExternalDirectoryObjectID jest zapisywany z powrotem w usłudze Active Directory. Ta funkcja dodaje obsługę aplikacji usługi Office 365. Używa OAuth2 do uzyskiwania dostępu do skrzynek pocztowych online i lokalnych w przypadku wdrożenia programu Hybrid Exchange.

**Naprawiono problemy z uaktualnieniem:**

* Nowsza wersja asystenta logowania jest dostępna na serwerze.
* Niestandardowa ścieżka instalacji została użyta do zainstalowania usługi Azure AD Sync.
* Nieprawidłowe kryterium sprzężenia niestandardowego blokuje uaktualnienie.

**Inne poprawki:**

* Poprawiono szablony pakietu Office Pro Plus.
* Naprawiono problemy z instalacją spowodowane nazwami użytkowników rozpoczynającymi się od kreski.
* Naprawiono utratę sourceAnchor ustawienie podczas uruchamiania kreatora instalacji po raz drugi.
* Poprawiono śledzenie ETW do synchronizacji haseł.

## <a name="104701023"></a>1.0.470.1023
Data premiery: październik 2014 r.

**Nowe funkcje:**

* Synchronizacja haseł z wielu lokalnych usług Active Directory do usługi Azure AD.
* Zlokalizowany interfejs użytkownika instalacji we wszystkich językach systemu Windows Server.

**Uaktualnianie z AADSync 1.0 GA**

Jeśli masz już zainstalowaną usługę Azure AD Sync, istnieje jeden dodatkowy krok, który należy podjąć w przypadku zmiany dowolnej z reguł synchronizacji out-of-box. Po uaktualnieniu do wersji 1.0.470.1023 zmodyfikowane reguły synchronizacji zostaną zduplikowane. Dla każdej zmodyfikowanej reguły synchronizacji wykonaj następujące czynności:

1. Znajdź zmodyfikowaną regułę synchronizacji i zanotuj zmiany.
1. Usuń regułę synchronizacji.
1. Znajdź nową regułę synchronizacji, która jest tworzona przez usługę Azure AD Sync, a następnie ponownie zastosować zmiany.

**Uprawnienia dla konta usługi Active Directory**

Konto usługi Active Directory musi mieć dodatkowe uprawnienia, aby móc odczytać skróty haseł z usługi Active Directory. Uprawnienia do przyznania są nazwane "Replikowanie zmian w katalogu" i "Replikowanie katalogu zmienia wszystko." Oba uprawnienia są wymagane, aby móc odczytać skróty haseł.

## <a name="104190911"></a>1.0.419.0911
Data premiery: wrzesień 2014 r.

**Początkowa wersja usługi Azure AD Sync.**

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
