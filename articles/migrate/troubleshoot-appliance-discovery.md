---
title: Rozwiązywanie problemów z wdrażaniem i odnajdywaniem urządzenia Azure Migrate
description: Uzyskaj pomoc dotyczącą wdrażania urządzenia Azure Migrate i odnajdywania maszyn.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: b2a01e816c0f0569c207aa65e5027b935210e3b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331781"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Rozwiązywanie problemów z urządzeniem Azure Migrate i odnajdywanie

Ten artykuł pomaga w rozwiązywaniu problemów podczas wdrażania urządzenia [Azure Migrate](migrate-services-overview.md) i korzystania z urządzenia w celu odnajdywania maszyn lokalnych.


## <a name="whats-supported"></a>Co jest obsługiwane?

[Zapoznaj](migrate-appliance.md) się z wymaganiami dotyczącymi obsługi urządzenia.


## <a name="invalid-ovf-manifest-entry"></a>"Nieprawidłowy wpis manifestu OVF"

Jeśli zostanie wyświetlony komunikat o błędzie "podany plik manifestu jest nieprawidłowy: nieprawidłowy wpis manifestu OVF", wykonaj następujące czynności:

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate został poprawnie pobrany, sprawdzając jego wartość skrótu. [Dowiedz się więcej](./tutorial-prepare-vmware.md). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
2. Jeśli wdrożenie nadal kończy się niepowodzeniem i używasz klienta VMware vSphere do wdrożenia pliku OVF, spróbuj wdrożyć go za pośrednictwem klienta sieci Web vSphere. Jeśli wdrożenie nadal kończy się niepowodzeniem, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci Web vSphere i podjęto próbę jego wdrożenia na vCenter Server 6,5 lub 6,7, spróbuj wdrożyć komórki jajowe bezpośrednio na hoście ESXi:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) z klientem sieci Web (https://<*adres IP hosta*>/UI).
   - W **obszarze**  >  **spis sprzętu**wybierz pozycję **plik**  >  **Wdróż OVF szablon**. Przejdź do komórek jajowych i Ukończ wdrożenie.
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, skontaktuj się z pomocą techniczną Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nie można nawiązać połączenia z Internetem

Taka sytuacja może wystąpić, jeśli komputer urządzenia znajduje się za serwerem proxy.

- Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy ich wymaga.
- Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Dodaj [te adresy URL](migrate-appliance.md#url-access) do listy dozwolonych.
- Jeśli używasz przechwycenia serwera proxy do łączenia się z Internetem, zaimportuj certyfikat serwera proxy na maszynę wirtualną urządzenia, wykonując [następujące kroki](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Nie można zalogować się do platformy Azure z poziomu aplikacji sieci Web urządzenia

Wystąpił błąd "Niestety, wystąpił problem podczas logowania użytkownika", jeśli używasz nieprawidłowego konta platformy Azure do logowania się do platformy Azure. Ten błąd występuje z kilku powodów:

- Jeśli zalogujesz się do aplikacji sieci Web dla chmury publicznej, przy użyciu poświadczeń konta użytkownika dla portalu w chmurze dla instytucji rządowych.
- Jeśli zalogujesz się do aplikacji sieci Web dla instytucji rządowych przy użyciu poświadczeń konta użytkownika dla portalu chmury prywatnej.

Upewnij się, że używasz poprawnych poświadczeń.

##  <a name="datetime-synchronization-error"></a>Błąd synchronizacji daty/godziny

Błąd dotyczący synchronizacji daty i godziny (802) wskazuje, że zegar serwera może nie być zsynchronizowany z bieżącym czasem przez więcej niż pięć minut. Zmień czas zegara na maszynie wirtualnej modułu zbierającego tak, aby był zgodny z bieżącą godziną:

1. Otwórz wiersz polecenia administratora na maszynie wirtualnej.
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

- Błąd 60052, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate" występuje, jeśli konto platformy Azure użyte do zarejestrowania urządzenia ma niewystarczające uprawnienia.
    - Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
    - [Dowiedz się więcej](./migrate-appliance.md#appliance---vmware) na temat wymaganych ról i uprawnień platformy Azure.
- Błąd 60039, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate" może wystąpić, jeśli rejestracja nie powiedzie się, ponieważ nie można odnaleźć projektu Azure Migrate użytego do rejestracji urządzenia.
    - W Azure Portal i sprawdź, czy projekt istnieje w grupie zasobów.
    - Jeśli projekt nie istnieje, Utwórz nowy projekt Azure Migrate w grupie zasobów i ponownie Zarejestruj urządzenie. [Dowiedz się, jak](./how-to-add-tool-first-time.md#create-a-project-and-add-a-tool) utworzyć nowy projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Błąd 60030/60031: operacja zarządzania Key Vault nie powiodła się

Jeśli zostanie wyświetlony błąd 60030 lub 60031, "Azure Key Vault operacji zarządzania nie powiodła się", wykonaj następujące czynności:
- Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
- Upewnij się, że konto ma dostęp do magazynu kluczy określonego w komunikacie o błędzie, a następnie spróbuj ponownie wykonać operację.
- Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.
- [Dowiedz się więcej](./migrate-appliance.md#appliance---vmware) o wymaganych rolach i uprawnieniach platformy Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Błąd 60028: nie można zainicjować odnajdywania

Błąd 60028: "nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla określonej listy hostów lub klastrów "oznacza, że nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z dostępem lub pobraniem informacji o maszynie wirtualnej. Pozostałe hosty zostały pomyślnie dodane.

- Ponownie Dodaj hosty wymienione w błędzie przy użyciu opcji **Dodaj hosta** .
- Jeśli wystąpi błąd walidacji, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, a następnie spróbuj ponownie wykonać operację **zapisywania i uruchamiania odnajdywania** .

## <a name="error-60025-azure-ad-operation-failed"></a>Błąd 60025: operacja usługi Azure AD nie powiodła się 
Błąd 60025: "operacja usługi Azure AD nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi Azure AD "występuje, gdy konto użytkownika platformy Azure używane do inicjowania odnajdywania jest inne niż konto użyte do zarejestrowania urządzenia. Wykonaj jedną z następujących czynności:

- Upewnij się, że konto użytkownika, które inicjuje odnajdywanie, jest takie samo jak użyte do zarejestrowania urządzenia.
- Podaj Azure Active Directory uprawnienia dostępu do aplikacji dla konta użytkownika, dla którego operacja odnajdywania kończy się niepowodzeniem.
- Usuń grupę zasobów utworzoną wcześniej dla projektu Azure Migrate. Utwórz kolejną grupę zasobów, aby ponownie uruchomić.
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
    1. Zaloguj się do komputera z systemem Linux i Otwórz plik konfiguracji SSH przy użyciu polecenia "VI/etc/ssh/sshd_config"
    2. Dla opcji "PasswordAuthentication" Ustaw wartość tak. Zapisz plik.
    3. Uruchom ponownie usługę SSH, uruchamiając "Service SSHD restart"
- Jeśli jest to system Windows Server, upewnij się, że port 5985 jest otwarty, aby umożliwić zdalne wywołania WMI.
- Jeśli odnajdywasz serwer GCP z systemem Linux i używasz użytkownika root, użyj następujących poleceń, aby zmienić domyślne ustawienie logowania głównego
    1. Zaloguj się do komputera z systemem Linux i Otwórz plik konfiguracji SSH przy użyciu polecenia "VI/etc/ssh/sshd_config"
    2. Dla opcji "PermitRootLogin" Ustaw wartość tak.
    3. Uruchom ponownie usługę SSH, uruchamiając "Service SSHD restart"

## <a name="error-no-suitable-authentication-method-found"></a>Błąd: nie znaleziono odpowiedniej metody uwierzytelniania

Upewnij się, że na serwerze z systemem Linux jest włączone uwierzytelnianie oparte na hasłach, wykonując następujące czynności:
    1. Zaloguj się do komputera z systemem Linux i Otwórz plik konfiguracji SSH przy użyciu polecenia "VI/etc/ssh/sshd_config"
    2. Dla opcji "PasswordAuthentication" Ustaw wartość tak. Zapisz plik.
    3. Uruchom ponownie usługę SSH, uruchamiając "Service SSHD restart"


## <a name="discovered-vms-not-in-portal"></a>Odnalezione maszyny wirtualne nie są w portalu

Jeśli stan odnajdywania to "odnajdywanie w toku", ale nie widzisz jeszcze maszyn wirtualnych w portalu, odczekaj kilka minut:
- Dla maszyny wirtualnej VMware trwa około 15 minut.
- Każdy dodany host na potrzeby odnajdywania maszyn wirtualnych funkcji Hyper-V zajmie około dwóch minut.

Jeśli zaczekasz, a stan nie zmieni się, wybierz pozycję **Odśwież** na karcie **serwery** . Ta wartość powinna zawierać liczbę odnalezionych serwerów w Azure Migrate: Ocena serwera i Azure Migrate: Migracja serwera.

Jeśli to nie zadziała, a będziesz odnajdywać serwery VMware:

- Sprawdź, czy określone konto vCenter ma ustawione uprawnienia prawidłowo, z dostępem do co najmniej jednej maszyny wirtualnej.
- Azure Migrate nie może odnaleźć maszyn wirtualnych VMware, jeśli konto vCenter ma dostęp udzielony na poziomie folderu vCenter VM. [Dowiedz się więcej](set-discovery-scope.md) na temat odnajdywania zakresu.

## <a name="vm-data-not-in-portal"></a>Dane maszyny wirtualnej nie są w portalu

Jeśli odnalezione maszyny wirtualne nie są wyświetlane w portalu lub dane maszyny wirtualnej są nieaktualne, odczekaj kilka minut. Zmiany w wykrytych konfiguracjach maszyn wirtualnych mogą pojawić się w portalu dopiero po 30 minutach. Zmiany w danych aplikacji mogą pojawić się dopiero po kilku godzinach. Jeśli po tym czasie nie ma danych, spróbuj odświeżyć w następujący sposób.

1. W obszarze **serwery**  >  **Azure Migrate oceny serwera**wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**.
3. Wybierz pozycję **Odśwież agenta**.
4. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.

## <a name="deleted-vms-appear-in-portal"></a>Usunięte maszyny wirtualne pojawiają się w portalu

Jeśli usuniesz maszyny wirtualne i nadal pojawią się one w portalu, odczekaj 30 minut. Jeśli nadal są wyświetlane, Odśwież zgodnie z powyższym opisem.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nie widzę danych o wydajności niektórych kart sieciowych na serwerach fizycznych

Taka sytuacja może wystąpić, jeśli na serwerze fizycznym jest włączona Wirtualizacja funkcji Hyper-V. Z powodu przerwy w działaniu produktu przepustowość sieci jest przechwytywana na odnalezionych wirtualnych kartach sieciowych.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Błąd: przekazany plik ma nieoczekiwany format
Niektóre narzędzia mają ustawienia regionalne, które tworzą plik CSV ze średnikiem jako ogranicznikiem. Zmień ustawienia, aby upewnić się, że ogranicznikiem jest przecinek.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Zaimportowano plik CSV, ale widzę komunikat „Trwa odnajdywanie”
Ten stan jest wyświetlany, jeśli przekazywanie pliku CSV nie powiodło się z powodu błędu walidacji. Spróbuj ponownie zaimportować plik CSV. Możesz pobrać raport o błędach poprzedniego przekazywania i postępować zgodnie z zaleceniami w pliku, aby naprawić błędy. Raport o błędach można pobrać z sekcji „Importowanie szczegółów” na stronie „Odnajdywanie maszyn”.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Nie wyświetlaj szczegółów aplikacji nawet po zaktualizowaniu poświadczeń gościa
Odnajdywanie aplikacji jest uruchamiane co 24 godziny. Jeśli chcesz natychmiast zobaczyć szczegóły, Odśwież w następujący sposób. Może to potrwać kilka minut, w zależności od tego, czy nie. wykrytych maszyn wirtualnych.

1. W obszarze **serwery**  >  **Azure Migrate oceny serwera**wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**.
3. Wybierz pozycję **Odśwież agenta**.
4. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.

## <a name="unable-to-export-application-inventory"></a>Nie można wyeksportować spisu aplikacji
Upewnij się, że użytkownik pobierający spis z portalu ma uprawnienia współautora w ramach subskrypcji.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Nie znaleziono odpowiedniej metody uwierzytelniania do ukończenia uwierzytelniania (PublicKey)
Uwierzytelnianie oparte na kluczach nie będzie działało, Użyj uwierzytelniania hasła.

## <a name="common-app-discovery-errors"></a>Typowe błędy funkcji odnajdywania aplikacji

Azure Migrate obsługuje odnajdywanie aplikacji, ról i funkcji przy użyciu Azure Migrate: Ocena serwera. Odnajdywanie aplikacji jest obecnie obsługiwane tylko przez oprogramowanie VMware. [Dowiedz się więcej](how-to-discover-applications.md) na temat wymagań i kroków dotyczących konfigurowania funkcji odnajdywania aplikacji.

Typowe błędy odnajdowania aplikacji zostały podsumowane w tabeli. 

**Błąd** | **Przyczyna** | **Akcja**
--- | --- | ---
9000: nie można wykryć stanu narzędzia VMware.     |   Narzędzia VMWare mogą nie być zainstalowane lub są uszkodzone.    |   Upewnij się, że narzędzia VMware zostały zainstalowane i uruchomione na maszynie wirtualnej.
9001: narzędzia VMware nie są zainstalowane.     |   Narzędzia VMWare mogą nie być zainstalowane lub są uszkodzone.    |   Upewnij się, że narzędzia VMware zostały zainstalowane i uruchomione na maszynie wirtualnej.
9002: narzędzia VMware nie są uruchomione.   |   Narzędzia VMWare mogą nie być zainstalowane lub są uszkodzone.    |   Upewnij się, że narzędzia VMware zostały zainstalowane i uruchomione na maszynie wirtualnej.
9003: typ systemu operacyjnego nie jest obsługiwany w przypadku odnajdywania maszyn wirtualnych gościa.    |   System operacyjny uruchomiony na serwerze nie jest systemem Windows ani Linux.    |   Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux. Jeśli serwer jest w rzeczywistości Windows lub Linux, Sprawdź typ systemu operacyjnego określony w vCenter Server.
9004: maszyna wirtualna nie jest uruchomiona.     |   Maszyna wirtualna jest wyłączona.  |   Upewnij się, że maszyna wirtualna jest włączona.
9005: typ systemu operacyjnego nie jest obsługiwany w przypadku odnajdywania maszyn wirtualnych gościa.    |   Typ systemu operacyjnego nie jest obsługiwany w przypadku odnajdywania maszyn wirtualnych gościa.     |   Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux.
9006: adres URL pobierania pliku metadanych z gościa jest pusty.     |   Może się tak zdarzyć, jeśli agent odnajdywania nie działa zgodnie z oczekiwaniami.    |   Problem powinien automatycznie rozwiązać in24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft.
9007: nie znaleziono procesu uruchamiania zadania odnajdywania w maszynie wirtualnej gościa.   |   Może się tak zdarzyć, jeśli agent odnajdywania nie działa prawidłowo.   |   Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft.
9008: nie można pobrać stanu procesu maszyny wirtualnej gościa.   |   Problem może wystąpić z powodu błędu wewnętrznego.   |   Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft.
9009: funkcja Windows UAC nie uniemożliwiła wykonania zadania odnajdywania na serwerze.  |   Ustawienia kontroli konta użytkownika systemu Windows (UAC) na serwerze są restrykcyjne i uniemożliwiają odnajdywanie zainstalowanych aplikacji.  |   W ustawieniach kontroli konta użytkownika na serwerze skonfiguruj ustawienie UAC na jeden z niższych poziomów.
9010: maszyna wirtualna jest wyłączona.     |   Maszyna wirtualna jest wyłączona.  |   Upewnij się, że maszyna wirtualna jest włączona.
9011: odnaleziony plik metadanych nie został znaleziony w systemie plików maszyny wirtualnej gościa.    |   Problem może wystąpić z powodu błędu wewnętrznego.   |   Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft.
9012: odnaleziony plik metadanych jest pusty.     |   Problem może wystąpić z powodu błędu wewnętrznego.   |   Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft.
9013: dla każdej nazwy logowania tworzony jest nowy profil tymczasowy.    |   Nowy profil tymczasowy jest tworzony dla każdej nazwy logowania na maszynie wirtualnej VMware.    |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9014: nie można pobrać metadanych z systemu plików maszyny wirtualnej gościa.     |   Brak łączności z hostem ESXi    |   Upewnij się, że urządzenie może połączyć się z portem 443 na hoście ESXi z uruchomioną maszyną wirtualną
9015: rola operacji gościa nie jest włączona na koncie użytkownika vCenter   |   Rola operacji gościa nie jest włączona na koncie użytkownika vCenter.   |   Upewnij się, że rola operacje gościa jest włączona na koncie użytkownika vCenter.
9016: nie można odnaleźć agenta, ponieważ agent operacji gościa jest nieaktualny.   |   Narzędzia VMware nie są poprawnie zainstalowane lub są nieaktualne.    |   Upewnij się, że narzędzia VMware są prawidłowo zainstalowane i aktualne.
9017: plik z odnalezionymi metadanymi nie został znaleziony na maszynie wirtualnej.  |   Problem może wystąpić z powodu błędu wewnętrznego.   |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9018: program PowerShell nie jest zainstalowany na maszynach wirtualnych gościa.  |   Program PowerShell nie jest dostępny na maszynie wirtualnej gościa.    |   Zainstaluj program PowerShell na maszynie wirtualnej gościa.
9019: nie można odnaleźć z powodu niepowodzeń operacji maszyny wirtualnej gościa.     |   Nie można wykonać operacji gościa VMware na maszynie wirtualnej.    |   Upewnij się, że poświadczenia maszyny wirtualnej są prawidłowe, a nazwa użytkownika podana w poświadczeniach maszyny wirtualnej gościa jest w formacie nazwy UPN.
9020: odmowa uprawnień do tworzenia pliku.    |   Rola skojarzona z użytkownikiem lub zasad grupy ogranicza użytkownikowi możliwość tworzenia pliku w folderze    |   Sprawdź, czy podany użytkownik-Gość ma uprawnienie Tworzenie dla pliku w folderze. Zobacz **powiadomienia** w obszarze Ocena serwera, aby uzyskać nazwę folderu.
9021: nie można utworzyć pliku w tymczasowej ścieżce systemowej.     |   Narzędzie VMware raportuje ścieżkę Temp systemu zamiast ścieżki tymczasowej użytkownika.    |   Uaktualnij wersję narzędzia VMware powyżej 10287 (format klienta NGC/VI).
9022: odmowa dostępu do obiektu WMI.    |   Rola skojarzona z użytkownikiem lub zasad grupy ogranicza użytkownikowi dostęp do obiektu WMI.  |   Skontaktuj się z pomoc techniczna firmy Microsoft.
9023: nie można uruchomić programu PowerShell, ponieważ wartość zmiennej środowiskowej główny_katalog_systemowy jest pusta.    |   Wartość zmiennej środowiskowej główny_katalog_systemowy jest pusta dla maszyny wirtualnej gościa.     |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9024: nie można odnaleźć jako wartość zmiennej środowiskowej TEMP jest pusta.    |   Wartość zmiennej środowiskowej TEMP jest pusta dla maszyny wirtualnej gościa.   |   Skontaktuj się z pomoc techniczna firmy Microsoft.
9025: program PowerShell jest uszkodzony na maszynach wirtualnych gościa.  |   Program PowerShell jest uszkodzony na maszynie wirtualnej gościa.    |   Zainstaluj ponownie program PowerShell na maszynie wirtualnej gościa i sprawdź, czy środowisko PowerShell można uruchomić na maszynie wirtualnej gościa.
9026: nie można uruchomić operacji gościa na maszynie wirtualnej.  |   Stan maszyny wirtualnej nie zezwala na uruchamianie operacji gościa na maszynie wirtualnej.   |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9027: Agent operacji gościa nie jest uruchomiony na maszynie wirtualnej.   |   Nie można skontaktować się z agentem operacji gościa uruchomionym w ramach maszyny wirtualnej.    |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9028: nie można utworzyć pliku z powodu niewystarczającej ilości miejsca na dysku w maszynie wirtualnej.     |   Za mało miejsca na dysku.   |   Upewnij się, że dostępna jest wystarczająca ilość miejsca w magazynie na dysku maszyny wirtualnej.
9029: nie można uzyskać dostępu do programu PowerShell na podanym poświadczeniu maszyny wirtualnej gościa.   |   Dostęp do programu PowerShell nie jest dostępny dla użytkownika.     |   Upewnij się, że użytkownik dodany na urządzeniu może uzyskać dostęp do programu PowerShell na maszynie wirtualnej gościa.
9030: nie można zebrać odnalezionych metadanych, ponieważ Host ESXi został odłączony.     |   Host ESXi jest w stanie odłączonym.   |   Upewnij się, że host ESXi z uruchomioną maszyną wirtualną jest połączony.
9031: nie można zebrać odnalezionych metadanych, ponieważ Host ESXi nie odpowiada.   |   Host zdalny jest w nieprawidłowym stanie.    |   Upewnij się, że host ESXi z uruchomioną maszyną wirtualną jest uruchomiony i połączony.
9032: nie można odnaleźć z powodu błędu wewnętrznego.   |   Problem może wystąpić z powodu błędu wewnętrznego.   |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9033: nie można odnaleźć, ponieważ nazwa użytkownika maszyny wirtualnej zawiera nieprawidłowe znaki.     |   W nazwie użytkownika wykryto nieprawidłowe znaki.   |   Podaj ponownie poświadczenia maszyny wirtualnej, aby upewnić się, że nie ma żadnych nieprawidłowych znaków.
9034: podana nazwa użytkownika nie jest w formacie nazwy UPN.    |   Nazwa użytkownika nie jest w formacie nazwy UPN.  |   Upewnij się, że nazwa użytkownika jest w formacie głównej nazwy użytkownika (UPN).
9035: nie można odnaleźć, ponieważ tryb języka programu PowerShell nie jest ustawiony na wartość "Full Language".  |   Tryb języka dla programu PowerShell na maszynie wirtualnej gościa nie jest ustawiony na język pełny.   |   Upewnij się, że tryb języka programu PowerShell jest ustawiony na wartość "Full Language".
9037: zbieranie danych zostało tymczasowo wstrzymane, ponieważ czas odpowiedzi maszyny wirtualnej jest zbyt duży.    |   Wykryta maszyna wirtualna trwa zbyt długo, aby odpowiedzieć     |   Żadna akcja nie jest wymagana. W przypadku odnajdywania aplikacji i 3 godzin próba analizy zależności (bez wykorzystania agentów) zostanie ponowiona w ciągu 24 godzin.
10000: typ systemu operacyjnego nie jest obsługiwany.   |   System operacyjny uruchomiony na serwerze nie jest systemem Windows ani Linux.    |   Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux.
10001: na urządzeniu nie znaleziono skryptu do odnajdowania serwerów.    |   Odnajdywanie nie działa zgodnie z oczekiwaniami.   |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
10002: zadanie odnajdywania nie zostało ukończone w czasie.     |   Agent odnajdywania nie działa zgodnie z oczekiwaniami.     |   Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft.
10003: proces wykonywania zadania odnajdywania zakończył się z powodu błędu.    |   Proces wykonywania zadania odnajdywania zakończył się z powodu błędu.  |   Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem nadal występuje, skontaktuj się z pomoc techniczna firmy Microsoft.
10004: nie podano poświadczeń dla typu systemu operacyjnego gościa.  |   Nie podano poświadczeń w celu uzyskania dostępu do maszyn tego typu systemu operacyjnego na urządzeniu Azure Migrate.    |   Dodawanie poświadczeń dla maszyn na urządzeniu
10005: podane poświadczenia są nieprawidłowe.   |   Poświadczenia podane dla urządzenia w celu uzyskania dostępu do serwera są nieprawidłowe.  |   Zaktualizuj poświadczenia podane w urządzeniu i upewnij się, że serwer jest dostępny przy użyciu poświadczeń.
10006: typ systemu operacyjnego gościa nie jest obsługiwany przez magazyn poświadczeń.  |   System operacyjny uruchomiony na serwerze nie jest systemem Windows ani Linux.    |   Obsługiwane typy systemów operacyjnych to tylko systemy Windows i Linux.
10007: nie można przetworzyć odnalezionych metadanych.    |   Wystąpił błąd podczas próby deserializacji JSON.    |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
10008: nie można utworzyć pliku na serwerze.    |  Problem może wystąpić z powodu błędu wewnętrznego.    |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
10009: nie można zapisać odnalezionych metadanych do pliku na serwerze.  |   Problem może wystąpić z powodu błędu wewnętrznego.   |   Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.




## <a name="next-steps"></a>Następne kroki
Skonfiguruj urządzenie dla programu [VMware](how-to-set-up-appliance-vmware.md), [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)lub [serwerów fizycznych](how-to-set-up-appliance-physical.md).
