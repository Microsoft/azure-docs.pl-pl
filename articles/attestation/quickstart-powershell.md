---
title: Konfigurowanie zaświadczania platformy Azure za pomocą Azure PowerShell
description: Jak skonfigurować i skonfigurować dostawcę zaświadczania przy użyciu Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 538aa29ab66fce48da944dbdf9ea79d5c8f7f330
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421292"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą Azure PowerShell

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć i skonfigurować dostawcę zaświadczania przy użyciu Azure PowerShell. Więcej informacji na temat instalowania i uruchamiania Azure PowerShell można znaleźć w temacie [omówienie Azure PowerShell](/powershell/azure/?view=azps-2.8.0&viewFallbackFrom=azps-2.4.0) .

Należy pamiętać, że Galeria programu PowerShell ma przestarzałe wersje Transport Layer Security (TLS) 1,0 i 1,1. Zalecane jest szyfrowanie TLS 1,2 lub nowsza. W związku z tym mogą pojawić się następujące błędy:

- Ostrzeżenie: nie można rozpoznać źródła pakietu " https://www.powershellgallery.com/api/v2 "
- PackageManagement\Install-Package: nie znaleziono dopasowania dla określonych kryteriów wyszukiwania i nazwy modułu 

Aby nadal korzystać z Galeria programu PowerShell, uruchom następujące polecenie przed poleceniami instalacji-modułu

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Instalowanie polecenia AZ. zaświadczanie programu PowerShell

Na maszynie z Azure PowerShell zainstaluj moduł programu PowerShell AZ. zaświadczania, który zawiera polecenia cmdlet dla zaświadczania platformy Azure.  

### <a name="initial-installation"></a>Instalacja początkowa

Przerwij wszystkie istniejące okna programu PowerShell.

Aby zainstalować aplikację "bieżący użytkownik", Uruchom okno programu PowerShell bez podwyższonego poziomu i uruchom polecenie:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Aby zainstalować aplikację "Wszyscy użytkownicy", Uruchom okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom polecenie:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Zamknij konsolę programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="update-the-installation"></a>Aktualizowanie instalacji

Przerwij wszystkie istniejące okna programu PowerShell.

Aby zaktualizować "bieżący użytkownik", Uruchom okno programu PowerShell bez podwyższonego poziomu i uruchom polecenie:

```powershell
Update-Module -Name Az.Attestation
```

Aby zaktualizować aplikację "Wszyscy użytkownicy", Uruchom okno programu PowerShell z podwyższonym poziomem uprawnień i uruchom polecenie:

```powershell
Update-Module -Name Az.Attestation
```

Zamknij konsolę programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="get-installed-modules"></a>Pobieranie zainstalowanych modułów

Minimalna wersja AZ modułów wymaganych do obsługi operacji zaświadczania:
- AZ 4.5.0
- AZ. Accounts 1.9.2
- AZ. zaświadczanie 0.1.8

Uruchom poniższe polecenie, aby sprawdzić zainstalowaną wersję wszystkich poleceń AZ module 

```powershell
Get-InstalledModule
```
Jeśli wersje nie są zgodne z wymaganiem minimalnym, uruchom polecenie Update-module.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w konsoli programu PowerShell (bez podniesionych uprawnień dostępu).

```powershell
Connect-AzAccount
```

W razie potrzeby przejdź do subskrypcji, która ma być używana na potrzeby zaświadczania platformy Azure.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Zarejestruj dostawcę zasobów Microsoft. zaświadczania

Zarejestruj dostawcę zasobów Microsoft. zaświadczania w subskrypcji. Aby uzyskać więcej informacji o dostawcach zasobów platformy Azure oraz sposobie konfigurowania dostawców zasobów i zarządzania nimi, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md). Należy pamiętać, że Rejestracja dostawcy zasobów jest wymagana tylko raz dla subskrypcji.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Regionalna dostępność zaświadczania platformy Azure

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

Utwórz grupę zasobów dla dostawcy zaświadczania. Zwróć uwagę, że inne zasoby platformy Azure (w tym maszyna wirtualna z wystąpieniem aplikacji klienta) można umieścić w tej samej grupie zasobów.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Tworzenie dostawcy zaświadczania i zarządzanie nim

