---
title: Jak przenieść magazyny Recovery Services Azure Backup
description: Instrukcje dotyczące przenoszenia magazynu Recovery Services w ramach subskrypcji i grup zasobów platformy Azure.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 4f75bec533181b29625fb0a10cc26d03f2875036
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466375"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Przenoszenie magazynu Recovery Services w ramach subskrypcji i grup zasobów platformy Azure

W tym artykule wyjaśniono, jak przenieść magazyn Recovery Services skonfigurowany pod kątem Azure Backup między subskrypcjami platformy Azure lub z inną grupą zasobów w ramach tej samej subskrypcji. Aby przenieść magazyn Recovery Services, można użyć Azure Portal lub programu PowerShell.

## <a name="supported-regions"></a>Obsługiwane regiony

Obsługiwane są wszystkie regiony publiczne i suwerenne regiony, z wyjątkiem Francji środkowej, Francja Południowa, Niemcy Wschodnie, Niemcy środkowe, Chiny Północne, Chiny North2, Chiny Wschodnie i Chiny 2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Wymagania wstępne dotyczące przeniesienia magazynu Recovery Services

- Podczas przenoszenia magazynu między grupami zasobów zarówno źródłowa, jak i docelowa Grupa zasobów są blokowane, co uniemożliwia wykonywanie operacji zapisu i usuwania. Więcej informacji znajduje się w tym [artykule](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Tylko subskrypcja administratora ma uprawnienia do przenoszenia magazynu.
- W przypadku przeniesienia magazynów między subskrypcjami subskrypcja docelowa musi znajdować się w tej samej dzierżawie co subskrypcja źródłowa, a jej stan musi być włączony. Aby przenieść magazyn do innego katalogu usługi Azure AD, zobacz [transfer subskrypcji do innego katalogu](../role-based-access-control/transfer-subscription.md) i [Magazyn usługi odzyskiwania — często zadawane pytania](backup-azure-backup-faq.md#recovery-services-vault).
- Musisz mieć uprawnienia do wykonywania operacji zapisu w docelowej grupie zasobów.
- Przeniesienie magazynu powoduje jedynie zmianę grupy zasobów. Magazyn Recovery Services będzie znajdował się w tej samej lokalizacji i nie można go zmienić.
- W danym momencie można przenieść tylko jeden magazyn Recovery Services na region.
- Jeśli maszyna wirtualna nie jest przenoszona do magazynu Recovery Services między subskrypcjami ani do nowej grupy zasobów, bieżące punkty odzyskiwania maszyny wirtualnej pozostaną nienaruszone w magazynie do momentu ich wygaśnięcia.
- Niezależnie od tego, czy maszyna wirtualna jest przenoszona z magazynem, czy nie, możesz zawsze przywrócić maszynę wirtualną z zachowanej historii kopii zapasowych w magazynie.
- Azure Disk Encryption wymaga, aby Magazyn kluczy i maszyny wirtualne znajdowały się w tym samym regionie i subskrypcji platformy Azure.
- Aby przenieść maszynę wirtualną z dyskami zarządzanymi, zobacz ten [artykuł](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Opcje przeniesienia zasobów wdrożonych za pomocą modelu klasycznego różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji, czy na nową subskrypcję. Więcej informacji znajduje się w tym [artykule](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Zasady tworzenia kopii zapasowych zdefiniowane dla magazynu są zachowywane po przejściu magazynu między subskrypcjami lub nową grupą zasobów.
- Można przenieść tylko magazyn zawierający dowolne z następujących typów elementów kopii zapasowej. Wszelkie elementy kopii zapasowej typów, które nie są wymienione poniżej, muszą zostać zatrzymane, a dane zostaną trwale usunięte przed przeniesieniem magazynu.
  - Azure Virtual Machines
  - Agent Microsoft Azure Recovery Services (MARS)
  - Serwer Microsoft Azure Backup (serwera usługi MAB)
  - Program Data Protection Manager (DPM)
- W przypadku przenoszenia magazynu zawierającego dane kopii zapasowej maszyny wirtualnej między subskrypcjami należy przenieść maszyny wirtualne do tej samej subskrypcji i użyć tej samej nazwy docelowej grupy zasobów maszyny wirtualnej (ponieważ była w starej subskrypcji) w celu kontynuowania wykonywania kopii zapasowych.

> [!NOTE]
> Przeniesienie Recovery Services magazynów dla Azure Backup w regionach platformy Azure nie jest obsługiwane.<br><br>
> W przypadku skonfigurowania maszyn wirtualnych (Azure IaaS, Hyper-V, VMware) lub fizycznych na potrzeby odzyskiwania po awarii przy użyciu **Azure Site Recovery** Operacja przenoszenia zostanie zablokowana. Jeśli chcesz przenieść magazyny dla Azure Site Recovery, zapoznaj się z [tym artykułem](../site-recovery/move-vaults-across-regions.md) , aby dowiedzieć się więcej o przenoszeniu magazynów ręcznie.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Przenoszenie magazynu Recovery Services do innej grupy zasobów przy użyciu Azure Portal

Aby przenieść magazyn Recovery Services i powiązane z nim zasoby do innej grupy zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz listę **magazynów Recovery Services** i wybierz magazyn, który chcesz przenieść. Gdy zostanie otwarty pulpit nawigacyjny magazynu, zostanie on wyświetlony jak pokazano na poniższej ilustracji.

   ![Otwórz magazyn Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Jeśli nie widzisz informacji **podstawowe** dla magazynu, wybierz ikonę listy rozwijanej. Należy teraz zobaczyć podstawowe informacje dotyczące magazynu.

   ![Karta informacji o Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przegląd magazynu wybierz pozycję **Zmień** obok **grupy zasobów**, aby otworzyć okienko **przenoszenie zasobów** .

   ![Zmień grupę zasobów](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. W okienku **przenoszenie zasobów** dla wybranego magazynu zalecamy przeniesienie opcjonalnych powiązanych zasobów, zaznaczając pole wyboru, jak pokazano na poniższej ilustracji.

   ![Przenieś subskrypcję](./media/backup-azure-move-recovery-services/move-resource.png)

5. Aby dodać docelową grupę zasobów, z listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub wybierz opcję **Utwórz nową grupę** .

   ![Utwórz zasób](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Po dodaniu grupy zasobów upewnij **się, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działały, dopóki nie zaktualizuję ich do używania nowych identyfikatorów zasobów** , a następnie wybierz **przycisk OK** , aby zakończyć przenoszenie magazynu.

   ![Komunikat z potwierdzeniem](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Przenoszenie magazynu Recovery Services do innej subskrypcji za pomocą Azure Portal

Magazyn Recovery Services i powiązane z nim zasoby można przenieść do innej subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz listę magazynów Recovery Services i wybierz magazyn, który chcesz przenieść. Po otwarciu pulpitu nawigacyjnego magazynu pojawia się on jak pokazano na poniższej ilustracji.

    ![Otwórz magazyn Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Jeśli nie widzisz informacji **podstawowe** dla magazynu, wybierz ikonę listy rozwijanej. Należy teraz zobaczyć podstawowe informacje dotyczące magazynu.

    ![Karta informacji o Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. W menu przegląd magazynu wybierz pozycję **Zmień** obok pozycji **subskrypcja**, aby otworzyć okienko **przenoszenie zasobów** .

   ![Zmień subskrypcję](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Wybierz zasoby do przeniesienia. w tym miejscu zalecamy użycie opcji **Zaznacz wszystko** , aby wybrać wszystkie zasoby opcjonalne.

   ![Przenieś zasób](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Wybierz subskrypcję docelową z listy rozwijanej **subskrypcja** , w której chcesz przenieść magazyn.
6. Aby dodać docelową grupę zasobów, z listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub wybierz opcję **Utwórz nową grupę** .

   ![Dodaj subskrypcję](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Zaznacz opcję **rozumiem, że narzędzia i skrypty skojarzone z przeniesionymi zasobami nie będą działały do momentu zaktualizowania ich do korzystania z nowych identyfikatorów zasobów** , aby potwierdzić, a następnie wybierz przycisk **OK**.

> [!NOTE]
> Kopia zapasowa między subskrypcjami (magazyn RS i chronione maszyny wirtualne znajdują się w różnych subskrypcjach) nie jest obsługiwanym scenariuszem. Ponadto opcja nadmiarowości magazynu z lokalnego nadmiarowego magazynu (LRS) na globalnie nadmiarowy magazyn (GRS) i na odwrót nie może zostać zmodyfikowana podczas operacji przenoszenia magazynu.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Przenoszenie magazynu Recovery Services przy użyciu programu PowerShell

Aby przenieść magazyn Recovery Services do innej grupy zasobów, użyj `Move-AzureRMResource` polecenia cmdlet. `Move-AzureRMResource` wymaga nazwy zasobu i typu zasobu. Oba te elementy można pobrać z `Get-AzureRmRecoveryServicesVault` polecenia cmdlet.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Aby przenieść zasoby do innej subskrypcji, Dołącz `-DestinationSubscriptionId` parametr.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Po wykonaniu powyższych poleceń cmdlet zostanie wyświetlony monit o potwierdzenie, że chcesz przenieść określone zasoby. Wpisz **Y** , aby potwierdzić. Po pomyślnej weryfikacji zasób jest przenoszony.

## <a name="use-cli-to-move-recovery-services-vault"></a>Przenoszenie magazynu Recovery Services przy użyciu interfejsu wiersza polecenia

Aby przenieść magazyn Recovery Services do innej grupy zasobów, użyj następującego polecenia cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Aby przejść do nowej subskrypcji, podaj `--destination-subscription-id` parametr.

## <a name="post-migration"></a>Zadania po migracji

1. Ustaw/Sprawdź kontrolę dostępu dla grup zasobów.  
2. Funkcję raportowania i monitorowania kopii zapasowej należy skonfigurować ponownie dla magazynu po zakończeniu przenoszenia. Poprzednia konfiguracja zostanie utracona podczas operacji przenoszenia.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Przenieś maszynę wirtualną platformy Azure do innego magazynu usługi Recovery Service. 

Jeśli chcesz przenieść maszynę wirtualną platformy Azure z włączoną usługą Azure Backup, masz dwie możliwości. Są one zależne od wymagań firmy:

- [Nie trzeba zachować poprzedniej kopii zapasowej danych](#dont-need-to-preserve-previous-backed-up-data)
- [Należy zachować poprzednie dane kopii zapasowej](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nie trzeba zachować poprzedniej kopii zapasowej danych

Aby chronić obciążenia w nowym magazynie, bieżąca Ochrona i dane muszą zostać usunięte w starym magazynie, a kopia zapasowa zostanie ponownie skonfigurowana.

>[!WARNING]
>Następująca operacja jest destrukcyjne i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

**Zatrzymaj i Usuń bieżącą ochronę w starym magazynie:**

1. Wyłącz usuwanie nietrwałe we właściwościach magazynu. Wykonaj następujące [kroki](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) , aby wyłączyć usuwanie nietrwałe.

2. Zatrzymaj ochronę i usuń kopie zapasowe z bieżącego magazynu. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **elementy kopii zapasowej**. Elementy wymienione w tym miejscu, które należy przenieść do nowego magazynu, muszą zostać usunięte wraz z danymi kopii zapasowych. Zobacz jak [usunąć chronione elementy w chmurze](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) i [usunąć chronione elementy lokalnie](backup-azure-delete-vault.md#delete-protected-items-on-premises).

3. Jeśli planujesz przenoszenie programu AFS (udziały plików platformy Azure), serwerów SQL lub serwerów SAP HANA, należy również je wyrejestrować. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **infrastruktura zapasowa**. Zobacz jak [wyrejestrować serwer SQL](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [wyrejestrować konto magazynu skojarzone z udziałami plików platformy Azure](manage-afs-backup.md#unregister-a-storage-account)i [wyrejestrować wystąpienie SAP HANA](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. Po usunięciu ze starego magazynu, kontynuuj konfigurowanie kopii zapasowych dla obciążenia w nowym magazynie.

### <a name="must-preserve-previous-backed-up-data"></a>Należy zachować poprzednie dane kopii zapasowej

Jeśli potrzebujesz zachować bieżące chronione dane w starym magazynie i kontynuować ochronę w nowym magazynie, dostępne są ograniczone opcje dla niektórych obciążeń:

- W przypadku usługi MARS można [zatrzymać ochronę z zachowaniem zachowanych danych](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) i zarejestrować agenta w nowym magazynie.

  - Usługa Azure Backup będzie nadal utrzymywać wszystkie istniejące punkty odzyskiwania starego magazynu.
  - Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w starym magazynie.
  - Będzie można przywrócić dane z kopii zapasowej tylko dla niewygasłych punktów odzyskiwania w starym magazynie.
  - Należy utworzyć nową początkową replikę danych w nowym magazynie.

- W przypadku maszyny wirtualnej platformy Azure można [zatrzymać ochronę z zachowaniem zachowania danych](backup-azure-manage-vms.md#stop-protecting-a-vm) dla maszyny wirtualnej w starym magazynie, przenieść maszynę wirtualną do innej grupy zasobów, a następnie włączyć ochronę maszyny wirtualnej w nowym magazynie. Zapoznaj się ze [wskazówkami i ograniczeniami](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) dotyczącymi przeniesienia maszyny wirtualnej do innej grupy zasobów.

  Maszyna wirtualna może być chroniona tylko w jednym magazynie naraz. Jednak maszyna wirtualna w nowej grupie zasobów może być chroniona w nowym magazynie, ponieważ jest traktowana jako inna maszyna wirtualna.

  - Usługa Azure Backup zachowa te punkty odzyskiwania, których kopia zapasowa została utworzona w starym magazynie.
  - Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w starym magazynie (zobacz [Azure Backup cennika](azure-backup-pricing.md) , aby uzyskać szczegółowe informacje).
  - W razie potrzeby będzie można przywrócić maszynę wirtualną z starego magazynu.
  - Pierwsza kopia zapasowa w nowym magazynie maszyny wirtualnej w nowym zasobie będzie repliką początkową.

## <a name="next-steps"></a>Następne kroki

Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcjami.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).