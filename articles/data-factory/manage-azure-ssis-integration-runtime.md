---
title: Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime
description: Dowiedz się, jak ponownie skonfigurować środowisko Azure-SSIS Integration Runtime w Azure Data Factory po jego zainicjowaniu.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 9228d33ac8ad4dd2d274c8ae642861bf3fbdc888
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118254"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób zmiany konfiguracji istniejącego środowiska Azure-SSIS Integration Runtime. Aby utworzyć środowisko Azure-SSIS Integration Runtime (IR) w Azure Data Factory, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory 
Za pomocą interfejsu użytkownika Data Factory można zatrzymać, edytować/zmienić konfigurację lub usunąć Azure-SSIS IR. 

1. W **interfejsie użytkownika Data Factory**przejdź do karty **Edycja** . Aby uruchomić interfejs użytkownika Data Factory, kliknij przycisk **utwórz & monitor** na stronie głównej fabryki danych.
2. W lewym okienku kliknij pozycję **połączenia**.
3. W okienku po prawej stronie przejdź do **środowiska Integration Runtime**. 
4. Za pomocą przycisków w kolumnie akcje można **zatrzymać**, **edytować**lub **usunąć** środowisko Integration Runtime. Przycisk **kod** w kolumnie **Akcje** umożliwia wyświetlenie definicji JSON skojarzonej z środowiskiem Integration Runtime.  
    
    ![Akcje dla środowiska Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Aby skonfigurować Azure-SSIS IR
1. Zatrzymaj środowisko Integration Runtime, klikając przycisk **Zatrzymaj** w kolumnie **Akcje** . Aby odświeżyć widok listy, kliknij przycisk **Odśwież** na pasku narzędzi. Po zatrzymaniu podczerwieni zobaczysz, że pierwsza akcja pozwala uruchomić środowisko IR. 

    ![Akcje środowiska Azure SSIS IR — po zatrzymaniu](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edytuj/Zmień konfigurację środowiska IR, klikając przycisk **Edytuj** w kolumnie **Akcje** . W oknie **instalatora Integration Runtime** Zmień ustawienia (na przykład rozmiar węzła, liczbę węzłów lub Maksymalna Liczba wykonań równoległych na węzeł). 
3. Aby ponownie uruchomić środowisko IR, kliknij przycisk **Start** w kolumnie **Akcje** .     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po zainicjowaniu i uruchomieniu wystąpienia środowiska Azure-SSIS Integration runtime można skonfigurować go ponownie, uruchamiając sekwencję `Stop`  -  `Set`  -  `Start` poleceń cmdlet programu PowerShell w następujący sposób. Na przykład poniższy skrypt programu PowerShell zmienia liczbę węzłów przydzieloną dla wystąpienia środowiska Azure-SSIS Integration Runtime na pięć.

### <a name="reconfigure-an-azure-ssis-ir"></a>Skonfiguruj ponownie Azure-SSIS IR

1. Najpierw Zatrzymaj środowisko Azure-SSIS Integration Runtime za pomocą polecenia cmdlet [stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . To polecenie zwalnia wszystkie jego węzły i przerywa rozliczanie.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Następnie skonfiguruj ponownie Azure-SSIS IR przy użyciu polecenia cmdlet [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Następujące przykładowe polecenie skaluje środowisko Azure-SSIS Integration Runtime do pięciu węzłów.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Następnie uruchom środowisko Azure-SSIS Integration Runtime za pomocą polecenia cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . To polecenie przypisuje wszystkie węzły do uruchamiania pakietów SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Usuwanie Azure-SSIS IR
1. Najpierw utwórz listę wszystkich istniejących usług Azure SSIS IRs w ramach fabryki danych.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Następnie Zatrzymaj wszystkie istniejące IRs usługi Azure SSIS w fabryce danych.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Następnie usuń wszystkie istniejące IRs z usługi Azure SSIS w swojej fabryce danych.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Na koniec Usuń fabrykę danych.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Jeśli utworzono nową grupę zasobów, Usuń grupę zasobów.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat środowiska uruchomieniowego Azure-SSIS, zobacz następujące tematy: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne dotyczące środowiska Integration Runtime, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia Azure-SSIS IR i używania Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego usługi Azure SQL i dołączania do sieci wirtualnej. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
 
