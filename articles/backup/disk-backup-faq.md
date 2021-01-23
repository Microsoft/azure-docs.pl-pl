---
title: Często zadawane pytania dotyczące usługi Kopia zapasowa Azure Disk
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Kopia zapasowa Azure Disk
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 3ef18a7d178075194e24889477768583f05f0cdd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734566"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>Często zadawane pytania dotyczące usługi Kopia zapasowa Azure Disk (wersja zapoznawcza)

>[!IMPORTANT]
>Kopia zapasowa Azure Disk jest w wersji zapoznawczej bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać dostęp do regionu, zobacz [Macierz obsługi](disk-backup-support-matrix.md).
>
>[Wypełnij ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) , aby utworzyć konto w wersji zapoznawczej.

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Kopia zapasowa Azure Disk. Aby uzyskać więcej informacji na temat dostępności obszaru [kopii zapasowej na dysku platformy Azure](disk-backup-overview.md) , obsługiwanych scenariuszy i ograniczeń, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Czy można utworzyć kopię zapasową dysku przy użyciu rozwiązania do tworzenia kopii zapasowych na platformie Azure, jeśli kopia zapasowa tego samego dysku zostanie utworzona przy użyciu kopii zapasowej maszyny wirtualnej

Azure Backup oferuje równoczesną obsługę kopii zapasowych dysku zarządzanego przy użyciu kopii zapasowej dysku i rozwiązań do [tworzenia kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-introduction.md) . Jest to przydatne, gdy konieczne jest jednodniowe wykonywanie kopii zapasowych maszyn wirtualnych, a także częste kopie zapasowe dysku systemu operacyjnego lub konkretnego dysku z danymi, które są spójne, bez wpływu na wydajność aplikacji produkcyjnej.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Jak mogę znaleźć grupę zasobów migawek, która została użyta w celu skonfigurowania kopii zapasowej dysku?

Na ekranie **wystąpienie kopii zapasowej** można znaleźć pole Grupa zasobów migawek w sekcji **podstawowe** informacje. Można wyszukiwać i wybierać wystąpienie kopii zapasowej odpowiedniego dysku z poziomu centrum kopii zapasowych lub magazynu kopii zapasowych.

