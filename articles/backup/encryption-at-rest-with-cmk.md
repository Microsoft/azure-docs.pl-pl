---
title: Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta
description: Dowiedz się, Azure Backup umożliwia szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta .
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: bd51be06e707674f3e35b3478d7f99d096be912a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718777"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Azure Backup umożliwia szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta zamiast kluczy zarządzanych przez platformę, które są domyślnie włączone. Klucze używane do szyfrowania danych kopii zapasowej muszą być przechowywane w Azure Key Vault [.](../key-vault/index.yml)

Klucz szyfrowania używany do szyfrowania kopii zapasowych może różnić się od klucza używanego dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na standardach AES 256( AES 256), który z kolei jest chroniony przy użyciu kluczy użytkownika (KEK). Zapewnia to pełną kontrolę nad danymi i kluczami. Aby zezwolić na szyfrowanie, magazyn usługi Recovery Services musi mieć dostęp do klucza szyfrowania w Azure Key Vault. Klucz można zmienić zgodnie z potrzebami i w razie potrzeby.

W tym artykule omówiono następujące kwestie:

- Tworzenie magazynu usługi Recovery Services
- Konfigurowanie magazynu usługi Recovery Services w celu szyfrowania danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta
- Wykonywanie kopii zapasowych w magazynach zaszyfrowanych przy użyciu kluczy zarządzanych przez klienta
- Przywracanie danych z kopii zapasowych

## <a name="before-you-start"></a>Przed rozpoczęciem

- Ta funkcja umożliwia szyfrowanie tylko **nowych magazynów usługi Recovery Services.** Magazyny zawierające istniejące elementy zarejestrowane lub próbujące je zarejestrować nie są obsługiwane.

- Po włączeniu magazynu usługi Recovery Services nie można przywrócić szyfrowania przy użyciu kluczy zarządzanych przez klienta przy użyciu kluczy zarządzanych przez platformę (ustawienie domyślne). Klucze szyfrowania można zmienić zgodnie z wymaganiami.

- Ta funkcja **obecnie nie obsługuje tworzenia kopii** zapasowych przy użyciu agenta MARS i może nie być w stanie używać magazynu zaszyfrowanego za pomocą kluczy CMK dla tego samego celu. Agent MARS używa szyfrowania opartego na hasłach użytkownika. Ta funkcja nie obsługuje również tworzenia kopii zapasowych klasycznych maszyn wirtualnych.

- Ta funkcja nie jest powiązana z [funkcją Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), która używa szyfrowania dysków maszyny wirtualnej opartego na gościu przy użyciu funkcji BitLocker (dla systemu Windows) i DM-Crypt (dla systemu Linux)

- Magazyn usługi Recovery Services można zaszyfrować tylko przy użyciu kluczy przechowywanych w Azure Key Vault, który znajduje się w **tym samym regionie.** Ponadto klucze muszą być tylko **kluczami RSA** i powinny być **włączone.**

- Przenoszenie magazynu usługi Recovery Services zaszyfrowanego za pomocą kluczy CMK między grupami zasobów i subskrypcjami nie jest obecnie obsługiwane.
- W przypadku przenoszenia już zaszyfrowanego magazynu usługi Recovery Services przy użyciu kluczy zarządzanych przez klienta do nowej dzierżawy należy zaktualizować magazyn usługi Recovery Services w celu ponownego tworzenia i ponownego konfigurowania tożsamości zarządzanej i klucza zarządzanego magazynu (który powinien być w nowej dzierżawie). Jeśli nie zostanie to zrobione, operacje tworzenia kopii zapasowej i przywracania rozpoczną się niepowodzeniem. Ponadto należy ponownie skonfigurować wszystkie uprawnienia kontroli dostępu opartej na rolach (RBAC) skonfigurowane w ramach subskrypcji.