New-AzAttestation tworzy dostawcę zaświadczania.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile to plik określający zestaw zaufanych kluczy podpisywania. Jeśli nazwa pliku jest określona dla parametru PolicySignerCertificateFile, dostawca zaświadczania można skonfigurować tylko przy użyciu zasad w formacie z podpisem JWT. Zasady else można skonfigurować w formacie text lub unsigned JWT.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Aby uzyskać przykład PolicySignersCertificateFile, zobacz [przykłady certyfikatu podpisywania zasad](policy-signer-examples.md).

Get-AzAttestation pobiera właściwości dostawcy zaświadczania, takie jak status i AttestURI. Zanotuj AttestURI, ponieważ będzie on potrzebny później.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Powyższe polecenie powinno generować dane wyjściowe podobne do poniższego: 

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

Dostawców zaświadczania można usunąć za pomocą polecenia cmdlet Remove-AzAttestation.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Zarządzanie zasadami

Aby można było zarządzać zasadami, użytkownik usługi Azure AD wymaga następujących uprawnień dla "akcji":
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/odczytanie
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/zapis
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/usunięcie

Te uprawnienia można przypisać do użytkownika usługi AD za pomocą roli, takiej jak "właściciel" (uprawnienia symboli wieloznacznych), "Współautor" (uprawnienia symboli wieloznacznych) lub "Współautor zaświadczania" (określone uprawnienia tylko do zaświadczania platformy Azure).  

Aby można było odczytać zasady, użytkownik usługi Azure AD wymaga następującego uprawnienia dla "akcji":
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/odczytanie

To uprawnienie można przypisać do użytkownika usługi AD za pomocą roli, takiej jak "czytelnik" (uprawnienia symboli wieloznacznych) lub "czytelnik zaświadczania" (określone uprawnienia tylko do zaświadczania platformy Azure).

Poniższe polecenia cmdlet programu PowerShell zapewniają Zarządzanie zasadami dla dostawcy zaświadczania (jedna TEE w danej chwili).

Get-AzAttestationPolicy zwraca bieżące zasady dla określonego TEE. Polecenie cmdlet wyświetla zasady zarówno w formacie tekstu, jak i JWT dla zasad.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Obsługiwane typy TEE to "sgxenclave" i "vbsenclave".

Set-AttestationPolicy ustawia nowe zasady dla określonego TEE. Polecenie cmdlet akceptuje zasady w formacie tekstowym lub JWT i jest kontrolowane przez parametr PolicyFormat. "Tekst" jest wartością domyślną dla PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Jeśli PolicySignerCertificateFile jest dostarczany podczas tworzenia dostawcy zaświadczania, zasady można skonfigurować tylko w formacie z podpisem JWT. Zasady else można skonfigurować w formacie text lub unsigned JWT.

Zasady zaświadczania w formacie JWT muszą zawierać element Claim o nazwie "AttestationPolicy". W przypadku zasad podpisanych token JWT musi być podpisany przy użyciu klucza prywatnego odpowiadającego dowolnemu z istniejących certyfikatów podpisywania zasad.

Aby uzyskać przykłady zasad, zobacz [przykłady zasad zaświadczania](policy-examples.md).

Reset-AzAttestationPolicy resetuje zasady do domyślnych dla określonego TEE.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Zarządzanie certyfikatami podpisywania zasad

Poniższe polecenia cmdlet programu PowerShell zapewniają zarządzanie certyfikatami podpisywania zasad dla dostawcy zaświadczania:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Certyfikat podpisywania zasad jest podpisanym tokenem JWT z usługą Claim o nazwie "Maa-policyCertificate". Wartość tego żądania to JWK, który zawiera zaufany klucz podpisywania do dodania. Token JWT musi być podpisany przy użyciu klucza prywatnego odpowiadającego dowolnemu z istniejących certyfikatów podpisywania zasad.

Należy pamiętać, że wszystkie operacje manipulowania semantyką certyfikatu osoby podpisującej zasady muszą być wykonywane poza programem PowerShell. Jeśli chodzi o program PowerShell, jest to prosty ciąg.

Aby zapoznać się z przykładem certyfikatu podpisywania zasad, zobacz [przykłady certyfikatu podpisywania zasad](policy-signer-examples.md).

Aby uzyskać więcej informacji na temat poleceń cmdlet i ich parametrów, zobacz [polecenia cmdlet programu PowerShell dotyczące zaświadczania platformy Azure](/powershell/module/az.attestation/?view=azps-4.3.0#attestation) 

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Zaświadcz SGX enklawy przy użyciu przykładów kodu](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
