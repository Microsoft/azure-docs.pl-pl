---
title: Korzystanie z certyfikatów i bezpiecznego dostępu Azure Key Vault przy użyciu programu Batch
description: Dowiedz się, jak programowo uzyskać dostęp do poświadczeń z Key Vault przy użyciu Azure Batch.
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaaeaa05caca7897eb649b56504b643038f08d53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260133"
---
# <a name="use-certificates-and-securely-access-azure-key-vault-with-batch"></a>Korzystanie z certyfikatów i bezpiecznego dostępu Azure Key Vault przy użyciu programu Batch

W tym artykule dowiesz się, jak skonfigurować węzły wsadowe pod kątem bezpiecznego dostępu do poświadczeń przechowywanych w [Azure Key Vault](../key-vault/general/overview.md). Nie ma żadnego punktu podczas umieszczania poświadczeń administratora w Key Vault, a następnie do uzyskiwania dostępu do Key Vault ze skryptu. Rozwiązanie polega na użyciu certyfikatu, który przyznaje węzłom partii dostęp do Key Vault.

Aby można było uwierzytelnić Azure Key Vault z węzła usługi Batch, potrzebne są:

- Poświadczenie Azure Active Directory (Azure AD)
- Certyfikat
- Konto w usłudze Batch
- Pula wsadowa z co najmniej jednym węzłem

## <a name="obtain-a-certificate"></a>Uzyskaj certyfikat

Jeśli nie masz jeszcze certyfikatu, najprostszym sposobem, aby uzyskać certyfikat z podpisem własnym, za pomocą `makecert` narzędzia wiersza polecenia.

Zwykle można znaleźć `makecert` w tej ścieżce: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>` . Otwórz wiersz polecenia jako administrator i przejdź do `makecert` poniższego przykładu.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Następnie użyj narzędzia, `makecert` Aby utworzyć pliki certyfikatów z podpisem własnym o nazwie `batchcertificate.cer` i `batchcertificate.pvk` . Użyta nazwa pospolita (CN) nie jest ważna dla tej aplikacji, ale warto ją określić, która informuje o tym, jak certyfikat jest używany.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Partia wymaga `.pfx` pliku. Użyj narzędzia [Pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) do przekonwertowania `.cer` `.pvk` plików utworzonych przez program `makecert` na pojedynczy `.pfx` plik.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Dostęp do Key Vault jest udzielany **użytkownikowi** lub jednostce **usługi**. Aby uzyskać dostęp do Key Vault programowo, użyj [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) z certyfikatem utworzonym w poprzednim kroku. Nazwa główna usługi musi znajdować się w tej samej dzierżawie usługi Azure AD co Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Adresy URL aplikacji nie są ważne, ponieważ są używane tylko do Key Vault dostępu.

## <a name="grant-rights-to-key-vault"></a>Przyznaj prawa Key Vault

Nazwa główna usługi utworzona w poprzednim kroku wymaga uprawnień do pobrania wpisów tajnych z Key Vault. Uprawnienie można udzielić za pomocą [Azure Portal](../key-vault/general/assign-access-policy-portal.md) lub przy użyciu poniższego polecenia programu PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Przypisywanie certyfikatu do konta w usłudze Batch

Utwórz pulę zadań wsadowych, a następnie przejdź do karty certyfikat w puli i przypisz utworzony certyfikat. Certyfikat znajduje się teraz we wszystkich węzłach w usłudze Batch.

Następnie przypisz certyfikat do konta w usłudze Batch. Przypisanie certyfikatu do konta umożliwia usłudze Batch przypisanie go do pul, a następnie do węzłów. Najprostszym sposobem, aby to zrobić, przejdź do konta w usłudze Batch w portalu, przejdź do pozycji **Certyfikaty**, a następnie wybierz pozycję **Dodaj**. Przekaż `.pfx` wygenerowany wcześniej plik i podaj hasło. Po zakończeniu ten certyfikat zostanie dodany do listy i będzie można zweryfikować odcisk palca.

Teraz podczas tworzenia puli wsadowej można przejść do **certyfikatów** w puli i przypisać utworzony certyfikat do tej puli. Gdy to zrobisz, upewnij się, że wybrano pozycję **LocalMachine** w polu Lokalizacja magazynu. Certyfikat jest ładowany we wszystkich węzłach wsadowych w puli.

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell

Jeśli planujesz uzyskać dostęp do Key Vault przy użyciu skryptów programu PowerShell w węzłach, musisz mieć zainstalowaną bibliotekę Azure PowerShell. Jeśli w węzłach jest zainstalowany program Windows Management Framework (WMF) 5, można go pobrać za pomocą polecenia install-module. Jeśli używasz węzłów, które nie mają programu WMF 5, najprostszym sposobem instalacji jest `.msi` połączenie pliku Azure PowerShell z plikami wsadowymi, a następnie Wywołaj Instalatora jako pierwszą część skryptu uruchamiania usługi Batch. Aby uzyskać szczegółowe informacje, zobacz ten przykład:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Uzyskiwanie dostępu do usługi Key Vault

Teraz możesz przystąpić do uzyskiwania dostępu do Key Vault w skryptach uruchomionych w węzłach wsadowych. Aby uzyskać dostęp do Key Vault ze skryptu, wystarczy, aby skrypt uwierzytelniał się w usłudze Azure AD przy użyciu certyfikatu. Aby to zrobić w programie PowerShell, użyj następujących przykładowych poleceń. Określ odpowiedni identyfikator GUID dla **odcisku palca**, **Identyfikator aplikacji** (identyfikator nazwy głównej usługi) i **Identyfikator dzierżawy** (dzierżawy, w której istnieje jednostka usługi).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Po uwierzytelnieniu należy uzyskać dostęp do magazynu kluczy w zwykły sposób.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Są to poświadczenia do użycia w skrypcie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Key Vault](../key-vault/general/overview.md).
- Zapoznaj się z [punktem odniesienia zabezpieczeń Azure dla usługi Batch](security-baseline.md).
- Dowiedz się więcej o funkcjach usługi Batch, takich jak [Konfigurowanie dostępu do węzłów obliczeniowych](pool-endpoint-configuration.md), [Korzystanie z węzłów obliczeniowych systemu Linux](batch-linux-nodes.md)i [Używanie prywatnych punktów końcowych](private-connectivity.md).