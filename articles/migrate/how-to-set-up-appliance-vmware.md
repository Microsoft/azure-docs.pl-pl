---
title: Konfigurowanie urządzenia Azure Migrate dla oprogramowania VMware
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceniania i migrowania maszyn wirtualnych VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81538260"
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
- Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

## <a name="download-the-ova-template"></a>Pobierz szablon komórki jajowe

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** kliknij przycisk **tak, z funkcją hypervisor programu VMware vSphere**.
3. Kliknij pozycję **Pobierz**, aby pobrać plik szablonu OVA.

  ![Wybrane do pobrania plik komórki jajowe](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla komórek jajowych:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. W przypadku najnowszej wersji urządzenia wygenerowany skrót powinien być zgodny z tymi [ustawieniami](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **plik**  >  **Wdróż OVF szablon**.
![Polecenie menu do wdrażania szablonu OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. W Kreatorze wdrażania szablonu OVF > **Źródło**Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja**Określ przyjazną nazwę maszyny wirtualnej. Wybierz obiekt spisu, w którym będzie hostowana maszyna wirtualna.
5. W obszarze **host/klaster**Określ hosta lub klaster, na którym będzie URUCHAMIANA maszyna wirtualna.
6. W obszarze **Magazyn**określ miejsce docelowe magazynu dla maszyny wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci**Określ sieć, z którą zostanie nawiązane połączenie z maszyną wirtualną. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do oceny serwera Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


## <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .


## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy. Jeśli urządzenie zostanie wdrożone przy użyciu skryptu zamiast szablonu komórki jajowe, pierwsze dwa kroki procedury nie będą miały zastosowania.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
4. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN .
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Aby odnajdywanie działało prawidłowo, czas na urządzeniu powinien być zsynchronizowany z czasem Internetu.
    - **Zainstaluj aktualizacje**: Azure Migrate sprawdza, czy są zainstalowane najnowsze aktualizacje urządzeń.
    - **Zainstaluj VDDK**Azure Migrate: sprawdza, czy jest zainstalowany zestaw SDK programu VMware VSPHERE (VDDK).
        - Migracje platformy Azure używają VDDK do replikowania maszyn podczas migracji na platformę Azure.
        - Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu.

## <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
2. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate. Następnie wybierz projekt.
3. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
4. Kliknij pozycję **Zarejestruj**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Rozpocznij odnajdowanie ciągłe, podając poświadczenia vCenter Server i maszyny wirtualnej

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

### <a name="specify-vcenter-server-details"></a>Określanie szczegółów programu vCenter Server
1. W obszarze **określ vCenter Server Szczegóły**Określ nazwę (FQDN) lub adres IP vCenter Server. Możesz opuścić port domyślny lub określić niestandardowy port, na którym nasłuchuje vCenter Server.
2. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta tylko do odczytu, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych na serwerze vCenter. Zakres odnajdywania można ograniczyć, ograniczając dostęp do konta vCenter. [Dowiedz się więcej](set-discovery-scope.md).
3. Kliknij pozycję **Sprawdź poprawność połączenia** , aby upewnić się, że urządzenie może połączyć się z vCenter Server.

### <a name="specify-vm-credentials"></a>Określ poświadczenia maszyny wirtualnej
W przypadku odnajdywania aplikacji, ról i funkcji oraz wizualizacji zależności maszyn wirtualnych można podać poświadczenia maszyny wirtualnej, które mają dostęp do maszyn wirtualnych VMware. Można dodać jedno poświadczenie dla maszyn wirtualnych z systemem Windows i jedno poświadczenie dla maszyn wirtualnych z systemem Linux. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) o wymaganych uprawnieniach dostępu.

> [!NOTE]
> Dane wejściowe są opcjonalne i są konieczne do włączenia funkcji odnajdywania aplikacji i wizualizacji zależności bez wykorzystania agentów.

1. W obszarze **Znajdź aplikacje i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia**.
2. Wybierz **system operacyjny**.
3. Podaj przyjazną nazwę dla poświadczenia.
4. W polu **Nazwa użytkownika** i **hasło**Określ konto, które ma co najmniej dostęp gościa na maszynach wirtualnych.
5. Kliknij pozycję **Dodaj**.

Po określeniu vCenter Server i poświadczeń maszyny wirtualnej (opcjonalnie) kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć odnajdywanie środowiska lokalnego.

Metadane wykrytych maszyn wirtualnych będą wyświetlane w portalu na około 15 minut. Odnajdywanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu, a czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 potrzeba około 1 godziny, aby spis aplikacji był wyświetlany w portalu Azure Migrate.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami dotyczącymi [oceny oprogramowania VMware](tutorial-assess-vmware.md) i [migracji bez wykorzystania agentów](tutorial-migrate-vmware.md).
