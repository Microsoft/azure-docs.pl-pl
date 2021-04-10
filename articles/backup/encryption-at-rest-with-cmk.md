---
title: Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta
description: Dowiedz się, jak Azure Backup umożliwia szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta (CMK).
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: b6cb1a288d0052b39bbeb52ed9fd20e68a6427ed
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167894"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Azure Backup umożliwia szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta (CMK) zamiast korzystania z kluczy zarządzanych przez platformę, która jest domyślnie włączona. Klucze używane do szyfrowania danych kopii zapasowej muszą być przechowywane w [Azure Key Vault](../key-vault/index.yml).

Klucz szyfrowania używany do szyfrowania kopii zapasowych może być inny niż użyty dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na protokole AES 256 (z szyfrowaniem), który z kolei jest chroniony przy użyciu kluczy (KEK). Zapewnia to pełną kontrolę nad danymi i kluczami. Aby umożliwić szyfrowanie, wymagane jest przyznanie magazynowi Recovery Services dostępu do klucza szyfrowania w Azure Key Vault. W razie potrzeby można zmienić klucz.

W tym artykule omówiono następujące zagadnienia:

- Tworzenie magazynu Recovery Services
- Konfigurowanie magazynu Recovery Services do szyfrowania danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta
- Wykonywanie kopii zapasowych magazynów szyfrowanych przy użyciu kluczy zarządzanych przez klienta
- Przywracanie danych z kopii zapasowych

## <a name="before-you-start"></a>Przed rozpoczęciem

- Ta funkcja umożliwia szyfrowanie **tylko nowych magazynów Recovery Services**. Wszystkie magazyny zawierające istniejące elementy zarejestrowane lub próby zarejestrowania w niej nie są obsługiwane.

- Po włączeniu obsługi magazynu Recovery Services szyfrowanie przy użyciu kluczy zarządzanych przez klienta nie może zostać przywrócone do korzystania z kluczy zarządzanych przez platformę (domyślnie). Klucze szyfrowania można zmienić zgodnie z wymaganiami.

- Ta funkcja obecnie **nie obsługuje tworzenia kopii zapasowych przy użyciu agenta Mars** i może nie być możliwe użycie magazynu CMK-Encrypted. Agent MARS używa szyfrowania opartego na hasłach użytkownika. Ta funkcja nie obsługuje także tworzenia kopii zapasowych klasycznych maszyn wirtualnych.

- Ta funkcja nie odnosi się do [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), która korzysta z szyfrowania na podstawie gościa dysków maszyny wirtualnej za pomocą funkcji BitLocker (dla systemu Windows) i DM-Crypt (w systemie Linux)

- Magazyn Recovery Services może być szyfrowany tylko z kluczami przechowywanymi w Azure Key Vault, które znajdują się w **tym samym regionie**. Ponadto klucze muszą być tylko **kluczami RSA** i powinny być w stanie **włączonym** .

- Przeniesienie CMK szyfrowanego magazynu Recovery Services między grupami zasobów i subskrypcjami nie jest obecnie obsługiwane.
- Po przeniesieniu magazynu Recovery Services już zaszyfrowanego przy użyciu kluczy zarządzanych przez klienta do nowej dzierżawy należy zaktualizować magazyn Recovery Services, aby ponownie utworzyć i ponownie skonfigurować tożsamość zarządzaną magazynu i CMK (który powinien znajdować się w nowej dzierżawie). Jeśli to nie zrobisz, operacje tworzenia kopii zapasowej i przywracania zakończą się niepowodzeniem. Ponadto należy zmienić konfigurację wszystkich uprawnień kontroli dostępu opartej na rolach (RBAC) skonfigurowanych w ramach subskrypcji.

- Tę funkcję można skonfigurować za pomocą Azure Portal i programu PowerShell.

    >[!NOTE]
    >Użyj AZ module 5.3.0 lub nowszego, aby używać kluczy zarządzanych przez klienta do tworzenia kopii zapasowych w magazynie Recovery Services.
    
    >[!Warning]
    >Jeśli używasz programu PowerShell do zarządzania kluczami szyfrowania dla kopii zapasowej, nie zalecamy aktualizowania kluczy z portalu.<br></br>W przypadku zaktualizowania klucza z portalu nie można użyć programu PowerShell do zaktualizowania klucza szyfrowania, dopóki Aktualizacja programu PowerShell obsługuje nowy model. Można jednak kontynuować aktualizowanie klucza z Azure Portal.

