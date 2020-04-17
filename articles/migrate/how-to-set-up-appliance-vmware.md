---
title: Konfigurowanie urządzenia migracji platformy Azure dla oprogramowania VMware
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny i migracji maszyn wirtualnych VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538260"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych VMware

W tym artykule należy skonfigurować urządzenie migracji platformy Azure do oceny za pomocą narzędzia [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) oraz migracji bez agenta przy użyciu narzędzia [Azure Migrate:Server Migration.](migrate-services-overview.md#azure-migrate-server-migration-tool)

[Urządzenie usługi Azure Migrate](migrate-appliance.md) to lekkie urządzenie używane przez usługę Azure Migrate:Server Assessment and Server Migration do odnajdowania lokalnych maszyn wirtualnych VMware, wysyłania metadanych/danych dotyczących wydajności maszyn wirtualnych na platformę Azure oraz replikacji maszyn wirtualnych maszyn wirtualnych podczas migracji bez agenta.

Urządzenie można wdrożyć przy użyciu kilku metod:

- Skonfiguruj na maszynie wirtualnej VMware przy użyciu pobranego szablonu OVA. Jest to metoda opisana w tym artykule.
- Skonfiguruj na maszynie wirtualnej VMware lub na komputerze fizycznym za pomocą skryptu instalatora programu PowerShell. [Ta metoda](deploy-appliance-script.md) powinna być używana, jeśli nie można skonfigurować maszyny Wirtualnej przy użyciu szablonu OVA lub jeśli jesteś w usłudze Azure government.

Po utworzeniu urządzenia można sprawdzić, czy można połączyć się z oceną migracji:serwera platformy Azure, skonfigurować go po raz pierwszy i zarejestrować go w projekcie migracji platformy Azure.


## <a name="appliance-deployment-ova"></a>Wdrażanie urządzeń (OVA)

Aby skonfigurować urządzenie przy użyciu szablonu OVA, należy:
- Pobierz plik szablonu OVA i zaimportuj go do serwera vCenter Server.
- Utwórz urządzenie i sprawdź, czy można połączyć się z oceną serwera migracji platformy Azure.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj go w projekcie migracji platformy Azure.

## <a name="download-the-ova-template"></a>Pobierz szablon OVA

1. W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij **przycisk Tak, z VMWare vSphere hypervisor**.
3. Kliknij pozycję **Pobierz**, aby pobrać plik szablonu OVA.

  ![Wybory do pobierania pliku OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku OVA sprawdź, czy plik ova jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. W przypadku najnowszej wersji urządzenia wygenerowany skrót powinien być zgodny z tymi [ustawieniami](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i utwórz maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję**Szablon OVF wdrażania** **plików** > .
![Polecenie Menu służące do wdrażania szablonu OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. W Kreatorze wdrażania szablonu OVF > **source**określ lokalizację pliku OVA.
3. W **obszarze Nazwa** i **lokalizacja**określ przyjazną nazwę maszyny Wirtualnej. Wybierz obiekt zapasów, w którym będzie obsługiwana maszyna wirtualna.
5. W **obszarze Host/Cluster**określ hosta lub klaster, na którym będzie uruchamiana maszyna wirtualna.
6. W **magazynie**określ miejsce docelowe magazynu dla maszyny Wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W **obszarze Mapowanie sieci**określ sieć, z którą zostanie nawiązyczana maszyna wirtualna. Sieć potrzebuje łączności z Internetem, aby wysłać metadane do oceny serwera migracji platformy Azure.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


## <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może łączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)


## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy. Jeśli urządzenie zostanie wdrożone przy użyciu skryptu zamiast szablonu ova, pierwsze dwa kroki w procedurze nie mają zastosowania.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Podaj język, strefę czasową i hasło urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może połączyć się z maszyną wirtualną, i otwórz adres URL aplikacji internetowej urządzenia: **https:// nazwę urządzenia lub adres*IP:* 44368**.

   Alternatywnie możesz otworzyć aplikację z pulpitu urządzenia, klikając skrót do aplikacji.
4. W aplikacji sieci web > **Konfigurowanie wymagań wstępnych**wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność:** Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres http://ProxyIPAddress http://ProxyFQDNserwera proxy i port nasłuchiwania w formularzu lub .
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu:** czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem korzystania z Internetu, aby odnajdowanie działało poprawnie.
    - **Zainstaluj aktualizacje:** Usługa Azure Migrate sprawdza, czy są zainstalowane najnowsze aktualizacje urządzeń.
    - **Zainstaluj zestaw VDDK:** Usługa Azure Migrate sprawdza, czy jest zainstalowany zestaw VMWare vSphere Virtual Disk Development Kit (VDDK).
        - Usługa Azure Migrates używa VDDK do replikowania maszyn podczas migracji na platformę Azure.
        - Pobierz aplikację VDDK 6.7 z VMware i pobierz pobraną zawartość zip do określonej lokalizacji urządzenia.

## <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie za pomocą usługi Azure Migrate

1. Kliknij **pozycję Zaloguj**się . Jeśli nie jest wyświetlany, upewnij się, że wyłączono blokowanie wyskakujących wyskakujących w przeglądarce.
2. Na nowej karcie zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu swojej nazwy użytkownika i hasła.
    - Logowanie się przy pomocy numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu wróć do aplikacji sieci web.
2. Wybierz subskrypcję, w której został utworzony projekt migracji platformy Azure. Następnie wybierz projekt.
3. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
4. Kliknij **pozycję Zarejestruj**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Rozpoczynanie ciągłego odnajdowania przez podanie poświadczeń serwera vCenter i maszyny Wirtualnej

Urządzenie musi połączyć się z serwerem vCenter, aby odnajdować dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

### <a name="specify-vcenter-server-details"></a>Określanie szczegółów programu vCenter Server
1. W **obszarze Określ szczegóły serwera vCenter**określ nazwę (FQDN) lub adres IP serwera vCenter Server. Można opuścić port domyślny lub określić port niestandardowy, na którym nasłuchuje serwer vCenter Server.
2. W **obszarze Nazwa użytkownika** i **Hasło**określ poświadczenia konta tylko do odczytu, które urządzenie będzie używane do odnajdywania maszyn wirtualnych na serwerze vCenter. Odnajdowanie można ograniczyć dostęp do konta vCenter. [Dowiedz się więcej](set-discovery-scope.md).
3. Kliknij **przycisk Sprawdź poprawność połączenia,** aby upewnić się, że urządzenie może łączyć się z serwerem vCenter Server.

### <a name="specify-vm-credentials"></a>Określanie poświadczeń maszyny Wirtualnej
W przypadku odnajdowania aplikacji, ról i funkcji oraz wizualizacji zależności maszyn wirtualnych można podać poświadczenie maszyny Wirtualnej, które ma dostęp do maszyn wirtualnych VMware. Można dodać jedno poświadczenie dla maszyn wirtualnych systemu Windows i jedno poświadczenie dla maszyn wirtualnych z systemem Linux. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) o wymaganych uprawnieniach dostępu.

> [!NOTE]
> To dane wejściowe są opcjonalne i są potrzebne do umożliwienia odnajdowania aplikacji i wizualizacji zależności bez agenta.

1. W **discover aplikacji i zależności na maszynach wirtualnych**kliknij przycisk Dodaj **poświadczenia**.
2. Wybierz **system operacyjny**.
3. Podaj przyjazną nazwę poświadczeń.
4. W **obszarze Nazwa użytkownika** i **hasło**określ konto, które ma co najmniej dostęp gościa na maszynach wirtualnych.
5. Kliknij pozycję **Add** (Dodaj).

Po określeniu poświadczeń serwera vCenter i maszyny Wirtualnej (opcjonalnie) kliknij przycisk **Zapisz i rozpocznij odnajdowanie,** aby rozpocząć odnajdowanie środowiska lokalnego.

Trwa około 15 minut dla metadanych odnalezionych maszyn wirtualnych do wyświetlenia w portalu. Odnajdowanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu, czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku 500 maszyn wirtualnych trwa około 1 godziny, aby spis aplikacji był wyświetlany w portalu migracji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Przejrzyj samouczki dotyczące [oceny VMware](tutorial-assess-vmware.md) i [migracji bez agenta](tutorial-migrate-vmware.md).