- Tę funkcję można skonfigurować za pomocą Azure Portal programu PowerShell.

    >[!NOTE]
    >Użyj modułu Az 5.3.0 lub większego, aby używać kluczy zarządzanych przez klienta na potrzeby tworzenia kopii zapasowych w magazynie usługi Recovery Services.
    
    >[!Warning]
    >Jeśli używasz programu PowerShell do zarządzania kluczami szyfrowania dla kopii zapasowej, nie zalecamy aktualizowania kluczy z portalu.<br>W przypadku zaktualizowania klucza z poziomu portalu nie można użyć programu PowerShell do dalszej aktualizacji klucza szyfrowania, dopóki nie zostanie dostępna aktualizacja programu PowerShell obsługująca nowy model. Możesz jednak kontynuować aktualizowanie klucza z Azure Portal.

Jeśli magazyn usługi Recovery Services nie został utworzony i skonfigurowany, możesz przeczytać tutaj, [jak to zrobić.](backup-create-rs-vault.md)

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurowanie magazynu do szyfrowania przy użyciu kluczy zarządzanych przez klienta

Ta sekcja obejmuje następujące kroki:

1. Włączanie tożsamości zarządzanej dla magazynu usługi Recovery Services

1. Przypisywanie do magazynu uprawnień dostępu do klucza szyfrowania w Azure Key Vault

1. Włącz usuwanie nie soft i ochronę przed przeczyszczaniem na Azure Key Vault

1. Przypisywanie klucza szyfrowania do magazynu usługi Recovery Services

Aby osiągnąć zamierzone wyniki, należy wykonać wszystkie te kroki w kolejności wymienionej powyżej. Każdy krok został szczegółowo omówiony poniżej.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Włączanie tożsamości zarządzanej dla magazynu usługi Recovery Services

Azure Backup używa tożsamości zarządzanych przypisanych przez system i tożsamości zarządzanych przypisanych przez użytkownika do uwierzytelniania magazynu usługi Recovery Services w celu uzyskania dostępu do kluczy szyfrowania przechowywanych w Azure Key Vault. Aby włączyć tożsamość zarządzaną dla magazynu usługi Recovery Services, wykonaj kroki wymienione poniżej.

>[!NOTE]
>Po włączeniu tożsamości zarządzanej nie **można jej wyłączyć** (nawet tymczasowo). Wyłączenie tożsamości zarządzanej może prowadzić do niespójnego zachowania.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Włączanie tożsamości zarządzanej przypisanej przez system dla magazynu

**W portalu:**