Jeśli magazyn Recovery Services nie został utworzony i skonfigurowany, możesz [zapoznać się z](backup-create-rs-vault.md)artykułem jak to zrobić.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurowanie magazynu do szyfrowania przy użyciu kluczy zarządzanych przez klienta

Ta sekcja obejmuje następujące kroki:

1. Włącz zarządzaną tożsamość magazynu Recovery Services

1. Przypisz uprawnienia do magazynu, aby uzyskać dostęp do klucza szyfrowania w Azure Key Vault

1. Włącz ochronę przed usuwaniem i przeczyszczaniem na Azure Key Vault

1. Przypisywanie klucza szyfrowania do magazynu Recovery Services

W celu osiągnięcia zamierzonych wyników należy wykonać wszystkie te kroki w podanej powyżej kolejności. Każdy krok został szczegółowo omówiony poniżej.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Włącz zarządzaną tożsamość magazynu Recovery Services

Azure Backup używa tożsamości zarządzanych przypisanych przez system i tożsamości zarządzanych przypisanych przez użytkownika w celu uwierzytelnienia magazynu Recovery Services w celu uzyskania dostępu do kluczy szyfrowania przechowywanych w Azure Key Vault. Aby włączyć zarządzaną tożsamość magazynu Recovery Services, wykonaj czynności opisane poniżej.

>[!NOTE]
>Po włączeniu **nie** można wyłączyć zarządzanej tożsamości (nawet tymczasowo). Wyłączenie zarządzanej tożsamości może prowadzić do niespójnych zachowań.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Włącz tożsamość zarządzaną przypisaną przez system do magazynu

**W portalu:**

