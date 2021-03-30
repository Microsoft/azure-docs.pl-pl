---
title: Przewodnik Szybki Start platformy Azure — Konfigurowanie maszyny wirtualnej z konfiguracją żądanego stanu | Microsoft Docs
description: Ten artykuł pomaga rozpocząć konfigurowanie maszyny wirtualnej z konfiguracją żądanego stanu.
services: automation
ms.subservice: dsc
keywords: dsc, konfiguracja, automatyzacja
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e7fec2bee61844ac294e5463bd5bc88ec3fb5e98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186082"
---
# <a name="configure-a-vm-with-desired-state-configuration"></a>Konfigurowanie maszyny wirtualnej z konfiguracją żądanego stanu

Włączając konfigurację stanu Azure Automation, można zarządzać konfiguracjami serwerów z systemami Windows i Linux przy użyciu konfiguracji żądanego stanu (DSC) oraz monitorować je. Konfiguracje, które można odróżnić od żądanej konfiguracji, mogą być identyfikowane lub automatycznie poprawione. Ten przewodnik Szybki Start pozwala włączyć maszynę wirtualną z systemem Linux i wdrożyć stos lampy przy użyciu konfiguracji stanu Azure Automation.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](./manage-runas-account.md).
* Azure Resource Manager maszyny wirtualnej (nie klasycznej) z systemem Red Hat Enterprise Linux, CentOS lub Oracle Linux. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Create your first Linux virtual machine in the Azure portal](../virtual-machines/linux/quick-create-portal.md) (Tworzenie pierwszej maszyny wirtualnej systemu Linux w witrynie Azure Portal)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="enable-a-virtual-machine"></a>Włącz maszynę wirtualną

Istnieje wiele różnych metod włączania funkcji konfiguracji stanu komputera. Ten przewodnik Szybki Start zawiera informacje na temat włączania funkcji dla maszyny wirtualnej przy użyciu konta usługi Automation. Aby dowiedzieć się więcej na temat różnych metod umożliwiających konfigurację stanu maszyn, należy przeczytać temat [Włączanie maszyn do zarządzania przez Azure Automation konfigurację stanu](./automation-dsc-onboarding.md).

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Konta automatyzacji**. Jeśli nie jest widoczny w okienku po lewej stronie, kliknij pozycję **wszystkie usługi** i Wyszukaj w wyświetlonym widoku.
1. Na liście wybierz konto automatyzacji.
1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**.
2. Kliknij pozycję **Dodaj**, aby otworzyć stronę wybierania maszyny wirtualnej.
3. Znajdź maszynę wirtualną, dla której ma zostać włączona Konfiguracja DSC. Aby znaleźć określoną maszynę wirtualną, możesz użyć opcji pola wyszukiwania i filtra.
4. Kliknij maszynę wirtualną, a następnie kliknij pozycję **Połącz** .
5. Wybierz ustawienia DSC odpowiednie dla maszyny wirtualnej. Jeśli konfiguracja została już przygotowana, możesz ją określić jako `Node Configuration Name` . Możesz ustawić [tryb konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig), aby sterować zachowaniem konfiguracji maszyny.
6. Kliknij przycisk **OK**. Gdy rozszerzenie DSC jest wdrożone na maszynie wirtualnej, stan jest wyświetlany jako `Connecting` .

