---
title: Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure
description: Wyjaśnienie i przykłady najczęstszych zadań w konfiguracji stanu automatyzacji usługi Azure (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 43268d5b48b44fffefa222f566c40151c85a5895
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392171"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure

W tym artykule wyjaśniono, jak wykonać najbardziej typowe zadania z konfiguracją stanu automatyzacji platformy Azure, takie jak tworzenie, importowanie i kompilowanie konfiguracji, dołączanie maszyn do zarządzania i wyświetlania raportów. Aby zapoznać się z omówieniem konfiguracji stanu automatyzacji platformy Azure, zobacz [Omówienie konfiguracji stanu automatyzacji platformy Azure.](automation-dsc-overview.md) Aby zapoznać się z dokumentacją konfiguracji żądanego stanu (DSC) zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Ten artykuł zawiera przewodnik krok po kroku dotyczący korzystania z konfiguracji stanu automatyzacji platformy Azure. Jeśli chcesz, aby przykładowe środowisko zostało już skonfigurowane bez wykonać czynności opisane w tym artykule, możesz użyć następującego szablonu Menedżera zasobów: [szablon węzła zarządzanego usługi Azure Automation](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ten szablon konfiguruje ukończone środowisko konfiguracji stanu automatyzacji platformy Azure, w tym maszynę wirtualną platformy Azure zarządzaną przez konfigurację stanu automatyzacji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzupełnić przykłady w tym artykule, wymagane są następujące czynności:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna usługi Azure Resource Manager (nie klasyczna) z [obsługiwanym systemem operacyjnym](automation-dsc-overview.md#operating-system-requirements). Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Tworzenie konfiguracji DSC

Tworzysz prostą [konfigurację DSC,](/powershell/scripting/dsc/configurations/configurations) która zapewnia obecność lub brak funkcji systemu Web **Server** Windows (IIS), w zależności od sposobu przypisywania węzłów.

1. Uruchom [vscode](https://code.visualstudio.com/docs) (lub dowolny edytor tekstu).
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

Ta konfiguracja wywołuje jeden zasób w każdym bloku [węzła, zasób WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Ten zasób zapewnia obecność lub brak funkcji **serwera sieci Web.**

## <a name="importing-a-configuration-into-azure-automation"></a>Importowanie konfiguracji do usługi Azure Automation

Następnie należy zaimportować konfigurację do konta automatyzacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Konfiguracje,** a następnie kliknij pozycję **Dodaj**.
1. W okienku Importowanie konfiguracji przejdź `TestConfig.ps1` do pliku na komputerze.

   ![Zrzut ekranu przedstawiający ostrze **Import Configuration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Kliknij przycisk **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Wyświetlanie konfiguracji w usłudze Azure Automation

Po zaimportowaniu konfiguracji można ją wyświetlić w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Konfiguracje,** a następnie kliknij przycisk **TestConfig**. Jest to nazwa konfiguracji zaimportowanej w poprzedniej procedurze.
1. W okienku Konfiguracja testconfig kliknij pozycję **Wyświetl źródło konfiguracji**.

   ![Zrzut ekranu przedstawiający blok konfiguracji TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Zostanie otwarte okienko źródła konfiguracji TestConfig, wyświetlające kod programu PowerShell dla konfiguracji.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilowanie konfiguracji w usłudze Azure Automation

Przed zastosowaniem żądanego stanu do węzła, konfiguracja DSC definiująca ten stan musi zostać skompilowana w jedną lub więcej konfiguracji węzłów (dokument MOF) i umieszczona na serwerze ściągania dsc automatyzacji. Aby uzyskać bardziej szczegółowy opis kompilacji konfiguracji w konfiguracji stanu automatyzacji platformy Azure, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
Aby uzyskać więcej informacji na temat kompilowania konfiguracji, zobacz [Konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Konfiguracje,** a następnie kliknij przycisk **TestConfig**. Jest to nazwa wcześniej zaimportowana konfiguracja.
1. W okienku Konfiguracja testconfig kliknij pozycję **Skompiluj**, a następnie kliknij przycisk **Tak**. Spowoduje to uruchomienie zadania kompilacji.

   ![Zrzut ekranu przedstawiający przycisk kompilacji strony konfiguracji TestConfig](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Podczas kompilowania konfiguracji w usłudze Azure Automation automatycznie wdraża wszystkie utworzone pliki MOF konfiguracji węzła na serwerze ściągania.

## <a name="viewing-a-compilation-job"></a>Wyświetlanie zadania kompilacji

Po uruchomieniu kompilacji można ją wyświetlić na kafelku **Zadania kompilacji** na stronie **Konfiguracja.** **Kafelek Zadania kompilacji** pokazuje aktualnie uruchomione, ukończone i nieudane zadania. Po otwarciu okienka zadania kompilacji, pokazuje informacje o tym zadaniu, w tym wszelkie błędy lub ostrzeżenia napotkane, parametry wejściowe używane w konfiguracji i dzienniki kompilacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Konfiguracje,** a następnie kliknij przycisk **TestConfig**. Jest to nazwa wcześniej zaimportowana konfiguracja.
1. W obszarze **Zadania kompilacji**wybierz zadanie kompilacji do wyświetlenia. Zostanie otwarte okienko Zadania kompilacji z etykietą daty rozpoczęcia zadania kompilacji.

   ![Zrzut ekranu przedstawiający stronę Zadania kompilacji](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknij dowolny kafelek w okienku Zadania kompilacji, aby wyświetlić dalsze szczegóły dotyczące zadania.

## <a name="viewing-node-configurations"></a>Wyświetlanie konfiguracji węzłów

Pomyślne zakończenie zadania kompilacji tworzy jedną lub więcej nowych konfiguracji węzła. Konfiguracja węzła jest dokumentem MOF, który jest wdrażany na serwerze ściągania i gotowy do wyciągnięcia i zastosowania przez jeden lub więcej węzłów. Konfiguracje węzłów można wyświetlić na koncie automatyzacji na stronie Konfiguracja stanu (DSC). Konfiguracja węzła ma nazwę `ConfigurationName.NodeName`z formularzem .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Skompilowane konfiguracje.**

   ![Zrzut ekranu przedstawiający kartę Skompilowane konfiguracje](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Dołączanie maszyny Wirtualnej platformy Azure do zarządzania za pomocą konfiguracji stanu automatyzacji usługi Azure

Za pomocą usługi Azure Automation State Configuration można zarządzać maszynami wirtualnymi platformy Azure (zarówno klasycznymi, jak i Menedżerem zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, maszynami wirtualnymi AWS i lokalnymi komputerami fizycznymi. W tym artykule dowiesz się, jak dołączać tylko maszyny wirtualne usługi Azure Resource Manager. Aby uzyskać informacje na temat dołączania innych typów maszyn, zobacz [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure.](automation-dsc-onboarding.md)

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Aby dołączać maszynę wirtualną usługi Azure Resource Manager do zarządzania przez konfigurację stanu automatyzacji platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) wybierz kartę **Węzły,** a następnie kliknij przycisk **+ Dodaj**.

   ![Zrzut ekranu przedstawiający stronę Węzły DSC z wyróżnioniem przycisku Dodaj maszynę wirtualną platformy Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. W okienku Maszyny wirtualne wybierz maszynę wirtualną.
1. W okienku szczegółów maszyny wirtualnej kliknij pozycję **+ Połącz**.

   > [!IMPORTANT]
   > Maszyna wirtualna musi być maszyną wirtualną usługi Azure Resource Manager z [obsługiwanym systemem operacyjnym](automation-dsc-overview.md#operating-system-requirements).

2. Na stronie Rejestracja wybierz nazwę konfiguracji węzła, która ma być stosowana do maszyny Wirtualnej w polu **Nazwa konfiguracji węzła.** Podanie nazwy w tym momencie jest opcjonalne. Po przykłęcaniu węzła można zmienić konfigurację przypisanego węzła.

3. Sprawdź **węzeł ponownego uruchomienia, jeśli jest to potrzebne,** a następnie kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający ostrze rejestracyjne](./media/automation-dsc-getting-started/RegisterVM.png)

   Określona konfiguracja węzła jest stosowana do maszyny Wirtualnej w odstępach czasu określonych przez wartość dla **częstotliwości trybu konfiguracji**. Maszyna wirtualna sprawdza aktualizacje konfiguracji węzła w odstępach czasu określonych przez wartość **częstotliwość odświeżania.** Aby uzyskać więcej informacji o sposobie ich użycia, zobacz [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaConfig).

Platforma Azure rozpoczyna proces dołączania maszyny Wirtualnej. Po zakończeniu maszyna wirtualna pojawia się na karcie **Węzły** strony konfiguracji stanu (DSC) na koncie automatyzacji.

## <a name="viewing-the-list-of-managed-nodes"></a>Wyświetlanie listy węzłów zarządzanych

Listę wszystkich maszyn, które zostały włączone do zarządzania, można wyświetlić na karcie **Węzły** na stronie Konfiguracja stanu (DSC).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Węzły.**

## <a name="viewing-reports-for-managed-nodes"></a>Wyświetlanie raportów dla węzłów zarządzanych

Za każdym razem, gdy konfiguracja stanu automatyzacji platformy Azure wykonuje sprawdzanie spójności w węźle zarządzanym, węzeł wysyła raport o stanie z powrotem do serwera ściągania. Można wyświetlić te raporty na stronie dla tego węzła.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Węzły.** W tym miejscu można zobaczyć omówienie stanu konfiguracji i szczegóły dla każdego węzła.

   ![Zrzut ekranu przedstawiający stronę Node](./media/automation-dsc-getting-started/NodesTab.png)

1. Na karcie **Węzły** kliknij rekord węzła, aby otworzyć raport. Kliknij raport, który chcesz wyświetlić dodatkowe szczegóły raportowania.

   ![Zrzut ekranu przedstawiający ostrze raportu](./media/automation-dsc-getting-started/NodeReport.png)

Na bloku dla pojedynczego raportu można wyświetlić następujące informacje o stanie dla odpowiedniego sprawdzania spójności:

- Stan raportu. Możliwe wartości:
    * Zgodny — węzeł jest zgodny z kontrolą.
   * Nie powiodło się — konfiguracja nie powiodła się.
   * Niezgodne — węzeł jest `ApplyandMonitor` w trybie, a maszyna nie jest w żądanym stanie.
- Godzina rozpoczęcia sprawdzania spójności.
- Całkowity czas wykonywania sprawdzania spójności.
- Typ sprawdzania spójności.
- Wszelkie błędy, w tym kod błędu i komunikat o błędzie.
- Wszelkie zasoby DSC używane w konfiguracji i stan każdego zasobu (czy węzeł jest w żądanym stanie dla tego zasobu). Możesz kliknąć każdy zasób, aby uzyskać bardziej szczegółowe informacje dotyczące tego zasobu.
- Nazwa, adres IP i tryb konfiguracji węzła.

Można również kliknąć **wyświetl raport nieprzetworzony,** aby wyświetlić rzeczywiste dane, które węzeł wysyła do serwera.
Aby uzyskać więcej informacji na temat korzystania z tych danych, zobacz [Korzystanie z serwera raportów DSC](/powershell/scripting/dsc/pull-server/reportserver).

Może upłynąć trochę czasu po węźle jest wbudowany, zanim pierwszy raport jest dostępny. Może być konieczne odczekanie do 30 minut na pierwszy raport po pokładzie węzła.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Ponowne przypisywanie węzła do innej konfiguracji węzła

Węzeł można przypisać, aby użyć innej konfiguracji węzła niż początkowo przypisany węzeł.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Węzły.**
1. Na karcie **Węzły** kliknij nazwę węzła, który chcesz ponownie przypisać.
1. Na stronie dla tego węzła kliknij pozycję **Przypisz konfigurację węzła**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła z wyróżnionym przyciskiem Konfiguracja węzła Przypisz](./media/automation-dsc-getting-started/AssignNode.png)

1. Na stronie Przypisywanie konfiguracji węzła wybierz konfigurację węzła, do której chcesz przypisać węzeł, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający stronę Przypisywanie konfiguracji węzła](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Wyrejestrowanie węzła

Jeśli nie chcesz już węzeł być zarządzane przez usługi Azure Automation DSC, można go wyrejestrować.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Węzły.**
1. Na karcie **Węzły** kliknij nazwę węzła, który chcesz wyrejestrować.
1. W okienku dla tego węzła kliknij pozycję **Wyrejestruj**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła z wyróżnioniem przycisku Wyrejestruj](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Pokrewne artykuły:

- [Omówienie konfiguracji stanu automatyzacji platformy Azure](automation-dsc-overview.md)
- [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md)
- [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Cennik konfiguracji stanu automatyzacji platformy Azure](https://azure.microsoft.com/pricing/details/automation/)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)