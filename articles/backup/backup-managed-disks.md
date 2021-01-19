---
title: Tworzenie kopii zapasowej Managed Disks platformy Azure
description: Dowiedz się, jak utworzyć kopię zapasową Managed Disks platformy Azure z Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 2169e2f44e3ffb2c05c674d633efabed2c531878
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573126"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>Tworzenie kopii zapasowej Managed Disks platformy Azure (w wersji zapoznawczej)

>[!IMPORTANT]
>Kopia zapasowa Azure Disk jest w wersji zapoznawczej bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać dostęp do regionu, zobacz [Macierz obsługi](disk-backup-support-matrix.md).
>
>[Wypełnij ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) , aby utworzyć konto w wersji zapoznawczej.

W tym artykule wyjaśniono, jak utworzyć kopię zapasową [dysku zarządzanego platformy Azure](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) z Azure Portal.

Ten artykuł obejmuje następujące zagadnienia:

- Tworzenie magazynu kopii zapasowych

- Tworzenie zasad kopii zapasowych

- Konfigurowanie kopii zapasowej dysku platformy Azure

- Uruchamianie zadania tworzenia kopii zapasowej na żądanie

Aby uzyskać informacje na temat dostępności obszaru kopii zapasowej na dysku platformy Azure, obsługiwanych scenariuszy i ograniczeń, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Tworzenie magazynu kopii zapasowych

Magazyn kopii zapasowych to jednostka magazynowa na platformie Azure, która przechowuje dane kopii zapasowej dla różnych nowszych obciążeń obsługiwanych przez Azure Backup, takich jak serwery Azure Database for PostgreSQL i dyski platformy Azure. Magazyny kopii zapasowych ułatwiają organizowanie danych kopii zapasowej, jednocześnie minimalizując koszty zarządzania. Magazyny kopii zapasowych są oparte na modelu Azure Resource Manager platformy Azure, który oferuje ulepszone funkcje ułatwiające zabezpieczanie danych kopii zapasowych.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).
1. W polu wyszukiwania wpisz **centrum kopii zapasowych** .
1. W obszarze **usługi** wybierz pozycję **centrum kopii zapasowych**.
1. Na stronie **centrum kopii zapasowych** wybierz pozycję **Magazyn**.

   ![Wybieranie magazynu w centrum kopii zapasowych](./media/backup-managed-disks/backup-center.png)

1. Na ekranie **Inicjowanie: Tworzenie magazynu** wybierz pozycję **Magazyn kopii zapasowych** i **Wykonaj operację**.

   ![Inicjowanie: Tworzenie magazynu](./media/backup-managed-disks/initiate-create-vault.png)