1. Przejdź do magazynu Recovery Services — > **tożsamość**

    ![Ustawienia tożsamości](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Przejdź do karty **przypisane do systemu** .

1. Zmień **stan** na **włączone**.

1. Kliknij przycisk **Zapisz** , aby włączyć tożsamość magazynu.

Generowany jest identyfikator obiektu, który jest zarządzaną przez system tożsamością magazynową.

>[!NOTE]
>Po włączeniu nie można wyłączyć zarządzanej tożsamości (nawet tymczasowo). Wyłączenie zarządzanej tożsamości może prowadzić do niespójnych zachowań.


**Za pomocą programu PowerShell:**

Użyj polecenia [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) , aby włączyć tożsamość zarządzaną przypisaną przez system dla magazynu usługi Recovery Services.

Przykład:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Dane wyjściowe:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do magazynu

Aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do magazynu Recovery Services, wykonaj następujące czynności:

1.  Przejdź do magazynu Recovery Services — > **tożsamość**

    ![Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do magazynu](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Przejdź do karty **przypisane przez użytkownika** .

1.  Kliknij pozycję **+ Dodaj** , aby dodać tożsamość zarządzaną przypisaną przez użytkownika.

1.  W otwartym bloku **Dodaj tożsamość zarządzaną przez użytkownika** wybierz subskrypcję dla swojej tożsamości.

1.  Wybierz tożsamość z listy. Można również filtrować według nazwy tożsamości lub grupy zasobów.

1.  Po zakończeniu kliknij przycisk **Dodaj** , aby zakończyć przypisywanie tożsamości.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Przypisz uprawnienia do magazynu Recovery Services, aby uzyskać dostęp do klucza szyfrowania w Azure Key Vault

>[!Note]
>Jeśli używasz tożsamości przypisanej do użytkownika, te same uprawnienia muszą być przypisane do tożsamości przypisanej do użytkownika.

Teraz musisz zezwolić magazynowi Recovery Services na dostęp do Azure Key Vault, który zawiera klucz szyfrowania. Jest to realizowane przez umożliwienie zarządzanej tożsamości magazynu Recovery Services dostępu do Key Vault.

**W portalu**:

1. Przejdź do **zasad dostępu**> Azure Key Vault. Kontynuuj **i Dodaj zasady dostępu**.

    ![Dodawanie zasad dostępu](./media/encryption-at-rest-with-cmk/access-policies.png)

1. W **obszarze uprawnienia klucza** wybierz pozycję **Pobierz**, **Wyświetl**, **Cofnij Zawijanie klucza** i **Zawijaj kluczowe** operacje. Określa akcje dla klucza, które będą dozwolone.

    ![Przypisywanie uprawnień klucza](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Przejdź do pozycji **Wybierz podmiot zabezpieczeń** i Wyszukaj swój magazyn w polu wyszukiwania przy użyciu jego nazwy lub tożsamości zarządzanej. Po wyświetleniu listy wybierz magazyn, a następnie wybierz **pozycję Wybierz** w dolnej części okienka.

    ![Wybierz podmiot zabezpieczeń](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Po zakończeniu wybierz pozycję **Dodaj** , aby dodać nowe zasady dostępu.

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany wprowadzone w zasadach dostępu Azure Key Vault.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Włącz ochronę przed usuwaniem i przeczyszczaniem na Azure Key Vault

Musisz **włączyć nietrwałe usuwanie i przeczyszczanie ochrony** na Azure Key Vault, w którym jest przechowywany klucz szyfrowania. Można to zrobić z poziomu interfejsu użytkownika Azure Key Vault, jak pokazano poniżej. (Można również ustawić te właściwości podczas tworzenia Key Vault). Więcej informacji na temat tych Key Vaultch właściwości można znaleźć [tutaj](../key-vault/general/soft-delete-overview.md).

![Włączanie usuwania nietrwałego i ochrony przed przeczyszczaniem](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Możesz również włączyć nietrwałe usuwanie i ochronę przed przeczyszczaniem za pomocą programu PowerShell, wykonując poniższe kroki:

1. Zaloguj się do konta platformy Azure.

    ```azurepowershell
    Login-AzAccount
    ```

1. Wybierz subskrypcję zawierającą magazyn.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Włączanie usuwania nietrwałego

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Włącz ochronę przed przeczyszczaniem

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Przypisywanie klucza szyfrowania do magazynu RS

>[!NOTE]
> Przed kontynuowaniem upewnij się, że:
>
> - Wszystkie kroki wymienione powyżej zostały pomyślnie wykonane:
>   - Zarządzana tożsamość magazynu Recovery Services została włączona i przypisano wymagane uprawnienia
>   - Azure Key Vault z włączonym usuwaniem nietrwałym i przeczyszczaniem
> - Magazyn Recovery Services, dla którego chcesz **włączyć szyfrowanie CMK nie ma** żadnych elementów chronionych ani zarejestrowanych w nim

Po upewnieniu się, że można kontynuować wybieranie klucza szyfrowania dla magazynu.

### <a name="to-assign-the-key-in-the-portal"></a>Aby przypisać klucz w portalu

1. Przejdź do Recovery Services magazynu — **właściwości** >

    ![Ustawienia szyfrowania](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Wybierz pozycję **Aktualizuj** w obszarze **Ustawienia szyfrowania**.

1. W okienku ustawienia szyfrowania wybierz opcję **Użyj własnego klucza** i Kontynuuj, aby określić klucz przy użyciu jednego z następujących sposobów. **Upewnij się, że klucz, którego chcesz użyć, jest kluczem RSA 2048, który jest w stanie włączonym.**

    1. Wprowadź **Identyfikator URI klucza** , za pomocą którego chcesz zaszyfrować dane w tym magazynie Recovery Services. Należy również określić subskrypcję, w której znajduje się Azure Key Vault (zawierająca ten klucz). Ten identyfikator URI klucza można uzyskać z odpowiedniego klucza w Azure Key Vault. Upewnij się, że identyfikator URI klucza został poprawnie skopiowany. Zalecane jest użycie przycisku **Kopiuj do schowka** dostarczonego z identyfikatorem klucza.

        >[!NOTE]
        >Podczas określania klucza szyfrowania przy użyciu identyfikatora URI klucza klucz nie będzie obracany. Aby aktualizacje kluczy były wykonywane ręcznie, należy w razie potrzeby podać nowy klucz.

        ![Wprowadź identyfikator URI klucza](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Przeglądaj i wybierz klucz z Key Vault w okienku selektora kluczy.

        >[!NOTE]
        >Przy określaniu klucza szyfrowania za pomocą okienka selektora kluczy, klucz zostanie przeobrócony przy każdym włączeniu nowej wersji klucza. [Dowiedz się więcej](#enabling-auto-rotation-of-encryption-keys) na temat włączania funkcji autorotacji kluczy szyfrowania.

        ![Wybierz klucz z magazynu kluczy](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Wybierz pozycję **Zapisz**.

1. **Śledzenie postępu i stanu aktualizacji klucza szyfrowania**: postęp i stan przypisania klucza szyfrowania można śledzić przy użyciu widoku **zadania tworzenia kopii zapasowej** na lewym pasku nawigacyjnym. Stan powinien wkrótce ulec zmianie na **zakończony**. Magazyn będzie teraz szyfrować wszystkie dane z określonym kluczem jako KEK.

    ![Stan zakończony](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Aktualizacje klucza szyfrowania są również rejestrowane w dzienniku aktywności magazynu.

    ![Dziennik aktywności](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>Aby przypisać klucz przy użyciu programu PowerShell

Użyj polecenia [Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) , aby włączyć szyfrowanie przy użyciu kluczy zarządzanych przez klienta oraz przypisać lub zaktualizować klucz szyfrowania, który ma być używany.

Przykład:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Dane wyjściowe:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Ten proces pozostaje taki sam, gdy chcesz zaktualizować lub zmienić klucz szyfrowania. Jeśli chcesz zaktualizować i użyć klucza z innego Key Vault (innego niż aktualnie używane), upewnij się, że:
>
> - Magazyn kluczy znajduje się w tym samym regionie co magazyn Recovery Services
>
> - Magazyn kluczy ma włączoną ochronę nietrwałego usuwania i przeczyszczania
>
> - Magazyn Recovery Services ma wymagane uprawnienia dostępu do magazynu kluczy.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Tworzenie kopii zapasowej w magazynie zaszyfrowanym przy użyciu kluczy zarządzanych przez klienta

Przed przystąpieniem do konfigurowania ochrony zdecydowanie zalecamy upewnienie się, że Poniższa lista kontrolna jest zgodna z. Jest to ważne, ponieważ po skonfigurowaniu elementu do utworzenia kopii zapasowej (lub próby jego skonfigurowania) do magazynu z zaszyfrowaną CMK nie można włączyć szyfrowania przy użyciu kluczy zarządzanych przez klienta i nadal będzie on używać kluczy zarządzanych przez platformę.

>[!IMPORTANT]
> Przed przystąpieniem do konfigurowania ochrony należy **pomyślnie** wykonać następujące czynności:
>
>1. Tworzenie magazynu kopii zapasowych
>1. Włączono tożsamość zarządzaną przez magazyn Recovery Services lub przypisaną do magazynu tożsamość zarządzaną przez użytkownika
>1. Przypisane uprawnienia do magazynu kopii zapasowych (lub tożsamości zarządzanej przypisanej przez użytkownika) w celu uzyskania dostępu do kluczy szyfrowania z Key Vault
>1. Włączone trwałe usuwanie i przeczyszczanie ochrony dla Key Vault
>1. Przypisano prawidłowy klucz szyfrowania dla magazynu kopii zapasowych
>
>Jeśli wszystkie powyższe kroki zostały potwierdzone, dopiero wtedy kontynuuj konfigurowanie kopii zapasowej.

Proces konfigurowania i wykonywania kopii zapasowych w magazynie Recovery Services szyfrowanym przy użyciu kluczy zarządzanych przez klienta jest taki sam jak w przypadku magazynu korzystającego z kluczy zarządzanych przez platformę, **bez wprowadzania zmian w środowisku**. Jest to prawdziwe w przypadku [tworzenia kopii zapasowych maszyn wirtualnych platformy Azure](./quick-backup-vm-portal.md) , a także tworzenia kopii zapasowych obciążeń uruchomionych wewnątrz maszyny wirtualnej (na przykład [SAP HANA](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) baz danych).

## <a name="restoring-data-from-backup"></a>Przywracanie danych z kopii zapasowej

### <a name="vm-backup"></a>Kopia zapasowa maszyny wirtualnej

Dane przechowywane w magazynie Recovery Services można przywrócić zgodnie z krokami opisanymi [tutaj](./backup-azure-arm-restore-vms.md). W przypadku przywracania z magazynu Recovery Services szyfrowanego przy użyciu kluczy zarządzanych przez klienta można zaszyfrować przywrócone dane przy użyciu zestawu szyfrowania dysku (DES).

#### <a name="restoring-vm--disk"></a>Przywracanie maszyny wirtualnej/dysku

1. Przywrócone dane zostaną zaszyfrowane przy użyciu algorytmu DES używanego do szyfrowania dysków źródłowej maszyny wirtualnej w przypadku odzyskiwania dysku/maszyny wirtualnej z punktu przywracania "migawka".

1. Podczas przywracania dysku/maszyny wirtualnej z punktu odzyskiwania z typem odzyskiwania jako "magazyn" można wybrać, aby przywrócone dane były szyfrowane przy użyciu algorytmu DES, określonego w momencie przywracania. Alternatywnie można wybrać opcję kontynuowania przywracania danych bez określania algorytmu DES. w takim przypadku zostanie ono zaszyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

Przywrócony dysk/maszynę wirtualną można zaszyfrować po zakończeniu przywracania, niezależnie od dokonanego wyboru podczas inicjowania przywracania.

![Punkty przywracania](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Wybierz zestaw szyfrowania dysków podczas przywracania z punktu odzyskiwania magazynu

**W portalu**:

Zestaw szyfrowanie dysków jest określony w obszarze Ustawienia szyfrowania w okienku przywracanie, jak pokazano poniżej:

1. Na stronie **szyfrowanie dysków przy użyciu klucza** wybierz pozycję **tak**.

1. Z listy rozwijanej wybierz pozycję DES, która ma być używana dla przywróconych dysków. **Upewnij się, że masz dostęp do algorytmu DES.**

>[!NOTE]
>Możliwość wybrania algorytmu DES podczas przywracania jest niedostępna, jeśli przywracana jest maszyna wirtualna, która używa Azure Disk Encryption.

![Szyfrowanie dysku przy użyciu klucza](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Za pomocą programu PowerShell**:

Użyj polecenia [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) z parametrem [ `-DiskEncryptionSetId <string>` ], aby [określić algorytm DES,](/powershell/module/az.compute/get-azdiskencryptionset) który ma być używany do szyfrowania przywróconego dysku. Aby uzyskać więcej informacji o przywracaniu dysków z kopii zapasowej maszyny wirtualnej, zobacz [ten artykuł](./backup-azure-vms-automation.md#restore-an-azure-vm).

Przykład:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Przywracanie plików

Podczas przywracania pliku przywrócone dane zostaną zaszyfrowane przy użyciu klucza używanego do szyfrowania lokalizacji docelowej.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Przywracanie baz danych SAP HANA/SQL na maszynach wirtualnych platformy Azure

W przypadku przywracania z kopii zapasowej SAP HANA/bazy danych SQL działającej na maszynie wirtualnej platformy Azure przywrócone dane zostaną zaszyfrowane przy użyciu klucza szyfrowania używanego w docelowej lokalizacji magazynu. Może to być klucz zarządzany przez klienta lub klucz zarządzany przez platformę służący do szyfrowania dysków maszyny wirtualnej.

## <a name="additional-topics"></a>Tematy dodatkowe

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Włącz szyfrowanie przy użyciu kluczy zarządzanych przez klienta podczas tworzenia magazynu (w wersji zapoznawczej)

>[!NOTE]
>Włączenie szyfrowania przy tworzeniu magazynu przy użyciu kluczy zarządzanych przez klienta ma ograniczoną publiczną wersję zapoznawczą i wymaga listy dozwolonych subskrypcji. Aby utworzyć konto w wersji zapoznawczej, Wypełnij [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) i Zapisz go na stronie [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Gdy subskrypcja jest dozwolona na liście, zostanie wyświetlona karta **szyfrowanie kopii zapasowej** . Pozwala to włączyć szyfrowanie kopii zapasowej przy użyciu kluczy zarządzanych przez klienta podczas tworzenia nowego magazynu Recovery Services. Aby włączyć szyfrowanie, wykonaj następujące czynności:

1. Obok karty **podstawowe** na karcie **szyfrowanie kopii zapasowej** Określ klucz szyfrowania i tożsamość do użycia na potrzeby szyfrowania.

   ![Włącz szyfrowanie na poziomie magazynu](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >Ustawienia dotyczą tylko kopii zapasowej i są opcjonalne.

1. Wybierz opcję **Użyj klucza zarządzanego przez klienta** jako typu szyfrowania.

1. Aby określić klucz, który ma być używany do szyfrowania, wybierz odpowiednią opcję.

   Możesz podać identyfikator URI klucza szyfrowania lub wyszukać i wybrać klucz. Po określeniu klucza przy użyciu opcji **wybierz Key Vault** automatyczne obracanie klucza szyfrowania zostanie włączone automatycznie. [Dowiedz się więcej na temat autorotacji](#enabling-auto-rotation-of-encryption-keys). 

1. Określ tożsamość zarządzaną przypisaną przez użytkownika, aby zarządzać szyfrowaniem przy użyciu kluczy zarządzanych przez klienta. Kliknij przycisk **Wybierz** , aby przeglądać i wybrać wymaganą tożsamość.

1. Po wykonaniu tych czynności przejdź do Dodaj Tagi (opcjonalne) i Kontynuuj tworzenie magazynu.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Włączanie funkcji autorotacji kluczy szyfrowania

W przypadku określenia klucza zarządzanego przez klienta, który ma być używany do szyfrowania kopii zapasowych, należy określić następujące metody:

- Wprowadź identyfikator URI klucza
- Wybierz z Key Vault

Użycie opcji **Wybierz z Key Vault** pomaga włączyć funkcję autorotacji dla wybranego klucza. Eliminuje to ręczny nakład pracy w celu zaktualizowania do kolejnej wersji. Jednak przy użyciu tej opcji:
- Aktualizacja wersji klucza może zająć do godziny.
- Gdy nowa wersja klucza zacznie obowiązywać, stara wersja powinna być również dostępna (w stanie włączonym) dla co najmniej jednego kolejnego zadania tworzenia kopii zapasowej po zastosowaniu aktualizacji klucza.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Czy można zaszyfrować istniejący magazyn kopii zapasowych za pomocą kluczy zarządzanych przez klienta?

Nie, szyfrowanie CMK można włączyć tylko dla nowych magazynów. W związku z tym magazyn nigdy nie powinien mieć żadnych elementów chronionych. W rzeczywistości nie trzeba podejmować żadnych prób ochrony żadnych elementów w magazynie przed włączeniem szyfrowania przy użyciu kluczy zarządzanych przez klienta.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Podjęto próbę włączenia ochrony elementu do mojego magazynu, ale jego awaria nie powiodła się, a magazyn nadal nie zawiera żadnych elementów, do których są chronione. Czy mogę włączyć szyfrowanie CMK dla tego magazynu?

Nie. magazyn nie może mieć żadnych prób ochrony żadnych elementów w przeszłości.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Mam magazyn, który korzysta z szyfrowania CMK. Czy można później powracać do szyfrowania przy użyciu kluczy zarządzanych przez platformę, nawet jeśli mam elementy kopii zapasowej chronione do magazynu?

Nie, po włączeniu szyfrowania CMK nie można go przywrócić, aby używać kluczy zarządzanych przez platformę. Można zmienić klucze używane zgodnie z wymaganiami.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Czy szyfrowanie CMK dla Azure Backup ma również zastosowanie do Azure Site Recovery?

Nie. w tym artykule omówiono szyfrowanie tylko danych kopii zapasowej. W przypadku Azure Site Recovery należy ustawić właściwość oddzielnie jako dostępną w usłudze.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Pominięto jeden z kroków tego artykułu i zaszło do ochrony mojego źródła danych. Czy nadal mogę używać szyfrowania CMK?

Nie wykonano kroków opisanych w artykule i kontynuowanie ochrony elementów może prowadzić do magazynu, w którym nie można używać szyfrowania przy użyciu kluczy zarządzanych przez klienta. Dlatego zalecamy odwoływanie się do [tej listy kontrolnej](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) przed kontynuowaniem ochrony elementów.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Czy użycie CMK-Encryption umożliwia dodanie kosztu tworzenia kopii zapasowych?

Korzystanie z szyfrowania CMK dla kopii zapasowych nie wiąże się z żadnymi dodatkowymi kosztami. Można jednak nadal ponosić koszty używania Azure Key Vault, w którym jest przechowywany klucz.

## <a name="next-steps"></a>Następne kroki

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](security-overview.md)
