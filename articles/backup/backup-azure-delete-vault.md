---
title: Usuwanie magazynu Microsoft Azure Recovery Services
description: W tym artykule dowiesz się, jak usunąć zależności, a następnie usunąć Azure Backup usługi Recovery Services.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: bb6be070ac0fb408ac37c8ae7b003b54da5d6dea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773661"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Usuwanie magazynu Azure Backup Recovery Services

W tym artykule opisano sposób usuwania magazynu [Azure Backup](backup-overview.md) Recovery Services. Zawiera on instrukcje dotyczące usuwania zależności, a następnie usuwania magazynu.

## <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu usługi Recovery Services z dowolnymi z następujących zależności:

- Nie można usunąć magazynu zawierającego chronione źródła danych (na przykład maszyny wirtualne IaaS, bazy danych SQL, udziały plików platformy Azure).
- Nie można usunąć magazynu zawierającego dane kopii zapasowej. Po usunięciu danych kopii zapasowej przejdzie on do stanu usunięcia nietrwałego.
- Nie można usunąć magazynu zawierającego dane kopii zapasowej w stanie usunięcia nie soft.
- Nie można usunąć magazynu, który zarejestrował konta magazynu.

Jeśli spróbujesz usunąć magazyn bez usuwania zależności, wystąpi jeden z następujących komunikatów o błędach:

- Nie można usunąć magazynu, ponieważ istnieją w nim zasoby. Upewnij się, że z tym magazynem nie są skojarzone żadne elementy kopii zapasowej, serwery chronione ani serwery zarządzania kopiami zapasowymi. Przed przystąpieniem do usuwania wyrejestruj następujące kontenery skojarzone z tym magazynem.

