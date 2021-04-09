---
title: Wprowadzenie do konfiguracji stanu Azure Automation
description: W tym artykule opisano sposób wykonywania najczęstszych zadań w konfiguracji stanu Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051502"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Wprowadzenie do konfiguracji stanu Azure Automation

Ten artykuł zawiera przewodnik krok po kroku dotyczący wykonywania najczęstszych zadań z konfiguracją stanu Azure Automation, takich jak tworzenie, importowanie i kompilowanie konfiguracji, Włączanie maszyn w celu zarządzania i wyświetlania raportów. Aby zapoznać się z konfiguracją stanu przeglądu, zobacz [Omówienie konfiguracji stanu](automation-dsc-overview.md). Aby zapoznać się z dokumentacją konfiguracji żądanego stanu (DSC), zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Jeśli chcesz, aby przykładowe środowisko zostało już skonfigurowane, nie wykonując kroków opisanych w tym artykule, możesz użyć [szablonu węzła zarządzanego Azure Automation](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ten szablon służy do konfigurowania środowiska konfiguracji kompletnego stanu (DSC), w tym maszyny wirtualnej platformy Azure, która jest zarządzana przez konfigurację stanu (DSC).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać przykłady opisane w tym artykule, wymagane są następujące elementy:

- Konto usługi Azure Automation. Aby dowiedzieć się więcej na temat konta usługi Automation i jego wymagań, zobacz temat [uwierzytelnianie konta usługi Automation — Omówienie](./automation-security-overview.md).
- Maszyna wirtualna Azure Resource Manager (nieklasyczna) z [obsługiwanym systemem operacyjnym](automation-dsc-overview.md#operating-system-requirements). Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>Tworzenie konfiguracji DSC

Można utworzyć prostą [konfigurację DSC](/powershell/scripting/dsc/configurations/configurations) , która zapewnia obecność lub brak funkcji systemu Windows **serwera sieci Web** (IIS), w zależności od sposobu przypisywania węzłów.

1. Uruchom [programu vscode](https://code.visualstudio.com/docs) (lub dowolny edytor tekstu).
1. Wpisz następujący tekst:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Zapisz plik jako **TestConfig.ps1**.

Ta konfiguracja wywołuje jeden zasób w każdym bloku węzła, [zasobu WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Ten zasób zapewnia obecność lub brak funkcji **serwera sieci Web** .

## <a name="import-a-configuration-into-azure-automation"></a>Zaimportuj konfigurację do Azure Automation

Następnie należy zaimportować konfigurację do konta usługi Automation.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **konfiguracje** , a następnie kliknij przycisk **Dodaj**.
1. W okienku Konfiguracja importu przejdź do `TestConfig.ps1` pliku na komputerze.

   ![Zrzut ekranu przedstawiający blok programu * * konfiguracja importowania * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Kliknij przycisk **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Wyświetl konfigurację w Azure Automation

Po zaimportowaniu konfiguracji można wyświetlić ją w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation wybierz pozycję  **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **konfiguracje** , a następnie kliknij pozycję **TestConfig**. Jest to nazwa konfiguracji zaimportowanej w poprzedniej procedurze.
1. W okienku Konfiguracja TestConfig kliknij pozycję **Wyświetl źródło konfiguracji**.

   ![Zrzut ekranu przedstawiający blok konfiguracji TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Zostanie otwarte okienko Źródło konfiguracji TestConfig, w którym jest wyświetlany kod programu PowerShell dla konfiguracji.

## <a name="compile-a-configuration-in-azure-automation"></a>Kompiluj konfigurację w Azure Automation

Aby można było zastosować żądany stan do węzła, Konfiguracja DSC definiująca ten stan musi być skompilowana do co najmniej jednej konfiguracji węzła (dokument MOF) i umieszczona na serwerze Automation DSC ściągania. Aby uzyskać bardziej szczegółowy opis kompilowania konfiguracji w konfiguracji stanu (DSC), zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
Aby uzyskać więcej informacji na temat kompilowania konfiguracji, zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **konfiguracje** , a następnie kliknij pozycję **TestConfig**. Jest to nazwa wcześniej zaimportowanej konfiguracji.
1. W okienku Konfiguracja TestConfig kliknij pozycję **Kompiluj**, a następnie kliknij przycisk **tak**. Spowoduje to uruchomienie zadania kompilacji.

   ![Zrzut ekranu przedstawiający przycisk Kompiluj wyróżnianie strony konfiguracji TestConfig](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Podczas kompilowania konfiguracji w Azure Automation automatycznie wdraża wszystkie utworzone pliki MOF konfiguracji węzła na serwerze ściągania.

## <a name="view-a-compilation-job"></a>Wyświetlanie zadania kompilacji

Po rozpoczęciu kompilacji można wyświetlić ją na kafelku **zadania kompilacji** na stronie **Konfiguracja** . Na kafelku **zadania kompilacji** są wyświetlane zadania uruchomione, ukończone i zakończone niepowodzeniem. Po otwarciu okienka zadania kompilacji zostaną wyświetlone informacje o tym zadaniu, w tym o napotkanych błędach lub ostrzeżeniach, parametrach wejściowych używanych w konfiguracji i dziennikach kompilacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **konfiguracje** , a następnie kliknij pozycję **TestConfig**. Jest to nazwa wcześniej zaimportowanej konfiguracji.
1. W obszarze **zadania kompilacji** wybierz zadanie kompilacji do wyświetlenia. Zostanie otwarte okienko zadania kompilacji z etykietą z datą rozpoczęcia zadania kompilacji.

   ![Zrzut ekranu strony zadania kompilacji](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknij dowolny kafelek w okienku zadania kompilacji, aby zobaczyć dalsze szczegółowe informacje o zadaniu.

## <a name="view-node-configurations"></a>Wyświetl konfiguracje węzłów

Pomyślne zakończenie zadania kompilacji powoduje utworzenie co najmniej jednej nowej konfiguracji węzła. Konfiguracja węzła to dokument MOF, który jest wdrażany na serwerze ściągania i gotowy do ściągnięcia i zastosowania przez co najmniej jeden węzeł. Konfiguracje węzłów można wyświetlić na koncie usługi Automation na stronie Konfiguracja stanu (DSC). Konfiguracja węzła ma nazwę z formularzem `ConfigurationName.NodeName` .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **skompilowane konfiguracje** .

   ![Zrzut ekranu przedstawiający kartę skompilowane konfiguracje](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Włącz maszynę wirtualną Azure Resource Manager na potrzeby zarządzania z konfiguracją stanu

Za pomocą konfiguracji stanu można zarządzać maszynami wirtualnymi platformy Azure (klasycznymi i Menedżer zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, AWS maszynami wirtualnymi i lokalnymi maszynami fizycznymi. W tym artykule dowiesz się, jak włączyć tylko Azure Resource Manager maszyny wirtualne. Informacje na temat włączania innych typów maszyn można znaleźć w temacie [enable Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) wybierz kartę **węzły** , a następnie kliknij pozycję **+ Dodaj**.

   ![Zrzut ekranu przedstawiający stronę węzły DSC z wyróżnionym przyciskiem Dodaj maszynę wirtualną platformy Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. W okienku Virtual Machines wybierz maszynę wirtualną.
1. W okienku szczegółów maszyny wirtualnej kliknij pozycję **+ Połącz**.

   > [!IMPORTANT]
   > Maszyna wirtualna musi być maszyną wirtualną Azure Resource Manager, na której działa [obsługiwany system operacyjny](automation-dsc-overview.md#operating-system-requirements).

2. Na stronie Rejestracja wybierz nazwę konfiguracji węzła, która ma zostać zastosowana do maszyny wirtualnej w polu **nazwa konfiguracji węzła** . Podanie nazwy w tym momencie jest opcjonalne. Po włączeniu węzła można zmienić konfigurację przypisanego węzła.

3. W **razie konieczności Sprawdź węzeł ponownego uruchomienia**, a następnie kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający blok rejestracji](./media/automation-dsc-getting-started/RegisterVM.png)

   Określona konfiguracja węzła zostanie zastosowana do maszyny wirtualnej w odstępach czasu określonych przez wartość podaną dla **częstotliwości trybu konfiguracji**. Maszyna wirtualna sprawdza dostępność aktualizacji konfiguracji węzła w odstępach czasu określonych przez wartość **częstotliwości odświeżania** . Aby uzyskać więcej informacji na temat sposobu używania tych wartości, zobacz [Konfigurowanie lokalnego Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Platforma Azure rozpocznie proces włączania maszyny wirtualnej. Po zakończeniu ta maszyna wirtualna będzie wyświetlana na karcie **węzły** na stronie Konfiguracja stanu (DSC) na koncie usługi Automation.

## <a name="view-the-list-of-managed-nodes"></a>Wyświetlanie listy węzłów zarządzanych

Listę wszystkich maszyn, na których włączono zarządzanie na koncie usługi Automation, można wyświetlić na karcie **węzły** na stronie Konfiguracja stanu (DSC).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **węzły** .

## <a name="view-reports-for-managed-nodes"></a>Wyświetlanie raportów dotyczących węzłów zarządzanych

Za każdym razem, gdy konfiguracja stanu wykonuje kontrolę spójności w zarządzanym węźle, węzeł wysyła raport o stanie z powrotem do serwera ściągania. Te raporty można wyświetlić na stronie dla tego węzła.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **węzły** . W tym miejscu można zobaczyć przegląd stanu konfiguracji i szczegółów dla każdego węzła.

   ![Zrzut ekranu strony węzła](./media/automation-dsc-getting-started/NodesTab.png)

1. Na karcie **węzły** kliknij rekord węzła, aby otworzyć raport. Kliknij raport, który chcesz wyświetlić dodatkowe szczegóły raportowania.

   ![Zrzut ekranu przedstawiający blok raportu](./media/automation-dsc-getting-started/NodeReport.png)

W bloku dla pojedynczego raportu można zobaczyć następujące informacje o stanie dla odpowiedniego sprawdzenia spójności:

- Stan raportu. Możliwe wartości:
    * Zgodne — węzeł jest zgodny z testem.
   * Niepowodzenie — sprawdzenie konfiguracji nie powiodło się.
   * Niezgodne — węzeł jest w `ApplyandMonitor` trybie, a maszyna nie jest w żądanym stanie.
- Godzina rozpoczęcia kontroli spójności.
- Łączny czas wykonywania kontroli spójności.
- Typ kontroli spójności.
- Wszelkie błędy, w tym kod błędu i komunikat o błędzie.
- Wszystkie zasoby DSC używane w konfiguracji oraz stan poszczególnych zasobów (czy węzeł jest w żądanym stanie dla tego zasobu). Możesz kliknąć każdy zasób, aby uzyskać bardziej szczegółowe informacje dotyczące tego zasobu.
- Nazwa, adres IP i tryb konfiguracji węzła.

Możesz również kliknąć pozycję **Wyświetl raport nieprzetworzony** , aby wyświetlić rzeczywiste dane wysyłane przez węzeł do serwera programu.
Aby uzyskać więcej informacji na temat korzystania z tych danych, zobacz [Korzystanie z serwera raportów DSC](/powershell/scripting/dsc/pull-server/reportserver).

Po włączeniu pierwszego raportu może upłynąć trochę czasu. Po włączeniu węzła może być konieczne odczekanie do 30 minut na pierwszy raport.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Przypisz ponownie węzeł do innej konfiguracji węzła

Węzeł można przypisać do innej konfiguracji węzła niż ta, która została początkowo przypisana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **węzły** .
1. Na karcie **węzły** kliknij nazwę węzła, który chcesz ponownie przypisać.
1. Na stronie tego węzła kliknij pozycję **Przypisz konfigurację węzła**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła z wyróżnionym przyciskiem Przypisz konfigurację węzła](./media/automation-dsc-getting-started/AssignNode.png)

1. Na stronie Konfigurowanie konfiguracji węzła wybierz konfigurację węzła, do której chcesz przypisać węzeł, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu strony Konfiguracja przypisywania węzła](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Wyrejestrowywanie węzła

Jeśli nie chcesz już zarządzać węzłem za pomocą konfiguracji stanu, możesz go wyrejestrować.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **węzły** .
1. Na karcie **węzły** kliknij nazwę węzła, który chcesz wyrejestrować.
1. W okienku dla tego węzła kliknij pozycję **Wyrejestruj**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła z wyróżnionym przyciskiem wyrejestrowywania](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [Omówienie konfiguracji stanu Azure Automation](automation-dsc-overview.md).
- Aby włączyć funkcję dla maszyn wirtualnych w środowisku, zobacz [Włączanie konfiguracji stanu Azure Automation](automation-dsc-onboarding.md).
- Aby poznać program PowerShell DSC, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
