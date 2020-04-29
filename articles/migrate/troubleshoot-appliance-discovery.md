---
title: Rozwiązywanie problemów z wdrażaniem i odnajdywaniem urządzenia Azure Migrate
description: Uzyskaj pomoc dotyczącą wdrażania urządzenia Azure Migrate i odnajdywania maszyn.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677322"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Rozwiązywanie problemów z urządzeniem Azure Migrate i odnajdywanie

Ten artykuł pomaga w rozwiązywaniu problemów podczas wdrażania urządzenia [Azure Migrate](migrate-services-overview.md) i korzystania z urządzenia w celu odnajdywania maszyn lokalnych.


## <a name="whats-supported"></a>Jakie operacje są obsługiwane?

[Zapoznaj](migrate-appliance.md) się z wymaganiami dotyczącymi obsługi urządzenia.


## <a name="invalid-ovf-manifest-entry"></a>"Nieprawidłowy wpis manifestu OVF"

Jeśli zostanie wyświetlony komunikat o błędzie "podany plik manifestu jest nieprawidłowy: nieprawidłowy wpis manifestu OVF", wykonaj następujące czynności:

1. Sprawdź, czy plik komórki jajowe urządzenia Azure Migrate został poprawnie pobrany, sprawdzając jego wartość skrótu. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Jeśli wartość skrótu nie jest zgodna, pobierz ponownie plik komórki jajowe i spróbuj ponownie wykonać wdrożenie.
2. Jeśli wdrożenie nadal kończy się niepowodzeniem i używasz klienta VMware vSphere do wdrożenia pliku OVF, spróbuj wdrożyć go za pośrednictwem klienta sieci Web vSphere. Jeśli wdrożenie nadal kończy się niepowodzeniem, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci Web vSphere i podjęto próbę jego wdrożenia na vCenter Server 6,5 lub 6,7, spróbuj wdrożyć komórki jajowe bezpośrednio na hoście ESXi:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) z klientem sieci Web (https://<*adres IP hosta*>/UI).
   -  > W **obszarze****spis sprzętu**wybierz **pozycję plik** > **Wdróż OVF szablon**. Przejdź do komórek jajowych i Ukończ wdrożenie.
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, skontaktuj się z pomocą techniczną Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nie można nawiązać połączenia z Internetem

Taka sytuacja może wystąpić, jeśli komputer urządzenia znajduje się za serwerem proxy.

- Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy ich potrzebuje.
- Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Dodaj [te adresy URL](migrate-appliance.md#url-access) do listy dozwolonych.
- Jeśli używasz przechwycenia serwera proxy do łączenia się z Internetem, zaimportuj certyfikat serwera proxy na maszynę wirtualną urządzenia, wykonując [następujące kroki](https://docs.microsoft.com/azure/migrate/concepts-collector).

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

- Sprawdź, czy korzystasz z najnowszej wersji urządzenia. Jeśli nie, Uaktualnij urządzenie do [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Jeśli problem nadal występuje w najnowszej wersji, urządzenie może nie być w stanie rozpoznać określonej nazwy vCenter Server lub określony port może być nieprawidłowy. Domyślnie, jeśli port nie jest określony, moduł zbierający podejmie próbę nawiązania połączenia z numerem portu 443.

    1. Wyślij polecenie ping *servername*. com z urządzenia.
    2. Jeśli krok 1 nie powiedzie się, spróbuj połączyć się z serwerem vCenter przy użyciu adresu IP.
    3. Określ poprawny numer portu, aby nawiązać połączenie z vCenter Server.
    4. Sprawdź, czy vCenter Server działa.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Błąd 60052/60039: urządzenie może nie być zarejestrowane

- Błąd 60052, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate" występuje, jeśli konto platformy Azure użyte do zarejestrowania urządzenia ma niewystarczające uprawnienia.
    - Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
    - [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) na temat wymaganych ról i uprawnień platformy Azure.
- Błąd 60039, "urządzenie mogło nie zostać pomyślnie zarejestrowane w projekcie Azure Migrate" może wystąpić, jeśli rejestracja nie powiedzie się, ponieważ nie można odnaleźć projektu Azure Migrate użytego do rejestracji urządzenia.
    - W Azure Portal i sprawdź, czy projekt istnieje w grupie zasobów.
    - Jeśli projekt nie istnieje, Utwórz nowy projekt Azure Migrate w grupie zasobów i ponownie Zarejestruj urządzenie. [Dowiedz się, jak](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) utworzyć nowy projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Błąd 60030/60031: operacja zarządzania Key Vault nie powiodła się

Jeśli zostanie wyświetlony błąd 60030 lub 60031, "Azure Key Vault operacji zarządzania nie powiodła się", wykonaj następujące czynności:
- Upewnij się, że konto użytkownika platformy Azure używane do zarejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
- Upewnij się, że konto ma dostęp do magazynu kluczy określonego w komunikacie o błędzie, a następnie spróbuj ponownie wykonać operację.
- Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o wymaganych rolach i uprawnieniach platformy Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Błąd 60028: nie można zainicjować odnajdywania

Błąd 60028: "nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla określonej listy hostów lub klastrów "oznacza, że nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z dostępem lub pobraniem informacji o maszynie wirtualnej. Pozostałe hosty zostały pomyślnie dodane.

- Ponownie Dodaj hosty wymienione w błędzie przy użyciu opcji **Dodaj hosta** .
- Jeśli wystąpi błąd walidacji, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, a następnie spróbuj ponownie wykonać operację **zapisywania i uruchamiania odnajdywania** .

## <a name="error-60025-azure-ad-operation-failed"></a>Błąd 60025: operacja usługi Azure AD nie powiodła się 
Błąd 60025: "operacja usługi Azure AD nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi Azure AD "występuje, gdy konto użytkownika platformy Azure używane do inicjowania odnajdywania jest inne niż konto użyte do zarejestrowania urządzenia. Wykonaj jedną z następujących czynności:

- Upewnij się, że konto użytkownika, które inicjuje odnajdywanie, jest takie samo jak użyte do zarejestrowania urządzenia.
- Podaj Azure Active Directory uprawnienia dostępu do aplikacji dla konta użytkownika, dla którego operacja odnajdywania kończy się niepowodzeniem.
- Usuń grupę zasobów utworzoną wcześniej dla projektu Azure Migrate. Utwórz kolejną grupę zasobów, aby ponownie uruchomić.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o Azure Active Directory uprawnieniach aplikacji.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Błąd 50004: nie można nawiązać połączenia z hostem lub klastrem

Błąd 50004: "nie można nawiązać połączenia z hostem lub klastrem, ponieważ nie można rozpoznać nazwy serwera. Kod błędu WinRM: 0x803381B9 "może wystąpić, jeśli usługa Azure DNS dla urządzenia nie może rozpoznać podanej nazwy klastra lub hosta.

- Jeśli ten błąd jest wyświetlany w klastrze, nazwa FQDN klastra.
- Ten błąd może również pojawić się w przypadku hostów w klastrze. Oznacza to, że urządzenie może połączyć się z klastrem, ale klaster zwraca nazwy hostów, które nie są nazwami FQDN. Aby rozwiązać ten problem, zaktualizuj plik Hosts na urządzeniu poprzez dodanie mapowania adresów IP i nazw hostów:
    1. Otwórz Notatnik jako administrator.
    2. Otwórz plik C:\Windows\System32\Drivers\etc\hosts.
    3. Dodaj adres IP i nazwę hosta w wierszu. Powtórz tę czynność dla każdego hosta lub klastra, w którym widzisz ten błąd.
    4. Zapisz i zamknij plik hosts.
    5. Sprawdź, czy urządzenie może nawiązać połączenie z hostami przy użyciu aplikacji do zarządzania urządzeniami. Po 30 minutach powinny zostać wyświetlone najnowsze informacje dotyczące tych hostów w Azure Portal.

## <a name="discovered-vms-not-in-portal"></a>Odnalezione maszyny wirtualne nie są w portalu

Jeśli stan odnajdywania to "odnajdywanie w toku", ale nie widzisz jeszcze maszyn wirtualnych w portalu, odczekaj kilka minut:
- Dla maszyny wirtualnej VMware trwa około 15 minut.
- Każdy dodany host na potrzeby odnajdywania maszyn wirtualnych funkcji Hyper-V zajmie około dwóch minut.

Jeśli zaczekasz, a stan nie zmieni się, wybierz pozycję **Odśwież** na karcie **serwery** . Ta wartość powinna zawierać liczbę odnalezionych serwerów w Azure Migrate: Ocena serwera i Azure Migrate: Migracja serwera.

Jeśli to nie zadziała, a będziesz odnajdywać serwery VMware:

- Sprawdź, czy określone konto vCenter ma ustawione uprawnienia prawidłowo, z dostępem do co najmniej jednej maszyny wirtualnej.
- Azure Migrate nie może odnaleźć maszyn wirtualnych VMware, jeśli konto vCenter ma dostęp na poziomie folderu vCenter VM. [Dowiedz się więcej](set-discovery-scope.md) na temat odnajdywania zakresu.

## <a name="vm-data-not-in-portal"></a>Dane maszyny wirtualnej nie są w portalu

Jeśli odnalezione maszyny wirtualne nie są wyświetlane w portalu lub dane maszyny wirtualnej są nieaktualne, odczekaj kilka minut. Zmiany w wykrytych konfiguracjach maszyn wirtualnych mogą pojawić się w portalu dopiero po 30 minutach. Zmiany w danych aplikacji mogą pojawić się dopiero po kilku godzinach. Jeśli po tym czasie nie ma danych, spróbuj odświeżyć w następujący sposób.

1. W obszarze **serwery** > **Azure Migrate oceny serwera**wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzaj**wybierz pozycję **Agent Health**.
3. Wybierz pozycję **Odśwież agenta**.
4. Poczekaj na zakończenie operacji odświeżania. Informacje powinny być teraz wyświetlane na bieżąco.

## <a name="deleted-vms-appear-in-portal"></a>Usunięte maszyny wirtualne pojawiają się w portalu

Jeśli usuniesz maszyny wirtualne i nadal pojawią się one w portalu, odczekaj 30 minut. Jeśli nadal są wyświetlane, Odśwież zgodnie z powyższym opisem.

## <a name="common-app-discovery-errors"></a>Typowe błędy funkcji odnajdywania aplikacji

Azure Migrate obsługuje odnajdywanie aplikacji, ról i funkcji przy użyciu Azure Migrate: Ocena serwera. Odnajdywanie aplikacji jest obecnie obsługiwane tylko przez oprogramowanie VMware. [Dowiedz się więcej](how-to-discover-applications.md) na temat wymagań i kroków dotyczących konfigurowania funkcji odnajdywania aplikacji.

Typowe błędy odnajdowania aplikacji zostały podsumowane w tabeli. 

**Błąd** | **Przyczyna** | **Akcja**
--- | --- | --- | ---
10000: "nie można odnaleźć aplikacji zainstalowanych na serwerze". | Taka sytuacja może wystąpić, jeśli system operacyjny komputera nie jest w systemie Windows lub Linux. | Funkcja odnajdywania aplikacji jest używana tylko w systemie Windows/Linux.
10001: "nie można pobrać aplikacji zainstalowanych na serwerze". | Błąd wewnętrzny — niektóre brakujące pliki w urządzeniu. | Contact Microsoft Support.
10002: "nie można pobrać aplikacji zainstalowanych na serwerze". | Agent odnajdywania na urządzeniu może nie działać prawidłowo. | Jeśli problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
10003 "nie można pobrać aplikacji zainstalowanych na serwerze". | Agent odnajdywania na urządzeniu może nie działać prawidłowo. | Jeśli problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
10004: "nie można odnaleźć zainstalowanych aplikacji dla <maszynach> z systemem Windows/Linux". |  W urządzeniu nie dostarczono poświadczeń dostępu do <z maszynami> z systemem Windows/Linux.| Dodaj poświadczenie do urządzenia, które ma dostęp do <> z systemem Windows/Linux.
10005: "nie można uzyskać dostępu do serwera lokalnego". | Poświadczenia dostępu mogą być nieprawidłowe. | Zaktualizuj poświadczenia urządzenia, aby upewnić się, że masz dostęp do odpowiedniej maszyny. 
10006: "nie można uzyskać dostępu do serwera lokalnego". | Taka sytuacja może wystąpić, jeśli system operacyjny komputera nie jest w systemie Windows lub Linux.|  Funkcja odnajdywania aplikacji jest używana tylko w systemie Windows/Linux.
10007: "nie można przetworzyć pobranych metadanych" | Wystąpił błąd wewnętrzny podczas próby deserializacji JSON | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie
9000/9001/9002: "nie można odnaleźć aplikacji zainstalowanych na serwerze". | Narzędzia VMware mogą nie być zainstalowane lub są uszkodzone. | Zainstaluj/ponownie zainstaluj narzędzia VMware na odpowiedniej maszynie i sprawdź, czy jest ono uruchomione.
9003: nie można odnaleźć aplikacji zainstalowanych na serwerze ". | Taka sytuacja może wystąpić, jeśli system operacyjny komputera nie jest w systemie Windows lub Linux. | Funkcja odnajdywania aplikacji jest używana tylko w systemie Windows/Linux.
9004: "nie można odnaleźć aplikacji zainstalowanych na serwerze". | Taka sytuacja może wystąpić, jeśli maszyna wirtualna jest wyłączona. | Na potrzeby odnajdywania upewnij się, że maszyna wirtualna jest włączona.
9005: "nie można odnaleźć aplikacji zainstalowanych na maszynie wirtualnej. | Taka sytuacja może wystąpić, jeśli system operacyjny komputera nie jest w systemie Windows lub Linux. | Funkcja odnajdywania aplikacji jest używana tylko w systemie Windows/Linux.
9006/9007: "nie można pobrać aplikacji zainstalowanych na serwerze". | Agent odnajdywania na urządzeniu może nie działać prawidłowo. | Jeśli problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
9008: "nie można pobrać aplikacji zainstalowanych na serwerze". | Może być błędem wewnętrznym.  | TF problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
9009: "nie można pobrać aplikacji zainstalowanych na serwerze". | Może wystąpić, jeśli ustawienia kontroli konta użytkownika systemu Windows (UAC) na serwerze są restrykcyjne i uniemożliwiają odnajdywanie zainstalowanych aplikacji. | Wyszukaj ustawienia "Kontrola konta użytkownika" na serwerze i skonfiguruj ustawienie UAC na serwerze na jednym z niższych poziomów.
9010: "nie można pobrać aplikacji zainstalowanych na serwerze". | Może być błędem wewnętrznym.  | TF problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
9011: "nie znaleziono pliku do pobrania z gościa na maszynie wirtualnej gościa" | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać rozwiązany automatycznie w ciągu 24 godzin. Jeśli problem nadal występuje, skontaktuj się z pomoc techniczna firmy Microsoft.
9012: "zawartość pliku wynikowego jest pusta". | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać rozwiązany automatycznie w ciągu 24 godzin. Jeśli problem nadal występuje, skontaktuj się z pomoc techniczna firmy Microsoft.
9013: "nowy profil tymczasowy jest tworzony dla każdej nazwy logowania na maszynie wirtualnej VMware" | Nowy profil tymczasowy jest tworzony dla każdej nazwy logowania na maszynie wirtualnej | Upewnij się, że nazwa użytkownika podana w poświadczeniach maszyny wirtualnej gościa jest w formacie nazwy UPN.
9015: "nie można nawiązać połączenia z maszynami wirtualnymi VMware z powodu niewystarczających uprawnień w programie vCenter" | Rola operacji gościa nie jest włączona na koncie użytkownika vCenter | Upewnij się, że rola operacje gościa jest włączona na koncie użytkownika vCenter.
9016: "nie można nawiązać połączenia z maszynami wirtualnymi VMware, ponieważ agent operacji gościa nie ma danych" | Narzędzia VMware nie są poprawnie zainstalowane lub są nieaktualne. | Upewnij się, że narzędzia VMware są prawidłowo zainstalowane i aktualne.
9017: "plik z odnalezionymi metadanymi nie został znaleziony na maszynie wirtualnej". | Problem może wystąpić z powodu błędu wewnętrznego. | Skontaktuj się pomoc techniczna firmy Microsoft, aby uzyskać rozwiązanie.
9018: "program PowerShell nie jest zainstalowany na maszynach wirtualnych gościa". | Program PowerShell nie jest dostępny na maszynie wirtualnej gościa. | Zainstaluj program PowerShell na maszynie wirtualnej gościa.
9019: "nie można odnaleźć z powodu błędów operacji maszyny wirtualnej gościa" | Nie można wykonać operacji gościa VMware na maszynie wirtualnej. | Upewnij się, że poświadczenia maszyny wirtualnej są prawidłowe, a nazwa użytkownika podana w poświadczeniach maszyny wirtualnej gościa jest w formacie nazwy UPN.
9020: "nie udzielono uprawnienia do tworzenia pliku". | Rola skojarzona z użytkownikiem lub zasad grupy ogranicza użytkownika do tworzenia pliku w folderze. | Sprawdź, czy podany użytkownik-Gość ma uprawnienie Tworzenie dla tego pliku w folderze. Zobacz **powiadomienia** w obszarze Ocena serwera, aby uzyskać nazwę folderu.
9021: "brak uprawnienia do tworzenia pliku w ścieżce tymczasowej systemu folderu". | Wersja narzędzia VMware na maszynie wirtualnej nie jest obsługiwana | Uaktualnij wersję narzędzia VMware powyżej 10.2.0.
9022: "odmowa dostępu do obiektu usługi WMI". | Rola skojarzona z użytkownikiem lub zasad grupy ogranicza użytkownikowi dostęp do obiektu WMI. | Skontaktuj się z pomoc techniczna firmy Microsoft.
9023: "wartość zmiennej środowiskowej główny_katalog_systemowy jest pusta". | Nieznane | Skontaktuj się z pomoc techniczna firmy Microsoft.
9024: "wartość zmiennej środowiskowej TEMP jest pusta". | Nieznane | Skontaktuj się z pomoc techniczna firmy Microsoft.
9025: "program PowerShell jest uszkodzony na maszynach wirtualnych gościa". | Nieznane | Zainstaluj ponownie program PowerShell na maszynie wirtualnej gościa i sprawdź, czy można uruchomić środowisko PowerShell na maszynie wirtualnej gościa.
8084: "nie można odnaleźć aplikacji z powodu błędu VMware: <Exception from VMware>" | Urządzenie Azure Migrate używa interfejsów API VMware do odnajdywania aplikacji. Ten problem może wystąpić, jeśli wyjątek jest zgłaszany przez vCenter Server podczas próby odnalezienia aplikacji. W oknie portalu zostanie wyświetlony komunikat o błędzie z programu VMware. | Wyszukaj komunikat w [dokumentacji programu VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)i postępuj zgodnie z instrukcjami, aby rozwiązać ten problem. Jeśli nie możesz rozwiązać tego problemu, skontaktuj się z pomocą techniczną firmy Microsoft.



## <a name="next-steps"></a>Następne kroki
Skonfiguruj urządzenie dla programu [VMware](how-to-set-up-appliance-vmware.md), [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)lub [serwerów fizycznych](how-to-set-up-appliance-physical.md).
