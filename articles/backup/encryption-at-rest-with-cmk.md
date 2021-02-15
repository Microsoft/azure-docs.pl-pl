---
title: Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta
description: Dowiedz się, jak Azure Backup umożliwia szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 230669e0a3543a0709dda3f7fee35a0cae300d5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369462"
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

- Magazyn Recovery Services może być szyfrowany tylko z kluczami przechowywanymi w Azure Key Vault, które znajdują się w **tym samym regionie**. Ponadto klucze muszą zawierać tylko **klucze RSA 2048** i powinny być w stanie **włączony** .

- Przeniesienie CMK szyfrowanego magazynu Recovery Services między grupami zasobów i subskrypcjami nie jest obecnie obsługiwane.
- Po przeniesieniu magazynu Recovery Services już zaszyfrowanego przy użyciu kluczy zarządzanych przez klienta do nowej dzierżawy należy zaktualizować magazyn Recovery Services, aby ponownie utworzyć i ponownie skonfigurować tożsamość zarządzaną magazynu i CMK (który powinien znajdować się w nowej dzierżawie). Jeśli to nie zrobisz, operacje tworzenia kopii zapasowej i przywracania zakończą się niepowodzeniem. Ponadto należy zmienić konfigurację wszystkich uprawnień kontroli dostępu opartej na rolach (RBAC) skonfigurowanych w ramach subskrypcji.

- Tę funkcję można skonfigurować za pomocą Azure Portal i programu PowerShell.

    >[!NOTE]
    >Użyj AZ module 5.3.0 lub nowszego, aby używać kluczy zarządzanych przez klienta do tworzenia kopii zapasowych w magazynie Recovery Services.

Jeśli magazyn Recovery Services nie został utworzony i skonfigurowany, możesz [zapoznać się z](backup-create-rs-vault.md)artykułem jak to zrobić.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurowanie magazynu do szyfrowania przy użyciu kluczy zarządzanych przez klienta

Ta sekcja obejmuje następujące kroki:

1. Włącz zarządzaną tożsamość magazynu Recovery Services

1. Przypisz uprawnienia do magazynu, aby uzyskać dostęp do klucza szyfrowania w Azure Key Vault

1. Włącz ochronę przed usuwaniem i przeczyszczaniem na Azure Key Vault

1. Przypisywanie klucza szyfrowania do magazynu Recovery Services

W celu osiągnięcia zamierzonych wyników należy wykonać wszystkie te kroki w podanej powyżej kolejności. Każdy krok został szczegółowo omówiony poniżej.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Włącz zarządzaną tożsamość magazynu Recovery Services

Azure Backup używa tożsamości zarządzanej przypisanej przez system do uwierzytelniania magazynu Recovery Services w celu uzyskania dostępu do kluczy szyfrowania przechowywanych w Azure Key Vault. Aby włączyć zarządzaną tożsamość magazynu Recovery Services, wykonaj czynności opisane poniżej.

>[!NOTE]
>Po włączeniu **nie** można wyłączyć zarządzanej tożsamości (nawet tymczasowo). Wyłączenie zarządzanej tożsamości może prowadzić do niespójnych zachowań.

**W portalu:**

1. Przejdź do magazynu Recovery Services — > **tożsamość**

    ![Ustawienia tożsamości](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Zmień **stan** na **włączone** i wybierz pozycję **Zapisz**.

1. Generowany jest identyfikator obiektu, który jest zarządzaną przez system tożsamością magazynową.

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

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Przypisz uprawnienia do magazynu Recovery Services, aby uzyskać dostęp do klucza szyfrowania w Azure Key Vault

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

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Włącz ochronę przed usuwaniem i przeczyszczaniem na Azure Key Vault

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

### <a name="assign-encryption-key-to-the-rs-vault"></a>Przypisywanie klucza szyfrowania do magazynu RS

>[!NOTE]
> Przed kontynuowaniem upewnij się, że:
>
> - Wszystkie kroki wymienione powyżej zostały pomyślnie wykonane:
>   - Zarządzana tożsamość magazynu Recovery Services została włączona i przypisano wymagane uprawnienia
>   - Azure Key Vault z włączonym usuwaniem nietrwałym i przeczyszczaniem
> - Magazyn Recovery Services, dla którego chcesz **włączyć szyfrowanie CMK nie ma** żadnych elementów chronionych ani zarejestrowanych w nim

Po upewnieniu się, że można kontynuować wybieranie klucza szyfrowania dla magazynu.

#### <a name="to-assign-the-key-in-the-portal"></a>Aby przypisać klucz w portalu

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
        >Przy określaniu klucza szyfrowania za pomocą okienka selektora kluczy, klucz zostanie przeobrócony przy każdym włączeniu nowej wersji klucza.

        ![Wybierz klucz z magazynu kluczy](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Wybierz pozycję **Zapisz**.

1. **Śledzenie postępu i stanu aktualizacji klucza szyfrowania**: postęp i stan przypisania klucza szyfrowania można śledzić przy użyciu widoku **zadania tworzenia kopii zapasowej** na lewym pasku nawigacyjnym. Stan powinien wkrótce ulec zmianie na **zakończony**. Magazyn będzie teraz szyfrować wszystkie dane z określonym kluczem jako KEK.

    ![Stan zakończony](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Aktualizacje klucza szyfrowania są również rejestrowane w dzienniku aktywności magazynu.

    ![Dziennik aktywności](./media/encryption-at-rest-with-cmk/activity-log.png)

#### <a name="to-assign-the-key-with-powershell"></a>Aby przypisać klucz przy użyciu programu PowerShell

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
> - Key Vault znajduje się w tym samym regionie co magazyn Recovery Services
>
> - Magazyn kluczy ma włączoną ochronę nietrwałego usuwania i przeczyszczania
>
> - Magazyn Recovery Services ma uprawnienia wymagane do uzyskania dostępu do Key Vault.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Tworzenie kopii zapasowej w magazynie zaszyfrowanym przy użyciu kluczy zarządzanych przez klienta

Przed przystąpieniem do konfigurowania ochrony zdecydowanie zalecamy upewnienie się, że Poniższa lista kontrolna jest zgodna z. Jest to ważne, ponieważ po skonfigurowaniu elementu do utworzenia kopii zapasowej (lub próby jego skonfigurowania) do magazynu z zaszyfrowaną CMK nie można włączyć szyfrowania przy użyciu kluczy zarządzanych przez klienta i nadal będzie on używać kluczy zarządzanych przez platformę.

>[!IMPORTANT]
> Przed przystąpieniem do konfigurowania ochrony należy **pomyślnie** wykonać następujące czynności:
>
>1. Tworzenie magazynu kopii zapasowych
>1. Włączono tożsamość zarządzaną przez magazyn kopii zapasowych przypisanej do systemu
>1. Przypisano uprawnienia do magazynu kopii zapasowych w celu uzyskania dostępu do kluczy szyfrowania z Key Vault
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