1. Przejdź do magazynu usługi Recovery Services — > **Identity**

    ![Ustawienia tożsamości](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Przejdź do **karty Przypisane przez** system.

1. Zmień stan **na** **Wł.**.

1. Kliknij **przycisk Zapisz,** aby włączyć tożsamość dla magazynu.

Generowany jest identyfikator obiektu, który jest przypisaną przez system tożsamością zarządzaną magazynu.

>[!NOTE]
>Po włączeniu tożsamości zarządzanej nie można jej wyłączyć (nawet tymczasowo). Wyłączenie tożsamości zarządzanej może prowadzić do niespójnego zachowania.


**Za pomocą programu PowerShell:**

Użyj polecenia [Update-AzRecoveryServicesVault,](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) aby włączyć tożsamość zarządzaną przypisaną przez system dla magazynu usługi Recovery Services.

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

Aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do magazynu usługi Recovery Services, wykonaj następujące kroki:

1.  Przejdź do magazynu usługi Recovery Services — > **Identity**

    ![Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do magazynu](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Przejdź do **karty Przypisano** użytkownika.

1.  Kliknij **pozycję +Dodaj,** aby dodać tożsamość zarządzaną przypisaną przez użytkownika.

1.  W bloku **Dodawanie tożsamości zarządzanej przypisanej** przez użytkownika, który zostanie otwarty, wybierz subskrypcję dla swojej tożsamości.

1.  Wybierz tożsamość z listy. Możesz również filtrować według nazwy tożsamości lub grupy zasobów.

1.  Po zakończeniu kliknij **przycisk Dodaj,** aby zakończyć przypisywanie tożsamości.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Przypisywanie uprawnień do magazynu usługi Recovery Services w celu uzyskania dostępu do klucza szyfrowania w Azure Key Vault

>[!Note]
>Jeśli używasz tożsamości przypisanych przez użytkownika, te same uprawnienia muszą być przypisane do tożsamości przypisanej przez użytkownika.

Teraz musisz zezwolić magazynowi usługi Recovery Services na dostęp do Azure Key Vault zawierającego klucz szyfrowania. W tym celu tożsamość zarządzana magazynu usługi Recovery Services może uzyskać dostęp do Key Vault.

**W portalu:**

1. Przejdź do swojego Azure Key Vault -> **zasad dostępu.** Przejdź do **+Dodaj zasady dostępu.**

    ![Dodawanie zasad dostępu](./media/encryption-at-rest-with-cmk/access-policies.png)

1. W **obszarze Uprawnienia klucza** wybierz pozycję **Pobierz,** **Lista,** **Odpakuj klucz** i **Opakuj klucz.** Określa akcje dotyczące klucza, które będą dozwolone.

    ![Przypisywanie uprawnień klucza](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Przejdź do **opcji Wybierz podmiot** zabezpieczeń i wyszukaj magazyn w polu wyszukiwania przy użyciu jego nazwy lub tożsamości zarządzanej. Po jego wybraniu wybierz magazyn i wybierz pozycję **Wybierz** w dolnej części okienka.

    ![Wybieranie podmiotu zabezpieczeń](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Po zakończeniu wybierz pozycję **Dodaj,** aby dodać nowe zasady dostępu.

1. Wybierz **pozycję** Zapisz, aby zapisać zmiany wprowadzone w zasadach dostępu Azure Key Vault.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Włączanie usuwania nie softowego i ochrony przed przeczyszczaniem na Azure Key Vault

Na **komputerze, w** Azure Key Vault który przechowuje klucz szyfrowania, należy włączyć ochronę usuwania nie softowego i przeczyszczania. Możesz to zrobić za pomocą interfejsu Azure Key Vault, jak pokazano poniżej. (Alternatywnie te właściwości można ustawić podczas tworzenia Key Vault). Więcej informacji o tych właściwościach Key Vault [tutaj.](../key-vault/general/soft-delete-overview.md)

![Włączanie usuwania nietrwałego i ochrony przed przeczyszczaniem](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Możesz również włączyć usuwanie nie softowe i ochronę przed przeczyszczaniem za pomocą programu PowerShell, korzystając z poniższych kroków:

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

1. Włączanie ochrony przed przeczyszczaniem

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Przypisywanie klucza szyfrowania do magazynu usługi RS

>[!NOTE]
> Przed przystąpieniem do dalszych czynności upewnij się, że:
>
> - Wszystkie kroki wymienione powyżej zostały wykonane pomyślnie:
>   - Tożsamość zarządzana magazynu usługi Recovery Services została włączona i ma przypisane wymagane uprawnienia
>   - W Azure Key Vault włączono usuwanie nie soft-delete i ochronę przed przeczyszczaniem
> - Magazyn usługi Recovery Services, dla którego chcesz włączyć szyfrowanie **CMK,** nie ma żadnych elementów chronionych ani zarejestrowanych w nim

Po upewniniu się, że powyższy klucz szyfrowania jest nadal wybierany dla magazynu.

### <a name="to-assign-the-key-in-the-portal"></a>Aby przypisać klucz w portalu

1. Przejdź do magazynu usługi Recovery Services — > **właściwości**

    ![Ustawienia szyfrowania](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Wybierz pozycję **Aktualizuj** w **obszarze Ustawienia szyfrowania.**

1. W okienku Ustawienia szyfrowania wybierz pozycję Użyj **własnego klucza** i kontynuuj określanie klucza, korzystając z jednego z następujących sposobów. **Upewnij się, że klucz, którego chcesz użyć, to klucz RSA 2048, który jest w stanie włączonym.**

    1. Wprowadź klucz **URI,** za pomocą którego chcesz zaszyfrować dane w tym magazynie usługi Recovery Services. Należy również określić subskrypcję, w której znajduje się Azure Key Vault (zawierająca ten klucz). Ten klucz URI można uzyskać z odpowiedniego klucza w Azure Key Vault. Upewnij się, że klucz URI został skopiowany poprawnie. Zaleca się użycie przycisku Kopiuj do **schowka** dostarczonego z identyfikatorem klucza.

        >[!NOTE]
        >Podczas określania klucza szyfrowania przy użyciu klucza URI klucz nie zostanie automatycznie obrócony. W związku z tym aktualizacje kluczy należy wykonać ręcznie, określając nowy klucz w razie potrzeby.

        ![Wprowadź klucz URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Przeglądaj i wybierz klucz z listy Key Vault w okienku selektora kluczy.

        >[!NOTE]
        >Podczas określania klucza szyfrowania przy użyciu okienka s wyboru klucza klucz będzie obracany automatycznie za każdym razem, gdy zostanie włączona nowa wersja klucza. [Dowiedz się więcej](#enabling-auto-rotation-of-encryption-keys) na temat włączania automatycznej rotacji kluczy szyfrowania.

        ![Wybieranie klucza z magazynu kluczy](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Wybierz pozycję **Zapisz**.

1. **Śledzenie postępu i stanu aktualizacji klucza** szyfrowania: postęp i stan przypisania  klucza szyfrowania można śledzić przy użyciu widoku Zadania tworzenia kopii zapasowej na pasku nawigacyjnym po lewej stronie. Stan powinien wkrótce zmienić się na **Ukończono.** Magazyn będzie teraz szyfrować wszystkie dane z określonym kluczem jako kluczem KEK.

    ![Stan ukończony](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Aktualizacje klucza szyfrowania są również rejestrowane w dzienniku aktywności magazynu.

    ![Dziennik aktywności](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>Aby przypisać klucz za pomocą programu PowerShell

Użyj polecenia [Set-AzRecoveryServicesVaultProperty,](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) aby włączyć szyfrowanie przy użyciu kluczy zarządzanych przez klienta oraz przypisać lub zaktualizować klucz szyfrowania do użycia.

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
> Ten proces pozostaje taki sam, jeśli chcesz zaktualizować lub zmienić klucz szyfrowania. Jeśli chcesz zaktualizować i użyć klucza z innej Key Vault (innej niż ta, która jest aktualnie używana), upewnij się, że:
>
> - Magazyn kluczy znajduje się w tym samym regionie co magazyn usługi Recovery Services
>
> - Magazyn kluczy ma włączoną ochronę przed usuwaniem nie soft-delete i przeczyszczaniem
>
> - Magazyn usługi Recovery Services ma wymagane uprawnienia dostępu do magazynu kluczy.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Backing up to a vault encrypted with customer-managed keys (Kopię zapasową w magazynie zaszyfrowanym przy użyciu kluczy zarządzanych przez klienta)

Przed przystąpieniem do konfigurowania ochrony zdecydowanie zalecamy przestrzeganie poniższej listy kontrolnej. Jest to ważne, ponieważ po skonfigurowaniu kopii zapasowej elementu (lub próbie skonfigurowania go) w magazynie niezaszyfrowanym za pomocą klucza CMK nie można włączyć szyfrowania przy użyciu kluczy zarządzanych przez klienta i będzie nadal używać kluczy zarządzanych przez platformę.

>[!IMPORTANT]
> Przed przystąpieniem do konfigurowania ochrony należy **pomyślnie** wykonać następujące czynności:
>
>1. Tworzenie magazynu kopii zapasowych
>1. Włączono tożsamość zarządzaną przypisaną przez system magazynu usługi Recovery Services lub przypisano do magazynu tożsamość zarządzaną przypisaną przez użytkownika
>1. Przypisane uprawnienia do magazynu kopii zapasowych (lub tożsamości zarządzanej przypisanej przez użytkownika) do uzyskiwania dostępu do kluczy szyfrowania z Key Vault
>1. Włączono ochronę usuwania nie soft i przeczyszczania dla Key Vault
>1. Przypisano prawidłowy klucz szyfrowania dla magazynu kopii zapasowych
>
>Jeśli wszystkie powyższe kroki zostały potwierdzone, tylko wtedy kontynuuj konfigurowanie kopii zapasowej.

Proces konfigurowania i wykonywania kopii zapasowych w magazynie usługi Recovery Services zaszyfrowanym za pomocą kluczy zarządzanych przez klienta jest taki sam, jak w przypadku magazynu, który używa kluczy zarządzanych przez platformę, bez żadnych zmian w **tym zakresie.** Dotyczy to kopii zapasowych maszyn wirtualnych platformy [Azure,](./quick-backup-vm-portal.md) a także kopii zapasowych obciążeń uruchomionych wewnątrz maszyny wirtualnej (na przykład SAP HANA [,](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) baz danych).

## <a name="restoring-data-from-backup"></a>Przywracanie danych z kopii zapasowej

### <a name="vm-backup"></a>Kopia zapasowa maszyny wirtualnej

Dane przechowywane w magazynie usługi Recovery Services można przywrócić zgodnie z krokami opisanymi [tutaj.](./backup-azure-arm-restore-vms.md) Podczas przywracania z magazynu usługi Recovery Services zaszyfrowanego przy użyciu kluczy zarządzanych przez klienta można wybrać szyfrowanie przywróconych danych przy użyciu zestawu szyfrowania dysków (DES).

#### <a name="restoring-vm--disk"></a>Przywracanie maszyny wirtualnej/dysku

1. Podczas odzyskiwania dysku/maszyny wirtualnej z punktu odzyskiwania "Migawka" przywrócone dane zostaną zaszyfrowane przy użyciu des des używanego do szyfrowania dysków źródłowej maszyny wirtualnej.

1. Podczas przywracania dysku/maszyny wirtualnej z punktu odzyskiwania z typem odzyskiwania "Magazyn" możesz wybrać, aby przywrócone dane były szyfrowane przy użyciu desek określonej w czasie przywracania. Alternatywnie możesz kontynuować przywracanie danych bez określania des — w takim przypadku zostaną one zaszyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

Przywrócony dysk/maszynę wirtualną można zaszyfrować po zakończeniu przywracania, niezależnie od wyboru dokonanego podczas inicjowania przywracania.

![Punkty przywracania](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Wybieranie zestawu szyfrowania dysków podczas przywracania z punktu odzyskiwania magazynu

**W portalu:**

Zestaw szyfrowania dysków jest określony w obszarze Ustawienia szyfrowania w okienku przywracania, jak pokazano poniżej:

1. W **elektorze Szyfruj dyski przy użyciu klucza wybierz** pozycję **Tak.**

1. Z listy rozwijanej wybierz des, które chcesz użyć dla przywróconych dysków. **Upewnij się, że masz dostęp do DES.**

>[!NOTE]
>Możliwość wybrania des podczas przywracania nie jest dostępna, jeśli przywracasz maszynę wirtualną, która używa Azure Disk Encryption.

![Szyfrowanie dysku przy użyciu klucza](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Za pomocą programu PowerShell:**

Użyj [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) polecenia z parametrem [ ] do określenia DES do użycia do szyfrowania `-DiskEncryptionSetId <string>` przywróconego dysku. [](/powershell/module/az.compute/get-azdiskencryptionset) Aby uzyskać więcej informacji na temat przywracania dysków z kopii zapasowej maszyny wirtualnej, zobacz [ten artykuł.](./backup-azure-vms-automation.md#restore-an-azure-vm)

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

Podczas przywracania plików przywrócone dane zostaną zaszyfrowane przy użyciu klucza używanego do szyfrowania lokalizacji docelowej.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Przywracanie baz danych SAP HANA/SQL na platformie Azure

Podczas przywracania z kopii zapasowej bazy danych SAP HANA/SQL uruchomionej na maszynie wirtualnej platformy Azure przywrócone dane będą szyfrowane przy użyciu klucza szyfrowania używanego w docelowej lokalizacji przechowywania. Może to być klucz zarządzany przez klienta lub klucz zarządzany przez platformę używany do szyfrowania dysków maszyny wirtualnej.

## <a name="additional-topics"></a>Tematy dodatkowe

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Włączanie szyfrowania przy użyciu kluczy zarządzanych przez klienta podczas tworzenia magazynu (w wersji zapoznawczej)

>[!NOTE]
>Włączanie szyfrowania podczas tworzenia magazynu przy użyciu kluczy zarządzanych przez klienta jest dostępne w ograniczonej publicznej wersji zapoznawczej i wymaga zezwalania na listę subskrypcji. Aby zarejestrować się w celu wersji zapoznawczej, wypełnij [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) i napisz do nas na stronie [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Gdy twoja subskrypcja będzie wyświetlana na liście, zostanie **wyświetlana** karta Szyfrowanie kopii zapasowej. Dzięki temu można włączyć szyfrowanie kopii zapasowej przy użyciu kluczy zarządzanych przez klienta podczas tworzenia nowego magazynu usługi Recovery Services. Aby włączyć szyfrowanie, wykonaj następujące kroki:

1. Obok karty **Podstawowe na** karcie **Szyfrowanie** kopii zapasowej określ klucz szyfrowania i tożsamość do użycia na użytek szyfrowania.

   ![Włączanie szyfrowania na poziomie magazynu](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >Ustawienia dotyczą tylko kopii zapasowej i są opcjonalne.

1. Wybierz **pozycję Użyj klucza zarządzanego przez klienta** jako typu szyfrowania.

1. Aby określić klucz, który ma być używany do szyfrowania, wybierz odpowiednią opcję.

   Możesz podać URI klucza szyfrowania lub przejrzeć i wybrać klucz. Po określeniu klucza przy użyciu opcji **Wybierz Key Vault** automatyczna rotacja klucza szyfrowania zostanie włączyć automatycznie. [Dowiedz się więcej na temat automatycznego obracania](#enabling-auto-rotation-of-encryption-keys). 

1. Określ tożsamość zarządzaną przypisaną przez użytkownika w celu zarządzania szyfrowaniem przy użyciu kluczy zarządzanych przez klienta. Kliknij **pozycję Wybierz,** aby przeglądać i wybrać wymaganą tożsamość.

1. Po zakończeniu dodaj pozycję Tagi (opcjonalnie) i kontynuuj tworzenie magazynu.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Włączanie automatycznej rotacji kluczy szyfrowania

W przypadku określenia klucza zarządzanego przez klienta, który ma być używany do szyfrowania kopii zapasowych, użyj następujących metod, aby go określić:

- Wprowadź klucz URI
- Wybierz z Key Vault

Użycie opcji **Wybierz z Key Vault** pomaga włączyć automatyczną rotację dla wybranego klucza. Eliminuje to ręczne aktualizowanie do następnej wersji. Jednak przy użyciu tej opcji:
- Aktualizacja wersji klucza może potrwać do godziny.
- Gdy nowa wersja klucza zostanie włączona, stara wersja powinna być również dostępna (w stanie włączone) dla co najmniej jednego kolejnego zadania tworzenia kopii zapasowej po wzięcie w życie aktualizacji klucza.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Używanie zasad platformy Azure do inspekcji i wymuszania szyfrowania przy użyciu kluczy zarządzanych przez klienta (w wersji zapoznawczej)

Azure Backup umożliwia używanie zasad platformy Azure do inspekcji i wymuszania szyfrowania danych w magazynie usługi Recovery Services przy użyciu kluczy zarządzanych przez klienta. Korzystanie z zasad platformy Azure:

- Zasady inspekcji mogą służyć do inspekcji magazynów z szyfrowaniem przy użyciu kluczy zarządzanych przez klienta, które są włączone po 2021-04-01. W przypadku magazynów z włączonym szyfrowaniem CMK przed tą datą zastosowanie zasad może się nie powieść lub mogą być wyświetlane wyniki fałszywie ujemne (oznacza to, że te magazyny mogą być zgłaszane jako niezgodne, mimo że włączono szyfrowanie **CMK).**
- Aby użyć zasad inspekcji do inspekcji magazynów z włączonym szyfrowaniem **CMK** przed 1.04.2021 r., należy użyć Azure Portal do zaktualizowania klucza szyfrowania. Ułatwia to uaktualnienie do nowego modelu. Jeśli nie chcesz zmieniać klucza szyfrowania, podaj ten sam klucz ponownie za pomocą klucza URI lub opcji wyboru klucza. 

   >[!Warning]
    >Jeśli używasz programu PowerShell do zarządzania kluczami szyfrowania dla kopii zapasowej, nie zalecamy aktualizowania kluczy z portalu.<br>W przypadku zaktualizowania klucza z portalu nie można użyć programu PowerShell do dalszej aktualizacji klucza szyfrowania, dopóki nie zostanie dostępna aktualizacja programu PowerShell do obsługi nowego modelu. Możesz jednak kontynuować aktualizowanie klucza z Azure Portal.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Czy mogę zaszyfrować istniejący magazyn kopii zapasowych przy użyciu kluczy zarządzanych przez klienta?

Nie, szyfrowanie CMK można włączyć tylko dla nowych magazynów. Magazyn nigdy nie może mieć chronionych żadnych elementów. W rzeczywistości przed włączeniem szyfrowania przy użyciu kluczy zarządzanych przez klienta nie należy podejmować żadnych prób ochrony żadnych elementów w magazynie.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Próbowano chronić element w magazynie, ale zakończył się on niepowodzeniem, a magazyn nadal nie zawiera żadnych elementów chronionych przez ten element. Czy mogę włączyć szyfrowanie CMK dla tego magazynu?

Nie, w przeszłości magazyn nie musiał próbować chronić żadnych elementów.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Mam magazyn korzystający z szyfrowania CMK. Czy mogę później przywrócić szyfrowanie przy użyciu kluczy zarządzanych przez platformę, nawet jeśli elementy kopii zapasowej są chronione w magazynie?

Nie, po włączeniu szyfrowania KLUCZEM nie można przywrócić go do używania kluczy zarządzanych przez platformę. Możesz zmienić używane klucze zgodnie z wymaganiami.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Czy szyfrowanie cmk dla Azure Backup ma również zastosowanie do Azure Site Recovery?

Nie. W tym artykule omówiono tylko szyfrowanie danych kopii zapasowej. Na Azure Site Recovery należy ustawić właściwość oddzielnie jako dostępną z usługi.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Pominięto jeden z kroków w tym artykule i chronię źródło danych. Czy nadal mogę używać szyfrowania CMK?

Nieusącanie kroków w tym artykule i kontynuowanie ochrony elementów może prowadzić do tego, że magazyn nie będzie mógł używać szyfrowania przy użyciu kluczy zarządzanych przez klienta. Dlatego zaleca się, aby [](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) przed przystąpieniem do ochrony elementów odwoływać się do tej listy kontrolnej.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Czy użycie szyfrowania cmk powoduje dodatkowe koszty tworzenia kopii zapasowych?

Użycie szyfrowania CMK dla kopii zapasowej nie wiąże się z żadnymi dodatkowymi kosztami. Możesz jednak nadal ponosić koszty korzystania z usługi Azure Key Vault, w której przechowywany jest klucz.

## <a name="next-steps"></a>Następne kroki

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](security-overview.md)
