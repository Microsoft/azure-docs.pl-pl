---
title: Często zadawane pytania dotyczące odzyskiwania po awarii oprogramowania VMware przy użyciu Azure Site Recovery
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: a272486eea111ab8c8e489556986f12f382e3f65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587796"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Typowe pytania dotyczące replikacji z programu VMware do platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące wdrażania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure.

## <a name="general"></a>Ogólne

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Co jest potrzebne w przypadku odzyskiwania po awarii maszyny wirtualnej VMware?

[Dowiedz się więcej na temat składników związanych](vmware-azure-architecture.md) z odzyskiwaniem po awarii maszyn wirtualnych VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Czy można używać Site Recovery do migrowania maszyn wirtualnych VMware na platformę Azure?

Tak. Oprócz korzystania z Site Recovery w celu skonfigurowania pełnego odzyskiwania po awarii dla maszyn wirtualnych VMware można także użyć Site Recovery do migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure. W tym scenariuszu lokalne maszyny wirtualne VMware są replikowane do usługi Azure Storage. Następnie Pracujesz w trybie failover z poziomu lokalnego na platformie Azure. Po przejściu w tryb failover aplikacje i obciążenia są dostępne i uruchamiane na maszynach wirtualnych platformy Azure. Proces ten jest podobny do konfigurowania pełnego odzyskiwania po awarii, z wyjątkiem tego, że w przypadku migracji nie można wrócić do trybu powrotu po awarii z platformy Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy moje konto platformy Azure wymaga uprawnień do tworzenia maszyn wirtualnych?

Jeśli jesteś administratorem subskrypcji, masz wymagane uprawnienia replikacji. Jeśli nie jesteś administratorem, musisz mieć uprawnienia do wykonywania następujących czynności:

- Utwórz MASZYNę wirtualną platformy Azure w grupie zasobów i sieci wirtualnej, którą określisz podczas konfigurowania Site Recovery.
- Zapisz na wybranym koncie magazynu lub na dysku zarządzanym na podstawie konfiguracji.

[Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) na temat wymaganych uprawnień.

### <a name="what-applications-can-i-replicate"></a>Jakie aplikacje mogę replikować?