![Pole grupy zasobów migawek](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Co to jest Grupa zasobów migawek?

Kopia zapasowa Azure Disk oferuje kopię zapasową warstwy operacyjnej dla dysku zarządzanego. Oznacza to, że migawki tworzone w ramach planowanych operacji tworzenia kopii zapasowych i na żądanie są przechowywane w grupie zasobów w ramach subskrypcji. Azure Backup oferuje natychmiastowe przywracanie, ponieważ migawki przyrostowe są przechowywane w ramach subskrypcji. Ta grupa zasobów jest określana jako Grupa zasobów migawek. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kopii zapasowej](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Dlaczego migawka grupy zasobów musi znajdować się w tej samej subskrypcji co dysk, na którym jest wykonywana kopia zapasowa?

Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku. Dlatego wybierz grupę zasobów w ramach tej samej subskrypcji, co w przypadku dysku, którego kopia zapasowa ma zostać utworzona. Dowiedz się więcej o [przyrostowej migawce](../virtual-machines/disks-incremental-snapshots.md#restrictions) dla dysków zarządzanych.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Dlaczego należy zapewnić przypisania ról, aby można było konfigurować kopie zapasowe, wykonywać zaplanowane i tworzone kopie zapasowe oraz przywracać operacje?

Usługa Kopia zapasowa Azure Disk używa metody najniższych uprawnień, aby odnajdywać, chronić i przywracać dyski zarządzane w ramach subskrypcji. Aby to osiągnąć, Azure Backup używa zarządzanej tożsamości [magazynu kopii zapasowych](backup-vault-overview.md) w celu uzyskania dostępu do innych zasobów platformy Azure. Tożsamość zarządzana przypisana przez system jest ograniczona do jednego na zasób i jest powiązana z cyklem życia tego zasobu. Uprawnienia do tożsamości zarządzanej można udzielić przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Tożsamość zarządzana to nazwa główna usługi typu specjalnego, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych](../active-directory/managed-identities-azure-resources/overview.md). Domyślnie magazyn kopii zapasowych nie ma uprawnień dostępu do dysku, którego kopia zapasowa ma zostać utworzona, tworzenie okresowych migawek, usuwanie migawek po okresie przechowywania oraz przywracanie dysku z kopii zapasowej. Jawnie przyznając przypisania ról do zarządzanej tożsamości magazynu kopii zapasowych, masz kontrolę nad zarządzaniem uprawnieniami do zasobów w ramach subskrypcji.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Dlaczego zasady tworzenia kopii zapasowych ograniczają czas trwania przechowywania?

Kopia zapasowa Azure Disk używa migawek przyrostowych, które są ograniczone do 200 migawek na dysk. Aby umożliwić tworzenie kopii zapasowych na żądanie poza zaplanowanymi kopiami zapasowymi, zasady tworzenia kopii zapasowych ograniczają całkowite kopie zapasowe do 180. Dowiedz się więcej na temat [przyrostowych migawek](../virtual-machines/disks-incremental-snapshots.md#restrictions) dla dysków zarządzanych.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Jak długo i dzienna częstotliwość tworzenia kopii zapasowych w zasadach tworzenia kopii zapasowych?

Kopia zapasowa Azure Disk oferuje wiele kopii zapasowych dziennie. Jeśli potrzebujesz kilku częstych kopii zapasowych, wybierz częstotliwość wykonywania kopii zapasowych **co godzinę** . Kopie zapasowe są planowane na podstawie wybranego przedziału **czasu** . Jeśli na przykład wybierzesz **co 4 godziny**, kopie zapasowe są wykonywane co około 4 godzin, dzięki czemu kopie zapasowe będą równomiernie rozłożone na cały dzień. Jeśli raz dziennie kopia zapasowa jest wystarczająca, wybierz częstotliwość **codziennej** kopii zapasowej. W przypadku częstotliwości codziennej kopii zapasowej można określić godzinę wykonywania kopii zapasowych. Należy pamiętać, że czas dnia wskazuje godzinę rozpoczęcia tworzenia kopii zapasowej, a nie godzinę ukończenia tworzenia kopii zapasowej. Czas wymagany do ukończenia operacji tworzenia kopii zapasowej zależy od różnych czynników, w tym współczynnika zmian między kolejnymi kopiami zapasowymi. Kopia zapasowa dysku platformy Azure to jednak kopia zapasowa bez agenta, która używa [migawek przyrostowych](../virtual-machines/disks-incremental-snapshots.md) , które nie mają wpływu na wydajność aplikacji produkcyjnej.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Dlaczego ustawienie nadmiarowości magazynu kopii zapasowych nie ma zastosowania do kopii zapasowych przechowywanych w warstwie operacyjnej (Grupa zasobów migawek)?

Azure Backup używa [przyrostowych migawek](../virtual-machines/disks-incremental-snapshots.md#restrictions) dysków zarządzanych, które przechowują tylko zmiany różnicowe na dyskach od momentu ostatniej migawki magazynu HDD w warstwie Standardowa, niezależnie od typu magazynu dysku nadrzędnego. W celu uzyskania większej niezawodności migawki przyrostowe są domyślnie przechowywane w obszarze strefowo nadmiarowy magazyn (ZRS) w regionach, które obsługują ZRS. Obecnie kopia zapasowa Azure Disk obsługuje operacyjne kopie zapasowe dysków zarządzanych, których kopie zapasowe nie są kopiowane do magazynu magazynu kopii zapasowych. W związku z tym nie ma zastosowania do punktów odzyskiwania ustawienia nadmiarowości magazynu kopii zapasowych.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Czy mogę używać usługi Backup Center do konfigurowania kopii zapasowych i zarządzania wystąpieniami kopii zapasowych dla dysków platformy Azure?

Tak. kopia zapasowa dysku platformy Azure jest zintegrowana z [centrum kopii zapasowych](backup-center-overview.md), co zapewnia **jednolite środowisko zarządzania** na platformie Azure dla przedsiębiorstw służące do zarządzania, monitorowania, obsługiwania i analizowania kopii zapasowych na dużą skalę. Magazynu kopii zapasowych można również używać do tworzenia kopii zapasowych, przywracania i zarządzania wystąpieniami, które są chronione w ramach magazynu.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Dlaczego należy utworzyć magazyn kopii zapasowych i nie używać magazynu Recovery Servicesego?

Magazyn kopii zapasowych to jednostka magazynowa na platformie Azure, która przechowuje dane kopii zapasowej dla niektórych nowszych obciążeń obsługiwanych przez Azure Backup. Magazynów kopii zapasowych można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich Azure Database for PostgreSQL serwerów, dysków platformy Azure i nowszych obciążeń obsługiwanych przez Azure Backup. Magazyny kopii zapasowych ułatwiają organizowanie danych kopii zapasowej, jednocześnie minimalizując koszty zarządzania. Aby dowiedzieć się więcej, zobacz [magazyny kopii zapasowych](./backup-vault-overview.md) .

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>Czy można utworzyć kopię zapasową dysku, a magazyn kopii zapasowych należy do różnych subskrypcji?

Tak, kopia zapasowa dysku zarządzanego przez program i magazyn kopii zapasowych mogą znajdować się w różnych subskrypcjach.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>Czy można utworzyć kopię zapasową dysku, a magazyn kopii zapasowych znajduje się w różnych regionach?

Nie, obecnie można utworzyć kopię zapasową dysku zarządzanego przez źródło, a magazyn kopii zapasowych musi znajdować się w tym samym regionie.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Czy mogę przywrócić dysk do innej subskrypcji?

Tak, możesz przywrócić dysk do innej subskrypcji niż dysk zarządzany przez program, z którego ma zostać utworzona kopia zapasowa.

### <a name="can-i-back-up-multiple-disks-together"></a>Czy mogę utworzyć kopię zapasową wielu dysków?

Nie jest obsługiwane tworzenie migawek z kilku dysków dołączonych do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kopii zapasowej](backup-managed-disks.md#configure-backup) i Dowiedz się więcej o ograniczeniach, zapoznaj się z [matrycą pomocy technicznej](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Jakie są moje opcje tworzenia kopii zapasowych dysków w wielu subskrypcjach?

Obecnie używanie Azure Portal do konfigurowania kopii zapasowych dysków jest ograniczone do maksymalnie 20 dysków z tej samej subskrypcji.

### <a name="what-is-a-target-resource-group"></a>Co to jest docelowa Grupa zasobów?

Podczas operacji przywracania można wybrać subskrypcję i grupę zasobów, do której ma zostać przywrócony dysk. Azure Backup utworzy nowe dyski z punktu odzyskiwania w wybranej grupie zasobów. Jest to nazywane grupą zasobów docelowych. Należy pamiętać, że zarządzana tożsamość magazynu kopii zapasowych wymaga przypisania roli w docelowej grupie zasobów, aby można było pomyślnie wykonać operację przywracania. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą przywracania](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Jakie są uprawnienia używane przez Azure Backup podczas operacji tworzenia kopii zapasowej i przywracania?

Poniżej przedstawiono akcje używane w roli **czytnika kopii zapasowej dysku** przypisanej na **dysku** , którego kopia zapasowa ma zostać utworzona:

"Microsoft. COMPUTE/disks/Read"

"Microsoft. COMPUTE/disks/beginGetAccess/Action"

Poniżej przedstawiono akcje używane w roli **współautor migawki dysku** przypisanej do **grupy zasobów migawek**:

"Microsoft. COMPUTE/snapshots/Delete"

"Microsoft. COMPUTE/snapshots/Write"

"Microsoft. COMPUTE/snapshots/Read"

"Microsoft. Storage/storageAccounts/Write"

"Microsoft. Storage/storageAccounts/Read"

"Microsoft. Storage/storageAccounts/Delete"

"Microsoft. resources/subscriptions/resourceGroups/Read"

"Microsoft. Storage/storageAccounts/ListKeys/Action"

"Microsoft. COMPUTE/snapshots/beginGetAccess/Action"

"Microsoft. COMPUTE/snapshots/endGetAccess/Action"

"Microsoft. COMPUTE/disks/beginGetAccess/Action"

Poniżej przedstawiono akcje używane w roli **operatora przywracania dysku** przypisanej do **docelowej grupy zasobów**:

"Microsoft. COMPUTE/disks/Write"

"Microsoft. COMPUTE/disks/Read"

"Microsoft. resources/subscriptions/resourceGroups/Read"

>[!NOTE]
>Uprawnienia do tych ról mogą ulec zmianie w przyszłości, w zależności od funkcji dodanych przez usługę Azure Backup.

## <a name="next-steps"></a>Następne kroki

- [Macierz obsługi kopii zapasowych dysków na platformie Azure](disk-backup-support-matrix.md)