![Włączanie maszyny wirtualnej platformy Azure dla DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importowanie modułów

Moduły zawierają zasoby DSC i wiele z nich można znaleźć w [Galeria programu PowerShell](https://www.powershellgallery.com). Wszystkie zasoby, które są używane w konfiguracjach, muszą zostać zaimportowane do konta usługi Automation przed kompilacją. W tym samouczku jest wymagany moduł o nazwie **nx**.

1. W lewym okienku konta usługi Automation wybierz pozycję **Galeria modułów** w obszarze **zasoby udostępnione**.
1. Wyszukaj moduł do zaimportowania, wpisując część jego nazwy: `nx` .
1. Kliknij moduł do zaimportowania.
1. Kliknij pozycję **Importuj**.

![Importowanie modułu DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importowanie konfiguracji

Ta opcja szybkiego startu używa konfiguracji DSC, która konfiguruje programy Apache HTTP Server, MySQL i PHP na maszynie. Zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

W edytorze tekstów wpisz następujące polecenie i Zapisz je lokalnie jako **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Aby zaimportować konfigurację:

1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**, a następnie kliknij kartę **Konfiguracje**.
2. Kliknij pozycję **+ Dodaj**.
3. Wybierz plik konfiguracji, który został zapisany w poprzednim kroku.
4. Kliknij przycisk **OK**.

## <a name="compile-a-configuration"></a>Kompilacja konfiguracji

Aby można było przypisać do węzła, należy skompilować konfigurację DSC do konfiguracji węzła (dokument MOF). Kompilacja weryfikuje konfigurację i pozwala na wprowadzanie wartości parametrów. Aby dowiedzieć się więcej na temat kompilowania konfiguracji, zobacz [Kompilowanie konfiguracji w konfiguracji stanu](automation-dsc-compile.md).

1. W lewym okienku konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)** , a następnie kliknij kartę **konfiguracje** .
1. Wybierz konfigurację `LAMPServer` .
1. Z opcji menu wybierz opcję **Kompiluj** , a następnie kliknij przycisk **tak**.
1. W widoku konfiguracji zobaczysz nowe zadanie kompilacji w kolejce. Po pomyślnym zakończeniu zadania możesz przejść do następnego kroku. W przypadku wystąpienia błędów można kliknąć zadanie kompilacji, aby uzyskać szczegółowe informacje.

## <a name="assign-a-node-configuration"></a>Przypisywanie konfiguracji węzła

Można przypisać konfigurację skompilowanego węzła do węzła DSC. Przypisanie stosuje konfigurację do komputera i monitoruje lub koryguje AutoCorrect dla dowolnego dryfu z tej konfiguracji.

1. W lewym okienku konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)** , a następnie kliknij kartę **węzły** .
1. Wybierz węzeł, do którego chcesz przypisać konfigurację.
1. Kliknij pozycję **Przypisywanie konfiguracji węzła**
1. Wybierz konfigurację węzła `LAMPServer.localhost` , a następnie kliknij przycisk **OK**. Konfiguracja stanu przypisuje teraz skompilowaną konfigurację do węzła, a stan węzła zmieni się na `Pending` . Przy następnej kontroli okresowej węzeł Pobiera konfigurację, stosuje ją i raportuje stan. Pobranie konfiguracji przez węzeł może potrwać do 30 minut, w zależności od ustawień węzła. 
1. Aby wymusić natychmiastowe sprawdzenie, możesz lokalnie wykonać następujące polecenie na maszynie wirtualnej systemu Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Przypisywanie konfiguracji węzła](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Wyświetl stan węzła

Stan wszystkich węzłów zarządzanych przez konfigurację stanu można wyświetlić na koncie usługi Automation. Informacje są wyświetlane po wybraniu opcji **Konfiguracja stanu (DSC)** i kliknięciu karty **węzły** . Można filtrować wyświetlanie według stanu, konfiguracji węzła lub wyszukiwania nazw.

![Stan węzła DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start włączono maszynę wirtualną z systemem Linux na potrzeby konfiguracji stanu, utworzono konfigurację dla stosu LAMP i wdrożono konfigurację na maszynie wirtualnej. Aby dowiedzieć się, jak można użyć konfiguracji stanu Azure Automation do włączenia ciągłego wdrażania, przejdź do artykułu:

> [!div class="nextstepaction"]
> [Konfigurowanie ciągłego wdrażania za pomocą narzędzia Chocolatey](./automation-dsc-cd-chocolatey.md)
