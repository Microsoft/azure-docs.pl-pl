---
title: Eksportuj certyfikat z Azure Key Vault
description: Eksportuj certyfikat z Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 9b7216c36e5dd1ab5e4f65c565bf43cbd17bfbee
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173027"
---
# <a name="export-certificate-from-azure-key-vault"></a>Eksportuj certyfikat z Azure Key Vault

Azure Key Vault umożliwia łatwe inicjowanie obsługi i wdrażanie certyfikatów cyfrowych dla sieci oraz zarządzanie nimi oraz zapewnia bezpieczną komunikację dla aplikacji. Aby uzyskać więcej ogólnych informacji o certyfikatach, zobacz [Azure Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>Informacje o certyfikacie magazynu kluczy Azure

### <a name="composition-of-certificate"></a>Składanie certyfikatu
Po utworzeniu certyfikatu Key Vault zostanie również utworzony klucz adresowy i wpis tajny o tej samej nazwie. Klucz Key Vault pozwala na wykonywanie kluczowych operacji, a klucz tajny Key Vault umożliwia pobieranie wartości certyfikatu jako klucza tajnego. Certyfikat Key Vault zawiera również publiczne metadane certyfikatu x509. [Dowiedz się więcej](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Klucze eksportujące lub NIEEKSPORTUJĄCE
Po utworzeniu certyfikatu Key Vault można go pobrać ze klucza prywatnego z adresami w formacie PFX lub PEM. Zasady użyte do utworzenia certyfikatu muszą wskazywać, że klucz jest eksportowalny. Jeśli zasady wskazują, że nie można eksportować, klucz prywatny nie jest częścią wartości, gdy zostanie pobrany jako wpis tajny.

Obsługiwane są dwa typy kluczy — moduł HSM RSA lub RSA z certyfikatami. Eksport jest dozwolony tylko za pomocą RSA, ale nie jest obsługiwany przez moduł HSM RSA. [Dowiedz się więcej](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Zapisany certyfikat w Azure Key Vault można eksportować za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell lub portalu.

> [!NOTE]
> Należy pamiętać, że tylko hasło certyfikatu jest wymagane tylko podczas importowania go w magazynie kluczy. Key Vault nie zapisze skojarzonego hasła, dlatego w przypadku eksportowania certyfikatu hasło będzie puste.

## <a name="exporting-certificate-using-cli"></a>Eksportowanie certyfikatu przy użyciu interfejsu wiersza polecenia
Poniższe polecenie umożliwi pobranie **publicznej części** certyfikatu Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Aby zapoznać się z przykładami i definicjami parametrów, [Wyświetl tutaj](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Jeśli chcesz pobrać cały certyfikat, np. **publiczną i prywatną część jego składu**, można to zrobić, pobierając certyfikat jako klucz tajny.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
W przypadku definicji parametrów [Wyświetl tutaj](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Eksportowanie certyfikatu przy użyciu programu PowerShell

To polecenie pobiera certyfikat o nazwie TestCert01 z magazynu kluczy o nazwie ContosoKV01. Aby pobrać certyfikat jako plik PFX, uruchom następujące polecenie. Te polecenia uzyskują dostęp do SecretId, a następnie zapisują zawartość jako plik PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Spowoduje to wyeksportowanie całego łańcucha certyfikatów z kluczem prywatnym, a ten certyfikat będzie chroniony hasłem.
Aby uzyskać więcej informacji na temat ```Get-AzKeyVaultCertificate``` polecenia i parametrów, zobacz [przykład 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0) .

## <a name="exporting-certificate-using-portal"></a>Eksportowanie certyfikatu przy użyciu portalu

W portalu po utworzeniu/zaimportowaniu certyfikatu w bloku certyfikatu otrzymasz powiadomienie o pomyślnym utworzeniu certyfikatu. Po wybraniu certyfikatu można kliknąć bieżącą wersję, a zobaczysz opcję pobierania.


Klikając przycisk "Pobierz w formacie CER" lub "Pobierz w formacie PFX/PEM", możesz pobrać certyfikat.


![Pobieranie certyfikatu](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Eksportowanie Certyfikat usługi App Service z magazynu kluczy

Azure App Service certyfikaty zapewniają wygodny sposób kupowania certyfikatów SSL i przypisywania ich do aplikacji platformy Azure bezpośrednio z poziomu portalu. Te certyfikaty można także wyeksportować z portalu jako pliki PFX do użycia w innym miejscu.
Po zaimportowaniu certyfikaty usługi App Service mogą znajdować się **w obszarze wpisy tajne**.

Instrukcje dotyczące eksportowania certyfikatów usługi App Service można [znaleźć tutaj](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>Dowiedz się więcej
* [Różne typy i definicje plików certyfikatów](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)