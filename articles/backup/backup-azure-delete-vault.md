---
title: Usuwanie magazynu Microsoft Azure Recovery Services
description: W tym artykule dowiesz się, jak usunąć zależności, a następnie usunąć Magazyn Azure Backup Recovery Services.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 5fcf8004cd5792b30ec57537d5d8ab0bc085dfb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183759"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Usuwanie magazynu Recovery Services Azure Backup

W tym artykule opisano sposób usuwania magazynu Recovery Services [Azure Backup](backup-overview.md) . Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwania magazynu.

## <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu Recovery Services z zależnościami, takimi jak serwery chronione lub serwery zarządzania kopiami zapasowymi, skojarzone z nimi.

- Magazyny zawierające dane kopii zapasowej nie mogą zostać usunięte (nawet jeśli ochrona została zatrzymana, ale zachowano dane kopii zapasowej).

- Jeśli usuniesz magazyn zawierający zależności, zostanie wyświetlony następujący komunikat:

  ![Usuń błąd magazynu.](./media/backup-azure-delete-vault/error.png)

- Po usunięciu lokalnego elementu chronionego z portalu zawierającego zależności zostanie wyświetlony komunikat ostrzegawczy:

  ![Usuń błąd serwera chronionego.](./media/backup-azure-delete-vault/error-message.jpg)

