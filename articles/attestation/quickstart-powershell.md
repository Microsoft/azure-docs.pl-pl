---
title: Konfigurowanie Azure Attestation za pomocą Azure PowerShell
description: Jak skonfigurować i skonfigurować dostawcę zaświadczenia przy użyciu Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eca74ffe7b62cc5071d8ebaeefab52e5e59409d4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832234"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Szybki start: konfigurowanie Azure Attestation za pomocą Azure PowerShell

Wykonaj poniższe kroki, aby utworzyć i skonfigurować dostawcę zaświadczenia przy użyciu Azure PowerShell. Zobacz [Omówienie Azure PowerShell,](/powershell/azure/) aby uzyskać informacje na temat sposobu instalowania i uruchamiania Azure PowerShell.

Należy pamiętać, że Galeria programu PowerShell jest przestarzała Transport Layer Security (TLS) w wersjach 1.0 i 1.1. Zalecana jest wersja TLS 1.2 lub nowsza. W związku z tym mogą wystąpić następujące błędy:

- OSTRZEŻENIE: Nie można rozpoznać źródła pakietu " https://www.powershellgallery.com/api/v2 "
- PackageManagement\Install-Package: Nie znaleziono dopasowania dla określonych kryteriów wyszukiwania i nazwy modułu 

Aby kontynuować interakcję z Galeria programu PowerShell, uruchom następujące polecenie przed Install-Module poleceniami

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Instalowanie modułu Az.Attestation programu PowerShell

Na maszynie z Azure PowerShell zainstaluj moduł Az.Attestation programu PowerShell, który zawiera polecenia cmdlet dla Azure Attestation.  

### <a name="initial-installation"></a>Instalacja początkowa

Zakończ wszystkie istniejące okna programu PowerShell.

Aby zainstalować dla "bieżącego użytkownika", uruchom okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Aby zainstalować dla "wszystkich użytkowników", uruchom okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Zamknij konsolę programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="update-the-installation"></a>Aktualizowanie instalacji

Zakończ wszystkie istniejące okna programu PowerShell.

Aby zaktualizować dla "bieżącego użytkownika", uruchom okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom:

```powershell
Update-Module -Name Az.Attestation
```

Aby zaktualizować program dla "wszystkich użytkowników", uruchom okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom:

```powershell
Update-Module -Name Az.Attestation
```

Zamknij konsolę programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="get-installed-modules"></a>Pobieranie zainstalowanych modułów

Minimalna wersja modułów Az wymagana do obsługi operacji zaświadczenia:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Uruchom poniższe polecenie, aby sprawdzić zainstalowaną wersję wszystkich modułów Az 

```powershell
Get-InstalledModule
```
Jeśli wersje nie są zgodne z minimalnym wymaganiem, uruchom Update-Module polecenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w konsoli programu PowerShell (bez podniesionych uprawnień dostępu).

```powershell
Connect-AzAccount
```

W razie potrzeby przejdź do subskrypcji, która ma być używana na potrzeby Azure Attestation.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.Attestation

Zarejestruj dostawcę zasobów Microsoft.Attestation w subskrypcji. Aby uzyskać więcej informacji na temat dostawców zasobów platformy Azure oraz sposobu konfigurowania dostawców zasobów i zarządzania nimi, zobacz Azure resource providers and types (Dostawcy [zasobów i typy zasobów platformy Azure).](../azure-resource-manager/management/resource-providers-and-types.md) Pamiętaj, że zarejestrowanie dostawcy zasobów jest wymagane tylko raz dla subskrypcji.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Dostępność regionalna Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

Utwórz grupę zasobów dla dostawcy zaświadczenia. Należy pamiętać, że inne zasoby platformy Azure (w tym maszyna wirtualna z wystąpieniem aplikacji klienckiej) można umieścić w tej samej grupie zasobów.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

 > [!NOTE]
   > Po utworzeniu dostawcy zaświadczeń w tej grupie zasobów użytkownik  usługi Azure AD musi mieć rolę współautora zaświadczeń na dostawcy, aby wykonywać operacje takie jak konfiguracja zasad/zarządzanie certyfikatami osoby podpisują zasady. Te uprawnienia mogą być również dziedziczone z rolami, takimi jak **Właściciel** (uprawnienia z symbolami wieloznacznych)/ **Współautor** (uprawnienia z symbolami wieloznaczny) w subskrypcji/grupie zasobów.  


## <a name="create-and-manage-an-attestation-provider"></a>Tworzenie dostawcy zaświadczenia i zarządzanie tym dostawcą

