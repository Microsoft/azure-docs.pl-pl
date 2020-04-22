---
title: Zarządzanie certyfikatami w usłudze Azure Automation
description: Certyfikaty są bezpiecznie przechowywane w usłudze Azure Automation, dzięki czemu elementy runbook lub konfiguracje DSC mogą uzyskiwać do nich dostęp do uwierzytelniania za pomocą platformy Azure i zasobów innych firm. W tym artykule opisano szczegóły dotyczące certyfikatów i sposobu pracy z nimi zarówno w tekście, jak i w formie graficznej.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732825"
---
# <a name="manage-certificates-in-azure-automation"></a>Zarządzanie certyfikatami w usłudze Azure Automation

Certyfikaty są bezpiecznie przechowywane w usłudze Azure Automation, dzięki czemu są dostępne dla liczeni lub konfiguracji DSC przy użyciu działania [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) dla zasobów usługi Azure Resource Manager. Bezpieczne przechowywanie certyfikatów umożliwia tworzenie śmięty umnień i konfiguracji DSC, które używają certyfikatów do uwierzytelniania lub dodają je do platformy Azure lub zasobów innych firm.

Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy zarządzanym przez system. Przed zapisaniem bezpiecznego zasobu klucz jest ładowany z usługi Key Vault, a następnie używany do szyfrowania zasobu. Ten proces jest zarządzany przez usługę Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="az-powershell-cmdlets"></a>Polecenia cmdlet programu Az PowerShell

W przypadku programu Az polecenia cmdlet w poniższej tabeli są używane do tworzenia zasobów poświadczeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu Az.Automation](/powershell/azure/overview), który jest dostępny do użytku w systemach runbook automation i konfiguracjach DSC.

|Polecenie cmdlet |Opis|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Przekazuje certyfikat usługi dla określonej usługi w chmurze.|
|[Certyfikat Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Pobiera informacje o certyfikacie do użycia w konfiguracji żyli lub dsc. Certyfikat można pobrać tylko przy `Get-AutomationCertificate` użyciu działania.|
|[Nowy certyfikat AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Tworzy nowy certyfikat w usłudze Azure Automation.|
|[Usuń-AzAutomationCertyfikat](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Usuwa certyfikat z usługi Azure Automation.|
|[Certyfikat Ustawiania AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Ustawia właściwości istniejącego certyfikatu, w tym przekazywanie pliku certyfikatu i ustawianie hasła dla pliku **.pfx.**|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do uzyskiwania dostępu do certyfikatów w konfiguracjach elementów runbook i DSC.

| Działania | Opis |
|:---|:---|
|`Get-AutomationCertificate`|Pobiera certyfikat do użycia w konfiguracji uruchomieniu lub DSC. Zwraca obiekt [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Należy unikać używania zmiennych w parametrze `Name` `Get-AutomationCertificate` w konfiguracji uruchomieniu lub DSC. Użycie zmiennych w tym parametrze komplikuje odnajdowanie zależności między elementami runbook lub konfiguracjami DSC i zmiennymi automatyzacji w czasie projektowania.

## <a name="python-2-functions"></a>Funkcje Pythona 2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do certyfikatów w uruchomieniu elementu runbook języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_certificate` | Pobiera informacje o zasobie certyfikatu. |

> [!NOTE]
> Aby uzyskać `automationassets` dostęp do funkcji zasobu, należy zaimportować moduł na początku elementów runbook języka Python.

## <a name="creating-a-new-certificate"></a>Tworzenie nowego certyfikatu

Podczas tworzenia nowego certyfikatu należy przekazać plik .cer lub .pfx do usługi Azure Automation. Jeśli oznaczysz certyfikat jako eksportowalny, możesz przenieść go z magazynu certyfikatów usługi Azure Automation. Jeśli nie można go wyeksportować, można go używać tylko do podpisywania w ramach konfiguracji systemu runbook lub DSC. Usługa Azure Automation wymaga, aby certyfikat miał dostawcę **microsoft enhanced RSA i dostawcy kryptograficznego AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Tworzenie nowego certyfikatu za pomocą witryny Azure Portal

1. Na koncie automatyzacji kliknij pozycję **Zasoby,** aby otworzyć stronę Zasoby.
2. Wybierz **pozycję Certyfikaty,** aby otworzyć stronę Certyfikaty.
3. Kliknij **pozycję Dodaj certyfikat** u góry strony.
4. Wpisz nazwę certyfikatu w polu **Nazwa.**
5. Aby wyszukać plik **cer** lub **pfx,** kliknij pozycję **Wybierz plik w** obszarze Przekaż plik **certyfikatu**. Jeśli wybierzesz plik **.pfx,** określ hasło i wskaż, czy można go wyeksportować.
6. Kliknij **przycisk Utwórz,** aby zapisać nowy zasób certyfikatu.

### <a name="create-a-new-certificate-with-powershell"></a>Tworzenie nowego certyfikatu za pomocą programu PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy certyfikat automatyzacji i oznaczyć go do eksportu. W tym przykładzie importuje istniejący plik **pfx.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Tworzenie nowego certyfikatu z szablonem Menedżera zasobów

W poniższym przykładzie pokazano, jak wdrożyć certyfikat na koncie automatyzacji przy użyciu szablonu Menedżera zasobów za pośrednictwem programu PowerShell:

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

Aby użyć certyfikatu, `Get-AutomationCertificate` użyj działania. Nie można użyć polecenia cmdlet [Get-AzAutomationCertificate,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) ponieważ zwraca informacje o zasobie certyfikatu, ale nie o samym certyfikacie.

### <a name="textual-runbook-example"></a>Przykład tekstowego podstawowego podstawowego podstawowego

W poniższym przykładzie pokazano, jak dodać certyfikat do usługi w chmurze w elementów runbook. W tym przykładzie hasło jest pobierane z zaszyfrowanej zmiennej automatyzacji.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Przykład podręcznika graficznego

Dodaj `Get-AutomationCertificate` działanie do graficznego podręcznika runbooka, klikając prawym przyciskiem myszy certyfikat w okienku Biblioteka i wybierając pozycję **Dodaj do kanwy**.

![Dodawanie certyfikatu do kanwy](../media/certificates/automation-certificate-add-to-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia certyfikatu w graficznym uruchomieniu bie. Jest to taka sama jak w poprzednim przykładzie, który pokazuje, jak dodać certyfikat do usługi w chmurze z podręcznika podstawowego.

![Przykładowe tworzenie graficzne](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Przykład Pythona 2

W poniższym przykładzie pokazano, jak uzyskać dostęp do certyfikatów w podręcznikach runbook języka Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o pracy z łączami w celu kontrolowania logicznego przepływu działań wykonywanych przez element runbook, zobacz [Łącza w tworzenia graficznym](../automation-graphical-authoring-intro.md#links-and-workflow). 
