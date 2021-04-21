---
title: Integrowanie usługi Key Vault z urzędem certyfikacji DigiCert
description: Jak zintegrować Key Vault z urzędu certyfikacji firmy DigiCert
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4351526c77961856b118bdeae07cecf48340f5fe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749316"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrowanie usługi Key Vault z urzędem certyfikacji DigiCert

Azure Key Vault umożliwia łatwe aprowizować i wdrażać certyfikaty cyfrowe dla sieci oraz zarządzać nimi oraz włączyć bezpieczną komunikację dla aplikacji. Certyfikat cyfrowy to poświadczenie elektroniczne służące do ustanawiania potwierdzenia tożsamości w ramach transakcji elektronicznej. 

Użytkownicy usługi Azure Key Vault mogą generować certyfikaty firmy DigiCert bezpośrednio z Key Vault. Key Vault zapewniać end-to-end zarządzanie cyklem życia certyfikatów dla certyfikatów wystawionych przez firmę DigiCert za pośrednictwem zaufanego partnerstwa firmy Key Vault z urzędu certyfikacji firmy DigiCert.

Aby uzyskać więcej ogólnych informacji na temat certyfikatów, [zobacz Azure Key Vault Certyfikaty](./about-certificates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz mieć następujące zasoby.
* Magazyn kluczy. Możesz użyć istniejącego magazynu kluczy lub utworzyć nowy, korzystając z kroków w jednym z tych przewodników Szybki start:
   - [Tworzenie magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)
   - [Tworzenie magazynu kluczy przy użyciu Azure PowerShell](../general/quick-create-powershell.md)
   - [Utwórz magazyn kluczy przy użyciu Azure Portal](../general/quick-create-portal.md).
*   Musisz aktywować konto DigiCert CertCentral. [Zarejestruj się,](https://www.digicert.com/account/signup/) aby uzyskać konto CertCentral.
*   Uprawnienia na poziomie administratora na twoich kontach.


### <a name="before-you-begin"></a>Zanim rozpoczniesz

Upewnij się, że masz następujące informacje przydatne z konta DigiCert CertCentral:
-   Identyfikator konta CertCentral
-   Identyfikator organizacji
-   Klucz interfejsu API

## <a name="adding-certificate-authority-in-key-vault"></a>Dodawanie urzędu certyfikacji w Key Vault 
Po zebraniu powyższych informacji z konta DigiCert CertCentral można teraz dodać firmę DigiCert do listy urząd certyfikacji w magazynie kluczy.

### <a name="azure-portal"></a>Azure Portal

1.  Aby dodać urząd certyfikacji DigiCert, przejdź do magazynu kluczy, do którego chcesz dodać firmę DigiCert. 
2.  Na stronach Key Vault właściwości wybierz **pozycję Certyfikaty.**
3.  Wybierz **kartę Urzędy** certyfikacji. ![ wybieranie urzędów certyfikacji](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Wybierz **opcję** Dodaj.
 ![dodawanie urzędów certyfikacji](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Na **ekranie Tworzenie urzędu certyfikacji** wybierz następujące wartości:
    -   **Nazwa:** dodaj rozpoznawalną nazwę wystawcy. Przykład digicertca
    -   **Dostawca:** wybierz z menu pozycję DigiCert.
    -   **Identyfikator konta:** wprowadź identyfikator konta DigiCert CertCentral
    -   **Hasło konta:** wprowadź klucz interfejsu API wygenerowany na koncie DigiCert CertCentral
    -   **Identyfikator organizacji:** wprowadź identyfikator organizacji zebrany z konta DigiCert CertCentral 
    -   Kliknij pozycję **Utwórz**.
   
6.  Zobaczysz, że firma DigicertCA została teraz dodana na liście Urzędy certyfikacji.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi za pomocą poleceń lub skryptów. Platforma Azure Azure Cloud Shell to interaktywne środowisko powłoki, z których można korzystać za pośrednictwem Azure Portal w samej przeglądarce.

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Tworzenie **grupy zasobów**

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Tworzenie **Key Vault**

Musisz użyć unikatowej nazwy magazynu kluczy. W tym miejscu "Contoso-Vaultname" to nazwa Key Vault w tym przewodniku.

- **Nazwa magazynu** Contoso-Vaultname.
- **Nazwa grupy zasobów** ContosoResourceGroup.
- **Lokalizacja** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Zdefiniuj zmienne dla informacji zebranych z konta DigiCert CertCentral.

- Definiowanie **zmiennej identyfikatora** konta
- Definiowanie **zmiennej identyfikatora** organizacji
- Definiowanie **zmiennej klucza interfejsu API**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Ustaw **wystawcę**. Spowoduje to dodanie firmy Digicert jako urzędu certyfikacji w magazynie kluczy. Aby dowiedzieć się więcej o parametrach, [przeczytaj tutaj](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Ustawianie zasad dla certyfikatu i wystawiania certyfikatu z** firmy DigiCert bezpośrednio Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Certyfikat został pomyślnie wystawiony przez urząd certyfikacji firmy Digicert w ramach określonej Key Vault tej integracji.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystawiony certyfikat ma stan "wyłączony" w Azure Portal, przejdź  do operacji certyfikatu, aby przejrzeć komunikat o błędzie firmy DigiCert dla tego certyfikatu.

 ![Operacja certyfikatu](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Komunikat o błędzie "Wykonaj scalanie, aby ukończyć to żądanie certyfikatu".
Aby ukończyć to żądanie, należy scalić żądanie CSR podpisane przez urząd certyfikacji. Więcej informacji można znaleźć [tutaj](./create-certificate-signing-request.md)

Aby uzyskać więcej informacji, zobacz [Operacje na certyfikatach w Key Vault API REST](/rest/api/keyvault). Aby uzyskać informacje na temat ustanawiania uprawnień, zobacz [Magazyny — tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — aktualizowanie zasad dostępu.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

- Czy mogę wygenerować certyfikat z symbolami wieloznaczny digicert za pomocą usługi KeyVault? 
   Tak. Będzie to zależeć od sposobu skonfigurowania konta digicert.
- Jak utworzyć certyfikat **OV-SSL lub EV-SSL** przy użyciu firmy DigiCert? 
   Magazyn kluczy obsługuje tworzenie certyfikatów SSL OV i EV. Podczas tworzenia certyfikatu kliknij pozycję Konfiguracja zasad zaawansowanych, a następnie określ typ certyfikatu. Obsługiwane wartości to: OV-SSL, EV-SSL
   
   Jeśli konto firmy Digicert na to pozwala, można utworzyć ten typ certyfikatu w magazynie kluczy. W przypadku tego typu certyfikatu weryfikacja jest wykonywana przez firmę DigiCert, a jej zespół pomocy technicznej byłby w stanie najlepiej pomóc w rozwiązaniu, jeśli weryfikacja zakończy się niepowodzeniem. Podczas tworzenia certyfikatu można dodać dodatkowe informacje, definiując je w nazwie subjectName.

Przykład
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Czy istnieje opóźnienie w tworzeniu certyfikatu digicert za pośrednictwem integracji w porównaniu z bezpośrednim uzyskaniem certyfikatu za pośrednictwem firmy digicert?
   Nie. Podczas tworzenia certyfikatu jest to proces weryfikacji, który może zająć trochę czasu, a weryfikacja jest zależna od procesu, który następuje przez firmę DigiCert.


## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)