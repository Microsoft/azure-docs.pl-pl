---
title: Jak przenieść magazyny Azure Backup Recovery Services
description: Instrukcje dotyczące przenoszenia magazynu usługi Recovery Services między subskrypcjami i grupami zasobów platformy Azure.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 49d6782af5a9c946eaf92147dab22e4605195d89
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514771"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Przenoszenie magazynu usługi Recovery Services między subskrypcjami platformy Azure i grupami zasobów

W tym artykule wyjaśniono, jak przenieść magazyn usługi Recovery Services skonfigurowany do Azure Backup między subskrypcjami platformy Azure lub do innej grupy zasobów w tej samej subskrypcji. Aby przenieść magazyn usługi Recovery Services, możesz użyć programu Azure Portal lub Programu PowerShell.

## <a name="supported-regions"></a>Obsługiwane regiony

Obsługiwane są wszystkie regiony publiczne i suwerenne, z wyjątkiem Francja Środkowa, Francja Południowa, Niemcy Północno-Wschodnie, Niemcy Środkowe, Chiny Północne, Chiny Północne2, Chiny Wschodnie Chiny Wschodnie2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Wymagania wstępne dotyczące przenoszenia magazynu usługi Recovery Services

- Podczas przenoszenia magazynu między grupami zasobów zarówno źródłową, jak i docelową grupę zasobów są blokowane, co uniemożliwia operacje zapisu i usuwania. Więcej informacji znajduje się w tym [artykule](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Tylko subskrypcja administratora ma uprawnienia do przenoszenia magazynu.
- W przypadku przenoszenia magazynów między subskrypcjami subskrypcja docelowa musi znajdować się w tej samej dzierżawie co subskrypcja źródłowa, a jej stan musi być włączony. Aby przenieść magazyn do innego katalogu usługi Azure AD, zobacz [Transfer subscription to a different directory](../role-based-access-control/transfer-subscription.md) (Przenoszenie subskrypcji do innego katalogu) i Recovery Service vault FAQ (Często zadawane pytania na temat przenoszenia subskrypcji do innego [katalogu).](/backup-azure-backup-faq.yml#recovery-services-vault)
- Musisz mieć uprawnienia do wykonywania operacji zapisu na docelowej grupie zasobów.
- Przeniesienie magazynu zmienia tylko grupę zasobów. Magazyn usługi Recovery Services będzie znajdować się w tej samej lokalizacji i nie można go zmienić.
- Jednocześnie można przenieść tylko jeden magazyn usługi Recovery Services na region.
- Jeśli maszyna wirtualna nie zostanie przesunięta z magazynem usługi Recovery Services między subskrypcjami lub do nowej grupy zasobów, bieżące punkty odzyskiwania maszyny wirtualnej pozostaną niezmienione w magazynie do momentu ich wygaśnięcia.
- Niezależnie od tego, czy maszyna wirtualna została przeniesiona z magazynem, zawsze możesz przywrócić maszynę wirtualną z zachowanej historii kopii zapasowych w magazynie.
- Usługa Azure Disk Encryption wymaga, aby magazyn kluczy i maszyny wirtualne znajdowały się w tym samym regionie i subskrypcji platformy Azure.
- Aby przenieść maszynę wirtualną z dyskami zarządzanymi, zobacz ten [artykuł.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- Opcje przenoszenia zasobów wdrożonych za pomocą modelu klasycznego różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji, czy do nowej subskrypcji. Więcej informacji znajduje się w tym [artykule](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Zasady tworzenia kopii zapasowych zdefiniowane dla magazynu są zachowywane po tym, jak magazyn zostanie przeniesiony między subskrypcjami lub do nowej grupy zasobów.
- Można przenieść tylko magazyn zawierający dowolny z następujących typów elementów kopii zapasowej. Wszystkie elementy kopii zapasowej typów, które nie zostały wymienione poniżej, muszą zostać zatrzymane, a dane zostaną trwale usunięte przed przeniesieniem magazynu.
  - Azure Virtual Machines
  - Microsoft Azure usługi Recovery Services (MARS)
  - Microsoft Azure Backup Server (MABS)
  - Program Data Protection Manager (DPM)
- Jeśli przeniesiesz magazyn zawierający dane kopii zapasowej maszyny wirtualnej między subskrypcjami, musisz przenieść maszyny wirtualne do tej samej subskrypcji i użyć tej samej nazwy docelowej grupy zasobów maszyny wirtualnej (co w starej subskrypcji), aby kontynuować tworzenie kopii zapasowych.

> [!NOTE]
> Przenoszenie magazynów usługi Recovery Services dla Azure Backup między regionami świadczenia usługi Azure nie jest obsługiwane.<br><br>
> Jeśli skonfigurowano maszyny wirtualne (Azure IaaS, Hyper-V, VMware) lub maszyny fizyczne do odzyskiwania po awarii przy użyciu programu **Azure Site Recovery,** operacja przenoszenia zostanie zablokowana. Jeśli chcesz przenieść magazyny dla Azure Site Recovery, [](../site-recovery/move-vaults-across-regions.md) zapoznaj się z tym artykułem, aby dowiedzieć się więcej o ręcznym przenoszeniu magazynów.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Użyj Azure Portal, aby przenieść magazyn usługi Recovery Services do innej grupy zasobów

Aby przenieść magazyn usługi Recovery Services i skojarzone z nim zasoby do innej grupy zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz listę **magazynów usługi Recovery Services i** wybierz magazyn, który chcesz przenieść. Po otworze pulpitu nawigacyjnego magazynu zostanie on wyświetlony, jak pokazano na poniższej ilustracji.

   ![Otwieranie magazynu usługi Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Jeśli nie widzisz informacji **podstawowych** dla magazynu, wybierz ikonę listy rozwijanej. Powinny zostać teraz wyświetlony podstawowe informacje dotyczące magazynu.

   ![Karta Podstawowe informacje](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przeglądu magazynu wybierz pozycję **Zmień** obok pozycji **Grupa zasobów,** aby otworzyć **okienko Przenoszenie** zasobów.

   ![Zmienianie grupy zasobów](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. W **okienku Przenoszenie** zasobów dla wybranego magazynu zaleca się przeniesienie opcjonalnych powiązanych zasobów, zaznaczając pole wyboru, jak pokazano na poniższej ilustracji.

   ![Przenoszenie subskrypcji](./media/backup-azure-move-recovery-services/move-resource.png)

5. Aby dodać docelową grupę  zasobów, z listy rozwijanej Grupa zasobów wybierz istniejącą grupę zasobów lub wybierz opcję Utwórz **nową** grupę.

   ![Tworzenie zasobu](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po dodaniu grupy  zasobów upewnij się, że rozumiem, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizuję ich w celu używania nowych identyfikatorów zasobów, a następnie wybierz przycisk **OK,** aby ukończyć przenoszenie magazynu.

   ![Komunikat potwierdzający](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Użyj Azure Portal, aby przenieść magazyn usługi Recovery Services do innej subskrypcji

Magazyn usługi Recovery Services i skojarzone z nim zasoby można przenieść do innej subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz listę magazynów usługi Recovery Services i wybierz magazyn, który chcesz przenieść. Po otworze pulpitu nawigacyjnego magazynu zostanie wyświetlony, jak pokazano na poniższej ilustracji.

    ![Otwieranie magazynu usługi Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Jeśli nie widzisz informacji **podstawowych** dla magazynu, wybierz ikonę listy rozwijanej. Powinny zostać teraz wyświetlony podstawowe informacje dotyczące magazynu.

    ![Karta Podstawowe informacje](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przeglądu magazynu wybierz pozycję **Zmień** obok pozycji **Subskrypcja,** aby otworzyć **okienko Przenoszenie** zasobów.

   ![Zmienianie subskrypcji](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Wybierz zasoby, które mają zostać przeniesione.  W tym miejscu zalecamy wybranie wszystkich wymienionych zasobów opcjonalnych za pomocą opcji Zaznacz wszystko.

   ![przenoszenie zasobu](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Wybierz subskrypcję docelową **z listy** rozwijanej Subskrypcja, do której chcesz przenieść magazyn.
6. Aby dodać docelową grupę  zasobów, z listy rozwijanej Grupa zasobów wybierz istniejącą grupę zasobów lub wybierz opcję Utwórz **nową** grupę.

   ![Dodaj subskrypcję](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Zaznacz **pole wyboru Rozumiem, że** narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizuję ich, aby użyć nowej opcji identyfikatorów zasobów w celu potwierdzenia, a następnie wybierz przycisk **OK.**

> [!NOTE]
> Tworzenie kopii zapasowej między subskrypcjami (magazyn RS i chronione maszyny wirtualne znajdują się w różnych subskrypcjach) nie jest obsługiwanym scenariuszem. Ponadto opcji nadmiarowości magazynu z magazynu lokalnie nadmiarowego (LRS) do magazynu nadmiarowego globalnego (GRS) i odwrotnie nie można modyfikować podczas operacji przenoszenia magazynu.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Przenoszenie magazynu usługi Recovery Services przy użyciu programu PowerShell

Aby przenieść magazyn usługi Recovery Services do innej grupy zasobów, użyj `Move-AzureRMResource` polecenia cmdlet . `Move-AzureRMResource` Wymaga nazwy zasobu i typu zasobu. Oba te polecenia można pobrać za pomocą `Get-AzureRmRecoveryServicesVault` polecenia cmdlet .

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Aby przenieść zasoby do innej subskrypcji, dołącz `-DestinationSubscriptionId` parametr .

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po wykonaniu powyższych polecenia cmdlet zostanie poproszony o potwierdzenie przeniesienia określonych zasobów. Wpisz **Y,** aby potwierdzić. Po pomyślnej weryfikacji zasób zostanie przeniesiony.

## <a name="use-cli-to-move-recovery-services-vault"></a>Przenoszenie magazynu usługi Recovery Services przy użyciu interfejsu wiersza polecenia

Aby przenieść magazyn usługi Recovery Services do innej grupy zasobów, użyj następującego polecenia cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Aby przejść do nowej subskrypcji, podaj `--destination-subscription-id` parametr .

## <a name="post-migration"></a>Zadania po migracji

1. Ustaw/zweryfikuj kontrole dostępu dla grup zasobów.  
2. Po zakończeniu przenoszenia należy ponownie skonfigurować funkcję raportowania i monitorowania kopii zapasowych dla magazynu. Poprzednia konfiguracja zostanie utracona podczas operacji przenoszenia.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Przenieś maszynę wirtualną platformy Azure do innego magazynu usługi odzyskiwania. 

Jeśli chcesz przenieść maszynę wirtualną platformy Azure z włączoną usługą Azure Backup, masz do wyboru dwie opcje. Zależą one od wymagań biznesowych:

- [Nie trzeba zachowywać poprzednich danych kopii zapasowej](#dont-need-to-preserve-previous-backed-up-data)
- [Musi zachować poprzednie dane kopii zapasowej](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nie trzeba zachowywać poprzednich danych kopii zapasowej

Aby chronić obciążenia w nowym magazynie, należy usunąć bieżącą ochronę i dane w starym magazynie, a następnie ponownie skonfigurować kopię zapasową.

>[!WARNING]
>Następująca operacja jest destruktywna i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

**Zatrzymaj i usuń bieżącą ochronę w starym magazynie:**

1. Wyłącz usuwanie nie softne we właściwościach magazynu. Wykonaj [następujące kroki,](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) aby wyłączyć usuwanie nie soft.

2. Zatrzymaj ochronę i usuń kopie zapasowe z bieżącego magazynu. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **Elementy kopii zapasowej.** Elementy wymienione w tym miejscu, które należy przenieść do nowego magazynu, muszą zostać usunięte wraz z danymi kopii zapasowej. Zobacz, jak [usuwać chronione elementy w chmurze i](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) [usuwać chronione elementy lokalnie.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

3. Jeśli planujesz przeniesienie usług AFS (udziałów plików platformy Azure), serwerów SQL lub serwerów SAP HANA, musisz również wyrejestrować te serwery. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **Infrastruktura kopii zapasowych.** Zobacz, jak [wyrejestrować](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)serwer SQL, wyrejestrować konto magazynu skojarzone z udziałami plików platformy [Azure](manage-afs-backup.md#unregister-a-storage-account)i wyrejestrować [SAP HANA wystąpienia](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. Po usunięciu ich ze starego magazynu kontynuuj konfigurowanie kopii zapasowych obciążenia w nowym magazynie.

### <a name="must-preserve-previous-backed-up-data"></a>Musi zachować poprzednie dane kopii zapasowej

Jeśli chcesz zachować bieżące chronione dane w starym magazynie i kontynuować ochronę w nowym magazynie, istnieją ograniczone opcje dla niektórych obciążeń:

- W przypadku usługi MARS można [zatrzymać ochronę z zachowaniem danych](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) i zarejestrować agenta w nowym magazynie.

  - Azure Backup usługa będzie nadal zachowywać wszystkie istniejące punkty odzyskiwania starego magazynu.
  - Aby zachować punkty odzyskiwania w starym magazynie, musisz zapłacić.
  - Będzie można przywrócić dane kopii zapasowej tylko dla niewydajnych punktów odzyskiwania w starym magazynie.
  - W nowym magazynie należy utworzyć nową początkową replikę danych.

- W przypadku maszyny wirtualnej platformy Azure możesz zatrzymać ochronę, zachowując dane maszyny wirtualnej w starym magazynie, przenieść maszynę wirtualną do innej grupy zasobów, a następnie chronić maszynę wirtualną w nowym magazynie. [](backup-azure-manage-vms.md#stop-protecting-a-vm) Zobacz [wskazówki i ograniczenia dotyczące](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) przenoszenia maszyny wirtualnej do innej grupy zasobów.

  Maszyna wirtualna może być chroniona tylko w jednym magazynie na raz. Jednak maszyna wirtualna w nowej grupie zasobów może być chroniona w nowym magazynie, ponieważ jest uważana za inną maszynę wirtualną.

  - Azure Backup zachowa punkty odzyskiwania, których kopię zapasową zapasową w starym magazynie.
  - Aby zachować punkty odzyskiwania w starym magazynie, musisz zapłacić (zobacz cennik usługi [Azure Backup,](azure-backup-pricing.md) aby uzyskać szczegółowe informacje).
  - W razie potrzeby będzie można przywrócić maszynę wirtualną ze starego magazynu.
  - Pierwszą kopią zapasową w nowym magazynie maszyny wirtualnej w nowym zasobie będzie replika początkowa.

## <a name="next-steps"></a>Następne kroki

Między grupami zasobów i subskrypcjami można przenosić wiele różnych typów zasobów.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).