1. Na karcie **podstawy** Podaj subskrypcję, grupę zasobów, nazwę magazynu kopii zapasowych, region i nadmiarowość magazynu kopii zapasowych. Kontynuuj, wybierając pozycję **Przegląd + Utwórz**. Dowiedz się więcej o [tworzeniu magazynu kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-vault-overview#create-a-backup-vault).

   ![Przeglądanie i tworzenie magazynu](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

1. W **magazynie kopii zapasowych** *DemoVault* utworzonym w poprzednim kroku przejdź do pozycji **zasady tworzenia kopii zapasowych** i wybierz pozycję **Dodaj**.

   ![Dodawanie zasad kopii zapasowych](./media/backup-managed-disks/backup-policies.png)

1. Na karcie **podstawy** Podaj nazwę zasad, a następnie wybierz pozycję **Typ źródła danych** jako **dysk platformy Azure**. Magazyn jest już wstępnie wypełniony i są prezentowane wybrane właściwości magazynu.

   >[!NOTE]
   > Mimo że wybrany magazyn może mieć ustawienie Global-nadmiarowości, obecnie kopia zapasowa dysku platformy Azure obsługuje tylko funkcję Snapshot Store. Wszystkie kopie zapasowe są przechowywane w grupie zasobów w subskrypcji i nie są kopiowane do magazynu magazynów kopii zapasowych.

   ![Wybierz typ źródła danych](./media/backup-managed-disks/datasource-type.png)

1. Na karcie **zasady tworzenia kopii zapasowych** wybierz częstotliwość harmonogramu tworzenia kopii zapasowych.

   ![Wybieranie częstotliwości harmonogramu kopii zapasowych](./media/backup-managed-disks/backup-schedule-frequency.png)

   Kopia zapasowa Azure Disk oferuje wiele kopii zapasowych dziennie. Jeśli potrzebujesz kilku częstych kopii zapasowych, wybierz częstotliwość wykonywania kopii zapasowych co **godzinę** , aby tworzyć kopie zapasowe z interwałami co 4, 6, 8 lub 12 godzin. Kopie zapasowe są planowane na podstawie wybranego przedziału **czasu** . Jeśli na przykład wybierzesz **co 4 godziny**, kopie zapasowe zostaną wykonane o około 4 godzinach, a kopie zapasowe są równomiernie rozłożone na cały dzień. Jeśli wystarcza kopia zapasowa raz dziennie, wybierz częstotliwość **codziennej** kopii zapasowej. W przypadku częstotliwości codziennej kopii zapasowej można określić godzinę wykonywania kopii zapasowych. Należy pamiętać, że czas dnia wskazuje godzinę rozpoczęcia tworzenia kopii zapasowej, a nie godzinę ukończenia tworzenia kopii zapasowej. Czas wymagany do ukończenia operacji tworzenia kopii zapasowej zależy od różnych czynników, w tym rozmiaru dysku, i współczynnika zmian między kolejnymi kopiami zapasowymi. Kopia zapasowa dysku platformy Azure to jednak kopia zapasowa bez agenta, która korzysta z [migawek przyrostowych](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal), co nie ma wpływu na wydajność aplikacji produkcyjnej.

1. Na karcie **zasady kopii zapasowej** wybierz pozycję Ustawienia przechowywania, które spełniają wymagania celu punktu odzyskiwania (RPO).

   Domyślna reguła przechowywania jest stosowana, jeśli nie określono innej reguły przechowywania. Domyślną regułę przechowywania można zmodyfikować, aby zmienić czas trwania przechowywania, ale nie można jej usunąć. Nową regułę przechowywania można dodać, wybierając pozycję **Dodaj regułę przechowywania**.

   ![Dodaj regułę przechowywania](./media/backup-managed-disks/add-retention-rule.png)

   Możesz wybrać **pierwszą pomyślną kopię zapasową** codziennie lub co tydzień, a także podać czas przechowywania, który ma zostać zachowany dla konkretnych kopii zapasowych przed ich usunięciem. Ta opcja jest przydatna do zachowywania określonych kopii zapasowych dnia lub tygodnia przez dłuższy czas. Wszystkie inne częste kopie zapasowe mogą być przechowywane przez krótszy czas.

   ![Ustawienia przechowywania](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Azure Backup dla Managed Disks używa migawek przyrostowych, które są ograniczone do 200 migawek na dysk. Aby umożliwić tworzenie kopii zapasowych na żądanie poza zaplanowanymi kopiami zapasowymi, zasady tworzenia kopii zapasowych ograniczają całkowite kopie zapasowe do 180. Dowiedz się więcej na temat [migawek przyrostowych](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) dla dysku zarządzanego.

1. Ukończ Tworzenie zasad tworzenia kopii zapasowych, wybierając pozycję **Przegląd + Utwórz**.

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Magazyn kopii zapasowych używa tożsamości zarządzanej do uzyskiwania dostępu do innych zasobów platformy Azure. Aby skonfigurować tworzenie kopii zapasowych dysków zarządzanych, zarządzana tożsamość magazynu kopii zapasowych wymaga zestawu uprawnień na dyskach źródłowych i grupach zasobów, w których migawki są tworzone i zarządzane.

Tożsamość zarządzana przypisana przez system jest ograniczona do jednego na zasób i jest powiązana z cyklem życia tego zasobu. Uprawnienia do tożsamości zarządzanej można udzielić przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Tożsamość zarządzana to nazwa główna usługi typu specjalnego, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aby skonfigurować tworzenie kopii zapasowych dysków zarządzanych, wymagane są następujące wymagania wstępne:

1. Przypisz rolę **czytnika kopii zapasowej dysku** do tożsamości zarządzanej magazynu kopii zapasowych na dysku źródłowym, którego kopia zapasowa ma zostać utworzona.

   1. Przejdź do dysku, dla którego ma zostać utworzona kopia zapasowa.

   1. Przejdź do **kontroli dostępu (IAM)** i wybierz pozycję **Dodaj przypisania ról**

   1. W okienku kontekstowym po prawej stronie wybierz pozycję **czytnik kopii zapasowej dysku** na liście rozwijanej **rola** . Wybierz zarządzaną tożsamość magazynu kopii zapasowych i **Zapisz**.

   >[!TIP]
   >Wpisz nazwę magazynu kopii zapasowych, aby wybrać zarządzaną tożsamość magazynu.

   ![Dodaj rolę czytnika kopii zapasowej dysku](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Przypisz rolę **współautor migawki dysku** do tożsamości zarządzanej magazynu kopii zapasowych w grupie zasobów, w której tworzone są kopie zapasowe i zarządzane przez usługę Azure Backup. Migawki dysku są przechowywane w grupie zasobów w ramach subskrypcji. Aby zezwolić usłudze Azure Backup na tworzenie i przechowywanie migawek oraz zarządzanie nimi, musisz podać uprawnienia do magazynu kopii zapasowych.

   **Wybieranie grupy zasobów do przechowywania migawek i zarządzania nimi:**

   - Nie wybieraj tej samej grupy zasobów co dysk źródłowy.

   - Jako wskazówkę zaleca się utworzenie dedykowanej grupy zasobów jako magazynu danych migawek, która ma być używana przez usługę Azure Backup. Posiadanie dedykowanej grupy zasobów pozwala ograniczyć uprawnienia dostępu do grupy zasobów, zapewniając bezpieczeństwo i łatwość zarządzania danymi kopii zapasowych.

   - Tej grupy zasobów można używać do przechowywania migawek na wielu dyskach, które są tworzone w ramach kopii zapasowej (lub planowanej ich).  

   - Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku. Dlatego wybierz grupę zasobów w ramach tej samej subskrypcji, co w przypadku dysku, którego kopia zapasowa ma zostać utworzona. Dowiedz się więcej o [przyrostowej migawce](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) dla dysków zarządzanych.

   Aby przypisać rolę, wykonaj następujące kroki:

   1. Przejdź do grupy zasobów. Na przykład grupa zasobów to *SnapshotRG*, która znajduje się w tej samej subskrypcji co dysk, którego kopia zapasowa ma zostać utworzona.

   1. Przejdź do pozycji **Kontrola dostępu (IAM)** i wybierz pozycję **Dodaj przypisania ról**.

   1. W okienku kontekstowym po prawej stronie wybierz pozycję **współautor migawki dysku** na liście rozwijanej **rola** . Wybierz zarządzaną tożsamość magazynu kopii zapasowych i **Zapisz**.

   >[!TIP]
   >Wpisz nazwę magazynu kopii zapasowych, aby wybrać zarządzaną tożsamość magazynu.

   ![Dodaj rolę współautor migawki dysku](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Jeśli dysk, którego kopia zapasowa ma zostać utworzona, jest szyfrowany przy użyciu [kluczy zarządzanych przez klienta (CMK)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal) lub przy użyciu [podwójnego szyfrowania przy użyciu kluczy zarządzanych przez platformę i kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal), przypisz uprawnienie roli **czytelnik** do zarządzanej tożsamości magazynu kopii zapasowych w ramach zasobu **zestawu szyfrowania dysku** .

1. Sprawdź, czy zarządzana tożsamość magazynu kopii zapasowych ma odpowiedni zestaw przypisań ról na dysku źródłowym i w grupie zasobów, która służy jako migawka magazynu danych.

   1. Przejdź do **magazynu kopii zapasowych — > tożsamość** i wybierz pozycję **przypisania ról platformy Azure**.

      ![Wybierz przypisania ról platformy Azure](./media/backup-managed-disks/azure-role-assignments.png)

   1. Sprawdź, czy rola, nazwa zasobu i typ zasobu są poprawnie odzwierciedlone.

      ![Weryfikowanie roli, nazwy zasobu i typu zasobu](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Mimo że przypisania ról są poprawnie widoczne w portalu, może upłynąć około 15 minut, aby można było zastosować uprawnienie do tożsamości zarządzanej magazynu kopii zapasowych.

1. Po spełnieniu wymagań wstępnych przejdź do **magazynu kopii zapasowych — omówienie >** a następnie wybierz pozycję **kopia zapasowa** , aby rozpocząć konfigurowanie kopii zapasowej dysków.

   ![Wybierz kopię zapasową](./media/backup-managed-disks/select-backup.png)

1. Na karcie **podstawy** wybierz pozycję **dysk platformy Azure** jako typ źródła danych.

   ![Wybieranie dysku platformy Azure](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup używa [przyrostowych migawek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) dysków zarządzanych, które przechowują tylko zmiany różnicowe na dysku od momentu ostatniej migawki magazynu HDD w warstwie Standardowa, niezależnie od typu magazynu dysku nadrzędnego. W celu uzyskania dodatkowej niezawodności migawki przyrostowe są domyślnie przechowywane w obszarze strefowo nadmiarowy magazyn (ZRS) w regionach, które obsługują ZRS. Obecnie kopia zapasowa Azure Disk obsługuje operacyjną kopię zapasową dysków zarządzanych, która nie kopiuje kopii zapasowych do magazynu magazynu kopii zapasowych. W związku z tym ustawienia nadmiarowości magazynu kopii zapasowych nie mają zastosowania do punktów odzyskiwania.

1. Na karcie **zasady kopii zapasowej** wybierz zasady tworzenia kopii zapasowych.

   ![Wybieranie zasad kopii zapasowych](./media/backup-managed-disks/choose-backup-policy.png)

1. Na karcie **zasoby** wybierz pozycję **Wybierz dysk platformy Azure**. W okienku kontekstowym po prawej stronie Wybierz dyski, których kopię zapasową chcesz utworzyć.

   ![Wybierz dyski, na których ma zostać utworzona kopia zapasowa](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Portal pozwala wybrać wiele dysków i skonfigurować tworzenie kopii zapasowych, a każdy dysk jest pojedynczym wystąpieniem kopii zapasowej. Obecnie kopia zapasowa Azure Disk obsługuje tylko tworzenie kopii zapasowych pojedynczych dysków. Tworzenie kopii zapasowych w czasie dla wielu dysków dołączonych do dysku wirtualnego nie jest obsługiwane.
   >
   >W przypadku korzystania z portalu można wybrać dyski w ramach tej samej subskrypcji. Jeśli masz kilka dysków, których kopię zapasową chcesz utworzyć, lub jeśli dyski są rozproszone w różnych subskrypcjach, Możesz zautomatyzować za pomocą skryptów.
   >
   >Aby uzyskać więcej informacji na temat dostępności obszaru kopii zapasowej na dysku platformy Azure, obsługiwanych scenariuszy i ograniczeń, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

1. Wybierz **grupę zasobów migawki** i wybierz pozycję **Weryfikuj**. Jest to grupa zasobów, w której utworzona została usługa Azure Backup i która zarządza przyrostowymi migawkami dla magazynu kopii zapasowych. Tożsamość zarządzana jest przypisana z wymaganymi uprawnieniami roli w ramach wymagań wstępnych.

   ![Wybierz grupę zasobów migawek](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Walidacja może potrwać kilka minut, zanim będzie można ukończyć Konfigurowanie przepływu pracy tworzenia kopii zapasowych. Walidacja może zakończyć się niepowodzeniem, jeśli:
   >
   > - dysk nie jest obsługiwany. Zapoznaj się z [matrycą pomocy technicznej](disk-backup-support-matrix.md) w przypadku nieobsługiwanych scenariuszy.
   > - tożsamość zarządzana magazynu kopii zapasowych nie ma prawidłowych przypisań ról na **dysku** do utworzenia kopii zapasowej lub w **grupie zasobów migawek** , w której są przechowywane migawki przyrostowe.

1. Po pomyślnej weryfikacji wybierz pozycję **Przejrzyj i skonfiguruj** , aby skonfigurować kopię zapasową wybranych dysków.

## <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

1. W **magazynie kopii zapasowych** *DemoVault* utworzonym w poprzednim kroku przejdź do pozycji **wystąpienia kopii zapasowej** i wybierz wystąpienie kopii zapasowej.

   ![Wybierz wystąpienie kopii zapasowej](./media/backup-managed-disks/select-backup-instance.png)

1. Na ekranie **wystąpienia kopii zapasowej** można znaleźć następujące informacje:

   - **podstawowe** informacje, w tym nazwa dysku źródłowego, Grupa zasobów migawek, w której są przechowywane migawki przyrostowe, magazyn kopii zapasowych i zasady tworzenia kopii zapasowych.
   - **Stan zadania** przedstawiający podsumowanie operacji tworzenia kopii zapasowej i przywracania oraz ich stanu w ciągu ostatnich siedmiu dni.
   - Lista **punktów przywracania** dla wybranego przedziału czasu.

1. Wybierz pozycję **kopia zapasowa** , aby zainicjować tworzenie kopii zapasowej na żądanie.

   ![Wybierz pozycję Utwórz kopię zapasową teraz](./media/backup-managed-disks/backup-now.png)

1. Wybierz jedną z reguł przechowywania skojarzonych z zasadami tworzenia kopii zapasowych. Ta reguła przechowywania określi czas przechowywania tej kopii zapasowej na żądanie. Wybierz pozycję **Utwórz kopię zapasową teraz** , aby rozpocząć tworzenie kopii zapasowej

   ![Inicjowanie kopii zapasowej](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Śledzenie operacji tworzenia kopii zapasowej

Usługa Azure Backup tworzy zadanie dla zaplanowanych kopii zapasowych lub wyzwala operację tworzenia kopii zapasowej na żądanie dla śledzenia. Aby wyświetlić stan zadania tworzenia kopii zapasowej:

1. Przejdź do ekranu **wystąpienia kopii zapasowej** . Pokazuje pulpit nawigacyjny zadań z operacją i stanem w ciągu ostatnich siedmiu dni.

   ![Pulpit nawigacyjny zadań](./media/backup-managed-disks/jobs-dashboard.png)

1. Aby wyświetlić stan operacji tworzenia kopii zapasowej, wybierz pozycję **Wyświetl wszystko** , aby wyświetlić bieżące i przeszłe zadania tego wystąpienia kopii zapasowej.

   ![Wybierz pozycję Wyświetl wszystko](./media/backup-managed-disks/view-all.png)

1. Przejrzyj listę zadań tworzenia kopii zapasowej i przywracania oraz ich stan. Wybierz zadanie z listy zadań, aby wyświetlić szczegóły zadania.

   ![Wybierz zadanie, aby wyświetlić szczegóły](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie Managed Disks platformy Azure](restore-managed-disks.md)
