---
title: Jak przenieść magazyny Recovery Services Azure Backup
description: Instrukcje dotyczące przenoszenia magazynu usług Recovery Services między subskrypcjami i grupami zasobów platformy Azure.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 4751a6068904ef03a4d30d322a285e4074476b70
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650917"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Przenoszenie magazynu Recovery Services w ramach subskrypcji i grup zasobów platformy Azure

W tym artykule wyjaśniono, jak przenieść magazyn Recovery Services skonfigurowany pod kątem Azure Backup między subskrypcjami platformy Azure lub z inną grupą zasobów w ramach tej samej subskrypcji. Aby przenieść magazyn Recovery Services, można użyć Azure Portal lub programu PowerShell.

## <a name="supported-regions"></a>Obsługiwane regiony

Przenoszenie zasobów dla magazynu Recovery Services jest obsługiwane w Australii Wschodniej, Australia Południowo-Wschodnia, Kanada środkowa, Kanada Wschodnia, Południowe Azja Wschodnia, Azja Wschodnia, środkowe stany USA, Północno-środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Południowo-środkowe stany USA i zachodnie stany USA, Europa Zachodnia, Japonia Zachodnia, zachodniej USA 2, Europa Północna, Indie Południowe , Europa Zachodnia, Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki, Południowe Zjednoczone Królestwo i Zachodnie Zjednoczone Królestwo.

## <a name="unsupported-regions"></a>Nieobsługiwane regiony

Francja środkowa, Francja Południowa, Niemcy Południowe, Niemcy środkowe, US Gov Iowa, Chiny Północne, Chiny North2, Chiny Wschodnie Chiny 2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Wymagania wstępne dotyczące przeniesienia magazynu Recovery Services

- Podczas przenoszenia magazynu między grupami zasobów zarówno źródłowa, jak i docelowa Grupa zasobów są blokowane, co uniemożliwia wykonywanie operacji zapisu i usuwania. Więcej informacji znajduje się w tym [artykule](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Tylko subskrypcja administratora ma uprawnienia do przenoszenia magazynu.
- W przypadku przeniesienia magazynów między subskrypcjami subskrypcja docelowa musi znajdować się w tej samej dzierżawie co subskrypcja źródłowa, a jej stan powinien być włączony.
- Musisz mieć uprawnienia do wykonywania operacji zapisu w docelowej grupie zasobów.
- Przeniesienie magazynu powoduje jedynie zmianę grupy zasobów. Magazyn Recovery Services będzie znajdował się w tej samej lokalizacji i nie można go zmienić.
- W danym momencie można przenieść tylko jeden magazyn Recovery Services na region.
- Jeśli maszyna wirtualna nie jest przenoszona do magazynu Recovery Services między subskrypcjami ani do nowej grupy zasobów, bieżące punkty odzyskiwania maszyny wirtualnej pozostaną nienaruszone w magazynie do momentu ich wygaśnięcia.
- Niezależnie od tego, czy maszyna wirtualna jest przenoszona z magazynem, czy nie, możesz zawsze przywrócić maszynę wirtualną z zachowanej historii kopii zapasowych w magazynie.
- Azure Disk Encryption wymaga, aby Magazyn kluczy i maszyny wirtualne znajdowały się w tym samym regionie i subskrypcji platformy Azure.
- Aby przenieść maszynę wirtualną z dyskami zarządzanymi, zobacz ten [artykuł](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
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
> W przypadku skonfigurowania maszyn wirtualnych (Azure IaaS, Hyper-V, VMware) lub fizycznych na potrzeby odzyskiwania po awarii przy użyciu **Azure Site Recovery**Operacja przenoszenia zostanie zablokowana. Jeśli chcesz przenieść magazyny dla Azure Site Recovery, zapoznaj się z [tym artykułem](../site-recovery/move-vaults-across-regions.md) , aby dowiedzieć się więcej o przenoszeniu magazynów ręcznie.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Przenoszenie magazynu Recovery Services do innej grupy zasobów przy użyciu Azure Portal

Aby przenieść magazyn usługi Recovery Services i skojarzone z nim zasoby do innej grupy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz listę **magazynów Recovery Services** i wybierz magazyn, który chcesz przenieść. Gdy zostanie otwarty pulpit nawigacyjny magazynu, zostanie on wyświetlony jak pokazano na poniższej ilustracji.

   ![Otwórz magazyn usługi odzyskiwania](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

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

    ![Otwórz magazyn usługi odzyskiwania](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

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

## <a name="next-steps"></a>Następne kroki

Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcjami.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).
