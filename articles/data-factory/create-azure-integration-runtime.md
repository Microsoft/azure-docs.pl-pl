---
title: Tworzenie środowiska Azure Integration Runtime w Azure Data Factory
description: Dowiedz się, jak utworzyć środowisko Azure Integration Runtime w Azure Data Factory, które jest używane do kopiowania działań przekształcania danych i wysyłania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 9615dc358b1c5bed0e48c07c2571ccce05fcdf2e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637211"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak utworzyć i skonfigurować Azure Integration Runtime
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) to infrastruktura obliczeniowa używana przez Azure Data Factory do zapewniania możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać więcej informacji na temat środowiska IR, zobacz [Integration Runtime](concepts-integration-runtime.md).

Azure IR udostępnia w pełni zarządzane obliczenia, które umożliwiają natywne wykonywanie operacji przekształcania danych i wysyłanie danych do usług obliczeniowych, takich jak HDInsight. Jest ona hostowana w środowisku platformy Azure i obsługuje łączenie się z zasobami w środowisku sieci publicznej za pomocą dostępnych publicznie punktów końcowych.

W tym dokumencie przedstawiono sposób tworzenia i konfigurowania Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Azure IR domyślne
Domyślnie każda Fabryka danych ma Azure IR w zapleczu, który obsługuje operacje dotyczące magazynów danych w chmurze i usług obliczeniowych w sieci publicznej. Lokalizacja tego Azure IR jest rozwiązywana automatycznie. Jeśli właściwość **właściwością connectvia** nie została określona w definicji połączonej usługi, zostanie użyta domyślna Azure IR. Musisz jawnie utworzyć Azure IR, gdy chcesz jawnie zdefiniować lokalizację środowiska IR, lub jeśli chcesz praktycznie grupować wykonania działania w innym urzędzie skarbowym do celów zarządzania. 

## <a name="create-azure-ir"></a>Utwórz Azure IR

Aby utworzyć i skonfigurować Azure IR, można użyć poniższych procedur.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Tworzenie Azure IR za pośrednictwem Azure PowerShell
Integration Runtime można utworzyć przy użyciu polecenia cmdlet **Set-AzDataFactoryV2IntegrationRuntime** programu PowerShell. Aby utworzyć Azure IR, należy określić nazwę, lokalizację i typ polecenia. Oto przykładowe polecenie służące do tworzenia Azure IR z lokalizacją "Europa Zachodnia":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
W przypadku Azure IR typ musi być ustawiony na **zarządzany** . Nie trzeba określać szczegółów obliczeń, ponieważ jest w pełni zarządzana elastycznie w chmurze. Określ szczegóły obliczeń, takie jak rozmiar węzła i liczba węzłów, jeśli chcesz utworzyć Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Tworzenie i konfigurowanie Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Istniejące Azure IR można skonfigurować w celu zmiany jego lokalizacji za pomocą polecenia cmdlet programu PowerShell Set-AzDataFactoryV2IntegrationRuntime. Aby uzyskać więcej informacji na temat lokalizacji Azure IR, zobacz [wprowadzenie do środowiska Integration Runtime](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Tworzenie Azure IR za pośrednictwem interfejsu użytkownika Azure Data Factory
Wykonaj następujące kroki, aby utworzyć Azure IR przy użyciu Azure Data Factory interfejsu użytkownika.

1. Na stronie Wprowadzenie Azure Data Factory interfejsu użytkownika wybierz [kartę Zarządzanie](./author-management-hub.md) w okienku po **lewej stronie.**

   ![Przycisk zarządzania stroną główną](media/doc-common-process/get-started-page-manage-button.png)

1. W lewym okienku wybierz pozycję **Integration Runtimes** , a następnie wybierz pozycję **+ Nowy** .

   ![Tworzenie środowiska Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na stronie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Azure, pozycję samodzielny** , a następnie wybierz pozycję **Kontynuuj** . 

1. Na poniższej stronie wybierz pozycję **Azure** , aby utworzyć Azure IR, a następnie wybierz pozycję **Kontynuuj** .
   ![Tworzenie środowiska Integration Runtime](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Wprowadź nazwę Azure IR i wybierz pozycję **Utwórz** .
   ![Tworzenie Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Po zakończeniu tworzenia zostanie wyświetlone wyskakujące powiadomienie. Na stronie **Integration Runtime (środowisko uruchomieniowe** ) Upewnij się, że na liście jest widoczny nowo utworzony port IR.

## <a name="use-azure-ir"></a>Użyj Azure IR

Po utworzeniu Azure IR można odwołać się do niego w definicji połączonej usługi. Poniżej znajduje się przykład sposobu odwoływania się do Azure Integration Runtime utworzonego powyżej z połączonej usługi Azure Storage:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi tworzenia innych typów środowiska Integration Runtime:

- [Tworzenie własnego środowiska Integration Runtime](create-self-hosted-integration-runtime.md)
- [Tworzenie środowiska Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)
