---
title: Zarządzanie certyfikatem w Azure Automation
description: Certyfikaty są bezpiecznie przechowywane w Azure Automation tak, aby elementy Runbook lub konfiguracje DSC mogły uzyskać do nich dostęp do uwierzytelniania na platformie Azure i w przypadku zasobów innych firm. W tym artykule opisano szczegóły certyfikatów i sposób pracy z nimi zarówno w tworzeniu tekstu, jak i w formie graficznej.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732825"
---
# <a name="manage-certificates-in-azure-automation"></a>Zarządzanie certyfikatami w Azure Automation

Certyfikaty są bezpiecznie przechowywane w Azure Automation, dzięki czemu można uzyskać do nich dostęp za pomocą elementów Runbook lub konfiguracji DSC przy użyciu działania [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) dla Azure Resource Manager zasobów. Bezpieczny magazyn certyfikatów umożliwia tworzenie elementów Runbook i konfiguracji DSC, które używają certyfikatów do uwierzytelniania, a także dodawanie ich do platformy Azure lub zasobów innych firm.

Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzany przez Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ PowerShell polecenia cmdlet

Polecenia AZ w poniższej tabeli służą do tworzenia zasobów poświadczeń usługi Automation i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu AZ. Automation](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

|Polecenie cmdlet |Opis|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Przekazuje certyfikat usługi dla określonej usługi w chmurze.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Pobiera informacje o certyfikacie do użycia w elemencie Runbook lub konfiguracji DSC. Sam certyfikat można pobrać tylko przy użyciu `Get-AutomationCertificate` działania.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Tworzy nowy certyfikat w Azure Automation.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Usuwa certyfikat z Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Ustawia właściwości istniejącego certyfikatu, włącznie z przekazywaniem pliku certyfikatu i ustawieniem hasła dla pliku **PFX** .|

## <a name="activities"></a>Działania

Działania w poniższej tabeli służą do uzyskiwania dostępu do certyfikatów w konfiguracjach elementów Runbook i DSC.

| Działania | Opis |
|:---|:---|
|`Get-AutomationCertificate`|Pobiera certyfikat do użycia w konfiguracji elementu Runbook lub DSC. Zwraca obiekt [System. Security. Cryptography. x509. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> Należy unikać używania zmiennych w `Name` parametrze `Get-AutomationCertificate` w elemencie Runbook lub konfiguracji DSC. Użycie zmiennych w tym parametrze komplikuje wykrywanie zależności między elementami Runbook lub konfiguracjami DSC a zmiennymi automatyzacji w czasie projektowania.

## <a name="python-2-functions"></a>Funkcje języka Python 2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do certyfikatów w elemencie Runbook języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_certificate` | Pobiera informacje o zasobach certyfikatu. |

> [!NOTE]
> Aby uzyskać dostęp do `automationassets` funkcji zasobów, należy zaimportować moduł na początku elementu Runbook języka Python.

## <a name="creating-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu należy przekazać plik cer lub PFX do Azure Automation. Jeśli oznaczesz certyfikat jako eksportowalny, możesz go przenieść z magazynu certyfikatów Azure Automation. Jeśli nie można go eksportować, może być używany tylko do podpisywania w ramach elementu Runbook lub konfiguracji DSC. Azure Automation wymaga, aby certyfikat miał dostawcę **usług kryptograficznych RSA i AES firmy Microsoft**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Utwórz nowy certyfikat z Azure Portal

1. Na koncie usługi Automation kliknij pozycję **zasoby** , aby otworzyć stronę zasoby.
2. Wybierz pozycję **Certyfikaty** , aby otworzyć stronę certyfikaty.
3. Kliknij pozycję **Dodaj certyfikat** w górnej części strony.
4. Wpisz nazwę certyfikatu w polu **Nazwa** .
5. Aby wyszukać plik **CER** lub **PFX** , kliknij pozycję **Wybierz plik** w obszarze **Przekaż plik certyfikatu**. W przypadku wybrania pliku **PFX**określ hasło i wskaż, czy można je wyeksportować.
6. Kliknij przycisk **Utwórz** , aby zapisać nowy zasób certyfikatu.

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

W poniższym przykładzie pokazano, jak wdrożyć certyfikat na koncie usługi Automation przy użyciu szablonu Menedżer zasobów za pomocą programu PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
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

## <a name="using-a-certificate"></a>Korzystanie z certyfikatu

Aby użyć certyfikatu, użyj `Get-AutomationCertificate` działania. Nie można użyć polecenia cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , ponieważ zwraca informacje o zasobie certyfikatu, ale nie sam certyfikat.

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

Aby dodać `Get-AutomationCertificate` działanie do graficznego elementu Runbook, kliknij prawym przyciskiem myszy certyfikat w okienku biblioteka i wybierz polecenie **Dodaj do kanwy**.

![Dodawanie certyfikatu do kanwy](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznym elemencie Runbook. Jest to takie samo jak w poprzednim przykładzie, który pokazuje, jak dodać certyfikat do usługi w chmurze z tekstowego elementu Runbook.

![Przykład tworzenia graficznego](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Przykładowa Python 2

Poniższy przykład pokazuje, jak uzyskać dostęp do certyfikatów w elementach Runbook python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat pracy z linkami w celu kontrolowania logicznego przepływu działań wykonywanych przez element Runbook, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow). 
