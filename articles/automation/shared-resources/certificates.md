---
title: Zarządzanie certyfikatami w Azure Automation
description: W tym artykule opisano sposób pracy z certyfikatami w celu uzyskania dostępu przez elementy Runbook i konfiguracje DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: a4cc6a08ab0725f8fa3ceb745b933153842075a6
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803181"
---
# <a name="manage-certificates-in-azure-automation"></a>Zarządzanie certyfikatami w Azure Automation

Azure Automation przechowuje certyfikaty bezpiecznie na potrzeby dostępu przez elementy Runbook i konfiguracje DSC przy użyciu polecenia cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) dla zasobów Azure Resource Manager. Bezpieczny magazyn certyfikatów umożliwia tworzenie elementów Runbook i konfiguracji DSC, które używają certyfikatów do uwierzytelniania, lub dodawanie ich do platformy Azure lub zasobów innych firm.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w usłudze Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Usługa Automation przechowuje klucz w usłudze Key Vault zarządzanej przez system. Przed zapisaniem bezpiecznego elementu zawartości Usługa Automation ładuje klucz z Key Vault, a następnie używa go do zaszyfrowania elementu zawartości. 

## <a name="powershell-cmdlets-to-access-certificates"></a>Polecenia cmdlet programu PowerShell do uzyskiwania dostępu do certyfikatów

Polecenia cmdlet w poniższej tabeli tworzą i zarządzają certyfikatami automatyzacji za pomocą programu PowerShell. Są one dostarczane jako część [AZ modules](modules.md#az-modules).

|Polecenie cmdlet |Opis|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Pobiera informacje o certyfikacie do użycia w elemencie Runbook lub konfiguracji DSC. Sam certyfikat można pobrać tylko przy użyciu wewnętrznego `Get-AutomationCertificate` polecenia cmdlet.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Tworzy nowy certyfikat w usłudze Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Usuwa certyfikat z automatyzacji.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Ustawia właściwości istniejącego certyfikatu, włącznie z przekazywaniem pliku certyfikatu i ustawieniem hasła dla pliku **PFX** .|

Za pomocą polecenia cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) można także przekazać certyfikat usługi dla określonej usługi w chmurze.

## <a name="internal-cmdlets-to-access-certificates"></a>Wewnętrzne polecenia cmdlet do uzyskiwania dostępu do certyfikatów

Wewnętrzne polecenie cmdlet w poniższej tabeli służy do uzyskiwania dostępu do certyfikatów w elementach Runbook. To polecenie cmdlet jest dołączone do modułu globalnego `Orchestrator.AssetManagement.Cmdlets` . Aby uzyskać więcej informacji, zobacz [wewnętrzne polecenia cmdlet](modules.md#internal-cmdlets).

| Wewnętrzne polecenie cmdlet | Opis |
|:---|:---|
|`Get-AutomationCertificate`|Pobiera certyfikat do użycia w konfiguracji elementu Runbook lub DSC. Zwraca obiekt [System. Security. Cryptography. x509. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> Należy unikać używania zmiennych w `Name` parametrze `Get-AutomationCertificate` w elemencie Runbook lub konfiguracji DSC. Takie zmienne mogą komplikują odnajdywanie zależności między elementami Runbook lub konfiguracjami DSC a zmiennymi automatyzacji w czasie projektowania.

## <a name="python-2-functions-to-access-certificates"></a>Funkcja Python 2 do uzyskiwania dostępu do certyfikatów

Użyj funkcji z poniższej tabeli, aby uzyskać dostęp do certyfikatów w elemencie Runbook języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_certificate` | Pobiera informacje o zasobach certyfikatu. |

> [!NOTE]
> `automationassets`Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł na początku elementu Runbook języka Python.

## <a name="create-a-new-certificate"></a>Utwórz nowy certyfikat

Podczas tworzenia nowego certyfikatu przekazuje plik cer lub PFX do automatyzacji. Jeśli oznaczesz certyfikat jako eksportowalny, możesz go przenieść z magazynu certyfikatów automatyzacji. Jeśli nie można go eksportować, może być używany tylko do podpisywania w ramach elementu Runbook lub konfiguracji DSC. Automatyzacja wymaga, aby certyfikat miał dostawcę **usług kryptograficznych RSA i AES firmy Microsoft**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Utwórz nowy certyfikat z Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Certyfikaty** w obszarze **zasób udostępniony**.
1. Na stronie **Certyfikaty** wybierz pozycję **Dodaj certyfikat**.
1. W polu **Nazwa** wpisz nazwę certyfikatu.
1. Aby wyszukać plik **CER** lub **PFX** , w obszarze **Przekaż plik certyfikatu**wybierz **pozycję Wybierz plik**. W przypadku wybrania pliku **PFX** określ hasło i wskaż, czy można je wyeksportować.
1. Wybierz pozycję **Utwórz** , aby zapisać nowy zasób certyfikatu.

### <a name="create-a-new-certificate-with-powershell"></a>Tworzenie nowego certyfikatu przy użyciu programu PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy certyfikat automatyzacji i oznaczyć go jako możliwy do eksportu. Ten przykład importuje istniejący plik **PFX** .

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Utwórz nowy certyfikat z szablonem Menedżer zasobów

Poniższy przykład ilustruje sposób wdrażania certyfikatu na koncie usługi Automation przy użyciu szablonu Menedżer zasobów za pomocą programu PowerShell:

```powershell-interactive
$PfxCertPath = '<PFX cert path>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Pobierz certyfikat

Aby pobrać certyfikat, użyj wewnętrznego `Get-AutomationCertificate` polecenia cmdlet. Nie można użyć polecenia cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) , ponieważ zwraca informacje o zasobie certyfikatu, ale nie sam certyfikat.

### <a name="textual-runbook-example"></a>Przykład tekstowego elementu Runbook

Poniższy przykład pokazuje, jak dodać certyfikat do usługi w chmurze w elemencie Runbook. W tym przykładzie hasło jest pobierane z zaszyfrowanej zmiennej automatyzacji.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Przykład graficzny elementu Runbook

Dodaj działanie wewnętrznego `Get-AutomationCertificate` polecenia cmdlet do graficznego elementu Runbook, klikając prawym przyciskiem myszy certyfikat w okienku biblioteka i wybierając pozycję **Dodaj do kanwy**.

![Zrzut ekranu przedstawiający Dodawanie certyfikatu do kanwy](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznym elemencie Runbook.

![Zrzut ekranu przedstawiający przykład tworzenia graficznego](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Przykładowa Python 2

Poniższy przykład pokazuje, jak uzyskać dostęp do certyfikatów w elementach Runbook języka Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet służących do uzyskiwania dostępu do certyfikatów, zobacz [Zarządzanie modułami w Azure Automation](modules.md).
* Aby uzyskać ogólne informacje o elementach Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](../automation-runbook-execution.md).
* Aby uzyskać szczegółowe informacje na temat konfiguracji DSC, zobacz [Konfiguracja stanu Azure Automation przegląd](../automation-dsc-overview.md).
