---
title: Przywracanie maszyn wirtualnych przy użyciu Azure Portal
description: Przywróć maszynę wirtualną platformy Azure z punktu odzyskiwania, korzystając z Azure Portal, w tym funkcji przywracania między regionami.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 4be107a1647ac1aa95aea9c1c68e6f024be82b1c
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746409"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Przywracanie danych maszyny wirtualnej platformy Azure w Azure Portal

W tym artykule opisano sposób przywracania danych maszyny wirtualnej platformy Azure z punktów odzyskiwania przechowywanych w magazynach Recovery Services [Azure Backup](backup-overview.md) .

## <a name="restore-options"></a>Opcje przywracania

Usługa Azure Backup umożliwia przywracanie maszyny wirtualnej na kilka sposobów.

**Opcja przywracania** | **Szczegóły**
--- | ---
**Tworzenie nowej maszyny wirtualnej** | Szybko tworzy i pobiera z punktu przywracania skonfigurowaną i uruchomioną podstawową maszynę wirtualną.<br/><br/> Możesz określić nazwę dla maszyny wirtualnej, wybrać grupę zasobów i sieć wirtualną (VNet), w której zostanie umieszczona, a następnie określić konto magazynu dla przywróconej maszyny wirtualnej. Nową maszynę wirtualną należy utworzyć w tym samym regionie co źródłową maszynę wirtualną.<br><br>Jeśli przywracanie maszyny wirtualnej nie powiedzie się, ponieważ jednostka SKU maszyny wirtualnej platformy Azure nie była dostępna w określonym regionie platformy Azure lub z powodu innych problemów, Azure Backup nadal przywraca dyski w określonej grupie zasobów.
**Przywracanie dysku** | Przywraca dysk maszyny wirtualnej, za pomocą którego można następnie utworzyć nową maszynę wirtualną.<br/><br/> Usługa Azure Backup udostępnia szablon ułatwiający dostosowanie i utworzenie maszyny wirtualnej. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć, aby określić niestandardowe ustawienia maszyny wirtualnej i utworzyć maszynę wirtualną.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie możesz dołączyć dysk do istniejącej maszyny wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, których nie było w czasie tworzenia kopii zapasowej, lub dodać ustawienia, które należy skonfigurować za pomocą szablonu lub programu PowerShell.
**Zamiana istniejącego** | Można przywrócić dysk i użyć go do zamienienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Wymaga to istnienia bieżącej maszyny wirtualnej. Jeśli została ona usunięta, nie można użyć tej opcji.<br/><br/> Azure Backup tworzy migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku i zapisuje ją w określonej lokalizacji przemieszczania. Istniejące dyski połączone z maszyną wirtualną są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po zakończeniu operacji Zamień dysk oryginalny dysk jest zachowywany w grupie zasobów. Możesz wybrać opcję ręcznego usuwania dysków oryginalnych, jeśli nie są one potrzebne. <br/><br/>Polecenie replace exist jest obsługiwane w przypadku nieszyfrowanych zarządzanych maszyn wirtualnych, w tym maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Nie jest to obsługiwane w przypadku klasycznych maszyn wirtualnych.<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie uwzględniać tylko konfigurację maszyny wirtualnej.<br><br> Zastąp istniejący jest również obsługiwany w przypadku maszyn wirtualnych z połączonymi zasobami, takimi jak [zarządzana tożsamość przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md) lub [Key Vault](../key-vault/general/overview.md).
**Między regionami (region pomocniczy)** | W celu przywrócenia maszyn wirtualnych platformy Azure w regionie pomocniczym, które jest [sparowanym regionem platformy Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions), można użyć funkcji przywracania między regionami.<br><br> Jeśli kopia zapasowa jest wykonywana w regionie pomocniczym, można przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania.<br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> <li> [Tworzenie maszyny wirtualnej](#create-a-vm) <br> <li> [Przywracanie dysków](#restore-disks) <br><br> Nie obsługujemy obecnie opcji [Zamień istniejące dyski](#replace-existing-disks) .<br><br> Uprawnienia<br> Operacja przywracania w regionie pomocniczym może być wykonywana przez administratorów kopii zapasowych i administratorów aplikacji.

> [!NOTE]
> Można także odzyskać określone pliki i foldery na maszynie wirtualnej platformy Azure. [Dowiedz się więcej](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Konta magazynu

Niektóre szczegóły dotyczące kont magazynu:

- **Tworzenie maszyny wirtualnej**: podczas tworzenia nowej maszyny wirtualnej maszyna wirtualna zostanie umieszczona na określonym koncie magazynu.
- **Przywróć dysk**: podczas przywracania dysku jest on kopiowany na określone konto magazynu. Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny wirtualnej. Ten szablon zostanie umieszczony na określonym koncie magazynu.
- **Zastąp dysk**: podczas wymiany dysku na istniejącej maszynie wirtualnej Azure Backup tworzenia migawki istniejącej maszyny wirtualnej przed zastąpieniem dysku. Migawka jest również kopiowana do magazynu Recovery Services przez transfer danych jako proces w tle. Jednak po zakończeniu fazy migawki zostanie wyzwolona operacja zastąp dyski. Po zakończeniu operacji Zamień dysk dyski źródłowej maszyny wirtualnej platformy Azure zostaną pozostawione w określonej grupie zasobów dla operacji, a wirtualne dyski twarde są przechowywane na określonym koncie magazynu. Możesz wybrać opcję usuwania lub zachowywania tych wirtualnych dysków twardych i dysku.
- **Lokalizacja konta magazynu**: konto magazynu musi znajdować się w tym samym regionie co magazyn. Wyświetlane są tylko te konta. Jeśli w lokalizacji nie ma żadnych kont magazynu, należy ją utworzyć.
- **Typ magazynu**: Magazyn obiektów BLOB nie jest obsługiwany.
- **Nadmiarowość magazynu**: Magazyn strefowo nadmiarowy (ZRS) nie jest obsługiwany. Informacje o replikacji i nadmiarowości dla konta są wyświetlane w nawiasach po nazwie konta.
- Usługa **Premium Storage**:
  - Podczas przywracania maszyn wirtualnych z systemem innym niż Premium konta magazynu w warstwie Premium nie są obsługiwane.
  - Podczas przywracania zarządzanych maszyn wirtualnych konta magazynu w warstwie Premium skonfigurowane z regułami sieci nie są obsługiwane.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby przywrócić maszynę wirtualną (utworzyć nową maszynę wirtualną), upewnij się, że masz odpowiednie [uprawnienia](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) kontroli dostępu opartej na rolach (Azure RBAC) dla operacji przywracania maszyny wirtualnej.

Jeśli nie masz uprawnień, możesz [przywrócić dysk](#restore-disks), a następnie po przywróceniu dysku można [użyć szablonu](#use-templates-to-customize-a-restored-vm) , który został wygenerowany w ramach operacji przywracania, aby utworzyć nową maszynę wirtualną.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Wybierz punkt przywracania

1. W magazynie skojarzonym z maszyną wirtualną, którą chcesz przywrócić, wybierz pozycję **elementy kopii zapasowej**  >  **Azure Virtual Machine**.
1. Wybierz maszynę wirtualną. Domyślnie na pulpicie nawigacyjnym maszyny wirtualnej są wyświetlane punkty odzyskiwania z ostatnich 30 dni. Możesz wyświetlić punkty odzyskiwania starsze niż 30 dni lub przefiltrować, aby znaleźć punkty odzyskiwania na podstawie dat, zakresów czasu i różnych typów spójności migawek.
1. Aby przywrócić maszynę wirtualną, wybierz pozycję **Przywróć maszynę wirtualną**.

    ![Punkt przywracania](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Wybierz punkt przywracania do użycia podczas odzyskiwania.

## <a name="choose-a-vm-restore-configuration"></a>Wybieranie konfiguracji przywracania maszyny wirtualnej

1. W obszarze **przywracanie maszyny wirtualnej** wybierz opcję przywracania:
    - **Utwórz nową**: Użyj tej opcji, jeśli chcesz utworzyć nową maszynę wirtualną. Można utworzyć maszynę wirtualną z prostymi ustawieniami lub przywrócić dysk i utworzyć dostosowaną maszynę wirtualną.
    - **Zastąp istniejące**: Użyj tej opcji, jeśli chcesz zastąpić dyski na istniejącej maszynie wirtualnej.

        ![Kreator przywracania konfiguracji maszyny wirtualnej](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Określ ustawienia wybranej opcji przywracania.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Jako jedna z [opcji przywracania](#restore-options)można szybko utworzyć maszynę wirtualną z ustawieniami podstawowymi z punktu przywracania.

1. W obszarze **Przywróć maszynę wirtualną**  >  **Utwórz nowy**  >  **Typ przywracania**, a następnie wybierz pozycję **Utwórz maszynę wirtualną**.
1. W polu **Nazwa maszyny wirtualnej** Określ maszynę wirtualną, która nie istnieje w subskrypcji.
1. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów dla nowej maszyny wirtualnej lub Utwórz nową z globalnie unikatową nazwą. Jeśli przypiszesz już istniejącą nazwę, platforma Azure przypisze grupę o tej samej nazwie co maszyna wirtualna.
1. W obszarze **Sieć wirtualna** wybierz sieć wirtualną, w której zostanie umieszczona maszyna wirtualna. Zostanie wyświetlona cała sieci wirtualnych skojarzona z subskrypcją. Wybierz podsieć. Pierwsza podsieć jest domyślnie zaznaczona.
1. W obszarze **Lokalizacja tymczasowa** Określ konto magazynu dla maszyny wirtualnej. [Dowiedz się więcej](#storage-accounts).

    ![Kreator przywracania konfiguracji — Wybieranie opcji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Wybierz pozycję **Przywróć** , aby wyzwolić operację przywracania.

## <a name="restore-disks"></a>Przywróć dyski

Jako jedną z [opcji przywracania](#restore-options)można utworzyć dysk z punktu przywracania. Następnie przy użyciu dysku można wykonać jedną z następujących czynności:

- Użyj szablonu wygenerowanego podczas operacji przywracania, aby dostosować ustawienia i wyzwolić wdrożenie maszyny wirtualnej. Można edytować domyślne ustawienia szablonu i przesłać szablon do wdrożenia maszyny wirtualnej.
- [Dołącz przywrócone dyski](../virtual-machines/windows/attach-managed-disk-portal.md) do istniejącej maszyny wirtualnej.
- [Utwórz nową maszynę wirtualną](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) na podstawie przywróconych dysków przy użyciu programu PowerShell.

1. W obszarze **Przywróć konfigurację**  >  **Utwórz nowy**  >  **Typ przywracania**, wybierz pozycję **Przywróć dyski**.
1. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów dla przywróconych dysków lub Utwórz nową z globalnie unikatową nazwą.
1. W obszarze **Lokalizacja tymczasowa** Określ konto magazynu, do którego mają zostać skopiowane wirtualne dyski twarde. [Dowiedz się więcej](#storage-accounts).

    ![Wybierz grupę zasobów i lokalizację przejściową](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Wybierz pozycję **Przywróć** , aby wyzwolić operację przywracania.

Jeśli maszyna wirtualna korzysta z dysków zarządzanych i wybierana jest opcja **Utwórz maszynę wirtualną** , Azure Backup nie używa określonego konta magazynu. W przypadku **przywracania dysków** i **natychmiastowego przywracania** konto magazynu jest używane tylko do przechowywania szablonu. Dyski zarządzane są tworzone w określonej grupie zasobów.
Gdy maszyna wirtualna używa dysków niezarządzanych, są one przywracane jako obiekty blob do konta magazynu.

### <a name="use-templates-to-customize-a-restored-vm"></a>Dostosowywanie przywróconej maszyny wirtualnej przy użyciu szablonów

Po przywróceniu dysku Użyj szablonu, który został wygenerowany w ramach operacji przywracania, aby dostosować i utworzyć nową maszynę wirtualną:

1. W obszarze **zadania tworzenia kopii zapasowej** wybierz odpowiednie zadanie przywracania.

1. W obszarze **przywracanie** wybierz pozycję **Wdróż szablon** , aby zainicjować wdrożenie szablonu.

    ![Przechodzenie do szczegółów zadania przywracania](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Aby dostosować ustawienie maszyny wirtualnej w szablonie, wybierz pozycję **Edytuj szablon**. Jeśli chcesz dodać więcej dostosowań, wybierz pozycję **Edytuj parametry**.
    - [Dowiedz się więcej](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o wdrażaniu zasobów na podstawie szablonu niestandardowego.
    - [Dowiedz się więcej](../azure-resource-manager/templates/template-syntax.md) o tworzeniu szablonów.

   ![Załaduj wdrożenie szablonu](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Wprowadź wartości niestandardowe dla maszyny wirtualnej, zaakceptuj **warunki i postanowienia** , a następnie wybierz pozycję **Kup**.

   ![Prześlij wdrożenie szablonu](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Zastąp istniejące dyski

Jako jedną z [opcji przywracania](#restore-options)można zastąpić istniejący dysk maszyny wirtualnej wybranym punktem przywracania. [Przejrzyj](#restore-options) wszystkie opcje przywracania.

1. W obszarze **Konfiguracja przywracania** wybierz pozycję **Zamień istniejące**.
1. W obszarze **Typ przywracania** wybierz pozycję **Zamień dysk/s**. Jest to punkt przywracania używany do zastępowania istniejących dysków maszyny wirtualnej.
1. W obszarze **Lokalizacja tymczasowa** Określ, gdzie migawki bieżących dysków zarządzanych mają być zapisywane podczas procesu przywracania. [Dowiedz się więcej](#storage-accounts).

   ![Kreator przywracania konfiguracji Zastąp istniejący](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Przywracanie między regionami

Jako jedna z [opcji przywracania](#restore-options), przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure.

Aby dołączyć do funkcji w wersji zapoznawczej, zapoznaj się z [sekcją przed rozpoczęciem](./backup-create-rs-vault.md#set-cross-region-restore).

Aby sprawdzić, czy CRR jest włączona, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie przywracania między regionami](backup-create-rs-vault.md#configure-cross-region-restore).

### <a name="view-backup-items-in-secondary-region"></a>Wyświetlanie elementów kopii zapasowej w regionie pomocniczym

Jeśli CRR jest włączona, można wyświetlić elementy kopii zapasowej w regionie pomocniczym.

1. W portalu przejdź do pozycji **Recovery Services**  >  **elementy kopii zapasowej** magazynu.
1. Wybierz **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

>[!NOTE]
>Na liście zostaną wyświetlone tylko typy zarządzania kopiami zapasowymi obsługujące funkcję CRR. Obecnie dozwolony jest tylko program obsługujący przywracanie danych regionu pomocniczego do regionu pomocniczego.

![Maszyny wirtualne w regionie pomocniczym](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Wybierz region pomocniczy](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Przywróć w regionie pomocniczym

Środowisko użytkownika do przywracania regionu pomocniczego będzie podobne do środowiska użytkownika w regionie podstawowym. Podczas konfigurowania szczegółów w okienku Przywracanie konfiguracji w celu skonfigurowania przywracania zostanie wyświetlony monit o podanie tylko parametrów regionu pomocniczego.

![Wybierz maszynę wirtualną do przywrócenia](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Wybierz punkt przywracania](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Przywróć konfigurację](./media/backup-azure-arm-restore-vms/rest-config.png)

![Powiadomienie o wyzwoleniu przywracania w toku](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Aby przywrócić i utworzyć maszynę wirtualną, zapoznaj się z tematem [Tworzenie maszyny wirtualnej](#create-a-vm).
- Aby przywrócić jako dysk, zapoznaj się z tematem [przywracanie dysków](#restore-disks).

>[!NOTE]
>
>- Po wyzwoleniu przywracania i fazie transferu danych nie można anulować zadania przywracania.
>- Funkcja przywracania między regionami przywraca maszyny wirtualne platformy Azure, których kopie zapasowe są włączone przy użyciu CMK (klucze zarządzane przez klienta), które nie są tworzone w magazynie Recovery Services, jako maszyny wirtualne z włączonym systemem innym niż CMK
>- Role platformy Azure, które muszą zostać przywrócone w regionie pomocniczym, są takie same jak w regionie podstawowym.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorowanie zadań przywracania regionu pomocniczego

1. W portalu przejdź do obszaru **Recovery Services**  >  **zadania tworzenia kopii zapasowej** magazynu
1. Wybierz **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

    ![Odfiltrowane zadania tworzenia kopii zapasowej](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Przywracanie niezarządzanych maszyn wirtualnych i dysków jako zarządzanych

Jest dostępna opcja przywracania [dysków niezarządzanych](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) jako [dysków zarządzanych](../virtual-machines/managed-disks-overview.md) podczas przywracania. Domyślnie niezarządzane maszyny wirtualne/dyski są przywracane jako niezarządzane maszyny wirtualne/dyski. Jeśli jednak zdecydujesz się na przywrócenie zarządzanych maszyn wirtualnych/dysków, możesz to zrobić. Te przywrócenia nie są wyzwalane z fazy migawki, ale tylko z fazy magazynu. Ta funkcja jest niedostępna dla niezarządzanych szyfrowanych maszyn wirtualnych.

![Przywróć jako dyski zarządzane](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Przywracanie maszyn wirtualnych z konfiguracjami specjalnymi

Istnieje kilka typowych scenariuszy, w których może być konieczne przywrócenie maszyn wirtualnych.

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie maszyn wirtualnych przy użyciu korzyści z używania hybrydowego** | Jeśli maszyna wirtualna z systemem Windows wykorzystuje [Licencjonowanie korzyści z używania hybrydowego](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Przywróć dyski i Utwórz nową maszynę wirtualną przy użyciu podanego szablonu (z **typem licencji** ustawionym na **Windows_Server**) lub PowerShell.  To ustawienie można również zastosować po utworzeniu maszyny wirtualnej.
**Przywracanie maszyn wirtualnych w trakcie awarii centrum danych platformy Azure** | Jeśli magazyn używa GRS i podstawowego centrum danych dla maszyny wirtualnej, Azure Backup obsługuje przywracanie kopii zapasowych maszyn wirtualnych do sparowanego centrum danych. W sparowanym centrum danych wybierz konto magazynu i przywróć je jako normalne. Azure Backup używa usługi obliczeniowej w sparowanym regionie do utworzenia przywróconej maszyny wirtualnej. [Dowiedz się więcej](/azure/architecture/resiliency/recovery-loss-azure-region) o odporności centrum danych.<br><br> Jeśli magazyn używa GRS, można wybrać nową funkcję, [przywracanie między regionami](#cross-region-restore). Pozwala to na przywrócenie do drugiego regionu w ramach pełnych lub częściowych scenariuszy przestojów, a nawet w przypadku braku przestojów.
**Przywracanie bez systemu operacyjnego** | Główna różnica między maszynami wirtualnymi platformy Azure i lokalnymi funkcjami hypervisor polega na tym, że żadna konsola maszyny wirtualnej nie jest dostępna na platformie Azure. Konsola programu jest wymagana w niektórych scenariuszach, takich jak odzyskiwanie przy użyciu odzyskiwania bez systemu operacyjnego (BMR). Jednak przywracanie maszyny wirtualnej z magazynu jest pełnym zamiennikiem BMR.
**Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami sieci** | Specjalne konfiguracje sieci obejmują maszyny wirtualne korzystające z wewnętrznego lub zewnętrznego równoważenia obciążenia, przy użyciu wielu kart sieciowych lub wielu zarezerwowanych adresów IP. Te maszyny wirtualne można przywrócić przy użyciu [opcji Przywróć dysk](#restore-disks). Ta opcja powoduje utworzenie kopii dysków VHD na określonym koncie magazynu, a następnie można utworzyć maszynę wirtualną z [wewnętrznym](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) lub [zewnętrznym](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) modułem równoważenia obciążenia, [wieloma](../virtual-machines/windows/multiple-nics.md)kartami sieciowymi lub [wieloma zastrzeżonymi adresami IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), zgodnie z konfiguracją.
**Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) na karcie sieciowej/podsieci** | Kopia zapasowa maszyny wirtualnej platformy Azure obsługuje tworzenie kopii zapasowych i przywracanie informacji sieciowej grupy zabezpieczeń na poziomie sieci wirtualnej, podsieci i karty sieciowej.
**Przypięte strefy maszyny wirtualne** | W przypadku tworzenia kopii zapasowej maszyny wirtualnej platformy Azure, która jest przypięta do strefy (z Azure Backup), można przywrócić ją w tej samej strefie, w której został przypięty. [Dowiedz się więcej](../availability-zones/az-overview.md)
**Przywróć maszynę wirtualną w dowolnym zestawie dostępności** | W przypadku przywracania maszyny wirtualnej z portalu nie jest dostępna opcja wyboru zestawu dostępności. Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji Przywróć dysk, możesz [określić zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny wirtualnej na podstawie dysku przy użyciu podanego szablonu lub programu PowerShell.
**Przywracanie specjalnych maszyn wirtualnych, takich jak maszyny wirtualne SQL** | Jeśli tworzysz kopię zapasową maszyny wirtualnej SQL przy użyciu kopii zapasowej maszyny wirtualnej platformy Azure, a następnie użyjesz opcji Przywróć maszynę wirtualną lub utworzysz maszynę wirtualną po przywróceniu dysków, nowo utworzona maszyna wirtualna musi być zarejestrowana u dostawcy SQL, jak wspomniano [tutaj](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash). Spowoduje to przekonwertowanie przywróconej maszyny wirtualnej na maszynę wirtualną SQL.

### <a name="restore-domain-controller-vms"></a>Przywracanie maszyn wirtualnych kontrolera domeny

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie pojedynczej maszyny wirtualnej kontrolera domeny w jednej domenie** | Przywróć maszynę wirtualną podobnie jak jakakolwiek inna maszyna wirtualna. Należy pamiętać, że:<br/><br/> Z perspektywy Active Directory maszyna wirtualna platformy Azure jest taka sama jak jakakolwiek inna maszyna wirtualna.<br/><br/> Tryb przywracania usług katalogowych (DSRM) jest również dostępny, dlatego wszystkie Active Directory scenariusze odzyskiwania są dostępne. [Dowiedz się więcej](#post-restore-steps) na temat zagadnień związanych z tworzeniem kopii zapasowych i przywracania w przypadku zwirtualizowanych kontrolerów domeny.
**Przywracanie wielu maszyn wirtualnych kontrolera domeny w jednej domenie** | Jeśli inne kontrolery domeny w tej samej domenie można osiągnąć za pośrednictwem sieci, kontroler domeny może zostać przywrócony do dowolnej maszyny wirtualnej. Jeśli jest to ostatni pozostały kontroler domeny w domenie lub zostanie wykonane odzyskiwanie w sieci izolowanej, należy użyć [odzyskiwania lasu](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie pojedynczej maszyny wirtualnej kontrolera domeny w konfiguracji z wieloma domenami** |  Przywracanie dysków i Tworzenie maszyny wirtualnej przy [użyciu programu PowerShell](backup-azure-vms-automation.md#restore-the-disks)  
**Przywracanie wielu domen w jednym lesie** | Zalecamy [odzyskanie lasu](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie Active Directory kontrolerów domeny](active-directory-backup-restore.md).

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania

Po zainicjowaniu operacji przywracania usługa Backup tworzy zadanie śledzenia. Azure Backup wyświetla powiadomienia dotyczące zadania w portalu. Jeśli nie są widoczne, wybierz symbol **powiadomienia** , a następnie wybierz pozycję **więcej zdarzeń w dzienniku aktywności** , aby wyświetlić stan procesu przywracania.

![Wyzwolono przywracanie](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Śledź przywracanie w następujący sposób:

1. Aby wyświetlić operacje dla zadania, wybierz hiperłącze powiadomienia. Alternatywnie w magazynie wybierz pozycję **zadania tworzenia kopii zapasowej**, a następnie wybierz odpowiednią maszynę wirtualną.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Aby monitorować postęp przywracania, Wybierz dowolne zadanie przywracania ze stanem **w toku**. Spowoduje to wyświetlenie paska postępu, który wyświetla informacje o postępie przywracania:

    - **Szacowany czas przywracania**: początkowo zapewnia czas potrzebny do ukończenia operacji przywracania. W miarę postępów operacji czas trwania jest zmniejszany i dociera do zera po zakończeniu operacji przywracania.
    - **Procent przywracania**. Przedstawia wartość procentową wykonywanej operacji przywracania.
    - **Liczba przesłanych bajtów**: w przypadku przywracania przez utworzenie nowej maszyny wirtualnej program wyświetli bajty, które zostały przeniesione do całkowitej liczby bajtów do przeniesienia.

## <a name="post-restore-steps"></a>Kroki po odzyskiwaniu

Po przywróceniu maszyny wirtualnej można pamiętać o kilku kwestiach:

- Rozszerzenia obecne w konfiguracji kopii zapasowej są zainstalowane, ale nie są włączone. Jeśli zobaczysz problem, zainstaluj ponownie rozszerzenia.
- Jeśli kopia zapasowa maszyny wirtualnej ma statyczny adres IP, przywrócona maszyna wirtualna będzie mieć dynamiczny adres IP, aby uniknąć konfliktu. [Do przywróconej maszyny wirtualnej można dodać statyczny adres IP](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji Przywróć dysk, możesz [określić zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny wirtualnej na podstawie dysku przy użyciu podanego szablonu lub programu PowerShell.
- Jeśli używasz dystrybucji systemu Linux opartej na chmurze, takiej jak Ubuntu, ze względów bezpieczeństwa hasło jest blokowane po przywróceniu. Aby [zresetować hasło](../virtual-machines/troubleshooting/reset-password.md), użyj rozszerzenia VMAccess na PRZYWRÓCONEJ maszynie wirtualnej. Zalecamy używanie kluczy SSH w tych dystrybucjach, więc nie trzeba resetować hasła po przywróceniu.
- Jeśli po przywróceniu nie można uzyskać dostępu do maszyny wirtualnej, ponieważ maszyna wirtualna ma naruszoną relację z kontrolerem domeny, wykonaj poniższe kroki, aby wyświetlić maszynę wirtualną:
  - Dołącz dysk systemu operacyjnego jako dysk danych do odzyskiwanej maszyny wirtualnej.
  - Ręcznie Zainstaluj agenta maszyny wirtualnej, jeśli usługa Azure Agent nie odpowiada, wykonując ten [link](../virtual-machines/troubleshooting/install-vm-agent-offline.md).
  - Włącz dostęp do konsoli szeregowej na maszynie wirtualnej, aby zezwolić na dostęp z wiersza polecenia do maszyny wirtualnej

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Po odbudowaniu maszyny wirtualnej Użyj Azure Portal resetowania konta i hasła administratora lokalnego
  - Użyj Konsola szeregowa dostępu i polecenia CMD, aby odłączyć maszynę wirtualną od domeny

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Po rozłączeniu i ponownym uruchomieniu maszyny wirtualnej będzie można pomyślnie włączyć protokół RDP na maszynie wirtualnej z poświadczeniami administratora lokalnego i ponownie dołączyć maszynę wirtualną do domeny.

## <a name="backing-up-restored-vms"></a>Tworzenie kopii zapasowej przywróconych maszyn wirtualnych

- W przypadku przywrócenia maszyny wirtualnej do tej samej grupy zasobów o takiej samej nazwie jak oryginalna kopia zapasowa maszyny wirtualnej kopia zapasowa jest kontynuowana na maszynie wirtualnej po przywróceniu.
- Jeśli maszyna wirtualna została przywrócona do innej grupy zasobów lub została określona inna nazwa dla przywróconej maszyny wirtualnej, należy skonfigurować kopię zapasową przywróconej maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpią problemy podczas procesu przywracania, [Przejrzyj](backup-azure-vms-troubleshoot.md#restore) typowe problemy i błędy.
- Po przywróceniu maszyny wirtualnej Dowiedz się więcej o [zarządzaniu maszynami wirtualnymi](backup-azure-manage-vms.md)
