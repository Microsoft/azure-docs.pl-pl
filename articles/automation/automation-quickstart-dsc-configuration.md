---
title: Szybki start platformy Azure — konfigurowanie maszyny wirtualnej za pomocą DSC | Microsoft Docs
description: Konfigurowanie stosu LAMP w maszynie wirtualnej systemu Linux za pomocą konfiguracji żądanego stanu (DSC)
services: automation
ms.subservice: dsc
keywords: dsc, konfiguracja, automatyzacja
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 1a146ab7c05d200b71a33a72fa6362c3cf62629a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457522"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Skonfiguruj maszynę wirtualną z konfiguracją żądanego stanu

Włączając konfigurację stanu Azure Automation, można zarządzać konfiguracjami serwerów z systemami Windows i Linux przy użyciu konfiguracji żądanego stanu (DSC) oraz monitorować je. Konfiguracje, które można odróżnić od żądanej konfiguracji, mogą być identyfikowane lub automatycznie poprawione. Ta procedura szybkiego startu pokazuje kroki dołączania maszyny wirtualnej systemu Linux i wdrażania stosu LAMP za pomocą DSC.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
* Maszyna wirtualna usługi Azure Resource Manager (nie klasycznej) z systemem Red Hat Enterprise Linux, CentOS lub Oracle Linux. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Create your first Linux virtual machine in the Azure portal](../virtual-machines/linux/quick-create-portal.md) (Tworzenie pierwszej maszyny wirtualnej systemu Linux w witrynie Azure Portal)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do platformy Azure https://portal.azure.compod adresem.

## <a name="onboard-a-virtual-machine"></a>Dołączanie maszyny wirtualnej

Istnieje wiele różnych metod dołączania maszyn i włączania DSC. Ten Szybki start obejmuje dołączanie za pomocą konta automatyzacji. Aby dowiedzieć się więcej o różnych metodach dołączania maszyn do konfiguracji stanu, Przeczytaj [artykuł](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) dotyczący dołączenia.

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Konta automatyzacji**. Jeśli nie jest widoczny w okienku po lewej stronie, kliknij pozycję **wszystkie usługi** i Wyszukaj w wyświetlonym widoku.
1. Na liście wybierz konto automatyzacji.
1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**.
2. Kliknij pozycję **Dodaj**, aby otworzyć stronę wybierania maszyny wirtualnej.
3. Znajdź maszynę wirtualną, dla której ma zostać włączona Konfiguracja DSC. Aby znaleźć określoną maszynę wirtualną, możesz użyć opcji pola wyszukiwania i filtra.
4. Kliknij maszynę wirtualną, a następnie kliknij pozycję **Połącz** .
5. Wybierz ustawienia DSC odpowiednie dla maszyny wirtualnej. Jeśli konfiguracja została już przygotowana, możesz ją określić jako `Node Configuration Name`. Możesz ustawić [tryb konfiguracji](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig), aby sterować zachowaniem konfiguracji maszyny.
6. Kliknij przycisk **OK**. Gdy rozszerzenie DSC jest wdrożone na maszynie wirtualnej, stan jest wyświetlany jako `Connecting`.

![Dołączanie maszyny wirtualnej platformy Azure do konfiguracji DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importowanie modułów

Moduły zawierają zasoby DSC i wiele z nich można znaleźć w [Galeria programu PowerShell](https://www.powershellgallery.com). Wszystkie zasoby, które są używane w konfiguracjach, muszą zostać zaimportowane do konta usługi Automation przed kompilacją. W tym samouczku jest wymagany moduł o nazwie **nx**.

1. W lewym okienku konta usługi Automation wybierz pozycję **Galeria modułów** w obszarze **zasoby udostępnione**.
1. Wyszukaj moduł do zaimportowania, wpisując część jego nazwy: `nx`.
1. Kliknij moduł do zaimportowania.
1. Kliknij pozycję **Importuj**.

![Importowanie modułu DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importowanie konfiguracji

Ta opcja szybkiego startu używa konfiguracji DSC, która konfiguruje programy Apache HTTP Server, MySQL i PHP na maszynie. Zobacz [konfiguracje DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

W edytorze tekstów wpisz następujące polecenie i Zapisz je lokalnie jako **AMPServer. ps1**.

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
1. Wybierz konfigurację `LAMPServer`.
1. Z opcji menu wybierz opcję **Kompiluj** , a następnie kliknij przycisk **tak**.
1. W widoku konfiguracji zobaczysz nowe zadanie kompilacji w kolejce. Po pomyślnym zakończeniu zadania możesz przejść do następnego kroku. W przypadku wystąpienia błędów można kliknąć zadanie kompilacji, aby uzyskać szczegółowe informacje.

## <a name="assign-a-node-configuration"></a>Przypisywanie konfiguracji węzła

Można przypisać konfigurację skompilowanego węzła do węzła DSC. Przypisanie stosuje konfigurację do komputera i monitoruje lub koryguje AutoCorrect dla dowolnego dryfu z tej konfiguracji.

1. W lewym okienku konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)** , a następnie kliknij kartę **węzły** .
1. Wybierz węzeł, do którego chcesz przypisać konfigurację.
1. Kliknij pozycję **Przypisywanie konfiguracji węzła**
1. Wybierz konfigurację `LAMPServer.localhost` węzła, a następnie kliknij przycisk **OK**. Konfiguracja stanu przypisuje teraz skompilowaną konfigurację do węzła, a stan węzła zmieni się na `Pending`. Przy następnej kontroli okresowej węzeł Pobiera konfigurację, stosuje ją i raportuje stan. Pobranie konfiguracji przez węzeł może potrwać do 30 minut, w zależności od ustawień węzła. 
1. Aby wymusić natychmiastowe sprawdzenie, możesz lokalnie wykonać następujące polecenie na maszynie wirtualnej systemu Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Przypisywanie konfiguracji węzła](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Wyświetl stan węzła

Stan wszystkich węzłów zarządzanych przez konfigurację stanu można wyświetlić na koncie usługi Automation. Informacje są wyświetlane po wybraniu opcji **Konfiguracja stanu (DSC)** i kliknięciu karty **węzły** . Można filtrować wyświetlanie według stanu, konfiguracji węzła lub wyszukiwania nazw.

![Stan węzła DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dołączono maszynę wirtualną z systemem Linux do konfiguracji stanu, utworzono konfigurację dla stosu LAMP i wdrożono konfigurację na maszynie wirtualnej. Aby dowiedzieć się, jak można użyć konfiguracji stanu Azure Automation do włączenia ciągłego wdrażania, przejdź do artykułu:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md) (Ciągłe wdrażanie na maszynie wirtualnej za pomocą DSC i Chocolatey)

* Aby dowiedzieć się więcej na temat programu PowerShell DSC, zobacz [Omówienie konfiguracji żądanego stanu programu PowerShell](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* Aby dowiedzieć się więcej o zarządzaniu konfiguracją stanu przy użyciu programu PowerShell, zobacz [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/).
* Aby dowiedzieć się, jak przekazywać raporty DSC do Azure Monitor dzienników raportowania i alertów, zobacz [przekazywanie raportów DSC do dzienników Azure monitor](automation-dsc-diagnostics.md).