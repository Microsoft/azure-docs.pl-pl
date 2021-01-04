---
title: Konfigurowanie urządzenia Azure Migrate dla oprogramowania VMware
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceniania i migrowania maszyn wirtualnych VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 749dd17c6f9b09db1841459da951b44470d8bb92
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704711"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych VMware

Postępuj zgodnie z tym artykułem, aby skonfigurować urządzenie Azure Migrate na potrzeby oceny za pomocą narzędzia do [oceny serwera Azure Migrate](migrate-services-overview.md#azure-migrate-server-assessment-tool) i dla migracji bez agentów przy użyciu narzędzia [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Tool.

[Urządzenie Azure Migrate](migrate-appliance.md) jest lekkim urządzeniem używanym przez Azure Migrate: oceny serwera i migracji serwera w celu odnajdywania lokalnych maszyn wirtualnych VMware, wysyłania metadanych maszyn wirtualnych/danych wydajności do platformy Azure oraz do replikacji maszyn wirtualnych VMware podczas migracji bez agentów.

Urządzenie można wdrożyć przy użyciu kilku metod:

- Skonfiguruj na maszynie wirtualnej VMware przy użyciu pobranego szablonu komórki jajowe. Jest to metoda opisana w tym artykule.
- Skonfiguruj na maszynie wirtualnej VMware lub na komputerze fizycznym za pomocą skryptu Instalatora programu PowerShell. [Tej metody](deploy-appliance-script.md) należy użyć, jeśli nie można skonfigurować maszyny wirtualnej przy użyciu szablonu komórki jajowe lub w przypadku korzystania z platformy Azure dla instytucji rządowych.

Po utworzeniu urządzenia sprawdź, czy może nawiązać połączenie z Azure Migrate: Ocena serwera, skonfiguruj ją po raz pierwszy i zarejestruj ją za pomocą projektu Azure Migrate.


## <a name="appliance-deployment-ova"></a>Wdrażanie urządzenia (komórki jajowe)

Aby skonfigurować urządzenie przy użyciu szablonu komórki jajowe:
- Podaj nazwę urządzenia i Wygeneruj klucz projektu Azure Migrate w portalu
- Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate przy użyciu klucza projektu Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Generowanie klucza projektu Azure Migrate

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **odnajdywanie** maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, aby uzyskać VMware vSphere funkcji hypervisor**.
3. W obszarze **1: generowanie klucza projektu Azure Migrate** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania maszyn wirtualnych VMware. nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywanie maszyn podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany **klucz projektu Azure Migrate** .
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="download-the-ova-template"></a>Pobierz szablon komórki jajowe
W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik komórki jajowe i kliknij pozycję **Pobierz**. 


   ![Wybory dla maszyn odnajdywania](./media/tutorial-assess-vmware/servers-discover.png)


   ![Wybory dla klucza generowania](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla komórek jajowych:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. W przypadku najnowszej wersji urządzenia wygenerowany skrót powinien być zgodny z tymi [ustawieniami](./tutorial-discover-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **plik**  >  **Wdróż OVF szablon**.
![Polecenie menu do wdrażania szablonu OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. W Kreatorze wdrażania szablonu OVF > **Źródło** Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja** Określ przyjazną nazwę maszyny wirtualnej. Wybierz obiekt spisu, w którym będzie hostowana maszyna wirtualna.
5. W obszarze **host/klaster** Określ hosta lub klaster, na którym będzie URUCHAMIANA maszyna wirtualna.
6. W obszarze **Magazyn** określ miejsce docelowe magazynu dla maszyny wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci** Określ sieć, z którą zostanie nawiązane połączenie z maszyną wirtualną. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do oceny serwera Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


## <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranych komórek jajowych, pierwsze dwa kroki tej procedury nie są istotne.

1. W konsoli klienta vSphere kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz polecenie **Otwórz konsolę**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, wybierając skrót do aplikacji.
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
   - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
     - Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy (w postaci http://ProxyIPAddress lub na http://ProxyFQDN) porcie nasłuchu.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Obsługiwane są tylko serwery proxy HTTP.
     - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
   - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje składników uruchomionych na urządzeniu.
   - **Zainstaluj VDDK**: Urządzenie sprawdza, czy zainstalowano pakiet VMware vSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu, zgodnie z **instrukcjami dotyczącymi instalacji**.

     Migracja serwera Azure Migrate przy użyciu programu VDDK umożliwia replikowanie maszyn podczas migracji na platformę Azure. 
1. Jeśli chcesz, możesz **ponownie uruchomić wymagania wstępne** w dowolnym momencie podczas konfigurowania urządzenia, aby sprawdzić, czy urządzenie spełnia wszystkie wymagania wstępne.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu Azure Migrate** skopiowany z portalu. Jeśli nie masz klucza, przejdź do pozycji **Ocena serwera> odkryj> zarządzanie istniejącymi urządzeniami**, wybierz nazwę urządzenia podaną w momencie generowania klucza i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.

    ![Modalne wyświetlanie kodu urządzenia](./media/tutorial-discover-vmware/device-code.png)

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
4. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie [uprawnienia](./tutorial-discover-vmware.md#prepare-an-azure-user-account) do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

1. W **kroku 1: podaj poświadczenia vCenter Server**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** do konta vCenter Server, które będzie używane przez urządzenie do odnajdywania maszyn wirtualnych w wystąpieniu vCenter Server.
    - Należy skonfigurować konto z uprawnieniami wymaganymi w [poprzednim samouczku](./tutorial-discover-vmware.md#create-an-account-to-access-vcenter).
    - Jeśli chcesz przeznaczyć zakres odnajdywania do określonych obiektów VMware (vCenter Server centrach danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych), zapoznaj się z instrukcjami w [tym artykule](set-discovery-scope.md) , aby ograniczyć konto używane przez Azure Migrate.
1. W **kroku 2: podaj vCenter Server Szczegóły**, kliknij pozycję **Dodaj źródło odnajdywania** , aby wybrać przyjazną nazwę dla poświadczeń z listy rozwijanej, określ **adres IP/nazwę FQDN** wystąpienia vCenter Server. Możesz pozostawić **port** domyślny (443) lub określić port niestandardowy, dla którego vCenter Server nasłuchiwanie i kliknąć przycisk **Zapisz**.
1. Po kliknięciu przycisku Zapisz Urządzenie spróbuje sprawdzić poprawność połączenia z vCenter Server przy użyciu podanych poświadczeń i wyświetlić **stan weryfikacji** w tabeli dotyczącej VCENTER Server adres IP/nazwa FQDN.
1. Przed rozpoczęciem odnajdywania można ponownie **sprawdzić poprawność** łączności, aby vCenter Server w dowolnym momencie.
1. W **kroku 3: podaj poświadczenia maszyny wirtualnej w celu odnalezienia zainstalowanych aplikacji i przeprowadzenia mapowania zależności bez agenta**, kliknij przycisk **Dodaj poświadczenia** i określ system operacyjny, dla którego podano poświadczenia, przyjazną nazwę dla poświadczeń oraz nazwę **użytkownika** i **hasło**. Następnie kliknij przycisk **Zapisz**.

    - Opcjonalnie możesz dodać poświadczenia tutaj, jeśli utworzono konto do użycia dla [funkcji odnajdywania aplikacji](how-to-discover-applications.md)lub [Funkcja analizy zależności bez agenta](how-to-create-group-machine-dependencies-agentless.md).
    - Jeśli nie chcesz korzystać z tych funkcji, możesz kliknąć suwak, aby pominąć ten krok. Zamiar można zmienić w dowolnym momencie później.
    - Przejrzyj poświadczenia potrzebne do [odnajdywania aplikacji](migrate-support-matrix-vmware.md#application-discovery-requirements)lub [analizy zależności bez agentów](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Kliknij przycisk **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie maszyn wirtualnych. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla vCenter Server adres IP/nazwa FQDN w tabeli.

Odnajdywanie działa w następujący sposób:
- Odnalezienie metadanych maszyny wirtualnej w portalu może potrwać około 15 minut.
- Odnajdywanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu. Czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 ilość spisu aplikacji do wyświetlenia w portalu Azure Migrate trwa około godzinę.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami dotyczącymi [oceny oprogramowania VMware](./tutorial-assess-vmware-azure-vm.md) i [migracji bez wykorzystania agentów](tutorial-migrate-vmware.md).