---
title: Eksportowanie certyfikatów z Azure Key Vault
description: Dowiedz się, jak eksportować certyfikaty z Azure Key Vault.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 0efe0164d8e1a4e5bc3b9d6d7313855740afd316
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767911"
---
# <a name="export-certificates-from-azure-key-vault"></a>Eksportowanie certyfikatów z Azure Key Vault

Dowiedz się, jak eksportować certyfikaty z Azure Key Vault. Certyfikaty można eksportować przy użyciu interfejsu wiersza polecenia platformy Azure Azure PowerShell lub Azure Portal. 

## <a name="about-azure-key-vault-certificates"></a>Informacje o certyfikatach usługi Azure Key Vault

Azure Key Vault umożliwia łatwe aprowizować i wdrażać certyfikaty cyfrowe dla sieci oraz zarządzać nimi. Umożliwia również bezpieczną komunikację dla aplikacji. Zobacz [Azure Key Vault certyfikatów,](./about-certificates.md) aby uzyskać więcej informacji.

### <a name="composition-of-a-certificate"></a>Budowa certyfikatu

Po utworzeniu Key Vault jest tworzony *adresowalny* klucz  i klucz tajny, które mają taką samą nazwę. Klucz Key Vault umożliwia operacje klucza. Klucz Key Vault umożliwia pobranie wartości certyfikatu jako klucza tajnego. Certyfikat Key Vault również zawiera metadane publicznego certyfikatu x509. Aby uzyskać [więcej informacji, przejdź do tematu Kompozycja](./about-certificates.md#composition-of-a-certificate) certyfikatu.

### <a name="exportable-and-non-exportable-keys"></a>Klucze eksportowalne i nieeksportowalne

Po utworzeniu Key Vault można go pobrać z adresowalnego klucza tajnego przy użyciu klucza prywatnego. Pobierz certyfikat w formacie PFX lub PEM.

- **Eksportowalny:** zasady użyte do utworzenia certyfikatu wskazują, że klucz można eksportować.
- **Brak eksportu:** zasady użyte do utworzenia certyfikatu wskazują, że klucza nie można wyeksportować. W takim przypadku klucz prywatny nie jest częścią wartości, gdy jest pobierany jako klucz tajny.

Obsługiwane typy kluczy: RSA, RSA-HSM, EC, EC-HSM, oct (wymienione [tutaj](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) Eksportowanie jest dozwolone tylko w przypadku RSA, EC. Kluczy HSM nie można eksportować.

Aby [uzyskać więcej informacji, Azure Key Vault informacje](./about-certificates.md#exportable-or-non-exportable-key) o certyfikatach certyfikatów.

## <a name="export-stored-certificates"></a>Eksportowanie przechowywanych certyfikatów

Do eksportowania certyfikatów przechowywanych w usłudze Azure Key Vault można użyć interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell lub witryny Azure Portal.

> [!NOTE]
> Hasło certyfikatu należy wymagać tylko podczas importowania certyfikatu w magazynie kluczy. Usługa Key Vault nie zapisuje powiązanego hasła. Podczas eksportowania certyfikatu hasło jest puste.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia w interfejsie  wiersza polecenia platformy Azure, aby pobrać publiczną część Key Vault wiersza polecenia.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Aby [uzyskać więcej informacji, zobacz przykłady](/cli/azure/keyvault/certificate#az_keyvault_certificate_download) i definicje parametrów.

Pobieranie jako certyfikatu oznacza pobranie części publicznej. Jeśli potrzebujesz klucza prywatnego i metadanych publicznych, możesz pobrać go jako klucz tajny.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Aby uzyskać więcej informacji, zobacz [definicje parametrów](/cli/azure/keyvault/secret#az_keyvault_secret_download).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj tego polecenia w Azure PowerShell, aby uzyskać certyfikat o nazwie **TestCert01** z magazynu kluczy o nazwie **ContosoKV01.** Aby pobrać certyfikat jako plik PFX, uruchom następujące polecenie. Te polecenia uzyskają dostęp **do wartości SecretId,** a następnie zapiszą zawartość jako plik PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

To polecenie eksportuje cały łańcuch certyfikatów z kluczem prywatnym (tj. taki sam jak zaimportowany). Certyfikat jest chroniony hasłem.
Aby uzyskać więcej informacji na temat **polecenia Get-AzKeyVaultCertificate** i parametrów, zobacz [Get-AzKeyVaultCertificate — przykład 2.](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na Azure Portal po utworzeniu/zaimportowaniu certyfikatu w  bloku Certyfikat otrzymasz powiadomienie o pomyślnym utworzeniu certyfikatu. Wybierz certyfikat i bieżącą wersję, aby wyświetlić opcję pobierania.

Aby pobrać certyfikat, wybierz pozycję Pobierz **w formacie CER** lub **Pobierz w formacie PFX/PEM.**

![Pobieranie certyfikatu](../media/certificates/quick-create-portal/current-version-shown.png)

**Eksportowanie Azure App Service certyfikatów**

Azure App Service certyfikatów to wygodny sposób zakupu certyfikatów SSL. Możesz przypisać je do usługi Azure Apps z poziomu portalu. Po zaimportowaniu certyfikatów usługi App Service wpisy **tajne.**

Aby uzyskać więcej informacji, zobacz kroki [eksportowania Azure App Service certyfikatów](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

---

## <a name="read-more"></a>Dowiedz się więcej
* [Różne typy i definicje plików certyfikatów](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