- Jeśli pojawi się komunikat ostrzegawczy, elementy kopii zapasowej znajdują się w stanie nietrwałego usunięcia, a użytkownik będzie musiał czekać na ich trwałe usunięcie. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Usuń błąd magazynu.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Nie można usunąć magazynów z zarejestrowanymi kontami magazynu. Aby dowiedzieć się, jak wyrejestrować konto, zobacz [Wyrejestrowywanie konta magazynu](manage-afs-backup.md#unregister-a-storage-account).
  
Aby usunąć magazyn, wybierz scenariusz zgodny z konfiguracją i wykonaj zalecane czynności:

Scenariusz | Procedura usuwania zależności do usuwania magazynu |
-- | --
Lokalne pliki i foldery są chronione za pomocą agenta Azure Backup, tworzenie kopii zapasowych na platformie Azure | Wykonaj kroki z sekcji [usuwanie elementów kopii zapasowej z poziomu konsoli zarządzania Mars](#delete-backup-items-from-the-mars-management-console)
Mam lokalne maszyny, które są chronione za pomocą serwera usługi MAB (Microsoft Azure Backup Server) lub DPM (System Center Data Protection Manager) na platformie Azure | Wykonaj kroki z sekcji [usuwanie elementów kopii zapasowej z poziomu konsoli zarządzania serwera usługi MAB](#delete-backup-items-from-the-mabs-management-console)
Mam chronione elementy w chmurze (na przykład maszyna wirtualna laaS lub udział Azure Files)  | Wykonaj kroki opisane w temacie [usuwanie chronionych elementów w chmurze](#delete-protected-items-in-the-cloud)
Mam chronione elementy zarówno lokalnie, jak i w chmurze | Wykonaj kroki we wszystkich następujących sekcjach w następującej kolejności: <br> 1. [Usuń chronione elementy w chmurze](#delete-protected-items-in-the-cloud)<br> 2. [Usuń elementy kopii zapasowej z konsoli zarządzania Mars](#delete-backup-items-from-the-mars-management-console) . <br> 3. [Usuń elementy kopii zapasowej z konsoli zarządzania serwera usługi MAB](#delete-backup-items-from-the-mabs-management-console)
Nie mam żadnych chronionych elementów lokalnie lub w chmurze; Mimo to nadal otrzymuję błąd usuwania magazynu | Wykonaj kroki opisane w temacie [Usuwanie magazynu Recovery Services przy użyciu Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> Upewnij się, że w magazynie nie zarejestrowano żadnych kont magazynu. Aby dowiedzieć się, jak wyrejestrować konto, zobacz [Wyrejestrowywanie konta magazynu](manage-afs-backup.md#unregister-a-storage-account).

## <a name="delete-protected-items-in-the-cloud"></a>Usuwanie chronionych elementów w chmurze

Najpierw zapoznaj się z sekcją **[przed rozpoczęciem](#before-you-start)** , aby poznać zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1. W portalu przejdź do **magazynu Recovery Services**, a następnie przejdź do **pozycji elementy kopii zapasowej**. Następnie wybierz chronione elementy w chmurze (na przykład Azure Virtual Machines, Azure Storage [usługa Azure Files] lub SQL Server na platformie Azure Virtual Machines).

    ![Wybierz typ kopii zapasowej.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kliknij prawym przyciskiem myszy, aby wybrać element kopii zapasowej. W zależności od tego, czy element kopii zapasowej jest chroniony, w menu wyświetlane jest okienko **Zatrzymaj tworzenie kopii** zapasowej lub **Usuń dane kopii zapasowej** .

    - Jeśli zostanie wyświetlone okienko **Zatrzymaj tworzenie kopii** zapasowej, wybierz pozycję **Usuń dane kopii zapasowej** z menu rozwijanego. Wprowadź nazwę elementu kopii zapasowej (w tym polu jest rozróżniana wielkość liter), a następnie wybierz przyczynę z menu rozwijanego. Wprowadź swoje komentarze, jeśli masz. Następnie wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

        ![Okienko Zatrzymaj tworzenie kopii zapasowej.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Jeśli zostanie wyświetlone okienko **Usuń dane kopii zapasowej** , wprowadź nazwę elementu kopii zapasowej (w tym polu jest rozróżniana wielkość liter), a następnie wybierz przyczynę z menu rozwijanego. Wprowadź swoje komentarze, jeśli masz. Następnie wybierz pozycję **Usuń**.

         ![Okienko Usuń dane kopii zapasowej.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Sprawdź ikonę **powiadomienia** : ![ikona powiadomienia.](./media/backup-azure-delete-vault/messages.png) Po zakończeniu procesu usługa wyświetli następujący komunikat: *Zatrzymywanie tworzenia kopii zapasowej i usuwanie danych kopii zapasowej dla elementu "* kopia zapasowa *"*. *Pomyślnie ukończono operację*.
4. Wybierz pozycję **Odśwież** w menu **elementy kopii zapasowej** , aby upewnić się, że element kopii zapasowej został usunięty.

      ![Strona Usuń elementy kopii zapasowej.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Usuń chronione elementy lokalnie

Najpierw zapoznaj się z sekcją **[przed rozpoczęciem](#before-you-start)** , aby poznać zależności i proces usuwania magazynu.

1. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **infrastruktura zapasowa**.
2. W zależności od scenariusza lokalnego wybierz jedną z następujących opcji:

      - W przypadku usługi MARS wybierz pozycję **chronione serwery** , a następnie **Azure Backup agenta**. Następnie wybierz serwer, który chcesz usunąć.

        ![W przypadku usługi MARS wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - W przypadku serwera usługi MAB lub DPM wybierz pozycję **serwery zarządzania kopiami zapasowymi**. Następnie wybierz serwer, który chcesz usunąć.

          ![W obszarze serwera usługi MAB wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Zostanie wyświetlone okienko **Usuń** z komunikatem ostrzegawczym.

     ![Okienko usuwania.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Przejrzyj komunikat ostrzegawczy i instrukcje w polu wyboru wyrażanie zgody.
    > [!NOTE]
    >
    >- Jeśli chroniony serwer jest synchronizowany z usługami platformy Azure i istnieją elementy kopii zapasowej, w polu wyboru zgoda zostanie wyświetlona liczba zależnych elementów kopii zapasowej oraz link umożliwiający wyświetlenie elementów kopii zapasowej.
    >- Jeśli chroniony serwer nie jest synchronizowany z usługami platformy Azure i istnieją elementy kopii zapasowej, w polu zgody będzie wyświetlana tylko liczba elementów kopii zapasowej.
    >- Jeśli nie ma żadnych elementów kopii zapasowej, pole wyboru zgody zostanie poproszony o usunięcie.

4. Zaznacz pole wyboru wyrażanie zgody, a następnie wybierz pozycję **Usuń**.

5. Sprawdź ![ikonę **powiadomienia** Usuń dane](./media/backup-azure-delete-vault/messages.png)kopii zapasowej. Po zakończeniu operacji usługa wyświetli komunikat: *Zatrzymywanie tworzenia kopii zapasowej i usuwanie danych kopii zapasowej dla elementu "kopia zapasowa".* *Pomyślnie ukończono operację*.
6. Wybierz pozycję **Odśwież** w menu **elementy kopii zapasowej** , aby upewnić się, że element kopii zapasowej został usunięty.

Po zakończeniu tego procesu można usunąć elementy kopii zapasowej z konsoli zarządzania programu:

- [Usuwanie elementów kopii zapasowej z konsoli zarządzania MARS](#delete-backup-items-from-the-mars-management-console)
- [Usuwanie elementów kopii zapasowej z konsoli zarządzania serwera usługi MAB](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Usuwanie elementów kopii zapasowej z konsoli zarządzania MARS

1. Otwórz konsolę zarządzania MARS, przejdź do okienka **Akcje** , a następnie wybierz pozycję **Zaplanuj kopię zapasową**.
2. Na stronie **Modyfikuj lub Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj korzystanie z tego harmonogramu kopii zapasowych i Usuń wszystkie przechowywane kopie zapasowe**. Następnie wybierz przycisk **dalej**.

    ![Zmodyfikuj lub Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zakończ**.

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. Aby to zrobić, najpierw Zaloguj się do Azure Portal.
5. Przejdź do obszaru **Recovery Services** > **Settings** > **Właściwości**ustawień magazynu.
6. W obszarze **zabezpieczający numer PIN**wybierz pozycję **Generuj**. Skopiuj ten kod PIN. Numer PIN jest prawidłowy tylko przez pięć minut.
7. W konsoli zarządzania Wklej kod PIN, a następnie wybierz przycisk **OK**.

    ![Generuj zabezpieczający numer PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikowanie postępu tworzenia kopii zapasowej** zostanie wyświetlony następujący komunikat: *usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuń infrastrukturę tworzenia kopii zapasowych.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj kolejne kroki z portalu.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Usuwanie elementów kopii zapasowej z konsoli zarządzania serwera usługi MAB

Istnieją dwie metody, których można użyć do usunięcia elementów kopii zapasowej z konsoli zarządzania serwera usługi MAB.

#### <a name="method-1"></a>Metoda 1

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1. Otwórz Konsola administratora programu DPM a następnie na pasku nawigacyjnym wybierz pozycję **Ochrona** .
2. W okienku wyświetlania wybierz członka grupy ochrony, który chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wybrać opcję **Zatrzymaj ochronę grup członków** .
3. W oknie dialogowym **Zatrzymywanie ochrony** wybierz pozycję **Usuń chronione dane**, a następnie zaznacz pole wyboru **Usuń magazyn online** . Następnie wybierz pozycję **Zatrzymaj ochronę**.

    ![Wybierz pozycję Usuń chronione dane z okienka zatrzymywanie ochrony.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Stan chronionego elementu członkowskiego jest *niedostępny w dostępnej nieaktywnej replice*.

4. Kliknij prawym przyciskiem myszy nieaktywną grupę ochrony i wybierz polecenie **Usuń nieaktywną ochronę**.

    ![Usuń nieaktywną ochronę.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. W oknie **Usuń nieaktywną ochronę** zaznacz pole wyboru **Usuń magazyn online** , a następnie wybierz przycisk **OK**.

    ![Usuń magazyn online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metoda 2

Otwórz konsolę **zarządzania serwera usługi MAB** . W obszarze **Wybierz metodę ochrony danych**wyczyść pole wyboru **Chcę chronić w trybie online** .

  ![Wybierz metodę ochrony danych.](./media/backup-azure-delete-vault/data-protection-method.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj kolejne kroki z portalu.

## <a name="delete-the-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

1. Po usunięciu wszystkich zależności przejdź do okienka **podstawowe** w menu magazyn.
2. Sprawdź, czy na liście nie ma żadnych elementów kopii zapasowych, serwerów zarządzania kopiami zapasowymi lub zreplikowanych elementów. Jeśli elementy nadal pojawiają się w magazynie, zapoznaj się z sekcją [przed rozpoczęciem](#before-you-start) .

3. Jeśli w magazynie nie ma więcej elementów, wybierz pozycję **Usuń** na pulpicie nawigacyjnym magazynu.

    ![Na pulpicie nawigacyjnym magazynu wybierz pozycję Usuń.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Wybierz pozycję **tak** , aby sprawdzić, czy chcesz usunąć magazyn. Magazyn zostanie usunięty. Portal powróci do menu **Nowa** usługa.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Usuwanie magazynu Recovery Services przy użyciu programu PowerShell

Najpierw zapoznaj się z sekcją **[przed rozpoczęciem](#before-you-start)** , aby poznać zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej:

- Jeśli używasz programu SQL w ramach kopii zapasowych maszyn wirtualnych platformy Azure i włączono funkcję autoochrony dla wystąpień programu SQL Server, należy najpierw wyłączyć ochronę autoprotection.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) na temat wyłączania ochrony dla elementu chronionego Azure Backup.

- Zatrzymaj ochronę i Usuń dane dla wszystkich elementów chronionych przez kopię zapasową w chmurze (np. Maszyna wirtualna w laaS, udział plików platformy Azure itp.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) na temat wyłączania ochrony dla elementu chronionego przez kopię zapasową.

- W przypadku plików i folderów lokalnych chronionych przy użyciu agenta Azure Backup (MARS) kopii zapasowej na platformie Azure Użyj następującego polecenia programu PowerShell, aby usunąć kopię zapasową danych z każdego modułu MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Opublikuj, gdzie zostanie wyświetlony następujący monit:

    *Microsoft Azure Backup czy na pewno chcesz usunąć te zasady kopii zapasowych? Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte. <br/> [T] tak [A] tak dla wszystkich [N] nie [L] nie do wszystkich [S] zawieszania [?] Pomoc (wartość domyślna to "Y"):*

- W przypadku maszyn lokalnych chronionych przy użyciu serwera usługi MAB (Microsoft Azure Backup Server) lub DPM do platformy Azure (System Center Data Protection Manager) Użyj następującego polecenia, aby usunąć kopię zapasową danych na platformie Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Opublikuj, gdzie zostanie wyświetlony następujący monit:

   *Microsoft Azure Backup* Czy na pewno chcesz usunąć te zasady kopii zapasowych? Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte. <br/>
   [T] tak [A] tak dla wszystkich [N] nie [L] nie do wszystkich [S] zawieszania [?] Pomoc (wartość domyślna to "Y"):*

Po usunięciu kopii zapasowej danych, należy wyrejestrować wszystkie kontenery lokalne i serwery zarządzania.

- W przypadku plików i folderów lokalnych chronionych przy użyciu agenta Azure Backup (MARS) tworzenie kopii zapasowej na platformie Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o usuwaniu rejestracji systemu Windows Server lub innego kontenera z magazynu.

- W przypadku maszyn lokalnych chronionych przy użyciu serwera usługi MAB (Microsoft Azure Backup Server) lub DPM do platformy Azure (System Center Data Protection Manage:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o wyrejestrowaniu kontenera zarządzania kopiami zapasowymi z magazynu.

Po trwałym usunięciu danych kopii zapasowej i wyrejestrowaniu wszystkich kontenerów, należy usunąć magazyn.

Aby usunąć magazyn Recovery Services:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) na temat usuwania magazynu usługi Recovery Services.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Usuwanie magazynu Recovery Services przy użyciu interfejsu wiersza polecenia

Najpierw zapoznaj się z sekcją **[przed rozpoczęciem](#before-you-start)** , aby poznać zależności i proces usuwania magazynu.

> [!NOTE]
> Obecnie Azure Backup interfejs wiersza polecenia obsługuje zarządzanie tylko kopiami zapasowymi maszyny wirtualnej platformy Azure, więc następujące polecenie usuwania magazynu działa tylko wtedy, gdy magazyn zawiera kopie zapasowe maszyn wirtualnych platformy Azure. Nie można usunąć magazynu przy użyciu interfejsu wiersza polecenia Azure Backup, jeśli magazyn zawiera element kopii zapasowej typu innego niż maszyny wirtualne platformy Azure.

Aby usunąć istniejący magazyn usługi Recovery Services, wykonaj następujące czynności:

- Aby zatrzymać ochronę i usunąć dane kopii zapasowej

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Aby uzyskać więcej informacji, zobacz ten [artykuł](/cli/azure/backup/protection#az-backup-protection-disable).

- Usuń istniejący magazyn usług Recovery Services:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Usuwanie magazynu Recovery Services przy użyciu Azure Resource Manager

Ta opcja usuwania magazynu Recovery Services jest zalecana tylko wtedy, gdy wszystkie zależności zostaną usunięte i nadal jest wyświetlany *błąd usuwania magazynu*. Wypróbuj dowolne lub wszystkie poniższe wskazówki:

- W okienku **podstawy** w menu magazyn Sprawdź, czy na liście nie ma żadnych elementów kopii zapasowych, serwerów zarządzania kopiami zapasowymi ani zreplikowanych elementów. Jeśli istnieją elementy kopii zapasowej, zapoznaj się z sekcją [przed rozpoczęciem](#before-you-start) .
- Spróbuj ponownie [usunąć magazyn z portalu](#delete-the-recovery-services-vault) .
- Jeśli wszystkie zależności zostaną usunięte i nadal *wystąpi błąd usuwania magazynu*, użyj narzędzia ARMClient, aby wykonać następujące czynności (po zanotowaniu).

1. Przejdź do [Chocolatey.org](https://chocolatey.org/) , aby pobrać i zainstalować czekoladę. Następnie zainstaluj ARMClient, uruchamiając następujące polecenie:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Zaloguj się do konta platformy Azure, a następnie uruchom następujące polecenie:

    `ARMClient.exe login [environment name]`

3. W Azure Portal Zbierz Identyfikator subskrypcji i nazwę grupy zasobów dla magazynu, który chcesz usunąć.

Aby uzyskać więcej informacji na temat polecenia ARMClient, zobacz [plik Readme ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usuwanie magazynu Recovery Services przy użyciu klienta Azure Resource Manager

1. Uruchom następujące polecenie, używając identyfikatora subskrypcji, nazwy grupy zasobów i nazwy magazynu. Jeśli nie masz żadnych zależności, magazyn zostanie usunięty po uruchomieniu następującego polecenia:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Jeśli magazyn nie jest pusty, zostanie wyświetlony następujący komunikat o błędzie: *nie można usunąć magazynu, ponieważ w tym magazynie znajdują się zasoby.* Aby usunąć chroniony element lub kontener w ramach magazynu, uruchom następujące polecenie:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. W Azure Portal upewnij się, że magazyn został usunięty.

## <a name="next-steps"></a>Następne kroki

[Informacje o magazynach Recovery Services](backup-azure-recovery-services-vault-overview.md)<br/>
[Informacje o monitorowaniu magazynów Recovery Services i zarządzaniu nimi](backup-azure-manage-windows-server.md)