Można replikować dowolne aplikacje lub obciążenia działające na maszynie wirtualnej VMware spełniającej [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery obsługuje replikację z obsługą aplikacji, dzięki czemu aplikacje mogą przełączeć w tryb failover i powrót po awarii do stanu inteligentnego.
- Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory. Działa również ściśle z wiodącymi dostawcami, takimi jak Oracle, SAP, IBM i Red Hat.

[Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Czy mogę użyć licencji serwera systemu operacyjnego gościa na platformie Azure?

Tak, klienci programu Microsoft Software Assurance mogą korzystać z usługi [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) , aby zaoszczędzić na kosztach licencjonowania dla maszyn z systemem Windows Server migrowanych do platformy Azure lub do odzyskiwania po awarii przy użyciu platformy Azure.

## <a name="security"></a>Zabezpieczenia

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Jakiego dostępu do serwerów VMware Site Recovery potrzebować?

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Skonfiguruj maszynę wirtualną VMware z uruchomionym serwerem konfiguracji Site Recovery.
- Automatyczne odnajdywanie maszyn wirtualnych na potrzeby replikacji.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Jakiego dostępu do maszyn wirtualnych VMware Site Recovery potrzebować?

- Aby można było przeprowadzić replikację, maszyna wirtualna VMware musi mieć zainstalowaną i uruchomioną usługę mobilności Site Recovery. Narzędzie można wdrożyć ręcznie lub określić, że Site Recovery przeprowadzić instalację wypychaną usługi po włączeniu replikacji dla maszyny wirtualnej.
- Podczas replikacji maszyny wirtualne komunikują się z Site Recovery w następujący sposób:
    - Maszyny wirtualne komunikują się z serwerem konfiguracji na porcie HTTPS 443 na potrzeby zarządzania replikacją.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania w porcie HTTPS 9443. (To ustawienie może być modyfikowane).
    - Jeśli włączysz spójność z wieloma MASZYNami wirtualnymi, maszyny wirtualne komunikują się ze sobą za pośrednictwem portu 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Czy dane replikacji są wysyłane do Site Recovery?

Nie, Site Recovery nie przechwytuje replikowanych danych i nie zawiera żadnych informacji o działaniach na maszynach wirtualnych. Dane replikacji są wymieniane między funkcją hypervisor programu VMware a usługą Azure Storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.

Site Recovery jest certyfikowany dla ISO 27001:2013 i 27018, HIPAA i DPA. Jest to proces oceny SOC2 i FedRAMP JAB.

## <a name="pricing"></a>Ceny

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Jak mogę obliczyć przybliżone opłaty za odzyskiwanie po awarii oprogramowania VMware?

Za pomocą [kalkulatora cen](https://aka.ms/asr_pricing_calculator) można oszacować koszty przy użyciu Site Recovery.

Aby uzyskać szczegółowe oszacowanie kosztów, uruchom narzędzie planista wdrażania dla oprogramowania [VMware](./site-recovery-deployment-planner.md) i Użyj [raportu szacowania kosztów](./site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Czy istnieje różnica w kosztach między replikacją do magazynu a usługą Managed disks bezpośrednio?

Usługi Managed disks są rozliczone nieco inaczej niż w przypadku kont magazynu. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/managed-disks/) o cenach dysków zarządzanych.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Czy w przypadku replikowania do konta magazynu Ogólnego przeznaczenia v2 występuje różnica w kosztach?

Zwykle zostanie wyświetlony wzrost kosztu transakcji ponoszonych na konta magazynu GPv2, ponieważ Azure Site Recovery to transakcje bardzo duże. [Dowiedz się więcej](../storage/common/storage-account-upgrade.md#pricing-and-billing) , aby oszacować zmianę.

## <a name="mobility-service"></a>Usługa mobilności

### <a name="where-can-i-find-the-mobility-service-installers"></a>Gdzie mogę znaleźć instalatorów usługi mobilności?

Instalatory znajdują się w folderze%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na serwerze konfiguracji.

## <a name="how-do-i-install-the-mobility-service"></a>Jak mogę zainstalować usługę mobilności?

Na każdej maszynie wirtualnej, która ma zostać zreplikowana, zainstaluj usługę przy użyciu jednej z kilku metod:

- [Instalacja wypychana](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalacja ręczna](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) z poziomu interfejsu użytkownika lub programu PowerShell
- Wdrażanie przy użyciu narzędzia do wdrażania, takiego jak [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Dyski zarządzane

### <a name="where-does-site-recovery-replicate-data-to"></a>Gdzie Usługa Site Recovery replikuje dane?

Site Recovery replikuje lokalne maszyny wirtualne VMware i serwery fizyczne do dysków zarządzanych na platformie Azure.

- Serwer przetwarzania Site Recovery zapisuje dzienniki replikacji na koncie magazynu pamięci podręcznej w regionie docelowym.
- Te dzienniki są używane do tworzenia punktów odzyskiwania na dyskach zarządzanych przez platformę Azure z prefiksem **asrseeddisk**.
- W przypadku przejścia w tryb failover wybrany punkt odzyskiwania służy do tworzenia nowego docelowego dysku zarządzanego. Ten dysk zarządzany jest podłączony do maszyny wirtualnej na platformie Azure.
- Nie ma to wpływu na maszyny wirtualne, które zostały wcześniej zreplikowane na konto magazynu (przed 2019 marca).

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Czy można replikować nowe maszyny do kont magazynu?

Nie. Począwszy od marca 2019, w Azure Portal można replikować tylko do usługi Azure Managed Disks.

Replikacja nowych maszyn wirtualnych do konta magazynu jest dostępna tylko przy użyciu programu PowerShell ([AZ. RecoveryServices Module Version 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) lub interfejsu API REST (wersja 2018-01-10 lub 2016-08-10). [Dowiedz się, jak](./vmware-azure-disaster-recovery-powershell.md) skonfigurować replikację za pomocą poleceń programu PowerShell.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Jakie korzyści wiążą się z replikacją do dysków zarządzanych?

[Dowiedz się, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery upraszcza odzyskiwanie po awarii za pomocą dysków zarządzanych.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Czy można zmienić typ dysku zarządzanego po włączeniu ochrony komputera?

Tak. możesz łatwo [zmienić typ dysku zarządzanego](../virtual-machines/windows/convert-disk-storage.md) na potrzeby bieżących replikacji. Przed zmianą typu upewnij się, że na dysku zarządzanym nie zostanie wygenerowany adres URL sygnatury dostępu współdzielonego:

1. Przejdź do zasobu **dysk zarządzany** na Azure Portal i sprawdź, czy masz transparent adresu URL sygnatury dostępu współdzielonego w bloku **Przegląd** .
1. Jeśli transparent jest obecny, wybierz go, aby anulować trwający eksport.
1. Zmień typ dysku w ciągu następnych kilku minut. Jeśli zmienisz typ zarządzanego dysku, poczekaj na wygenerowanie nowych punktów odzyskiwania przez Azure Site Recovery.
1. Użyj nowych punktów odzyskiwania dla dowolnego testowego trybu failover lub przejścia w tryb failover w przyszłości.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Czy można przełączyć replikację z dysków zarządzanych na dyski niezarządzane?

Nie. Przełączenie z zarządzanego do niezarządzanego nie jest obsługiwane.

## <a name="replication"></a>Replikacja

### <a name="what-are-the-replicated-vm-requirements"></a>Jakie są wymagania dotyczące replikowanych maszyn wirtualnych?

[Dowiedz się więcej](vmware-physical-azure-support-matrix.md#replicated-machines) o wymaganiach dotyczących obsługi maszyn wirtualnych VMware i serwerów fizycznych.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często mogę przeprowadzić replikację do platformy Azure?

Replikacja jest ciągła w przypadku replikowania maszyn wirtualnych VMware na platformę Azure.

### <a name="can-i-extend-replication"></a>Czy mogę zwiększyć replikację?

Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądaj tej funkcji na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="how-can-i-track-progress-of-initial-replicationsynchronization"></a>Jak mogę śledzić postęp replikacji początkowej/synchronizacji?

Ta funkcja była ostatnio Site Recovery usług. Aby uzyskać dokładne informacje, zaktualizuj infrastrukturę Site Recovery (serwery konfiguracji, serwery przetwarzania skalowalnego w poziomie) i agenta mobilności do wersji 9,36 lub nowszej. Dowiedz się więcej na temat sposobu śledzenia postępu w [tym miejscu](vmware-azure-enable-replication.md#monitor-initial-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Czy można wykonać replikację początkową w trybie offline?

Replikacja w trybie offline nie jest obsługiwana. Zażądaj tej funkcji na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Co to jest asrseeddisk?

Na każdym dysku źródłowym dane są replikowane na dysk zarządzany na platformie Azure. Ten dysk ma prefiks **asrseeddisk**. Przechowuje kopię dysku źródłowego i wszystkie migawki punktów odzyskiwania.

### <a name="can-i-exclude-disks-from-replication"></a>Czy można wykluczać dyski z replikacji?

Tak, możesz wykluczyć dyski.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?

Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Czy w przypadku korzystania z grup replikacji w ramach spójności między MASZYNami wirtualnymi można dodać nową maszynę wirtualną do istniejącej grupy replikacji?

Tak. nowe maszyny wirtualne można dodać do istniejącej grupy replikacji po włączeniu dla nich replikacji. Ale

- Po rozpoczęciu replikacji nie można dodać maszyny wirtualnej do istniejącej grupy replikacji.
- Nie można utworzyć grupy replikacji dla istniejących maszyn wirtualnych.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Czy mogę zmodyfikować maszyny wirtualne, które są replikowane przez dodanie lub zmianę rozmiarów dysków?

W przypadku replikacji oprogramowania VMware na platformę Azure można modyfikować rozmiar dysku źródłowych maszyn wirtualnych. Aby dodać nowe dyski, należy dodać dysk i ponownie włączyć ochronę maszyny wirtualnej.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Czy można przeprowadzić migrację maszyn lokalnych do nowego vCenter Server bez wpływu na trwającą replikację?

Zapoznaj się z naszymi [wskazówkami dotyczącymi](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) migrowania maszyn do nowego programu vCenter

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Czy można przeprowadzić replikację do pamięci podręcznej lub docelowego konta magazynu, dla którego skonfigurowano sieć wirtualną (z zaporami platformy Azure)?

Nie, Site Recovery nie obsługuje replikacji do usługi Azure Storage w sieciach wirtualnych.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Jaka jest częstotliwość generowania punktów odzyskiwania spójnych na poziomie awarii?

Site Recovery generuje punkty odzyskiwania spójne z awarią co 5 minut.

## <a name="component-upgrade"></a>Uaktualnienie składnika

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moja wersja agenta lub serwera konfiguracji usługi mobilności jest stara, a uaktualnienie nie powiodło się. Co mam zrobić?

Site Recovery jest zgodny z modelem wsparcia N-4. [Dowiedz się więcej](./service-updates-how-to.md#support-statement-for-azure-site-recovery) o tym, jak przeprowadzić uaktualnienie ze starszych wersji.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Gdzie można znaleźć informacje o wersji i pakiety zbiorcze aktualizacji dla Azure Site Recovery?

[Dowiedz się więcej o nowych aktualizacjach](site-recovery-whats-new.md)i [Pobierz informacje zbiorcze](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Gdzie mogę znaleźć informacje o uaktualnianiu odzyskiwania po awarii na platformie Azure?

[Dowiedz się więcej na temat uaktualniania](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Czy należy ponownie uruchomić maszyny źródłowe dla każdego uaktualnienia?

Zaleca się ponowne uruchomienie, ale nie jest to wymagane dla każdego uaktualnienia. [Dowiedz się więcej](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>Serwer konfiguracji

### <a name="what-does-the-configuration-server-do"></a>Do czego służy serwer konfiguracji?

Serwer konfiguracji uruchamia składniki Site Recovery lokalnych, w tym:

- Sam serwer konfiguracji. Serwer koordynuje komunikację między składnikami lokalnymi i platformą Azure oraz zarządza replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Ten serwer:
    1. odbiera dane replikacji,
    2. Optymalizuje dane z pamięci podręcznej, kompresji i szyfrowania.
    3. Wysyła dane do usługi Azure Storage.
  Serwer przetwarzania wykonuje także instalację wypychaną usługi mobilności na maszynach wirtualnych i wykonuje automatyczne odnajdowanie lokalnych maszyn wirtualnych VMware.
- Główny serwer docelowy, który obsługuje dane replikacji podczas powrotu po awarii z platformy Azure.

[Dowiedz się więcej](vmware-azure-architecture.md) o składnikach i procesach serwera konfiguracji.

### <a name="where-do-i-set-up-the-configuration-server"></a>Gdzie skonfigurować serwer konfiguracji?

Do serwera konfiguracji potrzebna jest pojedyncza maszyna wirtualna VMware o wysokiej dostępności. W przypadku odzyskiwania po awarii serwera fizycznego Zainstaluj serwer konfiguracji na komputerze fizycznym.

### <a name="what-do-i-need-for-the-configuration-server"></a>Co jest potrzebne dla serwera konfiguracji?

Zapoznaj się z [wymaganiami wstępnymi](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Czy można ręcznie skonfigurować serwer konfiguracji zamiast używać szablonu?

Zalecamy [utworzenie maszyny wirtualnej serwera konfiguracji](vmware-azure-deploy-configuration-server.md) przy użyciu najnowszej wersji szablonu Open Virtualization format (OVF). Jeśli nie możesz użyć szablonu (na przykład jeśli nie masz dostępu do serwera VMware), [Pobierz](physical-azure-set-up-source.md) plik instalacyjny z portalu i skonfiguruj serwer konfiguracji.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Czy serwer konfiguracji może być replikowany do więcej niż jednego regionu?

Nie. Aby przeprowadzić replikację do więcej niż jednego regionu, potrzebny jest serwer konfiguracji w każdym regionie.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Czy mogę hostować serwer konfiguracji na platformie Azure?

Chociaż jest to możliwe, maszyna wirtualna platformy Azure, na której działa serwer konfiguracji, będzie musiała komunikować się z lokalną infrastrukturą VMware i maszynami wirtualnymi. Ta komunikacja dodaje opóźnienia i wpływa na bieżącą replikację.

### <a name="how-do-i-update-the-configuration-server"></a>Jak mogę zaktualizować serwer konfiguracji?

[Dowiedz się](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) , jak zaktualizować serwer konfiguracji.

- Najnowsze informacje o aktualizacji można znaleźć na [stronie aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Najnowszą wersję można pobrać z portalu. Można też pobrać najnowszą wersję serwera konfiguracji bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).
- Jeśli Twoja wersja ma więcej niż cztery wersje starsze niż bieżąca wersja, zapoznaj się z [instrukcjami](./service-updates-how-to.md#support-statement-for-azure-site-recovery) dotyczącymi uaktualniania.

### <a name="should-i-back-up-the-configuration-server"></a>Czy należy utworzyć kopię zapasową serwera konfiguracji?

Zalecamy regularne zaplanowaną kopię zapasową serwera konfiguracji.

- W przypadku pomyślnego powrotu po awarii maszyny wirtualnej w bazie danych serwera konfiguracji musi istnieć niepowodzenie.
- Serwer konfiguracji musi być uruchomiony i być w stanie połączonym.
- [Dowiedz się więcej](vmware-azure-manage-configuration-server.md) o typowych zadaniach zarządzania serwerem konfiguracji.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Czy podczas konfigurowania serwera konfiguracji można ręcznie pobrać i zainstalować program MySQL?

Tak. Pobierz program MySQL i umieść go w folderze C:\Temp\ASRSetup. Następnie zainstaluj ją ręcznie. Po skonfigurowaniu maszyny wirtualnej serwera konfiguracji i zaakceptowaniu postanowień program MySQL zostanie wyświetlony **w obszarze** **Pobierz i zainstaluj**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Czy mogę uniknąć pobierania MySQL, ale Site Recovery go zainstalować?

Tak. Pobierz instalatora MySQL i umieść go w folderze C:\Temp\ASRSetup. Podczas konfigurowania maszyny wirtualnej serwera konfiguracji Zaakceptuj warunki i wybierz pozycję **Pobierz i zainstaluj**. Portal użyje Instalatora, który został dodany w celu zainstalowania programu MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Czy mogę użyć maszyny wirtualnej serwera konfiguracji dla wszystkich innych?

Nie. Użyj maszyny wirtualnej tylko dla serwera konfiguracji.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Czy można sklonować serwer konfiguracji i używać go do organizowania?

Nie. Skonfiguruj nowy serwer konfiguracji, aby uniknąć problemów z rejestracją.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Czy mogę zmienić magazyn, w którym zarejestrowano serwer konfiguracji?

Nie. Po skojarzeniu magazynu z serwerem konfiguracji nie można go zmienić. [Dowiedz się więcej](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) na temat rejestrowania serwera konfiguracji z innym magazynem.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Czy można używać tego samego serwera konfiguracji do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych?

Tak, ale należy zauważyć, że maszyna fizyczna może się nie powracać do maszyny wirtualnej VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Gdzie można pobrać hasło dla serwera konfiguracji?

[Dowiedz się](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) , jak pobrać hasło.

### <a name="where-can-i-download-vault-registration-keys"></a>Gdzie można pobrać klucze rejestracji magazynu?

W magazynie Recovery Services wybierz pozycję **serwery konfiguracji** w obszarze **Site Recovery zarządzanie infrastrukturą**  >  . Następnie w obszarze **serwery** wybierz pozycję **Pobierz klucz rejestracji** , aby pobrać plik poświadczeń magazynu.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Czy można użyć jednego serwera konfiguracji do ochrony wielu wystąpień programu vCenter?

Tak, jeden serwer konfiguracji może chronić maszyny wirtualne w wielu serwerach vCenter.  Nie ma limitu liczby wystąpień programu vCenter do dodania do serwera konfiguracji, jednak limity liczby maszyn wirtualnych, które mogą być chronione przez pojedynczy serwer konfiguracji.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Czy jeden serwer konfiguracji chroni wiele klastrów w programie vCenter?

Tak, Azure Site Recovery może chronić maszyny wirtualne w różnych klastrach.

## <a name="process-server"></a>Serwer przetwarzania

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Dlaczego nie mogę wybrać serwera przetwarzania po włączeniu replikacji?

Aktualizacje w wersji 9,24 i nowszych teraz wyświetlają [kondycję serwera przetwarzania po włączeniu replikacji](vmware-azure-enable-replication.md#enable-replication). Ta funkcja pomaga uniknąć ograniczania przepływności serwera i zminimalizować użycie serwerów przetwarzania w złej kondycji.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Jak mogę zaktualizować serwer przetwarzania do wersji 9,24 lub nowszej w celu uzyskania dokładnych informacji o kondycji?

Począwszy od [wersji 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)dodano więcej alertów w celu wskazania kondycji serwera przetwarzania. [Zaktualizuj składniki Site Recovery do wersji 9,24 lub nowszej](service-updates-how-to.md#links-to-currently-supported-update-rollups) , aby generować wszystkie alerty.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Jak zapewnić wysoką dostępność serwera przetwarzania?

Konfigurując więcej niż jeden serwer przetwarzania, projekt zapewnia elastyczność przenoszenia chronionych maszyn z serwera przetwarzania w złej kondycji na serwer przetwarzania. Przenoszenie komputera z jednego serwera przetwarzania do innego musi być inicjowane jawnie lub ręcznie poprzez zdefiniowane tutaj kroki: [przenoszenie maszyn wirtualnych między serwerami przetwarzania](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Przechodzenie w tryb failover i powrót po awarii

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Czy można użyć lokalnego serwera przetwarzania do powrotu po awarii?

Zdecydowanie zalecamy utworzenie serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii, aby uniknąć opóźnień transferu danych. Ponadto w przypadku rozdzielenia źródłowej sieci maszyn wirtualnych za pomocą sieci platformy Azure połączonej z siecią na serwerze konfiguracji należy użyć serwera przetwarzania utworzonego na platformie Azure do powrotu po awarii.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Czy mogę zachować adres IP w trybie failover?

Tak, adres IP można zachować w trybie failover. Przed przejściem do trybu failover upewnij się, że określisz docelowy adres IP w ustawieniach **obliczeń i sieci** maszyny wirtualnej. Ponadto Zamknij maszyny w czasie pracy awaryjnej, aby uniknąć konfliktów adresów IP podczas powrotu po awarii.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Czy mogę zmienić docelowy rozmiar maszyny wirtualnej lub typ maszyny wirtualnej przed przejściem w tryb failover?

Tak, możesz w dowolnym momencie zmienić typ lub rozmiar maszyny wirtualnej przed przejściem w tryb failover. W portalu Użyj ustawień **obliczenia i sieć** dla ZREPLIKOWANEJ maszyny wirtualnej.

### <a name="how-far-back-can-i-recover"></a>Jak daleko z powrotem mogę odzyskać?

W przypadku oprogramowania VMware na platformę Azure najstarszym punktem odzyskiwania, którego możesz użyć, jest 72 godzin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak mogę dostęp do maszyn wirtualnych platformy Azure po przejściu w tryb failover?

Po przejściu w tryb failover możesz uzyskiwać dostęp do maszyn wirtualnych platformy Azure za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN typu lokacja-lokacja lub za pośrednictwem usługi Azure ExpressRoute. Aby nawiązać połączenie, należy przygotować kilka rzeczy. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Czy dane są odporne na awarie?

Platforma Azure została zaprojektowana z myślą o odporności danych. Site Recovery został zaprojektowany w celu przełączenia w tryb failover do pomocniczego centrum danych platformy Azure zgodnie z umową dotyczącą poziomu usług (SLA) platformy Azure. W przypadku przejścia w tryb failover upewnijmy się, że Twoje metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla Twojego magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Aby rozpocząć pracę w trybie failover, możesz wybrać pojedynczy wybór w portalu lub użyć [programu PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia trybu failover.

### <a name="can-i-fail-back-to-a-different-location"></a>Czy mogę powrócić po awarii do innej lokalizacji?

Tak. W przypadku przełączenia w tryb failover na platformę Azure można wrócić do innej lokalizacji, jeśli jest ona niedostępna. [Dowiedz się więcej](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Dlaczego do powrotu po awarii jest potrzebna sieć VPN lub ExpressRoute z prywatną usługą komunikacji równorzędnej?

Po powrocie po awarii z platformy Azure dane z platformy Azure są kopiowane z powrotem do lokalnej maszyny wirtualnej, a dostęp prywatny jest wymagany.


## <a name="automation-and-scripting"></a>Automatyzacja i obsługa skryptów

### <a name="can-i-set-up-replication-with-scripting"></a>Czy można skonfigurować replikację za pomocą skryptów?

Tak. Przepływy pracy Site Recovery można zautomatyzować za pomocą interfejsu API REST, programu PowerShell lub zestawu Azure SDK. [Dowiedz się więcej](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Wydajność i pojemność

### <a name="can-i-throttle-replication-bandwidth"></a>Czy mogę ograniczyć przepustowość replikacji?

Tak. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj](vmware-physical-azure-support-matrix.md) wymagania dotyczące pomocy technicznej.
- [Konfiguracja](vmware-azure-tutorial.md) Replikacja z programu VMware do platformy Azure.