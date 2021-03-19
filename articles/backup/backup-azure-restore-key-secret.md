---
title: Przywróć klucz tajny & Key Vault klucza dla zaszyfrowanej maszyny wirtualnej
description: Dowiedz się, jak przywrócić klucz Key Vault i wpis tajny w Azure Backup przy użyciu programu PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 456ce18f253ffa02cd6b13826a7839f18beecba7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88827090"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Przywracanie klucza i wpisu tajnego usługi Key Vault dla szyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup

W tym artykule omówiono korzystanie z kopii zapasowej maszyny wirtualnej platformy Azure w celu wykonania przywracania zaszyfrowanych maszyn wirtualnych platformy Azure, jeśli klucz i wpis tajny nie istnieją w magazynie kluczy. Te kroki mogą być również używane, jeśli chcesz zachować osobną kopię klucza (klucz szyfrowania klucza) i klucz tajny (klucz szyfrowania funkcji BitLocker) dla przywróconej maszyny wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* **Tworzenie kopii zapasowych zaszyfrowanych maszyn wirtualnych** na zaszyfrowane maszyny wirtualne platformy Azure zostały wykonane przy użyciu Azure Backup. Aby uzyskać szczegółowe informacje na temat tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych platformy Azure, zapoznaj się z artykułem [Zarządzanie tworzeniem i przywracaniem maszyn wirtualnych Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md)
* **Skonfiguruj Azure Key Vault** — upewnij się, że magazyn kluczy, do którego należy przywrócić klucze i wpisy tajne, już istnieje. Zapoznaj się z artykułem [wprowadzenie do Azure Key Vault](../key-vault/general/overview.md) , aby uzyskać szczegółowe informacje na temat zarządzania magazynem kluczy.
* **Przywróć dysk** — upewnij się, że wyzwolono zadanie przywracania do przywracania dysków dla zaszyfrowanej maszyny wirtualnej przy użyciu [kroków programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Dzieje się tak, ponieważ to zadanie generuje plik JSON na koncie magazynu zawierającym klucze i wpisy tajne dla zaszyfrowanej maszyny wirtualnej, która ma zostać przywrócona.

## <a name="get-key-and-secret-from-azure-backup"></a>Pobieranie klucza i wpisu tajnego z Azure Backup

> [!NOTE]
> Po przywróceniu dysku dla zaszyfrowanej maszyny wirtualnej upewnij się, że:
>
> * $details jest wypełniana przy użyciu szczegółów zadania przywracania dysku, jak wspomniano w [sekcji kroki w programie PowerShell w temacie Przywracanie dysków](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Maszynę wirtualną należy utworzyć z przywróconych dysków dopiero **po przywróceniu klucza i wpisu tajnego do magazynu kluczy**.

Wykonaj zapytanie dotyczące przywróconych właściwości dysku w celu uzyskania szczegółowych informacji o zadaniu.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Ustaw kontekst usługi Azure Storage i Przywróć plik konfiguracji JSON zawierający dane klucza i wpisu tajnego dla zaszyfrowanej maszyny wirtualnej.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Przywróć klucz

Po wygenerowaniu pliku JSON w powyższej ścieżce docelowej wygeneruj plik Key BLOB z poziomu JSON i powróć do niego, aby przywrócić klucz polecenia cmdlet (KEK) z powrotem do magazynu kluczy.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Przywróć klucz tajny

Użyj pliku JSON wygenerowanego powyżej, aby uzyskać nazwę i wartość wpisu tajnego, a następnie podawanie jej w celu skonfigurowania wpisu tajnego w celu umieszczenia wpisu tajnego (klucz szyfrowania bloków) z powrotem w magazynie kluczy. Użyj tych poleceń cmdlet, jeśli **maszyna wirtualna jest zaszyfrowana przy użyciu klucz szyfrowania bloków i KEK**.

**Użyj tych poleceń cmdlet, jeśli maszyna wirtualna z systemem Windows jest zaszyfrowana przy użyciu klucz szyfrowania bloków i KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Użyj tych poleceń cmdlet, jeśli maszyna wirtualna z systemem Linux jest zaszyfrowana przy użyciu klucz szyfrowania bloków i KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Użyj pliku JSON wygenerowanego powyżej, aby uzyskać nazwę i wartość wpisu tajnego, a następnie podawanie jej w celu skonfigurowania wpisu tajnego w celu umieszczenia wpisu tajnego (klucz szyfrowania bloków) z powrotem w magazynie kluczy. Użyj tych poleceń cmdlet, jeśli **maszyna wirtualna jest zaszyfrowana tylko przy użyciu klucz szyfrowania bloków** .

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Wartość $secretname można uzyskać, odwołując się do danych wyjściowych $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl i przy użyciu tekstu po kluczach tajnych/na przykład, wyjściowy adres URL klucza tajnego to `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` i nazwa wpisu tajnego to B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa klucza tajnego.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Utwórz maszynę wirtualną z przywróconego dysku

Jeśli utworzono kopię zapasową zaszyfrowanej maszyny wirtualnej przy użyciu kopii zapasowej maszyny wirtualnej platformy Azure, polecenia cmdlet programu PowerShell wymienione powyżej umożliwiają przywrócenie klucza i wpisu tajnego z powrotem do magazynu kluczy. Po ich przywróceniu zapoznaj się z artykułem [Zarządzanie wykonywaniem kopii zapasowych i przywracaniem maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) , aby utworzyć zaszyfrowane maszyny wirtualne na podstawie przywróconego dysku, klucza i wpisu

## <a name="legacy-approach"></a>Starsze podejście

Wymienione powyżej podejście będzie działało dla wszystkich punktów odzyskiwania. Jednak starsze podejście do pobierania kluczy i wpisów tajnych z punktu odzyskiwania jest prawidłowe dla punktów odzyskiwania starszych niż 11 lipca 2017 dla maszyn wirtualnych szyfrowanych przy użyciu klucz szyfrowania bloków i KEK. Po zakończeniu przywracania zadania dyskowego dla zaszyfrowanej maszyny wirtualnej przy użyciu [kroków programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)upewnij się, że $RP jest wypełnione prawidłową wartością.

### <a name="restore-key-legacy-approach"></a>Przywróć klucz (podejście starsze)

Użyj następujących poleceń cmdlet, aby uzyskać informacje o kluczu (KEK) z punktu odzyskiwania i pobrać je z powrotem do polecenia cmdlet, aby przywrócić je w magazynie kluczy.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret-legacy-approach"></a>Przywróć klucz tajny (podejście starsze)

Użyj następujących poleceń cmdlet, aby uzyskać informacje o kluczu tajnym (klucz szyfrowania bloków) z punktu odzyskiwania i utworzyć je za pomocą polecenia cmdlet dla wpisu tajnego, aby umieścić je w magazynie kluczy.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Wartość $secretname można uzyskać, odwołując się do danych wyjściowych $rp 1. KeyAndSecretDetails. SecretUrl i używanie tekstu po wpisach tajnych/na przykład, wyjściowy adres URL to `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` i nazwa wpisu tajnego to B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa klucza tajnego.
> * Wartość DiskEncryptionKeyEncryptionKeyURL można uzyskać z magazynu kluczy po przywróceniu kluczy z powrotem i przy użyciu polecenia cmdlet [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey)
>
>

## <a name="next-steps"></a>Następne kroki

Po przywróceniu klucza i wpisu tajnego do magazynu kluczy zapoznaj się z artykułem [Zarządzanie kopią zapasową i przywracaniem maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) , aby utworzyć zaszyfrowane maszyny wirtualne z przywróconego dysku, klucza i wpisu tajnego
