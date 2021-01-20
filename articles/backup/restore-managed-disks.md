---
title: Przywracanie Managed Disks platformy Azure
description: Dowiedz się, jak przywrócić Managed Disks platformy Azure z Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 848a7476b1c5095d4e4d3156d4c7ce33da777090
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611138"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Przywracanie Managed Disks platformy Azure (w wersji zapoznawczej)

>[!IMPORTANT]
>Kopia zapasowa Azure Disk jest w wersji zapoznawczej bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać dostęp do regionu, zobacz [Macierz obsługi](disk-backup-support-matrix.md).
>
>[Wypełnij ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) , aby utworzyć konto w wersji zapoznawczej.

W tym artykule wyjaśniono, jak przywrócić [Managed disks platformy Azure](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) z punktu przywracania utworzonego przez Azure Backup.

Obecnie opcja odzyskiwania Original-Location (OLR) przywracania przez zastąpienie istniejącego dysku źródłowego, z którego wykonano kopie zapasowe, nie jest obsługiwana. Można przywrócić z punktu odzyskiwania, aby utworzyć nowy dysk w tej samej grupie zasobów co dysk źródłowy, z którego wykonano kopie zapasowe lub w innej grupie zasobów. Jest to nazywane Alternate-Location odzyskiwaniem (ALR) i pomaga zachować zarówno dysk źródłowy, jak i przywrócony (nowy) dysk.

Ten artykuł obejmuje następujące zagadnienia:

- Przywróć, aby utworzyć nowy dysk

- Śledzenie stanu operacji przywracania

## <a name="restore-to-create-a-new-disk"></a>Przywróć, aby utworzyć nowy dysk

Magazyn kopii zapasowych używa tożsamości zarządzanej do uzyskiwania dostępu do innych zasobów platformy Azure. Aby przywrócić kopię zapasową, zarządzana tożsamość magazynu kopii zapasowych wymaga zestawu uprawnień do grupy zasobów, w której dysk ma zostać przywrócony.

Magazyn kopii zapasowych używa tożsamości zarządzanej przypisanej do systemu, która jest ograniczona do jednego na zasób i jest związana z cyklem życia tego zasobu. Uprawnienia do tożsamości zarządzanej można udzielić przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Tożsamość zarządzana to nazwa główna usługi typu specjalnego, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Do wykonania operacji przywracania wymagane są następujące wymagania wstępne:

