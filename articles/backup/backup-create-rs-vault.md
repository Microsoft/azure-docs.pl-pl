---
title: Tworzenie magazynów Recovery Services
description: W tym artykule dowiesz się, jak utworzyć magazyny Recovery Services, w których są przechowywane kopie zapasowe i punkty odzyskiwania.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: f1f1a66bd6e0fbd28be59b0211a1ac17f030022e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298622"
---
# <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn Recovery Services zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-create-rs-vault/click-all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Lista filtrów zasobów zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję **magazyny Recovery Services**.

    ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Na pulpicie nawigacyjnym **Recovery Services magazynów** wybierz pozycję **Dodaj**.

    ![Dodawanie magazynu Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Zostanie otwarte okno dialogowe **magazyn Recovery Services** . Podaj wartości dla **nazwy**, **subskrypcji**, **grupy zasobów**i **lokalizacji**.

    ![Konfigurowanie magazynu Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nazwa**: wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa znaki, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i zawierać tylko litery, cyfry i łączniki.
   - **Subskrypcja**: wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, Użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji, które są dostępne tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure.
   - **Grupa zasobów**: Użyj istniejącej grupy zasobów lub Utwórz nową. Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Azure Resource Manager przegląd](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Lokalizacja**: Wybierz region geograficzny magazynu. Aby utworzyć magazyn do ochrony maszyn wirtualnych, magazyn **musi** znajdować się w tym samym regionie co maszyny wirtualne.

      > [!IMPORTANT]
      > Jeśli nie masz pewności co do lokalizacji maszyny wirtualnej, Zamknij okno dialogowe. Przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w kilku regionach, Utwórz magazyn Recovery Services w każdym regionie. Utwórz magazyn w pierwszej lokalizacji, zanim utworzysz magazyn dla innej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn Recovery Services i usługa Azure Backup obsługują automatycznie.
      >
      >

5. Gdy wszystko będzie gotowe do utworzenia magazynu Recovery Services, wybierz pozycję **Utwórz**.

    ![Tworzenie magazynu Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Utworzenie magazynu Recovery Services może chwilę potrwać. Monitoruj powiadomienia o stanie w obszarze **powiadomień** w prawym górnym rogu portalu. Po utworzeniu magazynu będzie on widoczny na liście magazynów Recovery Services. Jeśli Twój magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

     ![Odświeżanie listy magazynów kopii zapasowych](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ustawianie nadmiarowości magazynu

Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania magazynu.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn. W sekcji **Ustawienia** kliknij pozycję **Właściwości**.
2. W obszarze **Właściwości**, w obszarze **Konfiguracja kopii zapasowej**, kliknij przycisk **Aktualizuj**.

3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Zalecamy, aby Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, Kontynuuj, aby użyć domyślnego ustawienia **geograficznie nadmiarowego** .
   - Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
   - Dowiedz się więcej o nadmiarowości [geograficznym](../storage/common/storage-redundancy-grs.md) i [lokalnym](../storage/common/storage-redundancy-lrs.md) .

> [!NOTE]
> Przed skonfigurowaniem kopii zapasowych w magazynie należy zmienić **Typ replikacji magazynu** (lokalnie nadmiarowy lub geograficznie nadmiarowy) dla magazynu usługi Recovery Services. Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona i nie można zmienić **typu replikacji magazynu**.

## <a name="set-cross-region-restore"></a>Ustaw przywracanie między regionami

Jako jedna z opcji przywracania, przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest [sparowanym regionem platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Ta opcja umożliwia:

- Przeprowadzaj drążenie w przypadku spełnienia wymagań dotyczących inspekcji lub zgodności
- Przywróć maszynę wirtualną lub jej dysk w przypadku awarii w regionie podstawowym.

Aby wybrać tę funkcję, wybierz pozycję **Włącz przywracanie między regionami** w bloku **Konfiguracja kopii zapasowej** .

W przypadku tego procesu istnieją wpływ na ceny, w jakim znajduje się na poziomie magazynu.

>[!NOTE]
>Przed rozpoczęciem:
>
>- Zapoznaj się z [matrycą pomocy technicznej](backup-support-matrix.md#cross-region-restore) , aby zapoznać się z listą obsługiwanych typów i regionów zarządzanych.
>- Funkcja przywracania między regionami (CRR) jest teraz wyświetlana we wszystkich publicznych regionach platformy Azure.
>- CRR to funkcja wyboru poziomu magazynu dla dowolnego magazynu GRS (domyślnie wyłączona).
>- Po przypisaniu elementów kopii zapasowych w regionach pomocniczych może upłynąć do 48 godzin.
>- Obecnie CRR jest obsługiwana tylko dla typu zarządzania kopiami zapasowymi — ARM Azure VM (klasyczna maszyna wirtualna platformy Azure nie będzie obsługiwana).  Gdy dodatkowe typy zarządzania obsługują CRR, zostaną one **automatycznie** zarejestrowane.

### <a name="configure-cross-region-restore"></a>Konfigurowanie przywracania między regionami

Magazyn utworzony za pomocą nadmiarowości GRS zawiera opcję konfigurowania funkcji przywracania między regionami. Każdy magazyn GRS będzie miał baner, który zostanie połączony z dokumentacją. Aby skonfigurować CRR dla magazynu, przejdź do bloku Konfiguracja kopii zapasowej, który zawiera opcję włączenia tej funkcji.

 ![Transparent konfiguracji kopii zapasowej](./media/backup-azure-arm-restore-vms/banner.png)

1. W portalu przejdź do obszaru Recovery Services ustawienia > magazynu > właściwości.
2. Aby włączyć tę funkcję, kliknij pozycję **Włącz przywracanie między regionami w tym magazynie** .

   ![Przed kliknięciem przycisku Włącz przywracanie między regionami w tym magazynie](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Po kliknięciu przycisku Włącz przywracanie między regionami w tym magazynie](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Dowiedz się [, jak wyświetlać elementy kopii zapasowej w regionie pomocniczym](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Dowiedz się [, jak przywrócić w regionie pomocniczym](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Dowiedz się, jak [monitorować zadania przywracania regionu pomocniczego](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Modyfikowanie ustawień domyślnych

Zdecydowanie zalecamy przejrzenie ustawień domyślnych dla **typu replikacji magazynu** i **ustawień zabezpieczeń** przed skonfigurowaniem kopii zapasowych w magazynie.

- Domyślnie **Typ replikacji magazynu** jest ustawiony jako **Geograficznie nadmiarowy**. Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona. Wykonaj następujące [kroki](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) , aby przejrzeć i zmodyfikować ustawienia.

- Funkcja **usuwania nietrwałego** jest **włączana** domyślnie dla nowo utworzonych magazynów w celu ochrony danych kopii zapasowej przed przypadkowym lub złośliwym usunięciem. Wykonaj następujące [kroki](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete) , aby przejrzeć i zmodyfikować ustawienia.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny Recovery Services.
[Dowiedz się więcej o](backup-azure-delete-vault.md) Usuń magazyny Recovery Services.