- Nie można usunąć magazynu usługi Recovery Services, ponieważ w magazynie istnieją elementy kopii zapasowej w stanie nietrwałego usunięcia. Elementy usunięte nietrwałych są trwale usuwane po 14 dniach od operacji usuwania. Spróbuj usunąć magazyn po trwałym usunięciu elementów kopii zapasowej i w magazynie nie ma żadnego elementu w stanie usunięcia nietrwałych. Aby uzyskać więcej informacji, zobacz [Usuwanie nie Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Prawidłowy sposób usuwania magazynu

>[!WARNING]
>Następująca operacja jest destruktywna i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

Aby prawidłowo usunąć magazyn, należy wykonać kroki opisane w tej kolejności:

- **Krok 1.** Wyłączenie funkcji usuwania nie soft. [Zobacz tutaj,](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) aby uzyskać instrukcje wyłączania usuwania nie soft.

- **Krok 2.** Po wyłączeniu usuwania nie softowego sprawdź, czy istnieją elementy, które wcześniej pozostały w stanie usunięcia nie soft. Jeśli istnieją elementy w stanie usunięcia nieukreślone, należy cofnąć usunięcie i *usunąć* je ponownie.  [Wykonaj następujące kroki,](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) aby znaleźć elementy usuwania nietrwałych i trwale je usunąć.

- **Krok 3.** Należy sprawdzić wszystkie trzy poniższe miejsca, aby sprawdzić, czy istnieją jakieś chronione elementy:

  - **Elementy chronione w chmurze:** przejdź do menu pulpitu nawigacyjnego magazynu > **elementy kopii zapasowej.** Wszystkie elementy wymienione w tym miejscu należy usunąć za pomocą pozycji **Zatrzymaj** tworzenie kopii zapasowej lub Usuń dane kopii **zapasowej** wraz z danymi kopii zapasowej.  [Wykonaj następujące kroki,](#delete-protected-items-in-the-cloud) aby usunąć te elementy.
  - **SQL Server:** przejdź do menu pulpitu nawigacyjnego magazynu > **Backup Infrastructure**  >  **Protected Servers**. W chronione serwery, wybierz serwer, aby wyrejestrować. Aby usunąć magazyn, należy wyrejestrować wszystkie serwery. Kliknij prawym przyciskiem myszy chroniony serwer i wybierz polecenie **Wyrejestruj**.
  - **Serwery chronione przez usługę MARS:** przejdź do menu pulpitu nawigacyjnego magazynu > **Serwery chronione infrastruktury** kopii  >  **zapasowych.** Jeśli masz serwery chronione za pomocą usługi MARS, wszystkie elementy wymienione w tym miejscu muszą zostać usunięte wraz z ich danymi kopii zapasowej. [Wykonaj następujące kroki,](#delete-protected-items-on-premises) aby usunąć serwery chronione przez marsa.
  - **Serwery zarządzania usługi MABS lub DPM:** przejdź do menu pulpitu nawigacyjnego magazynu > **Kopii zapasowych infrastruktury** kopii zapasowych serwerów  >  **zarządzania.** Jeśli masz program DPM lub Azure Backup Server (MABS), wszystkie elementy wymienione w tym miejscu muszą zostać usunięte lub wyrejestrowane wraz z ich danymi kopii zapasowej. [Wykonaj następujące kroki,](#delete-protected-items-on-premises) aby usunąć serwery zarządzania.

- **Krok 4.** Należy upewnić się, że wszystkie zarejestrowane konta magazynu zostały usunięte. Przejdź do menu pulpitu nawigacyjnego magazynu i > **magazynu**  >  **infrastruktury kopii zapasowych.** Jeśli masz konta magazynu wymienione w tym miejscu, musisz wyrejestrować wszystkie z nich. Aby dowiedzieć się, jak wyrejestrować konto, zobacz [Wyrejestrowanie konta magazynu.](manage-afs-backup.md#unregister-a-storage-account)
- **Krok 5.** Upewnij się, że nie utworzono żadnych prywatnych punktów końcowych dla magazynu. Przejdź do menu pulpitu  nawigacyjnego magazynu > Połączenia z prywatnym punktem końcowym w obszarze "Ustawienia" > jeśli magazyn ma jakiekolwiek połączenia prywatnego punktu końcowego utworzone lub podjęto próbę utworzenia, upewnij się, że zostały usunięte przed przystąpieniem do usuwania magazynu. 

Po ukończeniu tych kroków możesz kontynuować usuwanie [magazynu](#delete-the-recovery-services-vault).

Jeśli nie masz żadnych chronionych elementów lokalnie lub w chmurze, ale nadal występuje błąd usuwania magazynu, wykonaj kroki opisane w te tematu Usuwanie magazynu usługi [Recovery Services](#delete-the-recovery-services-vault-by-using-azure-resource-manager) przy użyciu Azure Resource Manager

## <a name="delete-protected-items-in-the-cloud"></a>Usuwanie chronionych elementów w chmurze

Najpierw przeczytaj sekcję **[Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące kroki:

1. W portalu przejdź do magazynu **usługi Recovery Services,** a następnie do pozycji **Elementy kopii zapasowej.** Następnie na liście **Typ** zarządzania kopiami zapasowymi wybierz chronione elementy w chmurze (na przykład Azure Virtual Machines, Azure Storage [usługa Azure Files] lub SQL Server na platformie Azure Virtual Machines).

    ![Wybierz typ kopii zapasowej.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Zostanie wyświetlona lista wszystkich elementów dla kategorii. Kliknij prawym przyciskiem myszy, aby wybrać element kopii zapasowej. W zależności od tego, czy element kopii zapasowej jest chroniony, w menu jest wyświetlane okienko Zatrzymaj kopię zapasową lub **Usuń dane kopii zapasowej.** 

    - Jeśli zostanie **wyświetlone okienko Zatrzymaj** tworzenie kopii zapasowej, wybierz **pozycję Usuń** dane kopii zapasowej z menu rozwijanego. Wprowadź nazwę elementu kopii zapasowej (w tym polu jest zróżnicowa wielkość liter), a następnie wybierz przyczynę z menu rozwijanego. Wprowadź swoje komentarze, jeśli masz jakiekolwiek. Następnie wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej.**

        ![Okienko Zatrzymaj tworzenie kopii zapasowej.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Jeśli zostanie **wyświetlone okienko Usuń** dane kopii zapasowej, wprowadź nazwę elementu kopii zapasowej (w tym polu jest zróżnicowa wielkość liter), a następnie wybierz przyczynę z menu rozwijanego. Jeśli masz jakieś, wprowadź swoje komentarze. Następnie wybierz pozycję **Usuń**.

         ![Okienko Usuń dane kopii zapasowej.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Ta opcja powoduje usunięcie zaplanowanych kopii zapasowych, a także usunięcie kopii zapasowych na żądanie.
3. Sprawdź **ikonę** Powiadomienie: ![ Ikona powiadomienia.](./media/backup-azure-delete-vault/messages.png) Po zakończeniu procesu usługa wyświetla następujący komunikat: *Zatrzymywanie* tworzenia kopii zapasowej i usuwanie danych kopii zapasowej dla elementu kopii *zapasowej "*. *Pomyślnie ukończono operację*.
4. Wybierz **pozycję Odśwież** w menu Elementy **kopii** zapasowej, aby upewnić się, że element kopii zapasowej został usunięty.

      ![Strona Usuwanie elementów kopii zapasowej.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Usuwanie chronionych elementów w środowisku lokalnym

Najpierw przeczytaj **[sekcję Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

1. Z menu pulpitu nawigacyjnego magazynu wybierz pozycję **Infrastruktura kopii zapasowych.**
2. W zależności od scenariusza lokalnego wybierz jedną z następujących opcji:

      - W przypadku agenta MARS wybierz **pozycję Serwery chronione,** a  **następnie wybierz Azure Backup Agent**. Następnie wybierz serwer, który chcesz usunąć.

        ![W przypadku usługi MARS wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - W przypadku usługi MABS lub programu DPM wybierz **pozycję Serwery zarządzania kopiami zapasowymi.** Następnie wybierz serwer, który chcesz usunąć.

          ![W przypadku usługi MABS lub programu DPM wybierz magazyn, aby otworzyć jego pulpit nawigacyjny.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Zostanie **wyświetlone** okienko Usuwanie z komunikatem ostrzegawczym.

     ![Okienko usuwania.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Przejrzyj komunikat ostrzegawczy i instrukcje w polu wyboru Zgody.
    > [!NOTE]
    >
    >- Jeśli chroniony serwer jest synchronizowany z usługami platformy Azure i istnieją elementy kopii zapasowej, pole wyboru zgody spowoduje wyświetlenie liczby zależnych elementów kopii zapasowej oraz linku do wyświetlania elementów kopii zapasowej.
    >- Jeśli chroniony serwer nie jest synchronizowany z usługami platformy Azure i istnieją elementy kopii zapasowej, pole wyboru zgody spowoduje wyświetlenie tylko liczby elementów kopii zapasowej.
    >- Jeśli nie ma żadnych elementów kopii zapasowej, pole wyboru zgody poprosi o usunięcie.

4. Zaznacz pole wyboru zgody, a następnie wybierz pozycję **Usuń.**

5. Zaznacz **ikonę Powiadomienie,** ![ aby usunąć dane kopii zapasowej. ](./media/backup-azure-delete-vault/messages.png) Po zakończeniu operacji usługa wyświetla komunikat: Zatrzymywanie tworzenia kopii zapasowej i usuwanie danych kopii zapasowej *dla elementu kopii zapasowej.* *Pomyślnie ukończono operację*.
6. Wybierz **pozycję Odśwież** w menu Elementy **kopii** zapasowej, aby upewnić się, że element kopii zapasowej został usunięty.

>[!NOTE]
>Jeśli usuniesz lokalnie chroniony element z portalu zawierającego zależności, otrzymasz ostrzeżenie "Usuwanie rejestracji serwera jest operacją destruktywną i nie można jej cofnąć. Wszystkie dane kopii zapasowej (punkty odzyskiwania wymagane do przywrócenia danych) i elementy kopii zapasowej skojarzone z serwerem chronionym zostaną trwale usunięte."

Po zakończeniu tego procesu można usunąć elementy kopii zapasowej z konsoli zarządzania:

- [Usuwanie elementów kopii zapasowej z konsoli zarządzania usługi MARS](#delete-backup-items-from-the-mars-management-console)
- [Usuwanie elementów kopii zapasowej z konsoli zarządzania usługi MABS lub programu DPM](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Usuwanie elementów kopii zapasowej z konsoli zarządzania usługi MARS

>[!NOTE]
>Jeśli maszyna źródłowa została usunięta lub utracona bez zatrzymywania tworzenia kopii zapasowej, następna zaplanowana kopia zapasowa zakończy się niepowodzeniem. Stary punkt odzyskiwania wygasa zgodnie z zasadami, ale ostatni pojedynczy punkt odzyskiwania jest zawsze zachowywany do momentu zatrzymania tworzenia kopii zapasowej i usunięcia danych. W tym celu wykonaj kroki opisane w [tej sekcji](#delete-protected-items-on-premises).

1. Otwórz konsolę zarządzania usługą MARS, przejdź do **okienka Akcje** i wybierz pozycję **Zaplanuj tworzenie kopii zapasowej.**
2. Na stronie **Modyfikowanie lub zatrzymywanie zaplanowanej kopii** zapasowej wybierz pozycję Zatrzymaj używanie tego harmonogramu tworzenia kopii zapasowych i usuń **wszystkie przechowywane kopie zapasowe.** Następnie wybierz pozycję **Dalej**.

    ![Modyfikowanie lub zatrzymywanie zaplanowanej kopii zapasowej.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymywanie zaplanowanej kopii zapasowej** wybierz pozycję **Zakończ.**

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. W tym celu najpierw zaloguj się do Azure Portal.
5. Przejdź do **właściwości ustawień magazynu** usługi Recovery  >    >  Services.
6. W **obszarze Numer PIN zabezpieczeń** wybierz pozycję **Generuj**. Skopiuj ten numer PIN. Numer PIN jest ważny tylko przez pięć minut.
7. W konsoli zarządzania wklej numer PIN, a następnie wybierz przycisk **OK.**

    ![Wygeneruj numer PIN zabezpieczeń.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikowanie postępu tworzenia** kopii zapasowej zostanie wyświetlony następujący komunikat: Usunięte dane kopii zapasowej *będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuń infrastrukturę kopii zapasowych.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj następne kroki z portalu.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Usuwanie elementów kopii zapasowej z konsoli zarządzania usługi MABS lub programu DPM

>[!NOTE]
>Jeśli maszyna źródłowa została usunięta lub utracona bez zatrzymywania kopii zapasowej, następna zaplanowana kopia zapasowa zakończy się niepowodzeniem. Stary punkt odzyskiwania wygasa zgodnie z zasadami, ale ostatni pojedynczy punkt odzyskiwania jest zawsze zachowywany do momentu zatrzymania tworzenia kopii zapasowej i usunięcia danych. W tym celu wykonaj kroki opisane w [tej sekcji](#delete-protected-items-on-premises).

Istnieją dwie metody usuwania elementów kopii zapasowej z konsoli zarządzania usługi MABS lub programu DPM.

#### <a name="method-1"></a>Metoda 1

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1. Otwórz okienko Konsola administratora programu DPM a następnie wybierz **pozycję Ochrona** na pasku nawigacyjnym.
2. W okienku wyświetlania wybierz członka grupy ochrony, który chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wybrać **opcję Zatrzymaj ochronę członków** grupy.
3. W **oknie dialogowym Zatrzymywanie** ochrony wybierz pozycję Usuń **chronione dane,** a następnie zaznacz **pole wyboru Usuń magazyn online.** Następnie wybierz pozycję **Zatrzymaj ochronę.**

    ![Wybierz pozycję Usuń chronione dane w okienku Zatrzymaj ochronę.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Stan chronionego członka zmieni się na *Nieaktywna replika dostępna.*

4. Kliknij prawym przyciskiem myszy nieaktywną grupę ochrony i wybierz **polecenie Usuń nieaktywną ochronę.**

    ![Usuwanie nieaktywnej ochrony.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. W **oknie Usuwanie** nieaktywnej ochrony zaznacz **pole wyboru Usuń magazyn online,** a następnie wybierz przycisk **OK.**

    ![Usuń magazyn online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metoda 2

Otwórz **konsolę zarządzania usługą MABS** **lub konsolę zarządzania programu DPM.** W **obszarze Wybierz metodę ochrony danych** wyczyść pole wyboru Chcę  **chronić w** trybie online.

  ![Wybierz metodę ochrony danych.](./media/backup-azure-delete-vault/data-protection-method.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj następne kroki w portalu.

## <a name="delete-the-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

1. Po usunięciu wszystkich zależności przewiń do okienka **Podstawowe elementy** w menu magazynu.
2. Sprawdź, czy na liście nie ma żadnych elementów kopii zapasowej, serwerów zarządzania kopiami zapasowymi ani zreplikowanych elementów. Jeśli elementy nadal są wyświetlane w magazynie, zapoznaj się z [sekcją Przed rozpoczęciem.](#before-you-start)

3. Jeśli w magazynie nie ma już żadnych elementów, wybierz pozycję **Usuń na pulpicie** nawigacyjnym magazynu.

    ![Wybierz pozycję Usuń na pulpicie nawigacyjnym magazynu.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Wybierz **pozycję Tak,** aby sprawdzić, czy chcesz usunąć magazyn. Magazyn zostanie usunięty. Portal powróci do menu **Nowa** usługa.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Usuwanie magazynu usługi Recovery Services przy użyciu programu PowerShell

Najpierw przeczytaj **[sekcję Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej:

- Jeśli używasz bazy danych SQL na kopiach zapasowych maszyn wirtualnych platformy Azure i włączono automatyczną ochronę wystąpień SQL, najpierw wyłącz ochronę automatyczną.

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

  [Dowiedz się więcej](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) na temat wyłączania ochrony Azure Backup chronionego przez element.

- Zatrzymaj ochronę i usuń dane dla wszystkich elementów chronionych przez kopię zapasową w chmurze (na przykład: maszyna wirtualna IaaS, udział plików platformy Azure i tak dalej):

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

    [Dowiedz się więcej](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   Informacje o wyłącza ochronę elementu chronionego przez kopię zapasową.

- W przypadku plików i folderów lokalnych chronionych przy użyciu agenta Azure Backup (MARS) i kopii zapasowej na platformie Azure użyj następującego polecenia programu PowerShell, aby usunąć dane kopii zapasowej z każdego modułu programu PowerShell usługi MARS:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Następnie zostanie wyświetlony następujący monit:

    *Microsoft Azure Backup Czy na pewno chcesz usunąć te zasady tworzenia kopii zapasowych? Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte. <br/> [Y] Tak [A] Tak na Wszystkie [N] Nie [L] Nie do wszystkich [S] Wstrzymywanie [?] Pomoc (wartość domyślna to "Y"):*

- W przypadku maszyn lokalnych chronionych za pomocą usługi MABS (Microsoft Azure Backup Server) lub programu DPM (System Center Data Protection Manager) na platformie Azure użyj następującego polecenia, aby usunąć dane kopii zapasowej na platformie Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Następnie zostanie wyświetlony następujący monit:

   *Microsoft Azure Backup* Czy na pewno chcesz usunąć te zasady tworzenia kopii zapasowych? Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte. <br/>
   [Y] Tak [A] Tak na Wszystkie [N] Nie [L] Nie do wszystkich [S] Wstrzymywanie [?] Pomoc (wartość domyślna to "Y"):*

Po usunięciu danych kopii zapasowej należy wyrejestrować wszystkie lokalne kontenery i serwery zarządzania.

- W przypadku plików i folderów lokalnych chronionych przy użyciu Azure Backup Agent (MARS) kopii zapasowej na platformie Azure:

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

    [Dowiedz się więcej](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) na temat rejestrowania systemu Windows Server lub innego kontenera z magazynu.

- W przypadku maszyn lokalnych chronionych przy użyciu usługi MABS (Microsoft Azure Backup Server) lub programu DPM do platformy Azure (System Center Zarządzanie ochroną danych:

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

    [Dowiedz się więcej](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) na temat rejestrowania kontenera zarządzania usługą Backup z magazynu.

Po trwałym usunięciu kopii zapasowej danych i cofeniu rejestracji wszystkich kontenerów przejdź do usuwania magazynu.

Aby usunąć magazyn usługi Recovery Services:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Dowiedz się więcej](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) o usuwaniu magazynu usługi Recovery Services.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Usuwanie magazynu usługi Recovery Services przy użyciu interfejsu wiersza polecenia

Najpierw przeczytaj sekcję **[Przed rozpoczęciem,](#before-you-start)** aby zrozumieć zależności i proces usuwania magazynu.

> [!NOTE]
> Obecnie interfejs wiersza Azure Backup obsługuje zarządzanie tylko kopiami zapasami maszyn wirtualnych platformy Azure, dlatego następujące polecenie w celu usunięcia magazynu działa tylko wtedy, gdy magazyn zawiera kopie zapasowe maszyn wirtualnych platformy Azure. Nie można usunąć magazynu przy użyciu interfejsu wiersza Azure Backup, jeśli magazyn zawiera jakikolwiek element kopii zapasowej innego typu niż maszyny wirtualne platformy Azure.

Aby usunąć istniejący magazyn usługi Recovery Services, wykonaj następujące kroki:

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

    Aby uzyskać więcej informacji, zobacz ten [artykuł.](/cli/azure/backup/protection#az_backup_protection_disable)

- Usuń istniejący magazyn usługi Recovery Services:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Aby uzyskać więcej informacji, zobacz ten [artykuł](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Usuwanie magazynu usługi Recovery Services przy użyciu Azure Resource Manager

Ta opcja usuwania magazynu usługi Recovery Services jest zalecana tylko wtedy, gdy wszystkie zależności zostaną usunięte i nadal występuje błąd *usuwania magazynu*. Wypróbuj dowolne lub wszystkie poniższe porady:

- W **okienku Podstawowe elementy** w menu magazynu sprawdź, czy na liście nie ma żadnych elementów kopii zapasowej, serwerów zarządzania kopiami zapasowymi ani zreplikowanych elementów. Jeśli istnieją elementy kopii zapasowej, zapoznaj się z [sekcją Przed rozpoczęciem.](#before-you-start)
- Spróbuj [ponownie usunąć magazyn z portalu.](#delete-the-recovery-services-vault)
- Jeśli wszystkie zależności zostaną usunięte i nadal występuje błąd usuwania *magazynu,* użyj narzędzia ARMClient, aby wykonać następujące kroki (po uwagach).

1. Przejdź do [chocolatey.org,](https://chocolatey.org/) aby pobrać i zainstalować oprogramowanie Chocolatey. Następnie zainstaluj aplikację ARMClient, uruchamiając następujące polecenie:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Zaloguj się do konta platformy Azure, a następnie uruchom następujące polecenie:

    `ARMClient.exe login [environment name]`

3. W Azure Portal zbierz identyfikator subskrypcji i nazwę grupy zasobów dla magazynu, który chcesz usunąć.

Aby uzyskać więcej informacji na temat polecenia ARMClient, zobacz [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Używanie klienta Azure Resource Manager do usuwania magazynu usługi Recovery Services

1. Uruchom następujące polecenie, używając identyfikatora subskrypcji, nazwy grupy zasobów i nazwy magazynu. Jeśli nie masz żadnych zależności, magazyn zostanie usunięty po uruchomieniu następującego polecenia:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Jeśli magazyn nie jest pusty, zostanie wyświetlony następujący komunikat o błędzie: Nie można usunąć magazynu, ponieważ istnieją zasoby *w tym magazynie.* Aby usunąć chroniony element lub kontener w magazynie, uruchom następujące polecenie:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. W Azure Portal upewnij się, że magazyn został usunięty.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o magazynach usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) 
 [Dowiedz się więcej o monitorowaniu magazynów usługi Recovery Services i zarządzaniu nimi](backup-azure-manage-windows-server.md)
