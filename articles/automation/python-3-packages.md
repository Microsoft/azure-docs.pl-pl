---
title: Zarządzanie pakietami Python 3 w Azure Automation
description: W tym artykule opisano sposób zarządzania pakietami języka Python 3 (wersja zapoznawcza) w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122038"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Zarządzanie pakietami Python 3 (wersja zapoznawcza) w Azure Automation

Azure Automation umożliwia uruchamianie elementów Runbook języka Python 3 (wersja zapoznawcza) na platformie Azure oraz hybrydowych procesów roboczych elementów Runbook w systemie Linux. Aby ułatwić uproszczenie elementów Runbook, można użyć pakietów języka Python do zaimportowania potrzebnych modułów. Aby zaimportować pojedynczy pakiet, zobacz [Importowanie pakietu](#import-a-package). Aby zaimportować pakiet z wieloma pakietami, zobacz [Importuj pakiet z zależnościami](#import-a-package-with-dependencies). W tym artykule opisano sposób zarządzania pakietami języka Python 3 (wersja zapoznawcza) i korzystania z nich w Azure Automation.

## <a name="import-a-package"></a>Importowanie pakietu

Na koncie usługi Automation wybierz pozycję **pakiety języka Python** w obszarze **udostępnione zasoby**. Wybierz pozycję **+ Dodaj pakiet języka Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Zrzut ekranu przedstawiający stronę pakietów języka Python 3 zawiera pakiety Python 3 w menu po lewej stronie i dodano wyróżniony pakiet Python 2.":::

Na stronie **Dodaj pakiet języka Python** wybierz opcję **Python 3** dla **wersji** i wybierz pakiet lokalny do przekazania. Pakiet może być plikiem **. WHL** lub **. tar. gz** . Po wybraniu pakietu wybierz **przycisk OK** , aby go przekazać.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Zrzut ekranu przedstawia stronę Dodawanie pakietu Python 3 z wybranym plikem tar. gz.":::

Po zaimportowaniu pakietu jest on wyświetlany na stronie pakiety języka Python na koncie usługi Automation, w obszarze karty **Python 3 Packages (wersja zapoznawcza)** . Jeśli musisz usunąć pakiet, wybierz pakiet, a następnie kliknij przycisk **Usuń**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Zrzut ekranu przedstawia stronę pakietów języka Python 3 po zaimportowaniu pakietu.":::

### <a name="import-a-package-with-dependencies"></a>Importowanie pakietu z zależnościami

Można zaimportować pakiet Python 3 i jego zależności, importując następujący skrypt w języku Python do elementu Runbook języka Python 3, a następnie uruchamiając go.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importowanie skryptu do elementu Runbook
Aby uzyskać informacje na temat importowania elementu Runbook, zobacz [Importowanie elementu Runbook z Azure Portal](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Skopiuj plik z usługi GitHub do magazynu, do którego ma dostęp Portal, przed uruchomieniem importu.

Strona **Importowanie elementu Runbook** domyślnie przyjmuje nazwę elementu Runbook, aby odpowiadała nazwie skryptu. Jeśli masz dostęp do pola, możesz zmienić nazwę. **Typ elementu Runbook** może domyślnie mieć wartość **Python 2**. W takim przypadku należy zmienić go na **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Zrzut ekranu przedstawia stronę importowania elementów Runbook języka Python 3.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Wykonywanie elementu Runbook w celu zaimportowania pakietu i zależności

Po utworzeniu i opublikowaniu elementu Runbook uruchom go, aby zaimportować pakiet. Zobacz [Uruchamianie elementu Runbook w Azure Automation](start-runbooks.md) , aby uzyskać szczegółowe informacje na temat wykonywania elementu Runbook.

Skrypt ( `import_py3package_from_pypi.py` ) wymaga następujących parametrów.

| Parametr | Opis |
|---------------|-----------------|
|subscription_id | Identyfikator subskrypcji konta usługi Automation |
| resource_group | Nazwa grupy zasobów, w której jest zdefiniowane konto usługi Automation |
| automation_account | Nazwa konta usługi Automation |
| module_name | Nazwa modułu do zaimportowania `pypi.org` |

Aby uzyskać więcej informacji na temat używania parametrów z elementami Runbook, zobacz [Work with Runbook Parameters](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Korzystanie z pakietu w elemencie Runbook

Po zaimportowaniu pakietu można go używać w elemencie Runbook. Dodaj następujący kod, aby wyświetlić listę wszystkich grup zasobów w subskrypcji platformy Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Następne kroki

Aby przygotować element Runbook w języku Python, zobacz [Tworzenie elementu Runbook w języku Python](learn/automation-tutorial-runbook-textual-python-3.md).
