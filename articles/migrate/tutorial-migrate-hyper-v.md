---
title: Migrowanie maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu migracji Azure Migrate Server
description: Dowiedz się, jak przeprowadzić migrację lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu migracji Azure Migrate serwera
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 842513536bf4dbfc0a3e3bd82905b397da9fa6c4
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439073"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrowanie maszyn wirtualnych funkcji Hyper-V na platformę Azure 

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure za pomocą narzędzia do [migracji serwera Azure Migrate:](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Ten samouczek jest trzecią częścią serii, która pokazuje, jak oceniać i migrować maszyny na platformę Azure. 

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

1. [Przejrzyj](hyper-v-migration-architecture.md) architekturę migracji funkcji Hyper-V.
2. [Przegląd](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Wymagania hosta funkcji Hyper-V dotyczące migracji oraz adresy URL platformy Azure, do których hosty i klastry funkcji Hyper-V potrzebują dostępu do migracji maszyn wirtualnych.
3. [Zapoznaj](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) się z wymaganiami dotyczącymi maszyn wirtualnych funkcji Hyper-V, które chcesz zmigrować na platformę Azure.
4. Zalecamy, aby  [oceniać maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md) przed ich migracją na platformę Azure, ale nie musisz.

   
## <a name="add-the-azure-migrateserver-migration-tool"></a>Dodawanie Azure Migrate: Narzędzia migracji serwera

Dodaj Azure Migrate: Narzędzia migracji serwera. Jeśli nie masz jeszcze projektu Azure Migrate [, najpierw należy go utworzyć,](how-to-add-tool-first-time.md) aby skonfigurować Azure Migrate projekt. Podczas tworzenia projektu należy dodać Azure Migrate: Narzędzie migracji serwera.

Jeśli masz skonfigurowany projekt, Dodaj narzędzie w następujący sposób:

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**. 
2. W obszarze **odnajdywanie, ocenianie i**Migrowanie serwerów kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
3. W obszarze **Narzędzia migracji**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do migracji, gdy wszystko jest gotowe do migracji**.

    ![Wybierz narzędzie](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na liście Narzędzia wybierz pozycję **Azure Migrate: serwer**—  >  **Dodawanie narzędzia** do migracji

    ![Narzędzie do migracji serwera](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="download-and-install-the-provider"></a>Pobieranie i Instalowanie dostawcy

W przypadku migrowania maszyn wirtualnych funkcji Hyper-V Azure Migrate: Migracja serwera instaluje dostawców oprogramowania (Microsoft Azure dostawca Site Recovery i Microsoft Azure agenta usługi odzyskiwania) na hostach funkcji Hyper-V lub w węzłach klastra. Należy pamiętać, że [urządzenie Azure Migrate](migrate-appliance.md) nie jest używane do migracji funkcji Hyper-V.

1. Na **serwerach**Azure Migrate Project >, w **Azure Migrate: Migracja serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3. W **obszarze region docelowy**wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
6. Wybierz pozycję **Potwierdź, że docelowy region migracji to nazwa regionu**.
7. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle.
    - Jeśli już skonfigurowano migrację z Azure Migrate migracji do serwera, ta opcja nie zostanie wyświetlona, ponieważ zasoby zostały wcześniej skonfigurowane.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje znajdują się w tym regionie.
    
8. W obszarze **Przygotuj serwery hosta funkcji Hyper-v**Pobierz dostawcę replikacji funkcji Hyper-v i plik klucza rejestracji.
    - Klucz rejestracji jest wymagany do zarejestrowania hosta funkcji Hyper-V z migracją na serwer Azure Migrate.
    - Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobierz dostawcę i klucz](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Skopiuj plik Instalatora dostawcy i plik klucza rejestracji do każdego hosta funkcji Hyper-V (lub węzła klastra) z uruchomionymi maszynami wirtualnymi, które chcesz replikować.
5. Uruchom plik Instalatora dostawcy na każdym hoście, zgodnie z opisem w następnej procedurze.
6. Po zainstalowaniu dostawcy na hostach w obszarze **odnajdywanie maszyn**kliknij pozycję **finalizowanie rejestracji**.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Po zakończeniu rejestracji może upłynąć do 15 minut, dopóki odnalezione maszyny wirtualne nie pojawią się w ramach migracji na serwer Azure Migrate. Jako że maszyny wirtualne są odnajdywane, wzrasta liczba **odnalezionych serwerów** .

![Odnalezione serwery](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Replikowanie maszyn wirtualnych funkcji Hyper-V

Po ukończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych funkcji Hyper-V na platformę Azure.

> [!NOTE]
> Można replikować do 10 maszyn jednocześnie. Jeśli chcesz replikować więcej, a następnie Replikuj je jednocześnie w partiach 10.

1. Na **serwerach**Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.
2. W obszarze **replikacja**> **Ustawienia źródła**  >  **są zwirtualizowane na maszynach?** wybierz opcję **tak, z funkcją Hyper-V**. Następnie kliknij przycisk **Dalej: maszyny wirtualne**.
3. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

        ![Wybieranie oceny](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. W obszarze **Maszyny wirtualne** wyszukaj potrzebne maszyny wirtualne i sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-hyper-v/select-vms.png)

5. W obszarze **ustawienia docelowe**wybierz region docelowy, do którego zostanie przeprowadzona migracja, subskrypcja i Grupa zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
7. W obszarze **konto magazynu replikacji**wybierz konto usługi Azure Storage, w którym będą przechowywane zreplikowane dane na platformie Azure.
8. **Virtual Network**wybierz sieć wirtualną platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
9. W obszarze **Opcje dostępności**wybierz pozycję:
    -  Strefa dostępności służąca do przypinania zmigrowanej maszyny do określonej strefy dostępności w regionie. Użyj tej opcji, aby dystrybuować serwery tworzące wielowęzłową warstwę aplikacji między Strefy dostępności. W przypadku wybrania tej opcji należy określić strefę dostępności, która ma być używana dla każdej z wybranych maszyn na karcie obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności umożliwiający umieszczenie zmigrowanej maszyny w zestawie dostępności. Docelowa Grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Nie jest wymagana opcja nadmiarowości infrastruktury, jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla zmigrowanych maszyn.
10. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-hyper-v/target-settings.png)

11. W obszarze **obliczenia**Sprawdź nazwę maszyny wirtualnej, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności (w przypadku wybrania w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: Jeśli korzystasz z zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeniowe maszyny wirtualnej](./media/tutorial-migrate-hyper-v/compute-settings.png)

12. W obszarze **dyski**Określ dyski maszyn wirtualnych, które mają być replikowane na platformę Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Zrzut ekranu przedstawia kartę dyski okna dialogowego replikacja.](./media/tutorial-migrate-hyper-v/disks.png)

13. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji w obszarze **Zarządzanie**  >  **maszynami replikowanymi**. Ustawień nie można zmienić po rozpoczęciu replikacji.

## <a name="provision-for-the-first-time"></a>Udostępnianie po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, która jest replikowana w projekcie Azure Migrate, Azure Migrate: Migracja serwera automatycznie udostępnia te zasoby w tej samej grupie zasobów co projekt.

- **Service Bus**: Azure Migrate: Migracja serwera używa Service Bus do wysyłania komunikatów aranżacji replikacji do urządzenia.
- **Konto magazynu bramy**: Azure Migrate: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie dotyczących replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników**: urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych przez replikę.
- **Magazyn kluczy**: urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji. Należy skonfigurować uprawnienia wymagane przez Magazyn kluczy do uzyskiwania dostępu do konta magazynu podczas [przygotowywania usługi Azure](tutorial-prepare-hyper-v.md#prepare-azure) do oceny i migracji maszyn wirtualnych funkcji Hyper-V. 


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane na platformę Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej platformy Azure w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. W obszarze **migracja testowa**wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W Azure Migrate serwery > Project **Servers**  >  **Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie**  >  **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych**wybierz pozycję **tak**  >  **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby zsynchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Gwarantuje to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Wyłącza replikację maszyny lokalnej.
    - Usuwa maszynę z liczby **serwerów replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
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
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
