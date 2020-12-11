---
title: Migrowanie maszyny wirtualnej VMware bez agenta Azure Migrate migracji serwera
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware bez wykorzystania agentów przy użyciu Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 56a350f886229cfb5479b5adae52a9c871f5bb65
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032341"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (bez wykorzystania agentów)

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure przy użyciu narzędzia do [migracji Azure Migrate: serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) z migracją bez agenta. Można również migrować maszyny wirtualne VMware przy użyciu migracji opartej na agentach. [PORÓWNAJ](server-migrate-overview.md#compare-migration-methods) metody.

Ten samouczek jest trzecią częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure. 

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. 


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj narzędzie migracji platformy Azure: serwer.
> * Odnajdź maszyny wirtualne, które chcesz zmigrować.
> * Rozpocznij replikację maszyn wirtualnych.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Wykonaj pierwszy samouczek,](./tutorial-discover-vmware.md) aby przygotować platformę Azure i oprogramowanie VMware do migracji.
2. Zalecamy wykonanie drugiego samouczka w celu [oceny maszyn wirtualnych VMware](./tutorial-assess-vmware-azure-vm.md) przed przeprowadzeniem migracji na platformę Azure, ale nie jest to konieczne. 
3. Przejdź do już utworzonego projektu lub [Utwórz nowy projekt](./create-manage-projects.md)
4. Sprawdź uprawnienia do konta platformy Azure — Twoje konto platformy Azure wymaga uprawnień do utworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia Azure Migrate

Migracja serwera Azure Migrate uruchamia lekkim urządzeniem maszyny wirtualnej VMware, które służy do odnajdywania, oceny i migracji bez wykorzystania agentów maszyn wirtualnych VMware. Jeśli korzystasz z [samouczka oceny](./tutorial-assess-vmware-azure-vm.md), urządzenie zostało już skonfigurowane. Jeśli nie, skonfiguruj ją teraz, korzystając z jednej z następujących metod:

- **Szablon komórki jajowe**: [Konfiguracja](how-to-set-up-appliance-vmware.md) na maszynie wirtualnej VMware przy użyciu pobranego szablonu komórki jajowe.
- **Skrypt**: [Konfiguracja](deploy-appliance-script.md) na maszynie wirtualnej VMware lub na komputerze fizycznym przy użyciu skryptu Instalatora programu PowerShell. Tej metody należy użyć, jeśli nie można skonfigurować maszyny wirtualnej przy użyciu szablonu komórki jajowe lub jeśli jesteś w Azure Government.

Po utworzeniu urządzenia sprawdź, czy może nawiązać połączenie z Azure Migrate: Ocena serwera, skonfiguruj ją po raz pierwszy i zarejestruj ją za pomocą projektu Azure Migrate.

## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Po skonfigurowaniu urządzenia i zakończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych VMware na platformę Azure. 

- Jednocześnie można uruchomić maksymalnie 300 replikacji.
- W portalu można wybrać maksymalnie 10 maszyn wirtualnych na potrzeby migracji. Aby migrować więcej maszyn, należy dodać je do grup w partiach 10.

Aby włączyć replikację:

1. Na **serwerach** Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W obszarze **Urządzenie lokalne** wybierz nazwę skonfigurowanego urządzenia usługi Azure Migrate > przycisk **OK**. 

    ![Ustawienia źródła](./media/tutorial-migrate-vmware/source-settings.png)

4. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane. Aby zastosować rozmiar maszyny wirtualnej i typ dysku z oceny, jeśli został on uruchomiony, w obszarze **Importuj ustawienia migracji z oceny Azure Migrate?** wybierz opcję **tak**, a następnie wybierz grupę maszyn wirtualnych i nazwę oceny. Jeśli nie używasz ustawień oceny, wybierz pozycję **nie**.
   
    ![Wybieranie oceny](./media/tutorial-migrate-vmware/select-assessment.png)

5. W obszarze **maszyny wirtualne** Wybierz Maszyny wirtualne, które chcesz zmigrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-vms.png)

6. W obszarze **ustawienia docelowe** wybierz pozycję subskrypcja i region docelowy. Określ grupę zasobów, w której znajdują się maszyny wirtualne platformy Azure po migracji.
7. W **Virtual Network** wybierz sieć wirtualną lub podsieć platformy Azure, do której będą dołączane maszyny wirtualne platformy Azure po migracji.
8. W obszarze **Opcje dostępności** wybierz pozycję:
    -  Strefa dostępności służąca do przypinania zmigrowanej maszyny do określonej strefy dostępności w regionie. Użyj tej opcji, aby dystrybuować serwery tworzące wielowęzłową warstwę aplikacji między Strefy dostępności. W przypadku wybrania tej opcji należy określić strefę dostępności, która ma być używana dla każdej z wybranych maszyn na karcie obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności umożliwiający umieszczenie zmigrowanej maszyny w zestawie dostępności. Docelowa Grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Nie jest wymagana opcja nadmiarowości infrastruktury, jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla zmigrowanych maszyn.

9. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

10. W obszarze **obliczenia** w obszarze obliczenia Sprawdź nazwę maszyny wirtualnej, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności (w przypadku wybrania w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: Jeśli korzystasz z zaleceń dotyczących oceny, rozmiar maszyny wirtualnej zostanie wyświetlony na liście rozwijanej rozmiar zalecane. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Strefa dostępności**: Określ strefę dostępności, która ma zostać użyta.
    - **Zestaw dostępności**: Określ zestaw dostępności, który ma być używany.

> [!NOTE]
>Jeśli chcesz wybrać inną opcję dostępności dla zestawów maszyn wirtualnych, przejdź do kroku 1 i powtórz kroki, wybierając różne opcje dostępności po uruchomieniu replikacji dla jednego zestawu maszyn wirtualnych.


 ![Ustawienia obliczeniowe maszyny wirtualnej](./media/tutorial-migrate-vmware/compute-settings.png)

11. W obszarze **Dyski** określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowe dyski SSD/dyski twarde lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
   
    ![Zrzut ekranu przedstawia kartę dyski okna dialogowego replikacja.](./media/tutorial-migrate-vmware/disks.png)

12. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji (**Zarządzanie**  >  **maszynami replikowanymi**). Po uruchomieniu replikacji nie można zmienić ustawień.

### <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, która jest replikowana w projekcie, Migracja serwera automatycznie udostępnia te zasoby w tej samej grupie zasobów co projekt.

- **Service Bus**: Migracja serwera używa usługi Service Bus do wysyłania komunikatów aranżacji replikacji do urządzenia.
- **Konto magazynu bramy**: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników**: urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych repliki.
- **Magazyn kluczy**: urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji.

## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

1. Śledź stan zadania w powiadomieniach portalu.
2. Monitoruj stan replikacji, klikając opcję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.

     ![Monitorowanie replikacji](./media/tutorial-migrate-vmware/replicating-servers.png)

Replikacja odbywa się w następujący sposób:
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej. Dane dysku z migawki są replikowane do dysków zarządzanych repliki na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze serwery **celów migracji**  >    >  **Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W Azure Migrate serwery > Project   >  **Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie**  >  **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych** wybierz pozycję **tak**  >  **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby zsynchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Gwarantuje to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną, > **zatrzymać replikację**. Spowoduje to zatrzymanie replikacji maszyny lokalnej i oczyszczenie informacji o stanie replikacji dla maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej z [systemem Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach, jeśli komputer ma system operacyjny Linux. Podczas migracji automatycznie instalujemy agenta maszyny wirtualnej dla maszyn wirtualnych z systemem Windows.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](../security-center/security-center-just-in-time.md).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.