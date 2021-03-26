---
title: Rozwiązywanie problemów z wdrażaniem i odnajdywaniem urządzenia Azure Migrate
description: Uzyskaj pomoc dotyczącą wdrażania urządzeń i odnajdowania serwerów.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 995914fab0e7112327ebf6ab8e32fb67181f481e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608922"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Rozwiązywanie problemów z urządzeniem Azure Migrate i odnajdywanie

Ten artykuł pomaga w rozwiązywaniu problemów podczas wdrażania urządzenia [Azure Migrate](migrate-services-overview.md) i korzystania z urządzenia w celu odnajdywania serwerów lokalnych.

## <a name="whats-supported"></a>Co jest obsługiwane?

[Zapoznaj](migrate-appliance.md) się z wymaganiami dotyczącymi obsługi urządzenia.

## <a name="invalid-ovf-manifest-entry"></a>"Nieprawidłowy wpis manifestu OVF"

Jeśli zostanie wyświetlony komunikat o błędzie "podany plik manifestu jest nieprawidłowy: nieprawidłowy wpis manifestu OVF", wykonaj następujące czynności:

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate został poprawnie pobrany, sprawdzając jego wartość skrótu. [Dowiedz się więcej](./tutorial-discover-vmware.md). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
2. Jeśli wdrożenie nadal kończy się niepowodzeniem i używasz klienta VMware vSphere do wdrożenia pliku OVF, spróbuj wdrożyć go za pośrednictwem klienta sieci Web vSphere. Jeśli wdrożenie nadal kończy się niepowodzeniem, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci Web vSphere i podjęto próbę jego wdrożenia na vCenter Server 6,5 lub 6,7, spróbuj wdrożyć komórki jajowe bezpośrednio na hoście ESXi:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) z klientem sieci Web (https://<*adres IP hosta*>/UI).
   - W **obszarze**  >  **spis sprzętu** wybierz pozycję **plik**  >  **Wdróż OVF szablon**. Przejdź do komórek jajowych i Ukończ wdrożenie.
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, skontaktuj się z pomocą techniczną Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nie można nawiązać połączenia z Internetem

Taka sytuacja może wystąpić, jeśli serwer urządzenia znajduje się za serwerem proxy.

- Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy ich wymaga.
- Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Dodaj [te adresy URL](migrate-appliance.md#url-access) do dozwolonych.
- Jeśli używasz przechwycenia serwera proxy do łączenia się z Internetem, zaimportuj certyfikat serwera proxy na urządzenie, wykonując [następujące kroki](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Nie można zalogować się do platformy Azure z poziomu aplikacji sieci Web urządzenia

Wystąpił błąd "Niestety, wystąpił problem podczas logowania użytkownika", jeśli używasz nieprawidłowego konta platformy Azure do logowania się do platformy Azure. Ten błąd występuje z kilku powodów:

- Jeśli zalogujesz się do aplikacji sieci Web dla chmury publicznej, przy użyciu poświadczeń konta użytkownika dla portalu w chmurze dla instytucji rządowych.
- Jeśli zalogujesz się do aplikacji sieci Web dla instytucji rządowych przy użyciu poświadczeń konta użytkownika dla portalu chmury prywatnej.

Upewnij się, że używasz poprawnych poświadczeń.

## <a name="datetime-synchronization-error"></a>Błąd synchronizacji daty/godziny

Błąd dotyczący synchronizacji daty i godziny (802) wskazuje, że zegar serwera może nie być zsynchronizowany z bieżącym czasem przez więcej niż pięć minut. Zmień czas zegara na serwerze modułu zbierającego tak, aby był zgodny z bieżącą godziną:

1. Otwórz wiersz polecenia administratora na serwerze.
2. Aby sprawdzić strefę czasową, uruchom polecenie **W32tm/TZ**.
3. Aby zsynchronizować godzinę, uruchom polecenie **w32tm/resync**.

## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Jeśli wystąpi błąd połączenia, może nie być możliwe nawiązanie połączenia z serwerem vCenter Server *servername*. com: 9443. Szczegóły błędu wskazują, że nie ma punktu końcowego `https://\*servername*.com:9443/sdk` , który może zaakceptować ten komunikat.

- Sprawdź, czy korzystasz z najnowszej wersji urządzenia. Jeśli nie, Uaktualnij urządzenie do [najnowszej wersji](./migrate-appliance.md).
- Jeśli problem nadal występuje w najnowszej wersji, urządzenie może nie być w stanie rozpoznać określonej nazwy vCenter Server lub określony port może być nieprawidłowy. Domyślnie, jeśli port nie jest określony, moduł zbierający podejmie próbę nawiązania połączenia z numerem portu 443.

    1. Wyślij polecenie ping *servername*. com z urządzenia.
    2. Jeśli krok 1 nie powiedzie się, spróbuj połączyć się z serwerem vCenter przy użyciu adresu IP.
    3. Określ poprawny numer portu, aby nawiązać połączenie z vCenter Server.
    4. Sprawdź, czy vCenter Server działa.

## <a name="error-6005260039-appliance-might-not-be-registered"></a>Błąd 60052/60039: urządzenie może nie być zarejestrowane

- Błąd 60052, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie", jeśli konto platformy Azure użyte do zarejestrowania urządzenia ma niewystarczające uprawnienia.
    - Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
    - [Dowiedz się więcej](./migrate-appliance.md#appliance---vmware) na temat wymaganych ról i uprawnień platformy Azure.
- Błąd 60039. "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie" może wystąpić, jeśli rejestracja nie powiedzie się, ponieważ nie można odnaleźć projektu użytego do rejestracji urządzenia.
    - W Azure Portal i sprawdź, czy projekt istnieje w grupie zasobów.
    - Jeśli projekt nie istnieje, Utwórz nowy projekt w grupie zasobów i ponownie Zarejestruj urządzenie. [Dowiedz się, jak](./create-manage-projects.md#create-a-project-for-the-first-time) utworzyć nowy projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Błąd 60030/60031: operacja zarządzania Key Vault nie powiodła się

Jeśli zostanie wyświetlony błąd 60030 lub 60031, "Azure Key Vault operacji zarządzania nie powiodła się", wykonaj następujące czynności:

- Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
- Upewnij się, że konto ma dostęp do magazynu kluczy określonego w komunikacie o błędzie, a następnie spróbuj ponownie wykonać operację.
- Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.
- [Dowiedz się więcej](./migrate-appliance.md#appliance---vmware) o wymaganych rolach i uprawnieniach platformy Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Błąd 60028: nie można zainicjować odnajdywania

Błąd 60028: "nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla określonej listy hostów lub klastrów "oznacza, że nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z dostępem lub pobraniem informacji o serwerze. Pozostałe hosty zostały pomyślnie dodane.

- Ponownie Dodaj hosty wymienione w błędzie przy użyciu opcji **Dodaj hosta** .
- Jeśli wystąpi błąd walidacji, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, a następnie spróbuj ponownie wykonać operację **zapisywania i uruchamiania odnajdywania** .

## <a name="error-60025-azure-ad-operation-failed"></a>Błąd 60025: operacja usługi Azure AD nie powiodła się

Błąd 60025: "operacja usługi Azure AD nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi Azure AD "występuje, gdy konto użytkownika platformy Azure używane do inicjowania odnajdywania jest inne niż konto użyte do zarejestrowania urządzenia. Wykonaj jedną z następujących czynności:

- Upewnij się, że konto użytkownika, które inicjuje odnajdywanie, jest takie samo jak użyte do zarejestrowania urządzenia.
- Podaj Azure Active Directory uprawnienia dostępu do aplikacji dla konta użytkownika, dla którego operacja odnajdywania kończy się niepowodzeniem.
- Usuń grupę zasobów utworzoną wcześniej dla projektu. Utwórz kolejną grupę zasobów, aby ponownie uruchomić.
- [Dowiedz się więcej](./migrate-appliance.md#appliance---vmware) o Azure Active Directory uprawnieniach aplikacji.

## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Błąd 50004: nie można nawiązać połączenia z hostem lub klastrem

Błąd 50004: "nie można nawiązać połączenia z hostem lub klastrem, ponieważ nie można rozpoznać nazwy serwera. Kod błędu WinRM: 0x803381B9 "może wystąpić, jeśli usługa Azure DNS dla urządzenia nie może rozpoznać podanej nazwy klastra lub hosta.

- Jeśli ten błąd jest wyświetlany w klastrze, nazwa FQDN klastra.
- Ten błąd może również pojawić się w przypadku hostów w klastrze. Oznacza to, że urządzenie może połączyć się z klastrem, ale klaster zwraca nazwy hostów, które nie są nazwami FQDN. Aby rozwiązać ten problem, zaktualizuj plik Hosts na urządzeniu poprzez dodanie mapowania adresów IP i nazw hostów:
    1. Otwórz Notatnik jako administrator.
    2. Otwórz plik C:\Windows\System32\Drivers\etc\hosts.
    3. Dodaj adres IP i nazwę hosta w wierszu. Powtórz tę czynność dla każdego hosta lub klastra, w którym widzisz ten błąd.
    4. Zapisz i zamknij plik hosts.
    5. Sprawdź, czy urządzenie może nawiązać połączenie z hostami przy użyciu aplikacji do zarządzania urządzeniami. Po 30 minutach powinny zostać wyświetlone najnowsze informacje dotyczące tych hostów w Azure Portal.

## <a name="error-60001-unable-to-connect-to-server"></a>Błąd 60001: nie można nawiązać połączenia z serwerem

- Upewnij się, że na serwerze istnieje łączność z urządzeniem
- Jeśli jest to serwer z systemem Linux, upewnij się, że jest włączona funkcja uwierzytelniania opartego na hasłach, wykonując następujące czynności:
    1. Zaloguj się na serwerze z systemem Linux i Otwórz plik konfiguracji SSH przy użyciu polecenia "VI/etc/ssh/sshd_config"
    2. Dla opcji "PasswordAuthentication" Ustaw wartość tak. Zapisz plik.
    3. Uruchom ponownie usługę SSH, uruchamiając "Service SSHD restart"
- Jeśli jest to system Windows Server, upewnij się, że port 5985 jest otwarty, aby umożliwić zdalne wywołania WMI.
- Jeśli odnajdywasz serwer GCP z systemem Linux i używasz użytkownika root, użyj następujących poleceń, aby zmienić domyślne ustawienie logowania głównego
    1. Zaloguj się na serwerze z systemem Linux i Otwórz plik konfiguracji SSH przy użyciu polecenia "VI/etc/ssh/sshd_config"
    2. Dla opcji "PermitRootLogin" Ustaw wartość tak.
    3. Uruchom ponownie usługę SSH, uruchamiając "Service SSHD restart"

## <a name="error-no-suitable-authentication-method-found"></a>Błąd: nie znaleziono odpowiedniej metody uwierzytelniania

Upewnij się, że na serwerze z systemem Linux jest włączone uwierzytelnianie oparte na hasłach, wykonując następujące czynności:
    1. Zaloguj się na serwerze z systemem Linux i Otwórz plik konfiguracji SSH przy użyciu polecenia "VI/etc/ssh/sshd_config"
    2. Dla opcji "PasswordAuthentication" Ustaw wartość tak. Zapisz plik.
    3. Uruchom ponownie usługę SSH, uruchamiając "Service SSHD restart"

## <a name="discovered-servers-not-in-portal"></a>Wykryte serwery poza portalem

Jeśli odnajdywanie jest w toku, ale nie widzisz jeszcze serwerów w portalu, odczekaj kilka minut:

- W przypadku serwera w programie VMware trwa około 15 minut.
- Każdy dodany host na potrzeby odnajdywania funkcji Hyper-V zajmie około dwóch minut.

Jeśli zaczekasz, a stan nie zmieni się, wybierz pozycję **Odśwież** na karcie **serwery** . Ta wartość powinna zawierać liczbę odnalezionych serwerów w Azure Migrate: odnajdywanie i ocenianie i Azure Migrate: Migracja serwera.

Jeśli to nie zadziała, a będziesz odnajdywać serwery VMware:

- Sprawdź, czy określone konto vCenter ma ustawione uprawnienia prawidłowo, z dostępem do co najmniej jednego serwera.
- Azure Migrate nie może odnaleźć serwerów w oprogramowaniu VMware, jeśli konto vCenter ma dostęp udzielony na poziomie folderu vCenter VM. [Dowiedz się więcej](set-discovery-scope.md) na temat odnajdywania zakresu.

## <a name="server-data-not-in-portal"></a>Dane serwera nie są w portalu

Jeśli odnalezione serwery nie pojawiają się w portalu lub jeśli dane serwera są nieaktualne, odczekaj kilka minut. Zmiany w wykrytych konfiguracjach serwera mogą pojawić się w portalu dopiero po 30 minutach. Wprowadzenie zmian w danych spisu oprogramowania może potrwać kilka godzin. Jeśli po tym czasie nie ma danych, spróbuj odświeżyć w następujący sposób.

1. W **systemach Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i ocenianie**, wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj** wybierz pozycję **Agent Health**.
3. Wybierz pozycję **Odśwież agenta**.
4. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.

## <a name="deleted-servers-appear-in-portal"></a>Usunięte serwery pojawiają się w portalu

Jeśli usuniesz serwery i nadal pojawią się one w portalu, odczekaj 30 minut. Jeśli nadal są wyświetlane, Odśwież zgodnie z powyższym opisem.

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>Odnalezione zapasy oprogramowania i wystąpienia SQL Server i bazy danych nie są w portalu

Po zainicjowaniu odnajdywania urządzenia może upłynąć nawet 24 godziny, aby rozpocząć wyświetlanie danych spisu w portalu.

Jeśli nie podano uwierzytelniania systemu Windows lub SQL Server poświadczeń uwierzytelniania w Menedżerze konfiguracji urządzeń, należy dodać poświadczenia, aby urządzenie mogło używać ich do nawiązywania połączeń z odpowiednimi wystąpieniami SQL Server.

Po nawiązaniu połączenia urządzenie zbiera dane dotyczące konfiguracji i wydajności SQL Server wystąpień i baz danych. Dane konfiguracji SQL Server są aktualizowane co 24 godziny, a dane wydajności są przechwytywane co 30 sekund. W związku z tym każda zmiana właściwości wystąpienia SQL Server i baz danych, takich jak stan bazy danych, poziom zgodności itp., może zająć do 24 godzin.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>Wystąpienie SQL Server jest wyświetlane w stanie "niepołączone" w portalu

Aby wyświetlić problemy napotkane podczas odnajdywania wystąpień SQL Server i baz danych, kliknij stan "niepołączony" w kolumnie Stan połączenia na stronie "odnalezione serwery" w projekcie.

Tworzenie oceny na serwerach zawierających wystąpienia SQL, które nie zostały całkowicie wykryte lub są w stanie niepołączonym, może doprowadzić do gotowości jako "nieznany".

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nie widzę danych o wydajności niektórych kart sieciowych na serwerach fizycznych

Taka sytuacja może wystąpić, jeśli na serwerze fizycznym jest włączona Wirtualizacja funkcji Hyper-V. Z powodu przerwy w działaniu produktu przepustowość sieci jest przechwytywana na odnalezionych wirtualnych kartach sieciowych.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Błąd: przekazany plik ma nieoczekiwany format

Niektóre narzędzia mają ustawienia regionalne, które tworzą plik CSV ze średnikiem jako ogranicznikiem. Zmień ustawienia, aby upewnić się, że ogranicznikiem jest przecinek.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Zaimportowano plik CSV, ale widzę komunikat „Trwa odnajdywanie”

Ten stan jest wyświetlany, jeśli przekazywanie pliku CSV nie powiodło się z powodu błędu walidacji. Spróbuj ponownie zaimportować plik CSV. Możesz pobrać raport o błędach poprzedniego przekazywania i postępować zgodnie z zaleceniami w pliku, aby naprawić błędy. Raport o błędach można pobrać z sekcji "Importuj szczegóły" na stronie "odnajdywanie serwerów".

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Nie wyświetlaj szczegółów spisu oprogramowania nawet po zaktualizowaniu poświadczeń gościa

Funkcja odnajdywania spisu oprogramowania jest uruchamiana co 24 godziny. Jeśli chcesz natychmiast zobaczyć szczegóły, Odśwież w następujący sposób. Może to potrwać kilka minut, w zależności od tego, czy nie. odnalezionych serwerów.

1. W **systemach Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i ocenianie**, wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj** wybierz pozycję **Agent Health**.
3. Wybierz pozycję **Odśwież agenta**.
4. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.

## <a name="unable-to-export-software-inventory"></a>Nie można wyeksportować spisu oprogramowania

Upewnij się, że użytkownik pobierający spis z portalu ma uprawnienia współautora w ramach subskrypcji.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Nie znaleziono odpowiedniej metody uwierzytelniania do ukończenia uwierzytelniania (PublicKey)

Uwierzytelnianie oparte na kluczach nie będzie działało, Użyj uwierzytelniania hasła.

## <a name="common-app-discovery-errors"></a>Typowe błędy funkcji odnajdywania aplikacji

Azure Migrate obsługuje odnajdywanie spisu oprogramowania przy użyciu Azure Migrate: odnajdywania i oceny. Odnajdywanie aplikacji jest obecnie obsługiwane tylko przez oprogramowanie VMware. [Dowiedz się więcej](how-to-discover-applications.md) na temat wymagań i kroków dotyczących konfigurowania funkcji odnajdywania aplikacji.

Typowe błędy odnajdowania aplikacji zostały podsumowane w tabeli.

| **Błąd** | **Przyczyna** | **Akcja** |
|--|--|--|
| 9000: nie można wykryć stanu narzędzia VMware. | Narzędzia VMware mogą nie być zainstalowane lub są uszkodzone. | Upewnij się, że narzędzia VMware zostały zainstalowane i uruchomione na serwerze. |
| 9001: narzędzia VMware nie są zainstalowane. | Narzędzia VMware mogą nie być zainstalowane lub są uszkodzone. | Upewnij się, że narzędzia VMware zostały zainstalowane i uruchomione na serwerze. |
| 9002: narzędzia VMware nie są uruchomione. | Narzędzia VMware mogą nie być zainstalowane lub są uszkodzone. | Upewnij się, że narzędzia VMware zostały zainstalowane i uruchomione na serwerze. |
| 9003: typ systemu operacyjnego nie jest obsługiwany na potrzeby odnajdywania serwera gościa. | System operacyjny uruchomiony na serwerze nie jest systemem Windows ani Linux. | Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux. Jeśli serwer jest w rzeczywistości Windows lub Linux, Sprawdź typ systemu operacyjnego określony w vCenter Server. |
| 9004: serwer nie jest uruchomiony. | Serwer jest wyłączony. | Upewnij się, że serwer jest włączony. |
| 9005: typ systemu operacyjnego nie jest obsługiwany na potrzeby odnajdywania serwera gościa. | Typ systemu operacyjnego nie jest obsługiwany w przypadku odnajdywania serwera gościa. | Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux. |
| 9006: adres URL pobierania pliku metadanych z gościa jest pusty. | Może się tak zdarzyć, jeśli agent odnajdywania nie działa zgodnie z oczekiwaniami. | Problem powinien automatycznie rozwiązać in24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| 9007: nie znaleziono procesu uruchamiania zadania odnajdywania na serwerze gościa. | Może się tak zdarzyć, jeśli agent odnajdywania nie działa prawidłowo. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| 9008: nie można pobrać stanu procesu serwera gościa. | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| 9009: funkcja Windows UAC nie uniemożliwiła wykonania zadania odnajdywania na serwerze. | Ustawienia kontroli konta użytkownika systemu Windows (UAC) na serwerze są restrykcyjne i uniemożliwiają odnajdywanie zainstalowanego spisu oprogramowania. | W ustawieniach kontroli konta użytkownika na serwerze skonfiguruj ustawienie UAC na jeden z niższych poziomów. |
| 9010: serwer jest wyłączony. | Serwer jest wyłączony. | Upewnij się, że serwer jest włączony. |
| 9011: odnaleziony plik metadanych nie został znaleziony w systemie plików serwera gościa. | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| 9012: odnaleziony plik metadanych jest pusty. | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| 9013: dla każdej nazwy logowania tworzony jest nowy profil tymczasowy. | Nowy profil tymczasowy jest tworzony dla każdej nazwy logowania na serwerze programu VMware. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 9014: nie można pobrać metadanych z systemu plików serwera gościa. | Brak łączności z hostem ESXi | Upewnij się, że urządzenie może połączyć się z portem 443 na hoście ESXi z uruchomionym serwerem |
| 9015: rola operacji gościa nie jest włączona na koncie użytkownika vCenter | Rola operacji gościa nie jest włączona na koncie użytkownika vCenter. | Upewnij się, że rola operacje gościa jest włączona na koncie użytkownika vCenter. |
| 9016: nie można odnaleźć agenta, ponieważ agent operacji gościa jest nieaktualny. | Narzędzia VMware nie są poprawnie zainstalowane lub są nieaktualne. | Upewnij się, że narzędzia VMware są prawidłowo zainstalowane i aktualne. |
| 9017: na serwerze nie znaleziono pliku z odnalezionymi metadanymi. | Problem może wystąpić z powodu błędu wewnętrznego. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 9018: program PowerShell nie jest zainstalowany na serwerach gościa. | Program PowerShell nie jest dostępny na serwerze gościa. | Zainstaluj program PowerShell na serwerze gościa. |
| 9019: nie można odnaleźć z powodu błędów operacji serwera gościa. | Operacja gościa VMware na serwerze nie powiodła się. | Upewnij się, że poświadczenia serwera są prawidłowe, a nazwa użytkownika podana w poświadczeniach serwera gościa jest w formacie nazwy UPN. |
| 9020: odmowa uprawnień do tworzenia pliku. | Rola skojarzona z użytkownikiem lub zasad grupy ogranicza użytkownikowi możliwość tworzenia pliku w folderze | Sprawdź, czy podany użytkownik-Gość ma uprawnienie Tworzenie dla pliku w folderze. Zobacz **powiadomienia** w Azure Migrate: odnajdywanie i ocenianie nazwy folderu. |
| 9021: nie można utworzyć pliku w tymczasowej ścieżce systemowej. | Narzędzie VMware raportuje ścieżkę Temp systemu zamiast ścieżki tymczasowej użytkownika. | Uaktualnij wersję narzędzia VMware powyżej 10287 (format klienta NGC/VI). |
| 9022: odmowa dostępu do obiektu WMI. | Rola skojarzona z użytkownikiem lub zasad grupy ogranicza użytkownikowi dostęp do obiektu WMI. | Skontaktuj się z pomoc techniczna firmy Microsoft. |
| 9023: nie można uruchomić programu PowerShell, ponieważ wartość zmiennej środowiskowej główny_katalog_systemowy jest pusta. | Wartość zmiennej środowiskowej główny_katalog_systemowy jest pusta dla serwera gościa. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 9024: nie można odnaleźć jako wartość zmiennej środowiskowej TEMP jest pusta. | Wartość zmiennej środowiskowej TEMP jest pusta dla serwera gościa. | Skontaktuj się z pomoc techniczna firmy Microsoft. |
| 9025: program PowerShell jest uszkodzony na serwerach gościa. | Program PowerShell jest uszkodzony na serwerze gościa. | Zainstaluj ponownie program PowerShell na serwerze gościa i sprawdź, czy środowisko PowerShell można uruchomić na serwerze gościa. |
| 9026: nie można uruchomić operacji gościa na serwerze. | Stan serwera nie zezwala na uruchamianie operacji gościa na serwerze. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 9027: Agent operacji gościa nie jest uruchomiony na serwerze. | Nie można skontaktować się z agentem operacji gościa działającym na serwerze wirtualnym. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 9028: nie można utworzyć pliku z powodu niewystarczającej ilości miejsca na dysku na serwerze. | Za mało miejsca na dysku. | Upewnij się, że wystarczająca ilość miejsca jest dostępna w magazynie dyskowym serwera programu. |
| 9029: nie można uzyskać dostępu do programu PowerShell na serwerze gościa podano poświadczenie. | Dostęp do programu PowerShell nie jest dostępny dla użytkownika. | Upewnij się, że użytkownik dodany na urządzeniu może uzyskać dostęp do programu PowerShell na serwerze gościa. |
| 9030: nie można zebrać odnalezionych metadanych, ponieważ Host ESXi został odłączony. | Host ESXi jest w stanie odłączonym. | Upewnij się, że host ESXi z uruchomionym serwerem jest połączony. |
| 9031: nie można zebrać odnalezionych metadanych, ponieważ Host ESXi nie odpowiada. | Host zdalny jest w nieprawidłowym stanie. | Upewnij się, że host ESXi z uruchomionym serwerem działa i jest połączony. |
| 9032: nie można odnaleźć z powodu błędu wewnętrznego. | Problem może wystąpić z powodu błędu wewnętrznego. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 9033: nie można odnaleźć, ponieważ nazwa użytkownika serwera zawiera nieprawidłowe znaki. | W nazwie użytkownika wykryto nieprawidłowe znaki. | Podaj ponownie poświadczenie serwera, aby upewnić się, że nie ma żadnych nieprawidłowych znaków. |
| 9034: podana nazwa użytkownika nie jest w formacie nazwy UPN. | Nazwa użytkownika nie jest w formacie nazwy UPN. | Upewnij się, że nazwa użytkownika jest w formacie głównej nazwy użytkownika (UPN). |
| 9035: nie można odnaleźć, ponieważ tryb języka programu PowerShell nie jest ustawiony na wartość "Full Language". | Tryb języka dla programu PowerShell na serwerze gościa nie jest ustawiony na język pełny. | Upewnij się, że tryb języka programu PowerShell jest ustawiony na wartość "Full Language". |
| 9037: zbieranie danych zostało wstrzymane tymczasowo, ponieważ czas odpowiedzi serwera jest zbyt duży. | Wykryty serwer trwa zbyt długo, aby odpowiedzieć | Żadna akcja nie jest wymagana. Próba odnalezienia spisu oprogramowania i 3 godzin na potrzeby analizy zależności (bez agenta) zostanie ponowiona w ciągu 24 godzin. |
| 10000: typ systemu operacyjnego nie jest obsługiwany. | System operacyjny uruchomiony na serwerze nie jest systemem Windows ani Linux. | Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux. |
| 10001: na urządzeniu nie znaleziono skryptu do odnajdowania serwerów. | Odnajdywanie nie działa zgodnie z oczekiwaniami. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 10002: zadanie odnajdywania nie zostało ukończone w czasie. | Agent odnajdywania nie działa zgodnie z oczekiwaniami. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| 10003: proces wykonywania zadania odnajdywania zakończył się z powodu błędu. | Proces wykonywania zadania odnajdywania zakończył się z powodu błędu. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem nadal występuje, skontaktuj się z pomoc techniczna firmy Microsoft. |
| 10004: nie podano poświadczeń dla typu systemu operacyjnego gościa. | Nie podano poświadczeń w celu uzyskania dostępu do serwerów tego typu systemu operacyjnego na urządzeniu Azure Migrate. | Dodawanie poświadczeń dla serwerów na urządzeniu |
| 10005: podane poświadczenia są nieprawidłowe. | Poświadczenia podane dla urządzenia w celu uzyskania dostępu do serwera są nieprawidłowe. | Zaktualizuj poświadczenia podane w urządzeniu i upewnij się, że serwer jest dostępny przy użyciu poświadczeń. |
| 10006: typ systemu operacyjnego gościa nie jest obsługiwany przez magazyn poświadczeń. | System operacyjny uruchomiony na serwerze nie jest systemem Windows ani Linux. | Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux. |
| 10007: nie można przetworzyć odnalezionych metadanych. | Wystąpił błąd podczas próby deserializacji JSON. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 10008: nie można utworzyć pliku na serwerze. | Problem może wystąpić z powodu błędu wewnętrznego. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |
| 10009: nie można zapisać odnalezionych metadanych do pliku na serwerze. | Problem może wystąpić z powodu błędu wewnętrznego. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie. |

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>Typowe wystąpienia SQL Server i błędy odnajdywania bazy danych

Azure Migrate obsługuje odnajdywanie wystąpień SQL Server i baz danych działających na maszynach lokalnych przy użyciu Azure Migrate: odnajdywania i oceny. Funkcja odnajdywania SQL jest obecnie obsługiwana tylko przez oprogramowanie VMware. Aby rozpocząć, zapoznaj się z samouczkiem [odnajdywania](tutorial-discover-vmware.md) .

W tabeli zestawiono typowe błędy funkcji odnajdywania SQL.

| **Błąd** | **Przyczyna** | **Akcja** |
|--|--|--|
|30000: poświadczenia skojarzone z tym SQL Server nie działają.|Ręcznie skojarzone poświadczenia są nieprawidłowe lub autoskojarzone poświadczenia nie mogą już uzyskiwać dostępu do SQL Server.|Dodaj poświadczenia dla SQL Server na urządzeniu i poczekaj na kolejne cykle odnajdywania SQL lub Wymuś odświeżanie.|
|30001: nie można nawiązać połączenia z SQL Server z urządzenia.|1. urządzenie nie ma liniowej linii wglądu do SQL Server.<br/>2. blokowanie połączenia między SQL Server i urządzeniem przez zaporę.|1. Udostępnij SQL Server z urządzenia.<br/>2. Zezwól na połączenia przychodzące z urządzenia do SQL Server.|
|30003: certyfikat nie jest zaufany.|Zaufany certyfikat nie jest zainstalowany na komputerze z uruchomionym SQL Server.|Skonfiguruj zaufany certyfikat na serwerze. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153616)|
|30004: niewystarczające uprawnienia.|Ten błąd może wystąpić z powodu braku uprawnień wymaganych do skanowania wystąpień SQL Server. |Przyznaj roli sysadmin poświadczenia/konto udostępniane na urządzeniu w celu odnajdywania SQL Server wystąpień i baz danych. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153511)|
|30005: Logowanie SQL Server nie powiodło się z powodu problemu z jego domyślną główną bazą danych.|Sama baza danych jest nieprawidłowa lub logowanie nie ma uprawnień do nawiązania połączenia z bazą danych.|Użyj polecenia ALTER LOGIN, aby ustawić domyślną bazę danych w bazie danych Master.<br/>Przyznaj roli sysadmin poświadczenia/konto udostępniane na urządzeniu w celu odnajdywania SQL Server wystąpień i baz danych. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153615)|
|30006: nie można użyć nazwy logowania SQL Server z uwierzytelnianiem systemu Windows.|1. identyfikator logowania może być identyfikatorem logowania SQL Server, ale serwer akceptuje tylko uwierzytelnianie systemu Windows.<br/>2. próbujesz nawiązać połączenie przy użyciu uwierzytelniania SQL Server, ale użyta nazwa logowania nie istnieje w SQL Server.<br/>3. logowanie może korzystać z uwierzytelniania systemu Windows, ale logowanie jest nierozpoznanym podmiotem zabezpieczeń systemu Windows. Nierozpoznany podmiot zabezpieczeń systemu Windows oznacza, że logowanie nie może zostać zweryfikowane przez system Windows. Może to być spowodowane faktem, że logowanie systemu Windows pochodzi z niezaufanej domeny.|Jeśli próbujesz nawiązać połączenie przy użyciu uwierzytelniania SQL Server, sprawdź, czy SQL Server jest skonfigurowany w trybie uwierzytelniania mieszanego, a SQL Server Logowanie istnieje.<br/>Jeśli próbujesz nawiązać połączenie przy użyciu uwierzytelniania systemu Windows, sprawdź, czy użytkownik jest prawidłowo zalogowany do prawidłowej domeny. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153421)|
|30007: hasło wygasło.|Hasło konta wygasło.|Hasło logowania SQL Server mogło wygasnąć, zmienić ustawienie hasła i/lub zwiększyć datę wygaśnięcia hasła. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153419)|
|30008: należy zmienić hasło.|Należy zmienić hasło konta.|Zmień hasło podanego poświadczenia na potrzeby odnajdywania SQL Server. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153318)|
|30009: Wystąpił błąd wewnętrzny.|Wystąpił błąd wewnętrzny podczas odnajdywania wystąpień SQL Server i baz danych. |Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft.|
|30010: nie znaleziono baz danych.|Nie można znaleźć żadnych baz danych z wybranego wystąpienia serwera.|Przyznaj roli sysadmin poświadczenia/konto udostępniane na urządzeniu w celu odnajdywania baz danych SQL.|
|30011: Wystąpił błąd wewnętrzny podczas oceniania wystąpienia lub bazy danych SQL.|Wystąpił błąd wewnętrzny podczas przeprowadzania oceny.|Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft.|
|30012: połączenie SQL nie powiodło się.|1. Zapora na serwerze odrzuciła połączenie.<br/>2. usługa SQL Server Browser (SQLBrowser) nie została uruchomiona.<br/>3. SQL Server nie odpowiedział na żądanie klienta, ponieważ serwer prawdopodobnie nie został uruchomiony.<br/>4. Klient SQL Server nie może nawiązać połączenia z serwerem. Ten błąd może wystąpić, ponieważ serwer nie jest skonfigurowany do akceptowania połączeń zdalnych.<br/>5. Klient SQL Server nie może nawiązać połączenia z serwerem. Może wystąpić błąd, ponieważ klient nie może rozpoznać nazwy serwera lub nazwa serwera jest niepoprawna.<br/>6. protokoły TCP lub nazwanych potoków nie są włączone.<br/>7. określona nazwa wystąpienia SQL Server jest nieprawidłowa.|Skorzystaj z [tego](https://go.microsoft.com/fwlink/?linkid=2153317) interaktywnego przewodnika użytkownika, aby rozwiązać problem z łącznością. Zaczekaj 24 godziny, po upływie których dane mają zostać zaktualizowane w usłudze. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft.|
|30013: Wystąpił błąd podczas nawiązywania połączenia z wystąpieniem programu SQL Server.|1. nazwa SQL Server nie może zostać rozwiązana z urządzenia.<br/>2. SQL Server nie zezwala na połączenia zdalne.|Jeśli możesz wysłać polecenie ping do programu SQL Server z urządzenia, odczekaj 24 godziny, aby sprawdzić, czy ten problem został rozwiązany. Jeśli nie, skontaktuj się z pomocą techniczną firmy Microsoft. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153316)|
|30014: Nazwa użytkownika lub hasło jest nieprawidłowe.| Ten błąd może wystąpić z powodu niepowodzenia uwierzytelniania, które dotyczy nieprawidłowego hasła lub nazwy użytkownika.|Podaj poświadczenie z prawidłową nazwą użytkownika i hasłem. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153315)|
|30015: Wystąpił błąd wewnętrzny podczas odnajdywania wystąpienia SQL.|Wystąpił błąd wewnętrzny podczas odnajdywania wystąpienia programu SQL.|Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft.|
|30016: połączenie z wystąpieniem "% instance;" nie powiodło się z powodu przekroczenia limitu czasu.| Taka sytuacja może wystąpić, Jeśli Zapora na serwerze odmówi połączenia.|Sprawdź, czy Zapora w SQL Server jest skonfigurowana do akceptowania połączeń. Jeśli błąd będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2153611)|
|30017: Wystąpił błąd wewnętrzny.|Nieobsługiwany wyjątek.|Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft.|
|30018: Wystąpił błąd wewnętrzny.|Wystąpił błąd wewnętrzny podczas zbierania danych, takich jak rozmiar tymczasowej bazy danych, rozmiar pliku itp.|Poczekaj 24 godziny i skontaktuj się z pomocą techniczną firmy Microsoft, jeśli problem będzie się powtarzać.|
|30019: Wystąpił błąd wewnętrzny.|Wystąpił błąd wewnętrzny podczas zbierania metryk wydajności, takich jak użycie pamięci itp., w bazie danych lub wystąpieniu.|Poczekaj 24 godziny i skontaktuj się z pomocą techniczną firmy Microsoft, jeśli problem będzie się powtarzać.|

## <a name="next-steps"></a>Następne kroki

Skonfiguruj urządzenie dla programu [VMware](how-to-set-up-appliance-vmware.md), [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)lub [serwerów fizycznych](how-to-set-up-appliance-physical.md).
