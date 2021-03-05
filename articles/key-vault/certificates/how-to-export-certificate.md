---
title: Eksportuj certyfikaty z Azure Key Vault
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
ms.openlocfilehash: b5cf7f2e5957ef57009c1b461ae81863d6d8ab9b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199972"
---
# <a name="export-certificates-from-azure-key-vault"></a>Eksportuj certyfikaty z Azure Key Vault

Dowiedz się, jak eksportować certyfikaty z Azure Key Vault. Certyfikaty można eksportować za pomocą interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. 

## <a name="about-azure-key-vault-certificates"></a>Informacje o certyfikatach usługi Azure Key Vault

Azure Key Vault umożliwia łatwe inicjowanie obsługi i wdrażanie certyfikatów cyfrowych dla sieci oraz zarządzanie nimi. Umożliwia również bezpieczną komunikację z aplikacjami. Aby uzyskać więcej informacji, zobacz [Azure Key Vault Certificates](./about-certificates.md) .

### <a name="composition-of-a-certificate"></a>Budowa certyfikatu

Po utworzeniu certyfikatu Key Vault zostanie utworzony *klucz* , który można rozwiązać i *wpis tajny* o tej samej nazwie. Klucz Key Vault umożliwia wykonywanie najważniejszych operacji. Wpis tajny Key Vault umożliwia pobieranie wartości certyfikatu jako klucza tajnego. Certyfikat Key Vault zawiera również publiczne metadane certyfikatu x509. Aby uzyskać więcej informacji, przejdź do [kompozycji certyfikatu](./about-certificates.md#composition-of-a-certificate) .

### <a name="exportable-and-non-exportable-keys"></a>Klucze eksportujące i NIEEKSPORTUJĄCE

Po utworzeniu certyfikatu Key Vault można pobrać go z klucza prywatnego z możliwością adresowania. Pobierz certyfikat w formacie PFX lub PEM.

- Możliwe do **eksportowania**: zasady użyte do utworzenia certyfikatu wskazują, że klucz jest eksportowalny.
- **Nie można eksportować**: zasady użyte do utworzenia certyfikatu wskazują, że klucz nie jest eksportowalny. W takim przypadku klucz prywatny nie jest częścią wartości, gdy zostanie pobrany jako wpis tajny.

Obsługiwane typy kluczy: RSA, RSA-HSM, EC, we-HSM, Oct (wymienione [tutaj](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) można eksportować tylko za pomocą RSA, we. Klucze HSM nie mogą być eksportowane.

Aby uzyskać więcej informacji, zobacz [Informacje o Azure Key Vault certyfikatach](./about-certificates.md#exportable-or-non-exportable-key) .

## <a name="export-stored-certificates"></a>Eksportowanie przechowywanych certyfikatów

Do eksportowania certyfikatów przechowywanych w usłudze Azure Key Vault można użyć interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell lub witryny Azure Portal.

> [!NOTE]
> Wymagaj hasła certyfikatu tylko podczas importowania certyfikatu w magazynie kluczy. Usługa Key Vault nie zapisuje powiązanego hasła. Podczas eksportowania certyfikatu hasło jest puste.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj poniższego polecenia w interfejsie wiersza polecenia platformy Azure, aby pobrać **publiczną część** certyfikatu Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Wyświetl [przykłady i definicje parametrów,](/cli/azure/keyvault/certificate#az-keyvault-certificate-download) Aby uzyskać więcej informacji.

Pobranie jako certyfikatu oznacza pobranie publicznej części. Jeśli chcesz, aby zarówno klucz prywatny, jak i publiczne metadane, możesz pobrać go jako wpis tajny.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Aby uzyskać więcej informacji, zobacz [definicje parametrów](/cli/azure/keyvault/secret#az-keyvault-secret-download).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj tego polecenia w Azure PowerShell, aby uzyskać certyfikat o nazwie **TestCert01** z magazynu kluczy o nazwie **ContosoKV01**. Aby pobrać certyfikat jako plik PFX, uruchom następujące polecenie. Te polecenia uzyskują dostęp do **SecretId**, a następnie zapisują zawartość jako plik PFX.

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

To polecenie eksportuje cały łańcuch certyfikatów z kluczem prywatnym. Certyfikat jest chroniony hasłem.
Aby uzyskać więcej informacji na temat polecenia **Get-AzKeyVaultCertificate** i parametrów, zobacz [Get-AzKeyVaultCertificate-example 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na Azure Portal po utworzeniu/zaimportowaniu certyfikatu w bloku **certyfikatu** otrzymujesz powiadomienie o pomyślnym utworzeniu certyfikatu. Wybierz certyfikat i bieżącą wersję, aby wyświetlić opcję pobierania.

Aby pobrać certyfikat, wybierz opcję **Pobierz w formacie CER** lub **Pobierz w formacie PFX/PEM**.

![Pobieranie certyfikatu](../media/certificates/quick-create-portal/current-version-shown.png)

**Eksportowanie Azure App Service certyfikatów**

Azure App Service certyfikaty są wygodnym sposobem zakupu certyfikatów SSL. Możesz przypisać je do aplikacji platformy Azure z poziomu portalu. Po ich zaimportowaniu certyfikaty App Service znajdują się w obszarze wpisy **tajne**.

Aby uzyskać więcej informacji, zobacz procedurę [eksportowania Azure App Service certyfikatów](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

---

## <a name="read-more"></a>Dowiedz się więcej
* [Różne typy i definicje plików certyfikatów](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
