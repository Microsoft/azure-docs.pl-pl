---
title: Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime
description: Dowiedz się, jak ponownie skonfigurować środowisko uruchomieniowe integracji platformy Azure-SSIS w usłudze Azure Data Factory po jego zainicjowaniu.
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
ms.openlocfilehash: ffebb8f82a69f0404974e6c8ea91bec951ca80e1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415767"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób ponownego skonfigurowania istniejącego środowiska wykonawczego integracji platformy Azure-SSIS. Aby utworzyć środowisko uruchomieniowe integracji platformy Azure-SSIS (IR) w usłudze Azure Data Factory, zobacz [Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS.](create-azure-ssis-integration-runtime.md)  

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory 
Za pomocą interfejsu użytkownika fabryki danych można zatrzymać, edytować/ponownie skonfigurować lub usunąć identyfikator IR usługi Azure-SSIS. 

1. W interfejsie **użytkownika fabryki danych**przełącz się na kartę **Edycja.** Aby uruchomić interfejs użytkownika fabryki danych, kliknij pozycję **Author & Monitor** na stronie głównej fabryki danych.
2. W lewym okienku kliknij pozycję **Połączenia**.
3. W prawym okienku przełącz się do **środowiska wykonawczego integracji**. 
4. Za pomocą przycisków w kolumnie Akcje można **zatrzymać,** **edytować**lub **usunąć** środowisko uruchomieniowe integracji. Przycisk **Kod** w kolumnie **Akcje** umożliwia wyświetlanie definicji JSON skojarzonej ze środowiska wykonawczego integracji.  
    
    ![Akcje dla usługi Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Aby ponownie skonfigurować identyfikator IR usługi Azure-SSIS
1. Zatrzymaj środowisko uruchomieniowe integracji, klikając **przycisk Zatrzymaj** w kolumnie **Akcje.** Aby odświeżyć widok listy, kliknij przycisk **Odśwież** na pasku narzędzi. Po zatrzymaniu podczerwaskowego widać, że pierwsza akcja umożliwia uruchomienie podczerwa. 

    ![Akcje dla usługi Azure SSIS IR — po zatrzymaniu](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edytuj/ponownie skonfiguruj podczerwone środowisko, klikając przycisk **Edytuj** w kolumnie **Akcje.** W oknie **Ustawienia środowiska wykonawczego integracji** zmień ustawienia (na przykład rozmiar węzła, liczbę węzłów lub maksymalne równoległe wykonania na węzeł). 
3. Aby ponownie uruchomić podczerwenie, kliknij przycisk **Start** w kolumnie **Akcje.**     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po zainicjowaniu aprowizyzności i uruchomienia wystąpienia środowiska wykonawczego integracji platformy Azure-SSIS można go ponownie skonfigurować, uruchamiając kolejno sekwencję `Stop`  -  `Set`  -  `Start` poleceń cmdlet programu PowerShell. Na przykład następujący skrypt programu PowerShell zmienia liczbę węzłów przydzielonych dla wystąpienia środowiska uruchomieniowego integracji platformy Azure-SSIS na pięć.

### <a name="reconfigure-an-azure-ssis-ir"></a>Ponowne konfigurowanie identyfikatora IR usługi Azure-SSIS

1. Najpierw zatrzymaj środowisko uruchomieniowe integracji Azure-SSIS przy użyciu polecenia cmdlet [Stop-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) To polecenie zwalnia wszystkie jego węzły i zatrzymuje rozliczenia.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Następnie ponownie skonfiguruj identyfikator IR azure-SSIS przy użyciu polecenia cmdlet [Set-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) Poniższe przykładowe polecenie skaluje środowisko uruchomieniowe integracji platformy Azure-SSIS do pięciu węzłów.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Następnie uruchom środowisko uruchomieniowe integracji Azure-SSIS przy użyciu polecenia cmdlet [Start-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) To polecenie przydziela wszystkie węzły do uruchamiania pakietów SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Usuwanie identyfikatora IR usługi Azure-SSIS
1. Najpierw wyekslij wszystkie istniejące identyfikatory IRS usługi Azure SSIS w fabryce danych.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Następnie zatrzymaj wszystkie istniejące identyfikatory IRS usługi Azure SSIS w fabryce danych.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Następnie usuń wszystkie istniejące identyfikatory IRS usługi Azure SSIS w fabryce danych jeden po drugim.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Na koniec usuń fabrykę danych.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Jeśli utworzono nową grupę zasobów, usuń grupę zasobów.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat środowiska uruchomieniowego Azure-SSIS, zobacz następujące tematy: 

- [Środowisko uruchomieniowe integracji platformy Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje koncepcyjne dotyczące środowiska wykonawczego integracji w ogóle, w tym azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozwija się w samouczku i zawiera instrukcje dotyczące korzystania z wystąpienia zarządzanego bazy danych SQL azure i dołączania do podczerwenia do sieci wirtualnej. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
 