1. Przypisz rolę **operatora przywracania dysku** do tożsamości zarządzanej magazynu kopii zapasowych w grupie zasobów, w której dysk zostanie przywrócony przez usługę Azure Backup.

    >[!NOTE]
    > Można wybrać tę samą grupę zasobów, która jest używana na dysku źródłowym, z którego są tworzone kopie zapasowe, lub do innej grupy zasobów w ramach tej samej lub innej subskrypcji.

    1. Przejdź do grupy zasobów, w której ma zostać przywrócony dysk. Na przykład grupa zasobów to *TargetRG*.

    1. Przejdź do **kontroli dostępu (IAM)** i wybierz pozycję **Dodaj przypisania ról**

    1. W okienku kontekstowym po prawej stronie wybierz pozycję **operator przywracania dysku** na liście rozwijanej **rola** . Wybierz zarządzaną tożsamość magazynu kopii zapasowych i **Zapisz**.

        >[!TIP]
        >Wpisz nazwę magazynu kopii zapasowych, aby wybrać zarządzaną tożsamość magazynu.

        ![Wybierz rolę operatora przywracania dysku](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Sprawdź, czy zarządzana tożsamość magazynu kopii zapasowych ma odpowiedni zestaw przypisań ról w grupie zasobów, w której dysk zostanie przywrócony.

    1. Przejdź do **magazynu kopii zapasowych — tożsamość >** i wybierz pozycję **przypisania ról platformy Azure**

        ![Wybierz przypisania ról platformy Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Sprawdź, czy rola, nazwa zasobu i typ zasobu są poprawnie wyświetlane.

        ![Weryfikuj rolę, nazwę zasobu i typ zasobu](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Mimo że przypisania ról są poprawnie widoczne w portalu, może upłynąć około 15 minut, aby można było zastosować uprawnienie do tożsamości zarządzanej magazynu kopii zapasowych.
    >
    >Podczas zaplanowanych kopii zapasowych lub operacji tworzenia kopii zapasowej na żądanie Azure Backup przechowuje przyrostowe migawki dysku w grupie zasobów migawek podanej podczas konfigurowania kopii zapasowej dysku. Azure Backup używa tych migawek przyrostowych podczas operacji przywracania. Jeśli migawki zostaną usunięte lub przeniesione z grupy zasobów migawek lub przypisania roli magazynu kopii zapasowych zostaną odwołane w grupie zasobów migawka, operacja przywracania zakończy się niepowodzeniem.

1. Jeśli dysk, który ma zostać przywrócony, jest szyfrowany za pomocą [kluczy zarządzanych przez klienta (CMK)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal) lub przy użyciu [podwójnego szyfrowania przy użyciu kluczy zarządzanych przez platformę i kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal), przypisz uprawnienie roli **czytelnik** do zarządzanej tożsamości magazynu kopii zapasowych w ramach zasobu **zestawu szyfrowania dysku** .

Po spełnieniu wymagań wstępnych wykonaj następujące kroki, aby wykonać operację przywracania.

1. W [Azure Portal](https://portal.azure.com/)przejdź do **centrum kopii zapasowych**. Wybierz pozycję **wystąpienia kopii zapasowej** w sekcji **Zarządzanie** . Z listy wystąpienia kopii zapasowej wybierz wystąpienie kopii zapasowej dysku, dla którego chcesz wykonać operację przywracania.

    ![Lista wystąpień kopii zapasowych](./media/restore-managed-disks/backup-instances.png)

    Alternatywnie można wykonać tę operację z magazynu kopii zapasowych, który został użyty do skonfigurowania kopii zapasowej dysku.

1. Na ekranie **wystąpienie kopii zapasowej** wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie wybierz pozycję **Przywróć**.

    ![Wybierz punkt przywracania](./media/restore-managed-disks/select-restore-point.png)

1. W przepływie pracy **przywracania** zapoznaj się z tematem **podstawowe** informacje i **Wybierz kartę punkt odzyskiwania** , a następnie wybierz pozycję **Dalej: przywracanie parametrów**.

    ![Przegląd podstawowe i wybieranie informacji o punkcie odzyskiwania](./media/restore-managed-disks/review-information.png)

1. Na karcie **przywracanie parametrów** wybierz **docelową subskrypcję** i **docelową grupę zasobów** , do której chcesz przywrócić kopię zapasową. Podaj nazwę dysku, który ma zostać przywrócony. Wybierz kolejno pozycje **Dalej: przegląd i przywracanie**.

    ![Parametry przywracania](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Kopie zapasowe dysków tworzone przez Azure Backup przy użyciu rozwiązania do tworzenia kopii zapasowych dysków mogą być również tworzone w ramach Azure Backup przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure z magazynem Recovery Services. W przypadku skonfigurowania ochrony maszyny wirtualnej platformy Azure, do której jest dołączony ten dysk, można również użyć operacji przywracania maszyny wirtualnej platformy Azure. Można przywrócić maszynę wirtualną lub dyski i pliki lub foldery z punktu odzyskiwania odpowiedniego wystąpienia kopii zapasowej maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [kopia zapasowa maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/about-azure-vm-restore).

1. Po pomyślnym sprawdzeniu poprawności wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

    ![Zainicjuj operację przywracania](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Walidacja może potrwać kilka minut, zanim będzie można wyzwolić operację przywracania. Walidacja może zakończyć się niepowodzeniem, jeśli:
    >
    > - dysk o tej samej nazwie podany w **przywróconej nazwie dysku** już istnieje w **docelowej grupie zasobów**
    > - zarządzana tożsamość magazynu kopii zapasowych nie ma prawidłowych przypisań ról w **docelowej grupie zasobów**
    > - przypisania roli tożsamości zarządzanego magazynu kopii zapasowych są odwoływane w **grupie zasobów migawek** , w której są przechowywane migawki przyrostowe
    > - W przypadku usunięcia lub przeniesienia migawek przyrostowych z grupy zasobów migawek

Przywrócenie spowoduje utworzenie nowego dysku z wybranego punktu odzyskiwania w docelowej grupie zasobów, która została dostarczona podczas operacji przywracania. Aby użyć przywróconego dysku na istniejącej maszynie wirtualnej, należy wykonać więcej czynności:

- Jeśli przywrócony dysk jest dyskiem danych, można dołączyć istniejący dysk do maszyny wirtualnej. Jeśli przywrócony dysk jest dyskiem systemu operacyjnego, można zamienić dysk systemu operacyjnego maszyny wirtualnej z Azure Portal w okienku **maszyny wirtualnej** > menu **dyski** w sekcji **Ustawienia** .

    ![Wymiana dysków systemu operacyjnego](./media/restore-managed-disks/swap-os-disks.png)

- W przypadku maszyn wirtualnych z systemem Windows, jeśli przywrócony dysk jest dyskiem danych, postępuj zgodnie z instrukcjami, aby [odłączyć oryginalny dysk danych](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-the-portal) z maszyny wirtualnej. Następnie [Dołącz przywrócony dysk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) do maszyny wirtualnej. Postępuj zgodnie z instrukcjami, aby [zamienić dysk systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machines/windows/os-disk-swap) maszyny wirtualnej na przywrócony dysk.

- W przypadku maszyn wirtualnych z systemem Linux, jeśli przywrócony dysk jest dyskiem danych, postępuj zgodnie z instrukcjami, aby [odłączyć oryginalny dysk danych](https://docs.microsoft.com/azure/virtual-machines/linux/detach-disk#detach-a-data-disk-using-the-portal) z maszyny wirtualnej. Następnie [Dołącz przywrócony dysk](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#attach-an-existing-disk) do maszyny wirtualnej. Postępuj zgodnie z instrukcjami, aby [zamienić dysk systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machines/linux/os-disk-swap) maszyny wirtualnej na przywrócony dysk.

Zaleca się odwołanie przypisania roli **operatora przywracania dysku** z zarządzanej tożsamości magazynu kopii zapasowych w **docelowej grupie zasobów** po pomyślnym zakończeniu operacji przywracania.

## <a name="track-a-restore-operation"></a>Śledzenie operacji przywracania

Po zainicjowaniu operacji przywracania usługa Backup tworzy zadanie śledzenia. Azure Backup wyświetla powiadomienia dotyczące zadania w portalu. Aby wyświetlić postęp zadania przywracania:

1. Przejdź do ekranu **wystąpienia kopii zapasowej** . Pokazuje pulpit nawigacyjny zadań z operacją i stanem w ciągu ostatnich siedmiu dni.

    ![Pulpit nawigacyjny zadań](./media/restore-managed-disks/jobs-dashboard.png)

1. Aby wyświetlić stan operacji przywracania, wybierz pozycję **Wyświetl wszystko** , aby wyświetlić bieżące i przeszłe zadania tego wystąpienia kopii zapasowej.

    ![Wybierz pozycję Wyświetl wszystko](./media/restore-managed-disks/view-all.png)

1. Przejrzyj listę zadań tworzenia kopii zapasowej i przywracania oraz ich stan. Wybierz zadanie z listy zadań, aby wyświetlić szczegóły zadania.

    ![Lista zadań](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Następne kroki

- [Kopia zapasowa Azure Disk — często zadawane pytania](disk-backup-faq.md)
