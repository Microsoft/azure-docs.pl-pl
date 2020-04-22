---
title: Rozwiązywanie problemów z wdrażaniem i odnajdywania urządzeń migrujących platformy Azure
description: Uzyskaj pomoc dotyczącą wdrażania urządzenia migracji platformy Azure i odnajdywania maszyn.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677322"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Rozwiązywanie problemów z urządzeniem i odnajdywaniem usługi Azure Migrate

Ten artykuł ułatwia [rozwiązywanie](migrate-services-overview.md) problemów podczas wdrażania urządzenia migracji platformy Azure i używania urządzenia do odnajdowania maszyn lokalnych.


## <a name="whats-supported"></a>Jakie operacje są obsługiwane?

[Zapoznaj](migrate-appliance.md) się z wymaganiami dotyczącymi obsługi urządzeń.


## <a name="invalid-ovf-manifest-entry"></a>"Nieprawidłowy wpis manifestu OVF"

Jeśli zostanie wyświetlony błąd "Podany plik manifestu jest nieprawidłowy: Nieprawidłowy wpis manifestu OVF", wykonaj następujące czynności:

1. Sprawdź, czy plik ova urządzenia usługi Azure Migrate jest pobierany poprawnie, sprawdzając jego wartość mieszania. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Jeśli wartość mieszania nie jest zgodna, pobierz plik OVA ponownie i ponów próbę wdrożenia.
2. Jeśli wdrożenie nadal nie powiedzie się i używasz klienta VMware vSphere do wdrożenia pliku OVF, spróbuj wdrożyć go za pośrednictwem klienta sieci web vSphere. Jeśli wdrożenie nadal nie powiedzie się, spróbuj użyć innej przeglądarki sieci Web.
3. Jeśli używasz klienta sieci web vSphere i próbujesz go wdrożyć na serwerze vCenter Server 6.5 lub 6.7, spróbuj wdrożyć ova bezpośrednio na hoście ESXi:
   - Połącz się bezpośrednio z hostem ESXi (zamiast vCenter Server) z klientem sieci web (https://<*hosta adres IP*>/ui).
   - W **magazynie domowym** > **Inventory**wybierz**szablon OVF wdrażania** **plików** > . Przejdź do ova i zakończyć wdrożenie.
4. Jeśli wdrożenie nadal nie powiedzie się, skontaktuj się z pomocą techniczną usługi Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nie można połączyć się z Internetem

Może się tak zdarzyć, jeśli urządzenie znajduje się za serwerem proxy.

- Upewnij się, że podasz poświadczenia autoryzacji, jeśli serwer proxy ich potrzebuje.
- Jeśli używasz serwera proxy zapory opartej na adresie URL do kontrolowania łączności wychodzącej, dodaj [te adresy URL](migrate-appliance.md#url-access) do listy dozwolonych.
- Jeśli używasz przechwytującego serwera proxy do łączenia się z Internetem, zaimportuj certyfikat serwera proxy na maszynę wirtualną urządzenia, wykonując [następujące kroki](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Nie można zalogować się do platformy Azure za pomocą aplikacji sieci Web urządzenia

Błąd "Przepraszamy, ale mamy problemy z zalogowaniem się" pojawia się, jeśli używasz niepoprawnego konta platformy Azure do logowania się na platformie Azure. Ten błąd występuje z kilku powodów:

- Jeśli zalogujesz się do aplikacji sieci web urządzenia dla chmury publicznej, przy użyciu poświadczeń konta użytkownika dla portalu chmury rządowej.
- Jeśli zalogujesz się do aplikacji sieci web urządzenia dla chmury rządowej przy użyciu poświadczeń konta użytkownika dla portalu chmury prywatnej.

Upewnij się, że używasz poprawnych poświadczeń.

##  <a name="datetime-synchronization-error"></a>Błąd synchronizacji daty/godziny

Błąd dotyczący synchronizacji daty i godziny (802) wskazuje, że zegar serwera może być niezsynchronizowany z bieżącym czasem o więcej niż pięć minut. Zmień czas zegara na maszynie wirtualnej modułu zbierającego, aby dopasować bieżący czas:

1. Otwórz wiersz polecenia administratora na maszynie wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom **w32tm /tz**.
3. Aby zsynchronizować czas, uruchom **w32tm /resync**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Jeśli zostanie wyświetlony ten błąd połączenia, może być nie można połączyć się z *serwerem*serwera vCenter .com:9443. Szczegóły błędu wskazują, że nie ma `https://\*servername*.com:9443/sdk` punktu końcowego nasłuchiwania, który może zaakceptować komunikat.

- Sprawdź, czy korzystasz z najnowszej wersji urządzenia. Jeśli nie, uaktualnij urządzenie do [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Jeśli problem nadal występuje w najnowszej wersji, urządzenie może nie być w stanie rozpoznać określonej nazwy serwera vCenter lub określony port może być nieprawidłowy. Domyślnie, jeśli port nie jest określony, moduł zbierający spróbuje połączyć się z portem o numerze 443.

    1. Ping *Servername*.com z urządzenia.
    2. Jeśli krok 1 nie powiedzie się, spróbuj połączyć się z serwerem vCenter przy użyciu adresu IP.
    3. Zidentyfikuj poprawny numer portu, aby połączyć się z serwerem vCenter Server.
    4. Sprawdź, czy serwer vCenter jest uruchomiony.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Błąd 60052/60039: Urządzenie może nie być zarejestrowane

- Błąd 60052, "Urządzenie może nie zostać pomyślnie zarejestrowane w projekcie migracji platformy Azure", jeśli konto platformy Azure używane do rejestracji urządzenia ma niewystarczające uprawnienia.
    - Upewnij się, że konto użytkownika platformy Azure używane do rejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
    - [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o wymaganych rolach i uprawnieniach platformy Azure.
- Błąd 60039, "Urządzenie może nie zostać pomyślnie zarejestrowane w projekcie migracji platformy Azure", jeśli rejestracja zakończy się niepowodzeniem, ponieważ nie można odnaleźć projektu migracji platformy Azure używanego do rejestracji urządzenia.
    - W witrynie Azure portal i sprawdź, czy projekt istnieje w grupie zasobów.
    - Jeśli projekt nie istnieje, utwórz nowy projekt migracji platformy Azure w grupie zasobów i zarejestruj urządzenie ponownie. [Dowiedz się, jak](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) utworzyć nowy projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Błąd 60030/60031: Operacja zarządzania magazynem kluczy nie powiodła się

Jeśli zostanie wyświetlony błąd 60030 lub 60031, "Operacja zarządzania usługi Azure Key Vault nie powiodła się", wykonaj następujące czynności:
- Upewnij się, że konto użytkownika platformy Azure używane do rejestrowania urządzenia ma co najmniej uprawnienia współautora w ramach subskrypcji.
- Upewnij się, że konto ma dostęp do magazynu kluczy określonego w komunikacie o błędzie, a następnie ponów próbę wykonania operacji.
- Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o wymaganych rolach i uprawnieniach platformy Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Błąd 60028: nie można zainicjować odnajdywania

Błąd 60028: "Nie można zainicjować odnajdywania z powodu błędu. Operacja nie powiodła się dla określonej listy hostów lub klastrów" wskazuje, że nie można uruchomić odnajdywania na hostach wymienionych w błędzie z powodu problemu z uzyskiwaniem dostępu do informacji o maszynie wirtualnej lub pobieraniem. Reszta gospodarzy została pomyślnie dodana.

- Ponownie dodaj hosty wymienione w błędzie, używając opcji **Dodaj hosta.**
- Jeśli występuje błąd sprawdzania poprawności, przejrzyj wskazówki dotyczące korygowania, aby naprawić błędy, a następnie spróbuj ponownie użyć opcji **Zapisz i rozpocznij odnajdowanie.**

## <a name="error-60025-azure-ad-operation-failed"></a>Błąd 60025: operacja usługi Azure AD nie powiodła się 
Błąd 60025: "Operacja usługi Azure AD nie powiodła się. Wystąpił błąd podczas tworzenia lub aktualizowania aplikacji usługi Azure AD" występuje, gdy konto użytkownika platformy Azure używane do inicjowania odnajdywania różni się od konta używanego do rejestrowania urządzenia. Wykonaj jedną z następujących czynności:

- Upewnij się, że konto użytkownika inicjujące odnajdowanie jest takie samo jak konto używane do rejestracji urządzenia.
- Podaj uprawnienia dostępu do aplikacji usługi Azure Active Directory do konta użytkownika, dla którego operacja odnajdywania nie powiódł się.
- Usuń grupę zasobów utworzoną wcześniej dla projektu migracji platformy Azure. Utwórz inną grupę zasobów, aby rozpocząć ponownie.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o uprawnieniach aplikacji usługi Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Błąd 50004: nie można połączyć się z hostem lub klastrem

Błąd 50004: "Nie można połączyć się z hostem lub klastrem, ponieważ nie można rozpoznać nazwy serwera. Kod błędu Usługi WinRM: 0x803381B9" może wystąpić, jeśli usługa DNS platformy Azure dla urządzenia nie może rozpoznać podanej nazwy klastra lub hosta.

- Jeśli ten błąd jest widoczny w klastrze, funkcja FQDN klastra.
- Ten błąd może również zostać wyświetlony dla hostów w klastrze. Oznacza to, że urządzenie może łączyć się z klastrem, ale klaster zwraca nazwy hostów, które nie są nazwami FQDN. Aby rozwiązać ten problem, zaktualizuj plik hosts na urządzeniu, dodając mapowanie adresu IP i nazw hostów:
    1. Otwórz Notatnik jako administrator.
    2. Otwórz plik C:\Windows\System32\Drivers\etc\hosts.
    3. Dodaj adres IP i nazwę hosta w wierszu. Powtórz tę czynność dla każdego hosta lub klastra, w którym jest widoczny ten błąd.
    4. Zapisz i zamknij plik hosts.
    5. Sprawdź, czy urządzenie może łączyć się z hostami za pomocą aplikacji do zarządzania urządzeniami. Po 30 minutach powinny zostać wyświetlene najnowsze informacje dotyczące tych hostów w witrynie Azure portal.

## <a name="discovered-vms-not-in-portal"></a>Odnalezione maszyny wirtualne nie w portalu

Jeśli stan odnajdywania jest "Odnajdowanie w toku", ale nie widzą jeszcze maszyn wirtualnych w portalu, poczekaj kilka minut:
- VMware VMware VMware zajmuje około 15 minut.
- Trwa około dwóch minut dla każdego dodanego hosta dla odnajdowania maszyny Wirtualnej funkcji Hyper-V.

Jeśli poczekasz, a stan się nie zmieni, wybierz pozycję **Odśwież** na karcie **Serwery.** Powinno to pokazać liczbę wykrytych serwerów w usłudze Azure Migrate: Server Assessment i Azure Migrate: Server Migration.

Jeśli to nie zadziała i odnajdujesz serwery VMware:

- Sprawdź, czy określone konto vCenter ma poprawnie ustawione uprawnienia z dostępem do co najmniej jednej maszyny Wirtualnej.
- Usługa Azure Migrate nie może odnajdować maszyn wirtualnych VMware, jeśli konto vCenter ma dostęp udzielony na poziomie folderu vCenter VM. [Dowiedz się więcej](set-discovery-scope.md) o odnajdowaniu zakresu.

## <a name="vm-data-not-in-portal"></a>Dane maszyny Wirtualnej nie w portalu

Jeśli wykryte maszyny wirtualne nie są wyświetlane w portalu lub jeśli dane maszyny Wirtualnej są nieaktualne, poczekaj kilka minut. Trwa do 30 minut, aby zmiany w wykrytych danych konfiguracji maszyny Wirtualnej, aby pojawić się w portalu. Może upłynąć kilka godzin, aby zmiany w danych aplikacji, aby pojawić się. Jeśli po tej godzinie nie ma żadnych danych, spróbuj odświeżyć, w następujący sposób

1. W **obszarze Oceny** > **serwera azure migrate server**wybierz pozycję **Przegląd**.
2. W obszarze **Zarządzanie**wybierz pozycję **Kondycja agenta**.
3. Wybierz **pozycję Odśwież agenta**.
4. Poczekaj na zakończenie operacji odświeżania. Teraz powinny być widoczne aktualne informacje.

## <a name="deleted-vms-appear-in-portal"></a>Usunięte maszyny wirtualne są wyświetlane w portalu

Jeśli usuniesz maszyny wirtualne i nadal pojawiają się one w portalu, poczekaj 30 minut. Jeśli nadal się pojawiają, odśwież w sposób opisany powyżej.

## <a name="common-app-discovery-errors"></a>Typowe błędy odnajdywania aplikacji

Usługa Azure Migrate obsługuje odnajdowanie aplikacji, ról i funkcji przy użyciu usługi Azure Migrate: Server Assessment. Odnajdowanie aplikacji jest obecnie obsługiwane tylko dla VMware. [Dowiedz się więcej](how-to-discover-applications.md) o wymaganiach i krokach konfigurowania odnajdowania aplikacji.

Typowe błędy odnajdowania aplikacji są sumowane w tabeli. 

**Błąd** | **Spowodować** | **Akcja**
--- | --- | --- | ---
10000: "Nie można odnajdywać aplikacji zainstalowanych na serwerze". | Może to nastąpić, jeśli system operacyjny komputera nie jest Windows lub Linux. | Odnajdowania aplikacji należy używać tylko dla systemu Windows/Linux.
10001: "Nie można pobrać aplikacji zainstalowanych na serwerze". | Błąd wewnętrzny - niektóre brakujące pliki w urządzeniu. | Contact Microsoft Support.
10002: "Nie można pobrać aplikacji zainstalowanych na serwerze". | Agent odnajdowania urządzenia może nie działać poprawnie. | Jeśli problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
10003 "Nie można pobrać aplikacji zainstalowanych na serwerze". | Agent odnajdowania urządzenia może nie działać poprawnie. | Jeśli problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
10004: "Nie można wykryć zainstalowanych aplikacji dla <> komputerów z systemem Windows/Linux". |  Poświadczenia dostępu do <> komputerów systemu Windows/Linux nie zostały podane w urządzeniu.| Dodaj poświadczenia do urządzenia, które ma dostęp do <> komputerów z systemem Windows/Linux.
10005: "Nie można uzyskać dostępu do serwera lokalnego". | Poświadczenia dostępu mogą być błędne. | Zaktualizuj poświadczenia urządzenia upewnij się, że można uzyskać dostęp do odpowiedniego komputera z nimi. 
10006: "Nie można uzyskać dostępu do serwera lokalnego". | Może to nastąpić, jeśli system operacyjny komputera nie jest Windows lub Linux.|  Odnajdowania aplikacji należy używać tylko dla systemu Windows/Linux.
10007: "Nie można przetworzyć pobranych metadanych" | Ten błąd wewnętrzny wystąpił podczas próby deserializacji JSON | Skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania rozwiązania
9000/9001/9002: "Nie można odnajdywać aplikacji zainstalowanych na serwerze". | Narzędzia VMware mogą nie być zainstalowane lub uszkodzone. | Zainstaluj/zainstaluj ponownie narzędzia VMware na odpowiednim komputerze i sprawdź, czy jest uruchomiony.
9003: Nie można odnajdywać aplikacji zainstalowanych na serwerze". | Może to nastąpić, jeśli system operacyjny komputera nie jest Windows lub Linux. | Odnajdowania aplikacji należy używać tylko dla systemu Windows/Linux.
9004: "Nie można odnajdywać aplikacji zainstalowanych na serwerze". | Może się tak zdarzyć, jeśli maszyna wirtualna jest wyłączona. | Aby odnajdyć, upewnij się, że maszyna wirtualna jest wł..
9005: "Nie można wykryć aplikacji zainstalowanych na maszynie Wirtualnej. | Może to nastąpić, jeśli system operacyjny komputera nie jest Windows lub Linux. | Odnajdowania aplikacji należy używać tylko dla systemu Windows/Linux.
9006/9007: "Nie można pobrać zainstalowanych aplikacji serwera". | Agent odnajdowania urządzenia może nie działać poprawnie. | Jeśli problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
9008: "Nie można pobrać aplikacji zainstalowanych na serwerze". | Może to być błąd wewnętrzny.  | Tf problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
9009: "Nie można pobrać aplikacji zainstalowanych na serwerze". | Może wystąpić, jeśli ustawienia kontroli konta użytkownika systemu Windows (Kontrola konta użytkownika) na serwerze są restrykcyjne i uniemożliwiają odnajdowanie zainstalowanych aplikacji. | Wyszukaj na serwerze ustawienia "Kontrola konta użytkownika" i skonfiguruj ustawienie funkcji Kontrola konta użytkownika na serwerze na jeden z dwóch niższych poziomów.
9010: "Nie można pobrać aplikacji zainstalowanych na serwerze". | Może to być błąd wewnętrzny.  | Tf problem nie rozwiąże się w ciągu 24 godzin, skontaktuj się z pomocą techniczną.
9011: "Plik do pobrania od gościa nie znajduje się na maszynie wirtualnej gościa" | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft.
9012: "Zawartość pliku wynik jest pusta." | Problem może wystąpić z powodu błędu wewnętrznego. | Problem powinien zostać automatycznie rozwiązany w ciągu 24 godzin. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft.
9013: "Dla każdego logowania do maszyny wirtualnej VMware tworzony jest nowy profil tymczasowy" | Nowy profil tymczasowy jest tworzony dla każdego logowania do maszyny Wirtualnej | Upewnij się, że nazwa użytkownika podana w poświadczeniach maszyny Wirtualnej gościa jest w formacie UPN.
9015: "Nie można połączyć się z maszynami wirtualnymi VMware z powodu niewystarczających uprawnień w vCenter" | Rola Operacje gościa nie jest włączona na koncie użytkownika vCenter | Upewnij się, że funkcja Operacje gościa jest włączona na koncie użytkownika vCenter.
9016: "Nie można połączyć się z maszynami wirtualnymi VMware, ponieważ agent operacyjny gościa nie ma danych" | Narzędzia VMware nie są poprawnie zainstalowane lub nie są aktualne. | Upewnij się, że narzędzia VMware są prawidłowo zainstalowane i aktualne.
9017: "Plik z odnalezionymi metadanymi nie znajduje się na maszynie wirtualnej." | Problem może wystąpić z powodu błędu wewnętrznego. | Aby uzyskać rozwiązanie, skontaktuj się z pomocą techniczną firmy Microsoft.
9018: "Program PowerShell nie jest zainstalowany w maszynach wirtualnych gościa." | Program PowerShell nie jest dostępny na maszynie wirtualnej gościa. | Zainstaluj program PowerShell na maszynie wirtualnej gościa.
9019: "Nie można odnajdywać z powodu awarii operacji maszyny wirtualnej gościa" | Operacja gościa VMware nie powiodła się na maszynie wirtualnej. | Upewnij się, że poświadczenia maszyny Wirtualnej są prawidłowe, a nazwa użytkownika podana w poświadczeniach maszyny Wirtualnej gościa jest w formacie UPN.
9020: "Odmowa uprawnień do tworzenia plików". | Rola skojarzona z użytkownikiem lub zasadami grupy ogranicza użytkownika do tworzenia pliku w folderze | Sprawdź, czy podany użytkownik-gość ma uprawnienia do tworzenia pliku w folderze. Zobacz **Powiadomienia** w ocenie serwera dla nazwy folderu.
9021: "Odmowa uprawnienia do tworzenia plików w folderze Ścieżka tymczasowa systemu". | Wersja narzędzia VMware na maszynie wirtualnej nie jest podparta | Uaktualnij wersję narzędzia VMware powyżej 10.2.0.
9022: "Odmowa dostępu do obiektu WMI jest zabroniony". | Rola skojarzona z użytkownikiem lub zasadami grupy ogranicza użytkownika do dostępu do obiektu WMI. | Skontaktuj się z pomocą techniczną firmy Microsoft.
9023: "Wartość zmiennej środowiskowej systemroot jest pusta." | Nieznana | Skontaktuj się z pomocą techniczną firmy Microsoft.
9024: "Wartość zmiennej środowiska TEMP jest pusta." | Nieznana | Skontaktuj się z pomocą techniczną firmy Microsoft.
9025: "Program PowerShell jest uszkodzony w maszynach wirtualnych gościa." | Nieznana | Zainstaluj ponownie program PowerShell w maszynie wirtualnej gościa i sprawdź, czy program PowerShell może być uruchamiany na maszynie wirtualnej gościa.
8084: "Nie można odnajdywać <Exception from VMware>aplikacji z powodu błędu VMware: " | Urządzenie migracji platformy Azure używa interfejsów API VMware do odnajdowania aplikacji. Ten problem może się zdarzyć, jeśli wyjątek jest zgłaszany przez serwer vCenter podczas próby odnajdowania aplikacji. Komunikat o błędzie z VMware jest wyświetlany w komunikacie o błędzie wyświetlanym w portalu. | Wyszukaj wiadomość w [dokumentacji VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)i wykonaj kroki, które należy naprawić. Jeśli nie możesz naprawić, skontaktuj się z pomocą techniczną firmy Microsoft.



## <a name="next-steps"></a>Następne kroki
Skonfiguruj urządzenie dla serwerów [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)lub [fizycznych.](how-to-set-up-appliance-physical.md)
