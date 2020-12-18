---
title: Zarządzanie pakietami Python 2 w Azure Automation
description: W tym artykule opisano, jak zarządzać pakietami Python 2 w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/17/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: fd830afd5628591019902ca583f9cbc8e2a7ecad
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683388"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Zarządzanie pakietami Python 2 w Azure Automation

Azure Automation umożliwia uruchamianie elementów Runbook języka Python 2 na platformie Azure i w systemie Linux hybrydowych procesów roboczych elementów Runbook. Aby ułatwić uproszczenie elementów Runbook, można użyć pakietów języka Python do zaimportowania potrzebnych modułów. W tym artykule opisano sposób zarządzania pakietami języka Python i używania ich w programie Azure Automation.

## <a name="import-packages"></a>Importowanie pakietów

Na koncie usługi Automation wybierz **pakiety Python 2** w obszarze **udostępnione zasoby**. Kliknij pozycję **+ Dodaj pakiet Python 2**.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Zrzut ekranu przedstawiający stronę pakietów języka Python 2 zawiera pakiety Python 2 w menu po lewej stronie i dodano wyróżniony pakiet Python 2.":::

Na stronie Dodawanie pakietu Python 2 Wybierz pakiet lokalny do przekazania. Pakiet może być plikiem **. WHL** lub **. tar. gz** . Po wybraniu pakietu kliknij przycisk **OK** , aby go załadować.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Zrzut ekranu przedstawia stronę Dodawanie pakietu Python 2 z wybranym plikem tar. gz.":::

Po zaimportowaniu pakietu jest on wyświetlany na stronie pakiety języka Python 2 na koncie usługi Automation. Jeśli musisz usunąć pakiet, wybierz pakiet, a następnie kliknij przycisk **Usuń**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Zrzut ekranu przedstawia stronę pakietów języka Python 2 po zaimportowaniu pakietu.":::

## <a name="import-packages-with-dependencies"></a>Importuj pakiety z zależnościami

Usługa Azure Automation nie rozpoznaje zależności dla pakietów języka Python podczas procesu importowania. Istnieją dwa sposoby importowania pakietu ze wszystkimi jego zależnościami. Aby zaimportować pakiety do konta usługi Automation, należy użyć tylko jednego z następujących kroków.

### <a name="manually-download"></a>Pobierz ręcznie

Na komputerze z systemem Windows 64-bitowym, na którym zainstalowano środowisko [Python 2.7](https://www.python.org/downloads/release/latest/python2) i [PIP](https://pip.pypa.io/en/stable/) , uruchom następujące polecenie, aby pobrać pakiet i wszystkie jego zależności:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Po pobraniu pakietów można je zaimportować do konta usługi Automation.

### <a name="runbook"></a>Element Runbook

 Aby uzyskać element Runbook, [zaimportuj pakiety Python 2 z usługi PyPi do konta Azure Automation](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account) z Azure Automation organizacji GitHub do konta usługi Automation. Upewnij się, że ustawienia uruchomieniowe są ustawione na **platformę Azure** , i uruchom element Runbook za pomocą parametrów. Aby konto usługi Automation działało, element Runbook wymaga konta Uruchom jako. Dla każdego parametru upewnij się, że został on uruchomiony z przełącznikiem, jak pokazano na poniższej liście i obrazie:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Zrzut ekranu przedstawia stronę przegląd dla import_py2package_from_pypi przy użyciu okienka Uruchamianie elementu Runbook po prawej stronie.":::

Element Runbook umożliwia określenie pakietu do pobrania. Na przykład, użycie `Azure` parametru pobiera wszystkie moduły platformy Azure i wszystkie zależności (około 105).

Po zakończeniu elementu Runbook można sprawdzić **pakiety języka Python 2** w obszarze **udostępnione zasoby** na koncie usługi Automation, aby sprawdzić, czy pakiet został zaimportowany prawidłowo.

## <a name="use-a-package-in-a-runbook"></a>Korzystanie z pakietu w elemencie Runbook

Po zaimportowaniu pakietu można go używać w elemencie Runbook. W poniższym przykładzie zastosowano [pakiet narzędzi Azure Automation](https://github.com/azureautomation/azure_automation_utility). Ten pakiet ułatwia używanie języka Python z Azure Automation. Aby użyć pakietu, postępuj zgodnie z instrukcjami w repozytorium GitHub i Dodaj je do elementu Runbook. Można na przykład użyć, `from azure_automation_utility import get_automation_runas_credential` Aby zaimportować funkcję do pobrania konta Uruchom jako.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Opracowywanie i testowanie elementów Runbook w trybie offline

Aby tworzyć i testować elementy Runbook języka Python 2 w trybie offline, można [Azure Automation użyć modułu emulowane zasoby](https://github.com/azureautomation/python_emulated_assets) w języku Python w witrynie GitHub. Ten moduł umożliwia odwoływanie się do udostępnionych zasobów, takich jak poświadczenia, zmienne, połączenia i certyfikaty.

## <a name="next-steps"></a>Następne kroki

Aby przygotować element Runbook w języku Python, zobacz [Tworzenie elementu Runbook w języku Python](learn/automation-tutorial-runbook-textual-python2.md).
