---
title: Przywracanie maszyn wirtualnych przy użyciu Azure Portal
description: Przywróć maszynę wirtualną platformy Azure z punktu odzyskiwania przy użyciu Azure Portal, w tym funkcji przywracania między regionami.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 452ca5e1b1c6554a2ae1651068eae06ad9a6b232
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515230"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Jak przywrócić dane maszyny wirtualnej platformy Azure w Azure Portal

W tym artykule opisano sposób przywracania danych maszyny wirtualnej platformy Azure z punktów odzyskiwania przechowywanych [w magazynach Azure Backup](backup-overview.md) Recovery Services.

## <a name="restore-options"></a>Opcje przywracania

Azure Backup kilka sposobów przywracania maszyny wirtualnej.

**Opcja przywracania** | **Szczegóły**
--- | ---
**Tworzenie nowej maszyny wirtualnej** | Szybko tworzy i pobiera z punktu przywracania skonfigurowaną i uruchomioną podstawową maszynę wirtualną.<br/><br/> Możesz określić nazwę maszyny wirtualnej, wybrać grupę zasobów i sieć wirtualną, w której zostanie umieszczona, i określić konto magazynu dla przywróconej maszyny wirtualnej. Nową maszynę wirtualną należy utworzyć w tym samym regionie co źródłową maszynę wirtualną.<br><br>Jeśli przywracanie maszyny wirtualnej zakończy się niepowodzeniem z powodu braku dostępnej wersji SKU maszyny wirtualnej platformy Azure w określonym regionie platformy Azure lub z powodu innych problemów, program Azure Backup nadal przywraca dyski w określonej grupie zasobów.
**Przywracanie dysku** | Przywraca dysk maszyny wirtualnej, za pomocą którego można następnie utworzyć nową maszynę wirtualną.<br/><br/> Usługa Azure Backup udostępnia szablon ułatwiający dostosowanie i utworzenie maszyny wirtualnej. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny wirtualnej oraz utworzenia maszyny wirtualnej.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie możesz dołączyć dysk do istniejącej maszyny wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, których nie było w czasie tworzenia kopii zapasowej, lub dodać ustawienia, które należy skonfigurować za pomocą szablonu lub programu PowerShell.
**Zamiana istniejącego** | Możesz przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Wymaga to istnienia bieżącej maszyny wirtualnej. Jeśli została ona usunięta, nie można użyć tej opcji.<br/><br/> Azure Backup migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku i zapisuje ją w określisz lokalizacji przejściowej. Istniejące dyski połączone z maszyną wirtualną są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po operacji zastępowania dysku oryginalny dysk jest zachowywany w grupie zasobów. Możesz ręcznie usunąć oryginalne dyski, jeśli nie są potrzebne. <br/><br/>Zastąpienie istniejącej jest obsługiwane w przypadku niezaszyfrowanych zarządzanych maszyn wirtualnych, w tym maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Nie jest ona obsługiwana w przypadku klasycznych i niezaładowanych maszyn wirtualnych.<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie odzwierciedlać tylko konfigurację maszyny wirtualnej.<br><br> Zastąpienie istniejącej jest również obsługiwane w przypadku maszyn wirtualnych z połączonymi zasobami, na przykład [tożsamością](../active-directory/managed-identities-azure-resources/overview.md) zarządzaną przypisaną przez użytkownika [lub Key Vault](../key-vault/general/overview.md).
**Między regionami (region pomocniczy)** | Przywracanie między regionami może służyć do przywracania maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowany [z platformą Azure.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> Jeśli kopia zapasowa zostanie wykonana w regionie pomocniczym, możesz przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania.<br><br> Podczas tworzenia kopii zapasowej migawki nie są replikowane do regionu pomocniczego. Replikowane są tylko dane przechowywane w magazynie. Dlatego przywracanie regionu pomocniczego to tylko [przywracanie w warstwie](about-azure-vm-restore.md#concepts) magazynu. Czas przywracania dla regionu pomocniczego będzie prawie taki sam jak czas przywracania warstwy magazynu dla regionu podstawowego.  <br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> <li> [Tworzenie maszyny wirtualnej](#create-a-vm) <br> <li> [Przywracanie dysków](#restore-disks) <br><br> Obecnie nie obsługujemy opcji [Zamień istniejące](#replace-existing-disks) dyski.<br><br> Uprawnienia<br> Operację przywracania w regionie pomocniczym mogą wykonywać administratorzy kopii zapasowej i administratorzy aplikacji.

> [!NOTE]
> Możesz również odzyskać określone pliki i foldery na maszynie wirtualnej platformy Azure. [Dowiedz się więcej](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Konta magazynu

Szczegółowe informacje o kontach magazynu:

- **Tworzenie maszyny** wirtualnej: podczas tworzenia nowej maszyny wirtualnej zostanie ona umieszczona na koncie magazynu, które określisz.
- **Przywróć** dysk: podczas przywracania dysku jest on kopiowany na określone konto magazynu. Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny wirtualnej. Ten szablon jest umieszczany na określonym koncie magazynu.
- **Wymiana dysku:** podczas wymiany dysku na istniejącej maszynie wirtualnej program Azure Backup migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku. Migawka jest również kopiowana do magazynu usługi Recovery Services za pośrednictwem transferu danych w tle. Jednak po zakończeniu fazy migawki zostanie wyzwolona operacja wymiany dysków. Po zakończeniu operacji wymiany dysku dyski źródłowej maszyny wirtualnej platformy Azure zostaną pozostawione w określonej grupie zasobów dla operacji, a dyski VHD będą przechowywane na określonym koncie magazynu. Możesz usunąć lub zachować te dyski VHD i dyski.
- **Lokalizacja konta magazynu:** konto magazynu musi znajdować się w tym samym regionie co magazyn. Wyświetlane są tylko te konta. Jeśli w lokalizacji nie ma żadnych kont magazynu, należy je utworzyć.
- **Typ magazynu:** magazyn obiektów blob nie jest obsługiwany.
- **Nadmiarowość magazynu:** magazyn strefowo nadmiarowy (ZRS) nie jest obsługiwany. Informacje o replikacji i nadmiarowości konta są wyświetlane w nawiasach po nazwie konta.
- **Magazyn w chmurze Premium:**
  - W przypadku przywracania maszyn wirtualnych spoza wersji Premium konta usługi Premium Storage nie są obsługiwane.
  - Podczas przywracania zarządzanych maszyn wirtualnych konta magazynu w chmurze w chmurze skonfigurowane przy użyciu reguł sieciowych nie są obsługiwane.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby przywrócić maszynę wirtualną (utworzyć nową maszynę wirtualną), upewnij się, że masz odpowiednie uprawnienia kontroli dostępu na podstawie ról (RBAC) [](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) platformy Azure dla operacji przywracania maszyny wirtualnej.

Jeśli nie masz uprawnień, możesz przywrócić dysk, [a](#restore-disks)następnie po przywróceniu dysku możesz użyć szablonu, który został wygenerowany w ramach operacji przywracania, aby utworzyć nową maszynę wirtualną. [](#use-templates-to-customize-a-restored-vm)

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Wybieranie punktu przywracania

1. W magazynie skojarzonym z maszyną wirtualną, którą chcesz przywrócić, wybierz pozycję **Elementy kopii**  >  **zapasowej Maszyny wirtualnej platformy Azure.**
1. Wybierz maszynę wirtualną. Domyślnie na pulpicie nawigacyjnym maszyny wirtualnej są wyświetlane punkty odzyskiwania z ostatnich 30 dni. Można wyświetlić punkty odzyskiwania starsze niż 30 dni lub filtrować w celu znalezienia punktów odzyskiwania na podstawie dat, zakresów czasu i różnych typów spójności migawki.
1. Aby przywrócić maszynę wirtualną, wybierz **pozycję Przywróć maszynę wirtualną.**

    ![Punkt przywracania](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Wybierz punkt przywracania do użycia na użytek odzyskiwania.

## <a name="choose-a-vm-restore-configuration"></a>Wybieranie konfiguracji przywracania maszyny wirtualnej

1. W **menu Przywróć maszynę** wirtualną wybierz opcję przywracania:
    - **Utwórz nową:** użyj tej opcji, jeśli chcesz utworzyć nową maszynę wirtualną. Możesz utworzyć maszynę wirtualną z prostymi ustawieniami lub przywrócić dysk i utworzyć niestandardową maszynę wirtualną.
    - **Zastąp istniejący:** użyj tej opcji, jeśli chcesz zamienić dyski na istniejącej maszynie wirtualnej.

        ![Kreator przywracania konfiguracji maszyny wirtualnej](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Określ ustawienia dla wybranej opcji przywracania.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Jako jedną z opcji [przywracania](#restore-options)możesz szybko utworzyć maszynę wirtualną z podstawowymi ustawieniami z punktu przywracania.

1. W **przywróceniu**  >  **maszyny wirtualnej** Utwórz nowy typ  >  **przywracania** wybierz pozycję Utwórz **maszynę wirtualną.**
1. W **nazwie maszyny wirtualnej** określ maszynę wirtualną, która nie istnieje w subskrypcji.
1. W **grupie zasobów** wybierz istniejącą grupę zasobów dla nowej maszyny wirtualnej lub utwórz nową grupę o globalnie unikatowej nazwie. Jeśli przypiszesz nazwę, która już istnieje, platforma Azure przypisze grupie taką samą nazwę jak maszyna wirtualna.
1. W **sieci wirtualnej** wybierz sieć wirtualną, w której zostanie umieszczona maszyna wirtualna. Zostaną wyświetlone wszystkie sieci wirtualne skojarzone z subskrypcją. Wybierz podsieć. Pierwsza podsieć jest domyślnie wybrana.
1. W **pozycji Lokalizacja przemieszczania** określ konto magazynu dla maszyny wirtualnej. [Dowiedz się więcej](#storage-accounts).

    ![Kreator konfiguracji przywracania — wybieranie opcji przywracania](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Wybierz **pozycję Przywróć,** aby wyzwolić operację przywracania.

## <a name="restore-disks"></a>Przywracanie dysków

Jako jedną z [opcji przywracania](#restore-options)możesz utworzyć dysk z punktu przywracania. Następnie za pomocą dysku możesz wykonać jedną z następujących czynności:

- Użyj szablonu wygenerowanego podczas operacji przywracania, aby dostosować ustawienia i wyzwolić wdrożenie maszyny wirtualnej. Możesz edytować domyślne ustawienia szablonu i przesłać szablon do wdrożenia maszyny wirtualnej.
- [Dołączanie przywróconych dysków](../virtual-machines/windows/attach-managed-disk-portal.md) do istniejącej maszyny wirtualnej.
- [Utwórz nową maszynę wirtualną](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) na przywróconych dyskach przy użyciu programu PowerShell.

1. W **konfiguracji Przywracania**  >  **Utwórz nowy typ**  >  **przywracania** wybierz pozycję **Przywróć dyski.**
1. W **grupie zasobów** wybierz istniejącą grupę zasobów dla przywróconych dysków lub utwórz nową grupę o globalnie unikatowej nazwie.
1. W **pozycji Lokalizacja przemieszczania** określ konto magazynu, do którego mają zostać skopiowane dyski VHD. [Dowiedz się więcej](#storage-accounts).

    ![Wybieranie pozycji Grupa zasobów i Lokalizacja przemieszczania](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Wybierz **pozycję Przywróć,** aby wyzwolić operację przywracania.

Jeśli maszyna wirtualna używa dysków  zarządzanych i wybierzesz opcję Utwórz maszynę wirtualną, Azure Backup nie będzie używać określonego konta magazynu. W przypadku **przywracania dysków i** natychmiastowego **przywracania** konto magazynu jest używane tylko do przechowywania szablonu. Dyski zarządzane są tworzone w określonej grupie zasobów.
Gdy maszyna wirtualna używa dysków niezamaniowych, są one przywracane jako obiekty blob na koncie magazynu.

### <a name="use-templates-to-customize-a-restored-vm"></a>Dostosowywanie przywróconej maszyny wirtualnej przy użyciu szablonów

Po przywróceniu dysku użyj szablonu, który został wygenerowany w ramach operacji przywracania, aby dostosować i utworzyć nową maszynę wirtualną:

1. W **zadaniach tworzenia** kopii zapasowej wybierz odpowiednie zadanie przywracania.

1. W **przywróceniu** wybierz **pozycję Wdróć szablon,** aby zainicjować wdrożenie szablonu.

    ![Przechodzenie do szczegółów zadania przywracania](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Aby dostosować ustawienie maszyny wirtualnej podane w szablonie, wybierz pozycję **Edytuj szablon.** Jeśli chcesz dodać więcej dostosowań, wybierz pozycję **Edytuj parametry**.
    - [Dowiedz się więcej](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o wdrażaniu zasobów z szablonu niestandardowego.
    - [Dowiedz się więcej](../azure-resource-manager/templates/template-syntax.md) o szablonach tworzenia.

   ![Wdrażanie szablonu ładowania](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Wprowadź wartości niestandardowe dla maszyny wirtualnej, zaakceptuj **warunki i postanowienia, a** następnie wybierz pozycję **Kup.**

   ![Przesyłanie wdrożenia szablonu](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Wymiana istniejących dysków

Jedną z opcji [przywracania jest](#restore-options)zastąpienie istniejącego dysku maszyny wirtualnej wybranym punktem przywracania. [Przejrzyj](#restore-options) wszystkie opcje przywracania.

1. W **konfiguracji przywracania** wybierz pozycję **Zastąp istniejący**.
1. W **typ przywracania,** wybierz **zastąpić dysku/s.** Jest to punkt przywracania, który zostanie użyty do zastąpienia istniejących dysków maszyny wirtualnej.
1. W **lokalizacji przejściowej** określ miejsce, w którym migawki bieżących dysków zarządzanych mają być zapisywane podczas procesu przywracania. [Dowiedz się więcej](#storage-accounts).

   ![Kreator przywracania konfiguracji Zastąp istniejący](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Przywracanie między regionami

Jako jedna z [opcji](#restore-options)przywracania funkcja przywracania między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowany z platformą Azure.

Aby rozpocząć korzystanie z tej funkcji, przeczytaj [sekcję Przed rozpoczęciem.](./backup-create-rs-vault.md#set-cross-region-restore)

Aby sprawdzić, czy funkcja CRR jest włączona, postępuj zgodnie z instrukcjami w te tematu Configure Cross Region Restore (Konfigurowanie [przywracania między regionami).](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Wyświetlanie elementów kopii zapasowej w regionie pomocniczym

Jeśli opcja CRR jest włączona, możesz wyświetlić elementy kopii zapasowej w regionie pomocniczym.

1. W portalu przejdź do pozycji Elementy kopii **zapasowej magazynu** usługi Recovery  >  Services.
1. Wybierz **pozycję Region pomocniczy,** aby wyświetlić elementy w regionie pomocniczym.

>[!NOTE]
>Na liście będą wyświetlane tylko typy zarządzania kopiami zapasowymi, które obsługują funkcję CRR. Obecnie jest dozwolona tylko obsługa przywracania danych regionu pomocniczego do regionu pomocniczego.<br></br>Magazyn CRR dla maszyn wirtualnych platformy Azure jest obsługiwany w przypadku maszyn wirtualnych zarządzanych przez platformę Azure (w tym zaszyfrowanych maszyn wirtualnych platformy Azure).

![Maszyny wirtualne w regionie pomocniczym](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Wybieranie regionu pomocniczego](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Przywracanie w regionie pomocniczym

Środowisko użytkownika przywracania w regionie pomocniczym będzie podobne do interfejsu użytkownika przywracania regionu podstawowego. Podczas konfigurowania szczegółów w okienku Konfiguracja przywracania w celu skonfigurowania przywracania zostanie wyświetlony monit o podanie tylko parametrów regionu pomocniczego.

Obecnie cel [punktu](azure-backup-glossary.md#rpo-recovery-point-objective) dostępu w regionie pomocniczym wynosi do 12 godzin z regionu podstawowego, mimo że replikacja magazynu geograficznie nadmiarowego dostępnego do odczytu [(RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) wynosi 15 minut.

![Wybieranie maszyny wirtualnej do przywrócenia](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Wybieranie punktu przywracania](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Konfiguracja przywracania](./media/backup-azure-arm-restore-vms/rest-config.png)

![Powiadomienie o przywracaniu wyzwalacza w toku](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Aby przywrócić i utworzyć maszynę wirtualną, zobacz [Tworzenie maszyny wirtualnej.](#create-a-vm)
- Aby przywrócić jako dysk, zobacz [Przywracanie dysków.](#restore-disks)

>[!NOTE]
>
>- Po wyzwoleniu przywracania i w fazie transferu danych nie można anulować zadania przywracania.
>- Funkcja przywracania między regionami przywraca maszyny wirtualne platformy Azure z włączoną obsługą klucza CMK (kluczy zarządzanych przez klienta), których nie ma w magazynie usługi Recovery Services z włączoną obsługą klucza CMK, jako maszyny wirtualne z obsługą klucza CMK w regionie pomocniczym.
>- Role platformy Azure potrzebne do przywrócenia w regionie pomocniczym są takie same jak role w regionie podstawowym.

[Maszyny wirtualne przypięte do](../virtual-machines/windows/create-portal-availability-zone.md) strefy platformy Azure można przywrócić w dowolnych [strefach dostępności](../availability-zones/az-overview.md) w tym samym regionie.

W procesie przywracania zobaczysz opcję **Strefa dostępności.** Najpierw zobaczysz strefę domyślną. Aby wybrać inną strefę, wybierz numer wybranej strefy. Jeśli przypięta strefa jest niedostępna, nie będzie można przywrócić danych do innej strefy, ponieważ dane kopii zapasowej nie są replikowane w sposób strefowy. Przywracanie w strefach dostępności jest możliwe tylko z punktów odzyskiwania w warstwie magazynu.

![Wybieranie strefy dostępności](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorowanie zadań przywracania regionu pomocniczego

1. W portalu przejdź do zadania tworzenia kopii **zapasowej magazynu usługi Recovery**  >  **Services**
1. Wybierz **pozycję Region pomocniczy,** aby wyświetlić elementy w regionie pomocniczym.

    ![Przefiltrowane zadania kopii zapasowej](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Przywracanie nieza zarządzanych maszyn wirtualnych i dysków jako zarządzanych

Podczas przywracania jest dostępna opcja przywrócenia dysków [nieza](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) zarządzanych [jako](../virtual-machines/managed-disks-overview.md) dysków zarządzanych. Domyślnie niezaładowane maszyny wirtualne/dyski są przywracane jako niezaładowane maszyny wirtualne/dyski. Jeśli jednak zdecydujesz się przywrócić jako zarządzane maszyny wirtualne/dyski, teraz jest to możliwe. Te przywracanie nie jest wyzwalane z fazy migawki, ale tylko z fazy magazynu. Ta funkcja nie jest dostępna dla niezaładowanych zaszyfrowanych maszyn wirtualnych.

![Przywróć jako dyski zarządzane](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami

Istnieje wiele typowych scenariuszy, w których może być konieczne przywrócenie maszyn wirtualnych.

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie maszyn wirtualnych przy użyciu korzyści użycia hybrydowego** | Jeśli maszyna wirtualna z systemem Windows korzysta z licencjonowania korzyści użycia hybrydowego [(HUB),](../virtual-machines/windows/hybrid-use-benefit-licensing.md)przywróć dyski i utwórz nową maszynę wirtualną przy użyciu podanego szablonu (z typem licencji  **ustawionym na Windows_Server**) lub programu PowerShell.  To ustawienie można również zastosować po utworzeniu maszyny wirtualnej.
**Przywracanie maszyn wirtualnych podczas awarii centrum danych platformy Azure** | Jeśli magazyn używa magazynu GRS i podstawowe centrum danych dla maszyny wirtualnej nie działa, Azure Backup obsługuje przywracanie kopii zapasowych maszyn wirtualnych do sparowanego centrum danych. Wybierz konto magazynu w sparowanych centrach danych i przywróć je w zwykły sposób. Azure Backup używa usługi obliczeniowej w sparowanych regionach do utworzenia przywróconej maszyny wirtualnej. [Dowiedz się więcej](/azure/architecture/resiliency/recovery-loss-azure-region) o odporności centrum danych.<br><br> Jeśli magazyn używa magazynu GRS, możesz wybrać nową funkcję Przywracanie [między regionami.](#cross-region-restore) Dzięki temu można przywrócić do drugiego regionu w scenariuszach pełnej lub częściowej awarii, a nawet jeśli w ogóle nie ma awarii.
**Przywracanie od zdjętego systemu** | Główna różnica między maszynami wirtualnym platformy Azure i lokalnymi funkcjami hypervisor polega na tym, że na platformie Azure nie ma dostępnej konsoli maszyny wirtualnej. Konsola jest wymagana w niektórych scenariuszach, takich jak odzyskiwanie przy użyciu kopii zapasowej typu odzyskiwania systemu od zerowego (BMR, bare-metal recovery). Jednak przywracanie maszyny wirtualnej z magazynu jest pełnym zamiennikiem odzyskiwania po danych z magazynu.
**Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami sieci** | Specjalne konfiguracje sieci obejmują maszyny wirtualne korzystające z wewnętrznego lub zewnętrznego równoważenia obciążenia, wielu kart sieciowych lub wielu zastrzeżonych adresów IP. Te maszyny wirtualne można przywrócić przy użyciu [opcji przywróć dysk](#restore-disks). Ta opcja powoduje utworzenie kopii wirtualnych dysków twardych na określone konto [](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) magazynu, [](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) a następnie można utworzyć maszynę wirtualną z wewnętrznym lub zewnętrznym równoważeniem [obciążenia,](../virtual-machines/windows/multiple-nics.md)wieloma kartami sieciowym lub wieloma zastrzeżonym adresami [IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)zgodnie z konfiguracją.
**Sieciowa grupa zabezpieczeń (NSG) na kartę sieciową/podsieć** | Kopia zapasowa maszyny wirtualnej platformy Azure obsługuje tworzenie kopii zapasowych i przywracanie informacji sieciowej sieci na poziomie sieci wirtualnej, podsieci i karty sieciowej.
**Maszyny wirtualne przypięte do strefy** | Jeśli kopię zapasową maszyny wirtualnej platformy Azure przypiętą do strefy (z Azure Backup), możesz przywrócić ją w tej samej strefie, w której została przypięta. [Dowiedz się więcej](../availability-zones/az-overview.md)
**Przywracanie maszyny wirtualnej w dowolnym zestawie dostępności** | Podczas przywracania maszyny wirtualnej z portalu nie ma możliwości wybrania zestawu dostępności. Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji przywracania dysku, [](../virtual-machines/windows/tutorial-availability-sets.md) możesz określić zestaw dostępności podczas tworzenia maszyny wirtualnej na podstawie dysku przy użyciu podanego szablonu lub programu PowerShell.
**Przywracanie specjalnych maszyn wirtualnych, takich jak maszyny wirtualne SQL** | Jeśli tworzysz kopię zapasową maszyny wirtualnej SQL przy użyciu kopii zapasowej maszyny wirtualnej platformy Azure, a następnie użyj opcji przywracania maszyny wirtualnej lub utworzysz maszynę wirtualną po przywróceniu dysków, nowo utworzona maszyna wirtualna musi zostać zarejestrowana u dostawcy SQL, jak wspomniano [tutaj](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash). Spowoduje to przekonwertowanie przywróconej maszyny wirtualnej na maszynę wirtualną SQL.

### <a name="restore-domain-controller-vms"></a>Przywracanie maszyn wirtualnych kontrolera domeny

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie maszyny wirtualnej pojedynczego kontrolera domeny w jednej domenie** | Przywróć maszynę wirtualną tak jak każdą inną maszynę wirtualną. Należy pamiętać, że:<br/><br/> Z perspektywy usługi Active Directory maszyna wirtualna platformy Azure jest jak każda inna maszyna wirtualna.<br/><br/> Dostępny jest również tryb przywracania usług katalogowych (DSRM, Directory Services Restore Mode), więc wszystkie scenariusze odzyskiwania usługi Active Directory są możliwe do użycia. [Dowiedz się więcej o](#post-restore-steps) zagadnieniach dotyczących tworzenia kopii zapasowych i przywracania zwirtualizowanych kontrolerów domeny.
**Przywracanie wielu maszyn wirtualnych kontrolera domeny w jednej domenie** | Jeśli inne kontrolery domeny w tej samej domenie są dostępne za pośrednictwem sieci, kontroler domeny można przywrócić tak jak dowolną maszynę wirtualną. Jeśli jest to ostatni pozostały kontroler domeny w domenie lub jest wykonywane odzyskiwanie w sieci [izolowanej,](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)użyj odzyskiwania lasu .
**Przywracanie maszyny wirtualnej pojedynczego kontrolera domeny w konfiguracji z wieloma domenami** |  Przywracanie dysków i tworzenie maszyny wirtualnej przy [użyciu programu PowerShell](backup-azure-vms-automation.md#restore-the-disks)  
**Przywracanie wielu domen w jednym lesie** | Zalecamy odzyskanie [lasu](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

Aby uzyskać więcej informacji, zobacz [Temat Back up and restore Active Directory domain controllers (Kopii zapasowej i przywracanie kontrolerów domeny usługi Active Directory).](active-directory-backup-restore.md)

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania

Po wyzwoleniu operacji przywracania usługa tworzenia kopii zapasowej tworzy zadanie do śledzenia. Azure Backup wyświetla powiadomienia o zadaniu w portalu. Jeśli nie są widoczne, wybierz **symbol** Powiadomienia, a następnie wybierz pozycję Więcej zdarzeń w dzienniku **aktywności,** aby wyświetlić stan procesu przywracania.

![Wyzwolone przywracanie](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Śledź przywracanie w następujący sposób:

1. Aby wyświetlić operacje dla zadania, wybierz hiperlink powiadomień. Alternatywnie w magazynie wybierz pozycję **Zadania tworzenia kopii zapasowej,** a następnie wybierz odpowiednią maszynę wirtualną.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Aby monitorować postęp przywracania, wybierz dowolne zadanie przywracania ze stanem **W toku.** Zostanie wyświetlony pasek postępu, na którym są wyświetlane informacje o postępie przywracania:

    - **Szacowany czas przywracania:** początkowo zapewnia czas trwania operacji przywracania. W trakcie operacji czas jej działania zmniejsza się i osiąga zero po zakończeniu operacji przywracania.
    - **Procent przywracania**. Przedstawia procent wykonanej operacji przywracania.
    - **Liczba przeniesionych** bajtów: jeśli przywracasz przez utworzenie nowej maszyny wirtualnej, pokazuje ona bajty, które zostały przeniesione, względem całkowitej liczby bajtów do przeniesienia.

## <a name="post-restore-steps"></a>Kroki po przywróceniu

Po przywróceniu maszyny wirtualnej należy zwrócić uwagę na kilka rzeczy:

- Rozszerzenia obecne podczas konfiguracji kopii zapasowej są instalowane, ale nie są włączone. Jeśli zostanie wyświetlony problem, zainstaluj ponownie rozszerzenia.
- Jeśli maszyna wirtualna z kopią zapasową ma statyczny adres IP, przywrócona maszyna wirtualna będzie miała dynamiczny adres IP, aby uniknąć konfliktu. Do [przywróconej maszyny wirtualnej](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)możesz dodać statyczny adres IP.
- Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji przywracania dysku, [](../virtual-machines/windows/tutorial-availability-sets.md) możesz określić zestaw dostępności podczas tworzenia maszyny wirtualnej na podstawie dysku przy użyciu podanego szablonu lub programu PowerShell.
- W przypadku korzystania z dystrybucji systemu Linux opartej na chmurze, takiej jak Ubuntu, hasło jest blokowane po przywróceniu ze względów bezpieczeństwa. Użyj rozszerzenia VMAccess na przywróconej maszynie wirtualnej, [aby zresetować hasło](/troubleshoot/azure/virtual-machines/reset-password). Zalecamy używanie kluczy SSH w tych dystrybucjach, więc nie trzeba resetować hasła po przywróceniu.
- Jeśli nie możesz uzyskać dostępu do maszyny wirtualnej po jej przywróceniu, ponieważ maszyna wirtualna ma uszkodzoną relację z kontrolerem domeny, wykonaj poniższe kroki, aby wytworzyć maszynę wirtualną:
  - Dołącz dysk systemu operacyjnego jako dysk danych do odzyskanej maszyny wirtualnej.
  - Ręcznie zainstaluj agenta maszyny wirtualnej, jeśli agent platformy Azure nie odpowiada, korzystając z tego [linku.](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)
  - Włącz dostęp do konsoli szeregowej na maszynie wirtualnej, aby umożliwić dostęp wiersza polecenia do maszyny wirtualnej

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Po odbudowie maszyny wirtualnej użyj Azure Portal resetowania konta administratora lokalnego i hasła
  - Użyj Konsola szeregowa i cmd, aby odłączyć maszynę wirtualną od domeny

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Po odłączenia i ponownym uruchomieniu maszyny wirtualnej będzie można pomyślnie nawiązać z maszyną wirtualną za pomocą protokołu RDP poświadczenia administratora lokalnego i ponownie dołączyć maszynę wirtualną z powrotem do domeny.

## <a name="backing-up-restored-vms"></a>Backing up restored VMs

- Jeśli maszyna wirtualna została przywrócona do tej samej grupy zasobów o takiej samej nazwie, jak oryginalna kopia zapasowa maszyny wirtualnej, kopia zapasowa będzie kontynuowana na maszynie wirtualnej po jej przywróceniu.
- Jeśli maszyna wirtualna jest przywracana do innej grupy zasobów lub określono inną nazwę przywróconej maszyny wirtualnej, należy skonfigurować kopię zapasową dla przywróconej maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Jeśli podczas procesu przywracania wystąpią [trudności,](backup-azure-vms-troubleshoot.md#restore) przejrzyj typowe problemy i błędy.
- Po przywróceniu maszyny wirtualnej dowiedz się więcej o [zarządzaniu maszynami wirtualnymi](backup-azure-manage-vms.md)