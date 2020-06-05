---
title: Przywracanie maszyn wirtualnych przy użyciu Azure Portal
description: Przywróć maszynę wirtualną platformy Azure z punktu odzyskiwania przy użyciu Azure Portal
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 31d318f53dd8e55d3d2740d783be4f4dcae92344
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416668"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Przywracanie danych maszyny wirtualnej platformy Azure w Azure Portal

W tym artykule opisano sposób przywracania danych maszyny wirtualnej platformy Azure z punktów odzyskiwania przechowywanych w magazynach Recovery Services [Azure Backup](backup-overview.md) .

## <a name="restore-options"></a>Opcje przywracania

Azure Backup zapewnia wiele sposobów przywracania maszyny wirtualnej.

**Opcja przywracania** | **Szczegóły**
--- | ---
**Utworzenie nowej maszyny wirtualnej.** | Szybko tworzy i pobiera podstawową maszynę wirtualną i uruchamia ją z punktu przywracania.<br/><br/> Możesz określić nazwę dla maszyny wirtualnej, wybrać grupę zasobów i sieć wirtualną (VNet), w której zostanie umieszczona, a następnie określić konto magazynu dla przywróconej maszyny wirtualnej. Nową maszynę wirtualną należy utworzyć w tym samym regionie co źródłowa maszyna wirtualna.
**Przywracanie dysku** | Przywraca dysk maszyny wirtualnej, za pomocą którego można utworzyć nową maszynę wirtualną.<br/><br/> Azure Backup udostępnia szablon, który pomoże Ci dostosować i utworzyć maszynę wirtualną. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć, aby określić niestandardowe ustawienia maszyny wirtualnej i utworzyć maszynę wirtualną.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie możesz dołączyć dysk do istniejącej maszyny wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, które nie zostały w chwili tworzenia kopii zapasowej, lub dodać ustawienia, które należy skonfigurować za pomocą szablonu lub programu PowerShell.
**Zamiana istniejącego** | Można przywrócić dysk i użyć go do zamienienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Bieżąca maszyna wirtualna musi istnieć. Jeśli została usunięta, nie można użyć tej opcji.<br/><br/> Azure Backup tworzy migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku i zapisuje ją w określonej lokalizacji przemieszczania. Istniejące dyski połączone z maszyną wirtualną są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po zakończeniu operacji Zamień dysk oryginalny dysk jest zachowywany w grupie zasobów. Możesz wybrać opcję ręcznego usuwania dysków oryginalnych, jeśli nie są one potrzebne. <br/><br/>Polecenie replace exist jest obsługiwane w przypadku nieszyfrowanych zarządzanych maszyn wirtualnych, w tym maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Nie jest to obsługiwane w przypadku klasycznych maszyn wirtualnych.<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie uwzględniać tylko konfigurację maszyny wirtualnej.<br><br> Instrukcja Replace exist nie jest obsługiwana w przypadku maszyn wirtualnych z połączonymi zasobami (takich jak [zarządzane tożsamości przypisane przez użytkownika](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)), ponieważ aplikacja klient kopii zapasowej nie ma uprawnień do tych zasobów podczas przywracania.
**Między regionami (region pomocniczy)** | W celu przywrócenia maszyn wirtualnych platformy Azure w regionie pomocniczym, które jest [sparowanym regionem platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions), można użyć funkcji przywracania między regionami.<br><br> Jeśli kopia zapasowa jest wykonywana w regionie pomocniczym, można przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania.<br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> <li> [Tworzenie maszyny wirtualnej](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> <li> [Przywróć dyski](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Nie obsługujemy obecnie opcji [Zamień istniejące dyski](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Uprawnienia<br> Operacja przywracania w regionie pomocniczym może być wykonywana przez administratorów kopii zapasowych i administratorów aplikacji.

> [!NOTE]
> Można także odzyskać określone pliki i foldery na maszynie wirtualnej platformy Azure. [Dowiedz się więcej](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Konta magazynu

Niektóre szczegóły dotyczące kont magazynu:

- **Tworzenie maszyny wirtualnej**: podczas tworzenia nowej maszyny wirtualnej maszyna wirtualna zostanie umieszczona na określonym koncie magazynu.
- **Przywróć dysk**: podczas przywracania dysku jest on kopiowany na określone konto magazynu. Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny wirtualnej. Ten szablon zostanie umieszczony na określonym koncie magazynu.
- **Zastąp dysk**: podczas zamieniania dysku na istniejącą maszynę wirtualną, Azure Backup wykonuje migawkę istniejącej maszyny wirtualnej przed zastąpieniem dysku. Migawka jest przechowywana w lokalizacji przemieszczania (konto magazynu), którą określisz. To konto magazynu jest używane do tymczasowego przechowywania migawki podczas procesu przywracania i zalecamy utworzenie nowego konta w tym celu, które można łatwo usunąć.
- **Lokalizacja konta magazynu**: konto magazynu musi znajdować się w tym samym regionie co magazyn. Wyświetlane są tylko te konta. Jeśli w lokalizacji nie ma żadnych kont magazynu, należy ją utworzyć.
- **Typ magazynu**: Magazyn obiektów BLOB nie jest obsługiwany.
- **Nadmiarowość magazynu**: Magazyn strefowo nadmiarowy (ZRS) nie jest obsługiwany. Informacje o replikacji i nadmiarowości dla konta są wyświetlane w nawiasach po nazwie konta.
- Usługa **Premium Storage**:
  - Podczas przywracania maszyn wirtualnych z systemem innym niż Premium konta magazynu w warstwie Premium nie są obsługiwane.
  - Podczas przywracania zarządzanych maszyn wirtualnych konta magazynu w warstwie Premium skonfigurowane z regułami sieci nie są obsługiwane.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby przywrócić maszynę wirtualną (utworzyć nową maszynę wirtualną), upewnij się, że masz poprawne [uprawnienia](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) kontroli dostępu opartej na ROLACH (RBAC) dla operacji przywracania maszyny wirtualnej.

Jeśli nie masz uprawnień, możesz [przywrócić dysk](#restore-disks), a następnie po przywróceniu dysku można [użyć szablonu](#use-templates-to-customize-a-restored-vm) , który został wygenerowany w ramach operacji przywracania, aby utworzyć nową maszynę wirtualną.

## <a name="select-a-restore-point"></a>Wybierz punkt przywracania

1. W magazynie skojarzonym z maszyną wirtualną, którą chcesz przywrócić, kliknij pozycję **Utwórz kopię zapasową elementów**  >  **usługi Azure Virtual Machine**.
2. Kliknij maszynę wirtualną. Domyślnie na pulpicie nawigacyjnym maszyny wirtualnej są wyświetlane punkty odzyskiwania z ostatnich 30 dni. Możesz wyświetlić punkty odzyskiwania starsze niż 30 dni lub przefiltrować, aby znaleźć punkty odzyskiwania na podstawie dat, zakresów czasu i różnych typów spójności migawek.
3. Aby przywrócić maszynę wirtualną, kliknij pozycję **Przywróć maszynę wirtualną**.

    ![Punkt przywracania](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Wybierz punkt przywracania do użycia podczas odzyskiwania.

## <a name="choose-a-vm-restore-configuration"></a>Wybieranie konfiguracji przywracania maszyny wirtualnej

1. W obszarze **Konfiguracja przywracania**wybierz opcję przywracania:
    - **Utwórz nową**: Użyj tej opcji, jeśli chcesz utworzyć nową maszynę wirtualną. Można utworzyć maszynę wirtualną z prostymi ustawieniami lub przywrócić dysk i utworzyć dostosowaną maszynę wirtualną.
    - **Zastąp istniejące**: Użyj tej opcji, jeśli chcesz zastąpić dyski na istniejącej maszynie wirtualnej.

        ![Kreator przywracania konfiguracji](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Określ ustawienia wybranej opcji przywracania.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Jako jedna z [opcji przywracania](#restore-options)można szybko utworzyć maszynę wirtualną z ustawieniami podstawowymi z punktu przywracania.

1. W obszarze **Przywróć konfigurację**  >  **Utwórz nowy**  >  **Typ przywracania**, wybierz pozycję **Utwórz maszynę wirtualną**.
2. W polu **Nazwa maszyny wirtualnej**Określ maszynę wirtualną, która nie istnieje w subskrypcji.
3. W obszarze **Grupa zasobów**wybierz istniejącą grupę zasobów dla nowej maszyny wirtualnej lub Utwórz nową z globalnie unikatową nazwą. Jeśli przypiszesz już istniejącą nazwę, platforma Azure przypisze grupę o tej samej nazwie co maszyna wirtualna.
4. W obszarze **Sieć wirtualna**wybierz sieć wirtualną, w której zostanie umieszczona maszyna wirtualna. Zostanie wyświetlona cała sieci wirtualnych skojarzona z subskrypcją. Wybierz podsieć. Pierwsza podsieć jest domyślnie zaznaczona.
5. W polu **Lokalizacja magazynu**Określ konto magazynu dla maszyny wirtualnej. [Dowiedz się więcej](#storage-accounts).

    ![Kreator przywracania konfiguracji](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. W obszarze **Konfiguracja przywracania**wybierz pozycję **OK**. W obszarze **przywracanie**kliknij polecenie **Przywróć** , aby wyzwolić operację przywracania.

## <a name="restore-disks"></a>Przywróć dyski

Jako jedną z [opcji przywracania](#restore-options)można utworzyć dysk z punktu przywracania. Następnie przy użyciu dysku można wykonać jedną z następujących czynności:

- Użyj szablonu, który jest generowany podczas operacji przywracania, aby dostosować ustawienia i wyzwolić wdrożenie maszyny wirtualnej. Można edytować domyślne ustawienia szablonu i przesłać szablon do wdrożenia maszyny wirtualnej.
- [Dołącz przywrócone dyski](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) do istniejącej maszyny wirtualnej.
- [Utwórz nową maszynę wirtualną](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) na podstawie przywróconych dysków przy użyciu programu PowerShell.

1. W obszarze **Przywróć konfigurację**  >  **Utwórz nowy**  >  **Typ przywracania**, wybierz pozycję **Przywróć dyski**.
2. W obszarze **Grupa zasobów**wybierz istniejącą grupę zasobów dla przywróconych dysków lub Utwórz nową z globalnie unikatową nazwą.
3. W obszarze **konto magazynu**Określ konto, do którego mają zostać skopiowane wirtualne dyski twarde. [Dowiedz się więcej](#storage-accounts).

    ![Ukończono konfigurację odzyskiwania](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. W obszarze **Konfiguracja przywracania**wybierz pozycję **OK**. W obszarze **przywracanie**kliknij polecenie **Przywróć** , aby wyzwolić operację przywracania.

Jeśli maszyna wirtualna korzysta z dysków zarządzanych i wybierana jest opcja **Utwórz maszynę wirtualną** , Azure Backup nie używa określonego konta magazynu. W przypadku **przywracania dysków** i **natychmiastowego przywracania**konto magazynu jest używane tylko do przechowywania szablonu. Dyski zarządzane są tworzone w określonej grupie zasobów.
Gdy maszyna wirtualna używa dysków niezarządzanych, są one przywracane jako obiekty blob do konta magazynu.

### <a name="use-templates-to-customize-a-restored-vm"></a>Dostosowywanie przywróconej maszyny wirtualnej przy użyciu szablonów

Po przywróceniu dysku Użyj szablonu, który został wygenerowany w ramach operacji przywracania, aby dostosować i utworzyć nową maszynę wirtualną:

1. Otwórz **szczegóły zadania przywracania** dla odpowiedniego zadania.

2. W obszarze **szczegóły zadania przywracania**wybierz pozycję **Wdróż szablon** , aby zainicjować wdrożenie szablonu.

    ![Przechodzenie do szczegółów zadania przywracania](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Aby dostosować ustawienie maszyny wirtualnej w szablonie, kliknij przycisk **Edytuj szablon**. Jeśli chcesz dodać więcej dostosowań, kliknij przycisk **Edytuj parametry**.
    - [Dowiedz się więcej](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) o wdrażaniu zasobów na podstawie szablonu niestandardowego.
    - [Dowiedz się więcej](../azure-resource-manager/templates/template-syntax.md) o tworzeniu szablonów.

   ![Załaduj wdrożenie szablonu](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Wprowadź wartości niestandardowe dla maszyny wirtualnej, zaakceptuj **warunki i postanowienia** , a następnie kliknij przycisk **Kup**.

   ![Prześlij wdrożenie szablonu](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Zastąp istniejące dyski

Jako jedną z [opcji przywracania](#restore-options)można zastąpić istniejący dysk maszyny wirtualnej wybranym punktem przywracania. [Przejrzyj](#restore-options) wszystkie opcje przywracania.

1. W obszarze **Konfiguracja przywracania**kliknij pozycję **Zamień istniejące**.
2. W obszarze **Typ przywracania**wybierz pozycję **Zamień dysk/s**. Jest to punkt przywracania używany do zastępowania istniejących dysków maszyny wirtualnej.
3. W obszarze **Lokalizacja tymczasowa**Określ, gdzie migawki bieżących dysków zarządzanych mają być zapisywane podczas procesu przywracania. [Dowiedz się więcej](#storage-accounts).

   ![Kreator przywracania konfiguracji Zastąp istniejący](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Przywracanie między regionami

Jako jedna z [opcji przywracania](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options), przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure.

Aby dołączyć do funkcji w wersji zapoznawczej, zapoznaj się z [sekcją przed rozpoczęciem](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Aby sprawdzić, czy CRR jest włączona, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie przywracania między regionami](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Wyświetlanie elementów kopii zapasowej w regionie pomocniczym

Jeśli CRR jest włączona, można wyświetlić elementy kopii zapasowej w regionie pomocniczym.

1. W portalu przejdź do obszaru **Recovery Services**  >  **elementy kopii zapasowej** magazynu
2. Kliknij pozycję **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

![Maszyny wirtualne w regionie pomocniczym](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Wybierz region pomocniczy](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Przywróć w regionie pomocniczym

Środowisko użytkownika do przywracania regionu pomocniczego będzie podobne do środowiska użytkownika w regionie podstawowym. Podczas konfigurowania szczegółów w bloku Przywracanie konfiguracji w celu skonfigurowania przywracania zostanie wyświetlony monit o podanie tylko parametrów regionu pomocniczego.

![Wybierz maszynę wirtualną do przywrócenia](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Wybierz punkt przywracania](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Przywróć konfigurację](./media/backup-azure-arm-restore-vms/rest-config.png)

![Powiadomienie o wyzwoleniu przywracania w toku](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Aby przywrócić i utworzyć maszynę wirtualną, zapoznaj się z tematem [Tworzenie maszyny wirtualnej](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Aby przywrócić jako dysk, zapoznaj się z tematem [przywracanie dysków](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Po wyzwoleniu przywracania i fazie transferu danych nie można anulować zadania przywracania.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorowanie zadań przywracania regionu pomocniczego

1. W portalu przejdź do obszaru **Recovery Services**  >  **zadania tworzenia kopii zapasowej** magazynu
2. Kliknij pozycję **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

![Odfiltrowane zadania tworzenia kopii zapasowej](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Przywracanie maszyn wirtualnych z konfiguracjami specjalnymi

Istnieje kilka typowych scenariuszy, w których może być konieczne przywrócenie maszyn wirtualnych.

**Scenariusz** | **Wskazówki**
--- | ---
**Przywracanie maszyn wirtualnych przy użyciu korzyści z używania hybrydowego** | Jeśli maszyna wirtualna z systemem Windows wykorzystuje [Licencjonowanie korzyści z używania hybrydowego](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Przywróć dyski i Utwórz nową maszynę wirtualną przy użyciu podanego szablonu (z **typem licencji** ustawionym na **Windows_Server**) lub PowerShell.  To ustawienie można również zastosować po utworzeniu maszyny wirtualnej.
**Przywracanie maszyn wirtualnych w trakcie awarii centrum danych platformy Azure** | Jeśli magazyn używa GRS i podstawowego centrum danych dla maszyny wirtualnej, Azure Backup obsługuje przywracanie kopii zapasowych maszyn wirtualnych do sparowanego centrum danych. W sparowanym centrum danych wybierz konto magazynu i przywróć je jako normalne. Azure Backup używa usługi obliczeniowej w sparowanym regionie do utworzenia przywróconej maszyny wirtualnej. [Dowiedz się więcej](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) o odporności centrum danych.
**Przywracanie maszyny wirtualnej z jednym kontrolerem domeny w jednej domenie** | Przywróć maszynę wirtualną podobnie jak jakakolwiek inna maszyna wirtualna. Należy pamiętać, że:<br/><br/> Z perspektywy Active Directory maszyna wirtualna platformy Azure jest taka sama jak jakakolwiek inna maszyna wirtualna.<br/><br/> Tryb przywracania usług katalogowych (DSRM) jest również dostępny, dlatego wszystkie Active Directory scenariusze odzyskiwania są dostępne. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) na temat zagadnień związanych z tworzeniem kopii zapasowych i przywracania w przypadku zwirtualizowanych kontrolerów domeny.
**Przywracanie wielu maszyn wirtualnych kontrolera domeny w jednej domenie** | Jeśli inne kontrolery domeny w tej samej domenie można osiągnąć za pośrednictwem sieci, kontroler domeny może zostać przywrócony do dowolnej maszyny wirtualnej. Jeśli jest to ostatni pozostały kontroler domeny w domenie lub zostanie wykonane odzyskiwanie w sieci izolowanej, należy użyć [odzyskiwania lasu](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie wielu domen w jednym lesie** | Zalecamy [odzyskanie lasu](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Przywracanie bez systemu operacyjnego** | Główna różnica między maszynami wirtualnymi platformy Azure i lokalnymi funkcjami hypervisor polega na tym, że żadna konsola maszyny wirtualnej nie jest dostępna na platformie Azure. Konsola programu jest wymagana w niektórych scenariuszach, takich jak odzyskiwanie przy użyciu odzyskiwania bez systemu operacyjnego (BMR). Jednak przywracanie maszyny wirtualnej z magazynu jest pełnym zamiennikiem BMR.
**Przywracanie maszyn wirtualnych ze specjalnymi konfiguracjami sieci** | Specjalne konfiguracje sieci obejmują maszyny wirtualne korzystające z wewnętrznego lub zewnętrznego równoważenia obciążenia, przy użyciu wielu kart sieciowych lub wielu zarezerwowanych adresów IP. Te maszyny wirtualne można przywrócić przy użyciu [opcji Przywróć dysk](#restore-disks). Ta opcja powoduje utworzenie kopii dysków VHD na określonym koncie magazynu, a następnie można utworzyć maszynę wirtualną z [wewnętrznym](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) lub [zewnętrznym](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) modułem równoważenia obciążenia, [wieloma](../virtual-machines/windows/multiple-nics.md)kartami sieciowymi lub [wieloma zastrzeżonymi adresami IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), zgodnie z konfiguracją.
**Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) na karcie sieciowej/podsieci** | Kopia zapasowa maszyny wirtualnej platformy Azure obsługuje tworzenie kopii zapasowych i przywracanie informacji sieciowej grupy zabezpieczeń na poziomie sieci wirtualnej, podsieci i karty sieciowej.
**Przypięte strefy maszyny wirtualne** | W przypadku tworzenia kopii zapasowej maszyny wirtualnej platformy Azure, która jest przypięta do strefy (z Azure Backup), można przywrócić ją w tej samej strefie, w której został przypięty. [Dowiedz się więcej](https://docs.microsoft.com/azure/availability-zones/az-overview)
**Przywróć maszynę wirtualną w dowolnym zestawie dostępności** | W przypadku przywracania maszyny wirtualnej z portalu nie jest dostępna opcja wyboru zestawu dostępności. Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji Przywróć dysk, możesz [określić zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny wirtualnej na podstawie dysku przy użyciu podanego szablonu lub programu PowerShell.

## <a name="track-the-restore-operation"></a>Śledzenie operacji przywracania

Po zainicjowaniu operacji przywracania usługa Backup tworzy zadanie śledzenia. Azure Backup wyświetla powiadomienia dotyczące zadania w portalu. Jeśli nie są widoczne, wybierz symbol **powiadomienia** , a następnie wybierz pozycję **Wyświetl wszystkie zadania** , aby wyświetlić stan procesu przywracania.

![Wyzwolono przywracanie](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Śledź przywracanie w następujący sposób:

1. Aby wyświetlić operacje dla zadania, kliknij hiperlink powiadomienia. Alternatywnie w magazynie kliknij pozycję **zadania tworzenia kopii zapasowej**, a następnie kliknij odpowiednią maszynę wirtualną.

    ![Lista maszyn wirtualnych w magazynie](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Aby monitorować postęp przywracania, kliknij dowolne zadanie przywracania ze stanem **w toku**. Spowoduje to wyświetlenie paska postępu, który wyświetla informacje o postępie przywracania:

    - **Szacowany czas przywracania**: początkowo zapewnia czas potrzebny do ukończenia operacji przywracania. W miarę postępów operacji czas trwania jest zmniejszany i dociera do zera po zakończeniu operacji przywracania.
    - **Procent przywracania**. Przedstawia wartość procentową wykonywanej operacji przywracania.
    - **Liczba przesłanych bajtów**: w przypadku przywracania przez utworzenie nowej maszyny wirtualnej program wyświetli bajty, które zostały przeniesione do całkowitej liczby bajtów do przeniesienia.

## <a name="post-restore-steps"></a>Kroki po odzyskiwaniu

Po przywróceniu maszyny wirtualnej można pamiętać o kilku kwestiach:

- Rozszerzenia obecne w konfiguracji kopii zapasowej są zainstalowane, ale nie są włączone. Jeśli zobaczysz problem, zainstaluj ponownie rozszerzenia.
- Jeśli kopia zapasowa maszyny wirtualnej ma statyczny adres IP, przywrócona maszyna wirtualna będzie mieć dynamiczny adres IP, aby uniknąć konfliktu. [Do przywróconej maszyny wirtualnej można dodać statyczny adres IP](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Przywrócona maszyna wirtualna nie ma zestawu dostępności. Jeśli używasz opcji Przywróć dysk, możesz [określić zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) podczas tworzenia maszyny wirtualnej na podstawie dysku przy użyciu podanego szablonu lub programu PowerShell.
- Jeśli używasz dystrybucji systemu Linux opartej na chmurze, takiej jak Ubuntu, ze względów bezpieczeństwa hasło jest blokowane po przywróceniu. Aby [zresetować hasło](../virtual-machines/linux/reset-password.md), użyj rozszerzenia VMAccess na PRZYWRÓCONEJ maszynie wirtualnej. Zalecamy używanie kluczy SSH w tych dystrybucjach, więc nie trzeba resetować hasła po przywróceniu.
- Jeśli po przywróceniu nie można uzyskać dostępu do maszyny wirtualnej, ponieważ maszyna wirtualna ma naruszoną relację z kontrolerem domeny, wykonaj poniższe kroki, aby wyświetlić maszynę wirtualną:
  - Dołącz dysk systemu operacyjnego jako dysk danych do odzyskiwanej maszyny wirtualnej.
  - Ręcznie Zainstaluj agenta maszyny wirtualnej, jeśli usługa Azure Agent nie odpowiada, wykonując ten [link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
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
