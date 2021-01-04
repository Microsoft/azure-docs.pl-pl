---
title: Zarządzanie pakietami Python 3 w Azure Automation
description: W tym artykule opisano sposób zarządzania pakietami języka Python 3 (wersja zapoznawcza) w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734305"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Zarządzanie pakietami Python 3 (wersja zapoznawcza) w Azure Automation

Azure Automation umożliwia uruchamianie elementów Runbook języka Python 3 (wersja zapoznawcza) na platformie Azure oraz hybrydowych procesów roboczych elementów Runbook w systemie Linux. Aby ułatwić uproszczenie elementów Runbook, można użyć pakietów języka Python do zaimportowania potrzebnych modułów. W tym artykule opisano sposób zarządzania pakietami języka Python 3 (wersja zapoznawcza) i korzystania z nich w Azure Automation.

## <a name="import-packages"></a>Importowanie pakietów

Na koncie usługi Automation wybierz pozycję **pakiety języka Python** w obszarze **udostępnione zasoby**. Wybierz pozycję **+ Dodaj pakiet języka Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Zrzut ekranu przedstawiający stronę pakietów języka Python 3 zawiera pakiety Python 3 w menu po lewej stronie i dodano wyróżniony pakiet Python 2.":::

Na stronie **Dodaj pakiet języka Python** wybierz opcję Python 3 dla **wersji** i wybierz pakiet lokalny do przekazania. Pakiet może być plikiem **. WHL** lub **. tar. gz** . Po wybraniu pakietu wybierz **przycisk OK** , aby go przekazać.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Zrzut ekranu przedstawia stronę Dodawanie pakietu Python 3 z wybranym plikem tar. gz.":::

Po zaimportowaniu pakietu jest on wyświetlany na stronie pakiety języka Python na koncie usługi Automation, w obszarze karty **Python 3 Packages (wersja zapoznawcza)** . Jeśli musisz usunąć pakiet, wybierz pakiet, a następnie kliknij przycisk **Usuń**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Zrzut ekranu przedstawia stronę pakietów języka Python 3 po zaimportowaniu pakietu.":::

## <a name="import-packages-with-dependencies"></a>Importuj pakiety z zależnościami

Azure Automation nie rozpoznaje zależności dla pakietów języka Python podczas procesu importowania. Istnieje jednak możliwość zaimportowania pakietu ze wszystkimi jego zależnościami.

### <a name="manually-download"></a>Pobierz ręcznie

Na komputerze z systemem Windows 64-bitowym z zainstalowanym programem [Python 3,8](https://www.python.org/downloads/release/python-380/) i [PIP](https://pip.pypa.io/en/stable/) Uruchom następujące polecenie, aby pobrać pakiet i wszystkie jego zależności:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Po pobraniu pakietów można je zaimportować do konta usługi Automation.

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
