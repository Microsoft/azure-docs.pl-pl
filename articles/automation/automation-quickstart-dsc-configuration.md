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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457522"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Konfigurowanie maszyny wirtualnej przy obliczu konfiguracji żądanego stanu

Po włączeniu konfiguracji stanu automatyzacji platformy Azure można zarządzać konfiguracjami serwerów Windows i Linux i monitorować je przy użyciu konfiguracji żądanego stanu (DSC). Konfiguracje, które dryfują od żądanej konfiguracji, można zidentyfikować lub automatycznie poprawić. Ta procedura szybkiego startu pokazuje kroki dołączania maszyny wirtualnej systemu Linux i wdrażania stosu LAMP za pomocą DSC.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
* Maszyna wirtualna usługi Azure Resource Manager (nie klasycznej) z systemem Red Hat Enterprise Linux, CentOS lub Oracle Linux. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Create your first Linux virtual machine in the Azure portal](../virtual-machines/linux/quick-create-portal.md) (Tworzenie pierwszej maszyny wirtualnej systemu Linux w witrynie Azure Portal)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się na https://portal.azure.complatformie Azure na poziomie .

## <a name="onboard-a-virtual-machine"></a>Dołączanie maszyny wirtualnej

Istnieje wiele różnych metod na pokładzie komputera i włączyć DSC. Ten Szybki start obejmuje dołączanie za pomocą konta automatyzacji. Możesz dowiedzieć się więcej o różnych metodach dołączania komputerów do konfiguracji stanu, czytając artykuł [dołączania.](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Konta automatyzacji**. Jeśli nie jest widoczny w lewym okienku, kliknij **pozycję Wszystkie usługi** i wyszukaj w widoku wynikowym.
1. Na liście wybierz konto automatyzacji.
1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**.
2. Kliknij pozycję **Dodaj**, aby otworzyć stronę wybierania maszyny wirtualnej.
3. Znajdź maszynę wirtualną, dla której ma być włączana dsc. Aby znaleźć określoną maszynę wirtualną, możesz użyć opcji pola wyszukiwania i filtra.
4. Kliknij maszynę wirtualną, a następnie kliknij przycisk **Połącz**
5. Wybierz ustawienia DSC odpowiednie dla maszyny wirtualnej. Jeśli konfiguracja została już przygotowana, `Node Configuration Name`można ją określić jako . Możesz ustawić [tryb konfiguracji](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig), aby sterować zachowaniem konfiguracji maszyny.
6. Kliknij przycisk **OK**. Gdy rozszerzenie DSC jest wdrażane na maszynie wirtualnej, stan jest wyświetlany jako `Connecting`.

![Dołączanie maszyny wirtualnej platformy Azure do konfiguracji DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importowanie modułów

Moduły zawierają zasoby DSC, a wiele z nich można znaleźć w [Galerii programu PowerShell](https://www.powershellgallery.com). Wszelkie zasoby, które są używane w konfiguracjach muszą być importowane do konta automatyzacji przed kompilacją. W tym samouczku jest wymagany moduł o nazwie **nx**.

1. W lewym okienku konta automatyzacji wybierz pozycję **Galeria modułów** w obszarze **Zasoby udostępnione**.
1. Wyszukaj moduł do zaimportowania, `nx`wpisując część jego nazwy: .
1. Kliknij moduł, aby zaimportować.
1. Kliknij pozycję **Importuj**.

![Importowanie modułu DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importowanie konfiguracji

Ta opcja szybkiego startu używa konfiguracji DSC, która konfiguruje programy Apache HTTP Server, MySQL i PHP na maszynie. Zobacz [konfiguracje DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

W edytorze tekstu wpisz następujące polecenie i zapisz go lokalnie jako **AMPServer.ps1**.

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
3. Wybierz plik konfiguracyjny zapisany w poprzednim kroku.
4. Kliknij przycisk **OK**.

## <a name="compile-a-configuration"></a>Kompilacja konfiguracji

Należy skompilować konfigurację DSC do konfiguracji węzła (dokumentu MOF), zanim będzie można przypisać ją do węzła. Kompilacja weryfikuje konfigurację i pozwala na wprowadzanie wartości parametrów. Aby dowiedzieć się więcej na temat kompilowania konfiguracji, zobacz [Kompilowanie konfiguracji w konfiguracji stanu](automation-dsc-compile.md).

1. W lewym okienku konta automatyzacji wybierz pozycję **Konfiguracja stanu (DSC),** a następnie kliknij kartę **Konfiguracje.**
1. Wybierz konfigurację `LAMPServer`.
1. W menu wybierz polecenie **Skompiluj,** a następnie kliknij przycisk **Tak**.
1. W widoku Konfiguracja jest widoczna nowa praca kompilacji w kolejce. Po pomyślnym zakończeniu zadania możesz przejść do następnego kroku. Jeśli występują błędy, można kliknąć na zadanie kompilacji, aby uzyskać szczegółowe informacje.

## <a name="assign-a-node-configuration"></a>Przypisywanie konfiguracji węzła

Do węzła DSC można przypisać skompilowaną konfigurację węzła. Przypisanie stosuje konfigurację do komputera i monitoruje lub automatycznie koryguje wszelkie dryfowanie z tej konfiguracji.

1. W lewym okienku konta automatyzacji wybierz pozycję **Konfiguracja stanu (DSC),** a następnie kliknij kartę **Węzły.**
1. Wybierz węzeł, do którego ma zostać przypisana konfiguracja.
1. Kliknij pozycję **Przypisywanie konfiguracji węzła**
1. Wybierz konfigurację `LAMPServer.localhost` węzła i kliknij przycisk **OK**. Konfiguracja stanu przypisuje teraz skompilowaną konfigurację do `Pending`węzła, a stan węzła zmieni się na . Przy następnym sprawdzaniu okresowym węzeł pobiera konfigurację, stosuje ją i raportuje stan. Może upłynąć do 30 minut dla węzła, aby pobrać konfigurację, w zależności od ustawień węzła. 
1. Aby wymusić natychmiastowe sprawdzenie, możesz lokalnie wykonać następujące polecenie na maszynie wirtualnej systemu Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Przypisywanie konfiguracji węzła](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Wyświetl stan węzła

Stan wszystkich węzłów zarządzanych przez konfigurację stanu można wyświetlić na koncie automatyzacji. Informacje są wyświetlane przez wybranie opcji **Konfiguracja stanu (DSC)** i kliknięcie karty **Węzły.** Można filtrować wyświetlanie według stanu, konfiguracji węzła lub wyszukiwania nazw.

![Stan węzła DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dołączyłeś do konfiguracji maszyny Wirtualnej systemu Linux do stanu, utworzono konfigurację stosu LAMP i wdrożono konfigurację na maszynie wirtualnej. Aby dowiedzieć się, jak można użyć konfiguracji stanu automatyzacji platformy Azure, aby włączyć ciągłe wdrażanie, przejdź do tego artykułu:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md) (Ciągłe wdrażanie na maszynie wirtualnej za pomocą DSC i Chocolatey)

* Aby dowiedzieć się więcej o programie PowerShell DSC, zobacz [Omówienie konfiguracji żądanego stanu programu PowerShell](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* Aby dowiedzieć się więcej o zarządzaniu konfiguracją stanu z programu PowerShell, zobacz [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/).
* Aby dowiedzieć się, jak przesyłać dalej raporty DSC do dzienników usługi Azure Monitor w celu raportowania i alertów, zobacz [Przekazywanie raportów DSC do dzienników usługi Azure Monitor](automation-dsc-diagnostics.md).