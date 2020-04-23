---
title: Usuwanie skarbca usług odzyskiwania platformy Microsoft Azure
description: W tym artykule dowiesz się, jak usunąć zależności, a następnie usunąć magazyn usług Azure Backup Recovery Services.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025139"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Usuwanie magazynu usług odzyskiwania kopii zapasowych platformy Azure

W tym artykule opisano sposób usuwania magazynu usług [Azure Backup](backup-overview.md) Recovery Services. Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwania magazynu.

## <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu usług odzyskiwania, który ma zależności, takie jak chronione serwery lub serwery zarządzania kopiami zapasowymi, skojarzone z nim.

- Nie można usunąć przechowalni zawierających dane kopii zapasowej (nawet jeśli ochrona została zatrzymana, ale zachowana została kopia zapasowa).

- Jeśli usuniesz przechowalnię zawierającą zależności, pojawi się następujący komunikat:

  ![Usuń błąd przechowalni.](./media/backup-azure-delete-vault/error.png)

- Jeśli usuniesz lokalny element chroniony z portalu zawierającego zależności, zostanie wyświetlony komunikat ostrzegawczy:

  ![Usuń błąd chronionego serwera.](./media/backup-azure-delete-vault/error-message.jpg)

- Jeśli elementy kopii zapasowej są w stanie nietrwałym usuniętym poniżej komunikat ostrzegawczy pojawia się komunikat ostrzegawczy i trzeba będzie poczekać, aż zostaną trwale usunięte. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Usuń błąd przechowalni.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Nie można usunąć przechowalni, które mają zarejestrowane konta magazynu. Aby dowiedzieć się, jak wyrejestrować konto, zobacz [Wyrejestrowanie konta magazynu](manage-afs-backup.md#unregister-a-storage-account).
  
Aby usunąć przechowalnię, wybierz scenariusz zgodny z konfiguracją i wykonaj zalecane kroki:

Scenariusz | Kroki usuwania zależności w celu usunięcia przechowalni |
-- | --
Mam lokalne pliki i foldery chronione za pomocą agenta usługi Azure Backup, który tworzą kopię zapasową na platformie Azure | Wykonywanie czynności w obszarze [Usuwanie elementów kopii zapasowej z konsoli zarządzania marsjańską](#delete-backup-items-from-the-mars-management-console)
Mam komputery lokalne, które są chronione przy użyciu mabs (Microsoft Azure Backup Server) lub programu DPM (System Center Data Protection Manager) na platformie Azure | Wykonywanie czynności w obszarze [Usuwanie elementów kopii zapasowej z konsoli zarządzania MABS](#delete-backup-items-from-the-mabs-management-console)
Mam chronione elementy w chmurze (na przykład laaS maszyny wirtualnej lub udziału usługi Azure Files)  | Wykonywanie czynności w obszarze [Usuwanie elementów chronionych w chmurze](#delete-protected-items-in-the-cloud)
Chroniłem przedmioty zarówno lokalnie, jak i w chmurze | Wykonaj kroki we wszystkich poniższych sekcjach w następującej kolejności: <br> 1. [Usuwanie chronionych elementów w chmurze](#delete-protected-items-in-the-cloud)<br> 2. [Usuwanie elementów kopii zapasowej z konsoli zarządzania MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Usuwanie elementów kopii zapasowej z konsoli zarządzania MABS](#delete-backup-items-from-the-mabs-management-console)
Nie mam żadnych elementów chronionych w środowisku lokalnym lub w chmurze; jednak nadal oioboję się o błąd usunięcia skarbca | Wykonywanie kroków w obszarze [Usuwanie magazynu usług odzyskiwania przy użyciu usługi Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> Upewnij się, że w przechowalni nie ma żadnych kont magazynu. Aby dowiedzieć się, jak wyrejestrować konto, zobacz [Wyrejestrowanie konta magazynu](manage-afs-backup.md#unregister-a-storage-account).

## <a name="delete-protected-items-in-the-cloud"></a>Usuwanie chronionych elementów w chmurze

Najpierw przeczytaj sekcję **[Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1. W portalu przejdź do **magazynu usług odzyskiwania,** a następnie przejdź do **pozycji Zapasy kopii zapasowej**. Następnie wybierz chronione elementy w chmurze (na przykład maszyny wirtualne platformy Azure, usługi Azure Storage [usługa Azure Files service] lub SQL Server na maszynach wirtualnych platformy Azure).

    ![Wybierz typ kopii zapasowej.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kliknij prawym przyciskiem myszy, aby wybrać element kopii zapasowej. W zależności od tego, czy element kopii zapasowej jest chroniony, czy nie, w menu jest wyświetlane okienko **Zatrzymaj kopię zapasową** lub **Usuń dane kopii zapasowej.**

    - Jeśli zostanie wyświetlene okienko **Zatrzymaj kopię zapasową,** wybierz polecenie **Usuń dane kopii zapasowej** z menu rozwijanego. Wprowadź nazwę elementu kopii zapasowej (w tym polu jest rozróżniana wielkość liter), a następnie wybierz przyczynę z menu rozwijanego. Wprowadź swoje komentarze, jeśli masz jakieś. Następnie wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

        ![Okienko Zatrzymaj kopię zapasową.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Jeśli zostanie **wyświetlene** okienko Usuń dane kopii zapasowej, wprowadź nazwę elementu kopii zapasowej (w tym polu jest rozróżniana wielkość liter), a następnie wybierz przyczynę z menu rozwijanego. Wprowadź swoje komentarze, jeśli masz jakieś. Następnie wybierz pozycję **Usuń**.

         ![Okienko Usuń dane kopii zapasowej.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Sprawdź ikonę ![ **Powiadomienia:** Ikona powiadomienia.](./media/backup-azure-delete-vault/messages.png) Po zakończeniu procesu usługa wyświetla następujący komunikat: *Zatrzymywanie kopii zapasowej i usuwanie danych kopii zapasowej dla elementu*kopii zapasowej .*"* *Pomyślnie zakończono operację*.
4. Wybierz **polecenie Odśwież** w menu Elementy kopii **zapasowej,** aby upewnić się, że element kopii zapasowej został usunięty.

      ![Strona Usuń elementy kopii zapasowej.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Usuwanie elementów chronionych w środowisku lokalnym

Najpierw przeczytaj sekcję **[Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

1. Z menu pulpitu nawigacyjnego przechowalni wybierz polecenie **Infrastruktura kopii zapasowych**.
2. W zależności od scenariusza lokalnego wybierz jedną z następujących opcji:

      - W przypadku marsjańskich wybierz pozycję **Serwery chronione,** a następnie **agenta kopii zapasowej platformy Azure**. Następnie wybierz serwer, który chcesz usunąć.

        ![W przypadku marsjańskiego wybierz przechowalnię, aby otworzyć jej pulpit nawigacyjny.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - W przypadku programu MABS lub DPM wybierz pozycję **Serwery zarządzania kopiami zapasowymi**. Następnie wybierz serwer, który chcesz usunąć.

          ![W przypadku usługi MABS wybierz magazyn, aby otworzyć jego pulpit nawigacyjny.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Zostanie **wyświetlone** okienko Usuń z komunikatem ostrzegawczym.

     ![Okienko usuwania.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Przejrzyj komunikat ostrzegawczy i instrukcje w polu wyboru Zgoda.
    > [!NOTE]
    >- Jeśli serwer chroniony jest synchronizowany z usługami platformy Azure i istnieją elementy kopii zapasowej, w polu wyboru zgoda zostanie wyświetlona liczba elementów kopii zapasowej zależnych i łącze umożliwiające wyświetlenie elementów kopii zapasowej.
    >- Jeśli serwer chroniony nie jest synchronizowany z usługami platformy Azure i istnieją elementy kopii zapasowej, w polu wyboru zgoda zostanie wyświetlona tylko liczba elementów kopii zapasowej.
    >- Jeśli nie ma żadnych elementów kopii zapasowej, pole wyboru Zgoda poprosi o usunięcie.

4. Zaznacz pole wyboru Zgoda, a następnie wybierz pozycję **Usuń**.

5. Sprawdź **Notification** ikonę ![Powiadomienie](./media/backup-azure-delete-vault/messages.png)usuń dane kopii zapasowej . Po zakończeniu operacji usługa wyświetla komunikat: *Zatrzymanie kopii zapasowej i usunięcie danych kopii zapasowej dla "Elementu kopii zapasowej".* *Pomyślnie zakończono operację*.
6. Wybierz **polecenie Odśwież** w menu Elementy kopii **zapasowej,** aby upewnić się, że element kopii zapasowej został usunięty.

Po zakończeniu tego procesu można usunąć elementy kopii zapasowej z konsoli zarządzania:

- [Usuwanie elementów kopii zapasowej z konsoli zarządzania marsjańską](#delete-backup-items-from-the-mars-management-console)
- [Usuwanie elementów kopii zapasowej z konsoli zarządzania MABS](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Usuwanie elementów kopii zapasowej z konsoli zarządzania marsjańską

1. Otwórz konsolę zarządzania mars, przejdź do okienka **Akcje** i wybierz pozycję **Zaplanuj kopię zapasową**.
2. Na stronie **Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj korzystanie z tego harmonogramu tworzenia kopii zapasowych i usuń wszystkie zapisane kopie zapasowe**. Następnie wybierz przycisk **Dalej**.

    ![Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zakończ**.

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie kodu PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. Aby to zrobić, najpierw zaloguj się do witryny Azure portal.
5. Przejdź do pozycji**Właściwości****ustawień** >  **magazynu** > usług odzyskiwania .
6. W obszarze **Kod PIN zabezpieczeń**wybierz pozycję **Generuj**. Skopiuj ten kod PIN. Kod PIN jest ważny tylko przez pięć minut.
7. W konsoli zarządzania wklej kod PIN, a następnie wybierz przycisk **OK**.

    ![Generowanie kodu PIN zabezpieczeń.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikuj postęp kopii zapasowej** zostanie wyświetlony następujący komunikat: *Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuń infrastrukturę kopii zapasowej.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj następne kroki z portalu.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Usuwanie elementów kopii zapasowej z konsoli zarządzania MABS

Istnieją dwie metody usuwania elementów kopii zapasowej z konsoli zarządzania MABS.

#### <a name="method-1"></a>Metoda 1

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1. Otwórz konsolę administratora programu DPM, a następnie wybierz pozycję **Ochrona** na pasku nawigacyjnym.
2. W okienku wyświetlania wybierz członka grupy ochrony, który chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wybrać opcję **Zatrzymaj ochronę członków grupy.**
3. W oknie dialogowym **Ochrona zatrzymania** wybierz pozycję Usuń **chronione dane**, a następnie zaznacz pole wyboru Usuń przechowywanie **w trybie online.** Następnie wybierz **pozycję Zatrzymaj ochronę**.

    ![Wybierz pozycję Usuń chronione dane z okienka Ochrona zatrzymania.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Stan chronionego elementu członkowskiego zmienia się *na Niedostępną replikę nieaktywną*.

4. Kliknij prawym przyciskiem myszy nieaktywną grupę ochrony i wybierz polecenie **Usuń nieaktywną ochronę**.

    ![Usuń nieaktywną ochronę.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. W oknie **Usuń ochronę nieaktywną** zaznacz pole wyboru **Usuń magazyn online,** a następnie wybierz przycisk **OK**.

    ![Usuń magazyn online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metoda 2

Otwórz konsolę **zarządzania MABS.** W obszarze **Wybierz metodę ochrony danych**wyczyść pole wyboru **Chcę chronić online.**

  ![Wybierz metodę ochrony danych.](./media/backup-azure-delete-vault/data-protection-method.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj następne kroki z portalu.

## <a name="delete-the-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

1. Po usunięciu wszystkich zależności przewiń do okienka **Essentials** w menu przechowalni.
2. Sprawdź, czy na liście nie ma żadnych elementów kopii zapasowej, serwerów zarządzania kopiami zapasowymi ani elementów replikowanych. Jeśli elementy nadal pojawiają się w przechowalni, zapoznaj się z sekcją [Przed rozpoczęciem.](#before-you-start)

3. Jeśli w przechowalni nie ma więcej elementów, wybierz pozycję **Usuń** na pulpicie nawigacyjnym przechowalni.

    ![Wybierz pozycję Usuń na pulpicie nawigacyjnym przechowalni.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Wybierz **opcję Tak,** aby sprawdzić, czy chcesz usunąć przechowalnię. Przechowalnia zostanie usunięta. Portal powraca do menu **Nowa** usługa.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Usuwanie magazynu usług odzyskiwania przy użyciu programu PowerShell

Najpierw przeczytaj sekcję **[Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej:

- Jeśli używasz SQL w kopii zapasowej maszyn wirtualnych platformy Azure i włączone automatyczneochłaz dla wystąpień SQL, najpierw wyłączyć automatyczną ochronę.

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

  [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) na temat wyłączania ochrony elementu chronionego za pomocą kopii zapasowej platformy Azure.

- Zatrzymaj ochronę i usuń dane dla wszystkich elementów chronionych za kopię zapasową w chmurze (np. laaS VM, udział plików platformy Azure itp.):

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

    [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) o wyłączeniu ochrony elementu chronionego za kopię zapasową.

- W przypadku plików lokalnych i folderów chronionych przy użyciu usługi Azure Backup Agent (MARS) kopii zapasowej na platformie Azure, użyj następującego polecenia programu PowerShell, aby usunąć dane kopii zapasowej z każdego modułu programu MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Opublikuj następujące monity:

    *Tworzenie kopii zapasowej platformy Microsoft Azure Czy na pewno chcesz usunąć te zasady tworzenia kopii zapasowych? Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte. <br/> [Y] Tak [A] Tak dla wszystkich [N] Nie [L] Nie dla Wszystkich [S] Suspend [?] Pomoc (domyślnie jest to "Y"):*

- W przypadku maszyn lokalnych chronionych przy użyciu usługi MABS (Microsoft Azure Backup Server) lub programu DPM na platformę Azure (Menedżer ochrony danych w centrum systemu) użyj następującego polecenia, aby usunąć kopie zapasowe danych na platformie Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Opublikuj następujące monity:

   *Kopia zapasowa platformy Microsoft Azure* Czy na pewno chcesz usunąć te zasady tworzenia kopii zapasowych? Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte. <br/>
   [Y] Tak [A] Tak dla wszystkich [N] Nie [L] Nie dla Wszystkich [S] Suspend [?] Pomoc (domyślnie jest to "Y"):*

Po usunięciu kopii zapasowej danych odłączyć rejestrację wszystkich kontenerów lokalnych i serwerów zarządzania.

- W przypadku plików lokalnych i folderów chronionych przy użyciu usługi Azure Backup Agent (MARS) kopii zapasowej na platformie Azure:

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

    [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o odsyłaniu systemu Windows Server lub innego kontenera z przechowalni.

- W przypadku komputerów lokalnych chronionych przy użyciu usługi MABS (Microsoft Azure Backup Server) lub programu DPM na platformie Azure (System Center Data Protection Manage:

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

    [Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o odsłanianiu kontenera zarządzania kopiami zapasowymi z magazynu.

Po trwałym usunięciu kopii zapasowej danych i odudnieniu rejestracji wszystkich kontenerów przejdź do usuwania magazynu.

Aby usunąć magazyn usług odzyskiwania:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) o usuwaniu magazynu usług odzyskiwania.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Usuwanie magazynu usług odzyskiwania przy użyciu interfejsu wiersza polecenia

Najpierw przeczytaj sekcję **[Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

> [!NOTE]
> Obecnie narzędzie CLI kopii zapasowej platformy Azure obsługuje zarządzanie tylko kopiami zapasowymi maszyn wirtualnych platformy Azure, więc następujące polecenie usunięcia magazynu działa tylko wtedy, gdy magazyn zawiera kopie zapasowe maszyn wirtualnych platformy Azure. Nie można usunąć magazynu przy użyciu interfejsu wiersza polecenia kopii zapasowej platformy Azure, jeśli magazyn zawiera dowolny element kopii zapasowej typu innego niż maszyny wirtualne platformy Azure.

Aby usunąć istniejący magazyn usług odzyskiwania, wykonaj następujące czynności:

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

- Usuwanie istniejącego magazynu usług odzyskiwania:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Usuwanie magazynu usług odzyskiwania przy użyciu usługi Azure Resource Manager

Ta opcja usunięcia magazynu usług odzyskiwania jest zalecana tylko wtedy, gdy wszystkie zależności zostaną usunięte i nadal pojawia się *błąd usunięcia przechowalni.* Wypróbuj dowolną lub wszystkie z następujących wskazówek:

- W okienku **Essentials** w menu przechowalni sprawdź, czy na liście nie ma żadnych elementów kopii zapasowej, serwerów zarządzania kopiami zapasowymi ani elementów replikowanych. Jeśli istnieją elementy kopii zapasowej, zapoznaj się z sekcją [Przed rozpoczęciem.](#before-you-start)
- Spróbuj ponownie [usunąć magazyn z portalu.](#delete-the-recovery-services-vault)
- Jeśli wszystkie zależności zostaną usunięte i nadal pojawia się *błąd usuwania programu Vault,* użyj narzędzia ARMClient, aby wykonać następujące kroki (po notatce).

1. Przejdź do [chocolatey.org,](https://chocolatey.org/) aby pobrać i zainstalować Chocolatey. Następnie zainstaluj system ARMClient, uruchamiając następujące polecenie:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Zaloguj się do konta platformy Azure, a następnie uruchom następujące polecenie:

    `ARMClient.exe login [environment name]`

3. W witrynie Azure portal zebrać identyfikator subskrypcji i nazwę grupy zasobów dla magazynu, który chcesz usunąć.

Aby uzyskać więcej informacji na temat polecenia ARMClient, zobacz [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usuwanie magazynu usług odzyskiwania za pomocą klienta usługi Azure Resource Manager

1. Uruchom następujące polecenie przy użyciu identyfikatora subskrypcji, nazwy grupy zasobów i nazwy przechowalni. Jeśli nie masz żadnych zależności, przechowalnia jest usuwana po uruchomieniu następującego polecenia:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Jeśli przechowalnia nie jest pusta, zostanie wyświetlony następujący komunikat o błędzie: *Nie można usunąć przechowalni, ponieważ w tym magazynie znajdują się istniejące zasoby.* Aby usunąć chroniony element lub kontener w przechowalni, uruchom następujące polecenie:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. W witrynie Azure portal upewnij się, że magazyn został usunięty.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o magazynach usług odzyskiwania](backup-azure-recovery-services-vault-overview.md)<br/>
[Dowiedz się więcej o monitorowaniu magazynów usług odzyskiwania i zarządzaniu nimi](backup-azure-manage-windows-server.md)
