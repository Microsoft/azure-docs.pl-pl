---
title: Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime
description: Dowiedz się, jak ponownie skonfigurować środowisko Azure-SSIS Integration Runtime w Azure Data Factory po jego zainicjowaniu.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 7545f593aea2aca8c0aa240d44bcef3b7a1ca7c6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636276"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób zmiany konfiguracji istniejącego środowiska Azure-SSIS Integration Runtime. Aby utworzyć środowisko Azure-SSIS Integration Runtime (IR) w Azure Data Factory, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory 
Za pomocą interfejsu użytkownika Data Factory można zatrzymać, edytować/zmienić konfigurację lub usunąć Azure-SSIS IR. 

1. Otwórz Data Factory interfejs użytkownika, wybierając kafelek **tworzenie & monitor** na stronie głównej fabryki danych.
2. Wybierz pozycję **Zarządzaj** centrum poniżej **strony głównej** , **Edytuj** i **Monitoruj** centra, aby wyświetlić okienko **połączenia** .

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Aby skonfigurować Azure-SSIS IR
W okienku **połączenia** okienka **Zarządzanie** centrum przejdź do strony **Integration Runtimes** i wybierz pozycję **Odśwież** . 

   ![Okienko połączenia](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Możesz edytować/zmienić konfigurację Azure-SSIS IR, wybierając jego nazwę. Możesz również wybrać odpowiednie przyciski do monitorowania/uruchamiania/zatrzymywania/usuwania Azure-SSIS IR, automatycznie wygenerować potok ADF z działaniem pakietu SSIS do uruchomienia na Azure-SSIS IR, a następnie wyświetlić kod JSON/ładunek Azure-SSIS IR.  Edytowanie/usuwanie Azure-SSIS IR można wykonać tylko wtedy, gdy jest ono zatrzymane.

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
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](./tutorial-deploy-ssis-packages-azure.md). Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia Azure-SSIS IR i używania Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego usługi Azure SQL i dołączania do sieci wirtualnej. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach.