---
title: Zarządzanie certyfikatami w Azure Automation
description: W tym artykule opisano sposób pracy z certyfikatami w celu uzyskania dostępu za pomocą ustawień Runbook i DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788f15cd1edad228e695e6e87f5b630b8e4fdf55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834448"
---
# <a name="manage-certificates-in-azure-automation"></a>Zarządzanie certyfikatami w Azure Automation

Azure Automation bezpiecznie przechowuje certyfikaty, aby uzyskać do nich dostęp za pomocą ustawień Runbook i DSC, używając polecenia cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) Azure Resource Manager zasobów. Bezpieczny magazyn certyfikatów umożliwia tworzenie podręczników Runbook i konfiguracji DSC, które używają certyfikatów do uwierzytelniania, lub dodawanie ich do zasobów platformy Azure lub innych firm.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w u usługi Automation przy użyciu unikatowego klucza generowanego dla każdego konta usługi Automation. Usługa Automation przechowuje klucz w usłudze zarządzanej Key Vault systemowej. Przed zapisaniem bezpiecznego zasobu automatyzacja ładuje klucz z Key Vault, a następnie używa go do szyfrowania zasobu.

## <a name="powershell-cmdlets-to-access-certificates"></a>Polecenia cmdlet programu PowerShell do uzyskiwania dostępu do certyfikatów

Polecenia cmdlet w poniższej tabeli tworzą certyfikaty usługi Automation i zarządzają nimi za pomocą programu PowerShell. Są one częścią modułów [Az](modules.md#az-modules).

|Polecenie cmdlet |Opis|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Pobiera informacje o certyfikacie do użycia w konfiguracji runbook lub DSC. Certyfikat można pobrać tylko za pomocą wewnętrznego `Get-AutomationCertificate` polecenia cmdlet .|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Tworzy nowy certyfikat w u usługi Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Usuwa certyfikat z usługi Automation.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Ustawia właściwości istniejącego certyfikatu, w tym przekazywanie pliku certyfikatu i ustawianie hasła dla **pliku pfx.**|

Polecenia cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) można również użyć do przekazania certyfikatu usługi dla określonej usługi w chmurze.

## <a name="internal-cmdlets-to-access-certificates"></a>Wewnętrzne polecenia cmdlet do uzyskiwania dostępu do certyfikatów

Wewnętrzne polecenie cmdlet w poniższej tabeli służy do uzyskiwania dostępu do certyfikatów w twoich elementy Runbook. To polecenie cmdlet jest dostarczany z modułem globalnym `Orchestrator.AssetManagement.Cmdlets` . Aby uzyskać więcej informacji, zobacz [Wewnętrzne polecenia cmdlet](modules.md#internal-cmdlets).

| Wewnętrzne polecenie cmdlet | Opis |
|:---|:---|
|`Get-AutomationCertificate`|Pobiera certyfikat do użycia w konfiguracji runbook lub DSC. Zwraca [obiekt System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE]
> Należy unikać używania zmiennych w `Name` parametrze w `Get-AutomationCertificate` konfiguracji runbook lub DSC. Takie zmienne mogą komplikować odnajdywanie zależności między podręcznikami Runbook lub konfiguracjami DSC i zmiennymi automatyzacji w czasie projektowania.

## <a name="python-functions-to-access-certificates"></a>Funkcje języka Python do uzyskiwania dostępu do certyfikatów

Użyj funkcji z poniższej tabeli, aby uzyskać dostęp do certyfikatów w runbook języka Python 2 i 3. Aplikacje Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_certificate` | Pobiera informacje o zasobie certyfikatu. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, musisz zaimportować moduł na początku `automationassets` swojego runbook języka Python.

## <a name="create-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu należy przekazać plik cer lub pfx do usługi Automation. Jeśli oznaczysz certyfikat jako eksportowalny, możesz przenieść go z magazynu certyfikatów usługi Automation. Jeśli nie można go eksportować, można go używać tylko do podpisywania w ramach konfiguracji runbook lub DSC. Usługa Automation wymaga certyfikatu, aby dostawca **Microsoft Enhanced RSA i AES Cryptographic Provider miał dostawcę**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Utwórz nowy certyfikat przy użyciu Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Certyfikaty w obszarze** **Zasób udostępniony.**
1. Na **stronie Certyfikaty** wybierz pozycję **Dodaj certyfikat.**
1. W **polu** Nazwa wpisz nazwę certyfikatu.
1. Aby przejść do pliku **cer** lub **pfx,** w obszarze **Przekaż** plik certyfikatu wybierz **pozycję Wybierz plik**. W przypadku wybrania **pliku pfx** określ hasło i wskaż, czy można go wyeksportować.
1. Wybierz **pozycję Utwórz,** aby zapisać nowy zasób certyfikatu.

### <a name="create-a-new-certificate-with-powershell"></a>Tworzenie nowego certyfikatu przy użyciu programu PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy certyfikat usługi Automation i oznaczyć go jako eksportowalny. Ten przykład importuje istniejący **plik pfx.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath -Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Tworzenie nowego certyfikatu przy użyciu Resource Manager szablonu

W poniższym przykładzie pokazano, jak wdrożyć certyfikat na koncie usługi Automation przy użyciu szablonu Resource Manager za pomocą programu PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Uzyskiwanie certyfikatu

Aby pobrać certyfikat, użyj wewnętrznego `Get-AutomationCertificate` polecenia cmdlet . Nie można użyć polecenia cmdlet [Get-AzAutomationCertificate,](/powershell/module/Az.Automation/Get-AzAutomationCertificate) ponieważ zwraca ono informacje o zasobie certyfikatu, ale nie o samym certyfikacie.

### <a name="textual-runbook-examples"></a>Tekstowe przykłady runbook

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W poniższym przykładzie pokazano, jak dodać certyfikat do usługi w chmurze w elementy Runbook. W tym przykładzie hasło jest pobierane z zaszyfrowanej zmiennej automatyzacji.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

W poniższym przykładzie pokazano, jak uzyskać dostęp do certyfikatów w elementy Runbook języka Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

W poniższym przykładzie pokazano, jak uzyskać dostęp do certyfikatów w elementy Runbook języka Python 3 (wersja zapoznawcza).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Przykład graficznego runbook

Dodaj działanie dla wewnętrznego polecenia cmdlet do graficznego element runbook, klikając prawym przyciskiem myszy certyfikat w okienku Biblioteka i wybierając `Get-AutomationCertificate` polecenie **Dodaj do kanwy.**

![Zrzut ekranu przedstawiający dodawanie certyfikatu do kanwy](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznym elementie Runbook.

![Zrzut ekranu przedstawiający przykład tworzenia graficznego](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o poleceniach cmdlet używanych do uzyskiwania dostępu do certyfikatów, zobacz Zarządzanie modułami w [programie Azure Automation](modules.md).
* Aby uzyskać ogólne informacje na temat runbook, zobacz [Runbook execution in Azure Automation (Wykonywanie runbook w programie Azure Automation](../automation-runbook-execution.md)).
* Aby uzyskać szczegółowe informacje o konfiguracjach DSC, [zobacz Azure Automation State Configuration omówienie](../automation-dsc-overview.md).