New-AzAttestation tworzy dostawcę zaświadczenia.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile to plik określający zestaw zaufanych kluczy podpisywania. Jeśli dla parametru PolicySignerCertificateFile zostanie określona nazwa pliku, dostawcę zasypowania można skonfigurować tylko przy użyciu zasad w formacie JWT ze podpisem. W innym przypadku zasady można skonfigurować w formacie tekstowym lub niepodpisanym JWT.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Przykład PolicySignersCertificateFile można znaleźć w [przykładach certyfikatu podpisywania zasad.](policy-signer-examples.md)

Get-AzAttestation pobiera właściwości dostawcy zaświadczenia, takie jak stan i attestURI. Zanotuj attestURI, ponieważ będzie on potrzebny później.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Powyższe polecenie powinno tworzyć dane wyjściowe podobne do poniższych: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Dostawców zaswiadczenia można usunąć za pomocą Remove-AzAttestation cmdlet.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Zarządzanie zasadami

Aby zarządzać zasadami, użytkownik usługi Azure AD wymaga następujących uprawnień dla akcji:
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

 Aby wykonać te akcje, użytkownik usługi Azure AD musi mieć **rolę** Współautor zaświadczenia u dostawcy zaswiadczenia. Te uprawnienia mogą być również dziedziczone z rolami, takimi jak **Właściciel** (uprawnienia z symbolami wieloznacznych)/ **Współautor** (uprawnienia z symbolami wieloznaczny) w subskrypcji/grupie zasobów.  

Aby można było odczytać zasady, użytkownik usługi Azure AD wymaga następującego uprawnienia dla akcji:
- Microsoft.Attestation/attestationProviders/attestation/read

 Aby wykonać tę akcję, użytkownik usługi Azure AD musi **mieć** rolę Czytelnik zaświadczenia u dostawcy zaświadczenia. Uprawnienie do odczytu może być również dziedziczone z rolami, takimi jak **Czytelnik** (uprawnienia z symbolami wieloznacznymi) w subskrypcji/grupie zasobów.  

Poniższe polecenia cmdlet programu PowerShell zapewniają zarządzanie zasadami dla dostawcy zaświadczenia (po jednej tee na raz).

Get-AzAttestationPolicy zwraca bieżące zasady dla określonej aplikacji TEE. Polecenie cmdlet wyświetla zasady w formacie tekstowym i JWT zasad.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Obsługiwane typy TEE to "SgxEnclave", "OpenEnclave" i "VbsEnclave".

Set-AttestationPolicy ustawia nowe zasady dla określonej aplikacji TEE. Polecenie cmdlet akceptuje zasady w formacie tekstowym lub JWT i jest kontrolowane przez parametr PolicyFormat. Wartość domyślna dla formatu PolicyFormat to "Text". 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Jeśli plik PolicySignerCertificateFile zostanie podany podczas tworzenia dostawcy zaswiadczenia, zasady można skonfigurować tylko w formacie JWT ze podpisem. W innym przypadku zasady można skonfigurować w formacie tekstowym lub niepodpisanym JWT.

Zasady zaświadczenia w formacie JWT muszą zawierać oświadczenie o nazwie "AttestationPolicy". W przypadku podpisanych zasad JWT musi być podpisany za pomocą klucza prywatnego odpowiadającego dowolne z istniejących certyfikatów osoby podpiszcej zasady.

Przykłady zasad można znaleźć [w przykładach zasad zaświadczenia.](policy-examples.md)

Reset-AzAttestationPolicy zasady są resetowane do domyślnych dla określonej aplikacji TEE.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Zarządzanie certyfikatami podpisatora zasad

Poniższe polecenia cmdlet programu PowerShell zapewniają zarządzanie certyfikatami podpisywania zasad dla dostawcy zaświadczeń:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Certyfikat osoby podpisywanej zasad to podpisany JWT z oświadczeniem o nazwie "maa-policyCertificate". Wartość oświadczenia to zestaw JWK zawierający zaufany klucz podpisywania do dodania. JWT musi być podpisany za pomocą klucza prywatnego odpowiadającego dowolnym z istniejących certyfikatów osoby podpiszcej zasady.

Należy pamiętać, że wszystkie semantyczne manipulowanie certyfikatem podpisatora zasad musi odbywać się poza programem PowerShell. Jeśli chodzi o program PowerShell, jest to prosty ciąg.

Przykład certyfikatu podpisywania zasad można znaleźć w [przykładach certyfikatu podpisywania zasad.](policy-signer-examples.md)

Aby uzyskać więcej informacji na temat poleceń cmdlet i ich parametrów, zobacz [Azure Attestation poleceń cmdlet programu PowerShell](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczenia](author-sign-policy.md)
- [Attest enklawy SGX przy użyciu przykładów kodu](/samples/browse/?expanded=azure&terms=attestation)
