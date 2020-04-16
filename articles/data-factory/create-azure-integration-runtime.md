---
title: Tworzenie środowiska wykonawczego integracji platformy Azure w usłudze Azure Data Factory
description: Dowiedz się, jak utworzyć środowisko uruchomieniowe integracji platformy Azure w usłudze Azure Data Factory, która służy do kopiowania danych i wysyłania działań transformacji.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: e32530ece3626807b199850a2b4af5461ff51cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414075"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak utworzyć i skonfigurować środowisko uruchomieniowe integracji platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory w celu zapewnienia możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać więcej informacji na temat środowiska IR, zobacz [Środowisko uruchomieniowe integracji](concepts-integration-runtime.md).

Usługa Azure IR zapewnia w pełni zarządzane obliczenia do natywnie wykonywać działania przekształcania danych i wysyłać dane do usług obliczeniowych, takich jak HDInsight. Jest on hostowany w środowisku platformy Azure i obsługuje łączenie się z zasobami w środowisku sieci publicznej z publicznych dostępnych punktów końcowych.

W tym dokumencie przedstawiono sposób tworzenia i konfigurowania środowiska uruchomieniowego integracji platformy Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Domyślna usługa Azure IR
Domyślnie każda fabryka danych ma środowisko Azure IR w wewnętrznej pońończeniu, który obsługuje operacje w magazynach danych w chmurze i usługach obliczeniowych w sieci publicznej. Lokalizacja tej usługi Azure IR jest automatyczne rozwiązywanie. Jeśli **właściwość connectVia** nie jest określona w definicji usługi połączonej, używana jest domyślna usługa Azure IR. Wystarczy jawnie utworzyć podczerwenie platformy Azure, gdy chcesz jawnie zdefiniować lokalizację podczerwonego lub jeśli chcesz praktycznie pogrupować wykonania działań na różnych irs do celów zarządzania. 

## <a name="create-azure-ir"></a>Tworzenie usługi Azure IR

Aby utworzyć i skonfigurować usługę Azure IR, można użyć następujących procedur.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Tworzenie usługi Azure IR za pośrednictwem programu Azure PowerShell
Środowisko uruchomieniowe integracji można tworzyć za pomocą polecenia cmdlet Programu PowerShell **Set-AzDataFactoryV2IntegrationRuntime.** Aby utworzyć usługę Azure IR, należy określić nazwę, lokalizację i typ polecenia. Oto przykładowe polecenie utworzenia podczerwonego podczerwenia platformy Azure z lokalizacją ustawioną na "Europa Zachodnia":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
W przypadku usługi Azure IR typ musi być ustawiony na **Zarządzany**. Nie trzeba określać szczegóły obliczeń, ponieważ jest w pełni zarządzane elastycznie w chmurze. Określ szczegóły obliczeń, takie jak rozmiar węzła i liczba węzłów, gdy chcesz utworzyć usługę Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Tworzenie i konfigurowanie usługi Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Można skonfigurować istniejącą usługę Azure IR, aby zmienić jego lokalizację przy użyciu polecenia cmdlet Programu PowerShell Set-AzDataFactoryV2IntegrationRuntime PowerShell. Aby uzyskać więcej informacji na temat lokalizacji środowiska IR platformy Azure, zobacz [Wprowadzenie do środowiska wykonawczego integracji.](concepts-integration-runtime.md)

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Tworzenie usługi Azure IR za pośrednictwem interfejsu użytkownika usługi Azure Data Factory
Aby utworzyć usługę Azure IR przy użyciu interfejsu użytkownika usługi Azure Data Factory, należy wykonać następujące kroki.

1. Na stronie **Rozpocznijmy pracę** interfejsu użytkownika usługi Azure Data Factory, wybierz kartę **Autor** w lewym okienku.

   ![Przycisk Autor strony głównej](media/doc-common-process/get-started-page-author-button.png)

1. Wybierz **pozycję Połączenia** u dołu lewego okienka i wybierz pozycję Środowiska **wykonawcze integracji** w oknie **Połączenia.** Wybierz **+Nowy**.

   ![Tworzenie środowiska Integration Runtime](media/create-azure-integration-runtime/new-integration-runtime.png)

1. Na stronie **Ustawienia środowiska wykonawczego integracji** wybierz pozycję **Azure, Self-Hosted**, a następnie wybierz pozycję **Kontynuuj**. 

1. Na następnej stronie wybierz pozycję **Azure,** aby utworzyć usługę Azure IR, a następnie wybierz pozycję **Kontynuuj**.
   ![Tworzenie środowiska Integration Runtime](media/create-azure-integration-runtime/new-azure-ir.png)

1. Wprowadź nazwę urządzenia IR platformy Azure i wybierz pozycję **Utwórz**.
   ![Tworzenie usługi Azure IR](media/create-azure-integration-runtime/create-azure-ir.png)

1. Po zakończeniu tworzenia zostanie wyświetlone wyskakujące powiadomienie. Na stronie **Środowiska wykonawcze integracji** upewnij się, że na liście jest widoczna nowo utworzona podczerwona.

## <a name="use-azure-ir"></a>Korzystanie z usługi Azure IR

Po utworzeniu usługi Azure IR można odwoływać się do niego w definicji usługi połączonej. Poniżej znajduje się przykład, jak można odwoływać się do środowiska wykonawczego integracji platformy Azure utworzone powyżej z usługi linked Azure Storage:

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
Zobacz następujące artykuły na temat tworzenia innych typów środowiska wykonawczego integracji:

- [Tworzenie środowiska wykonawczego integracji hostowanego samodzielnie](create-self-hosted-integration-runtime.md)
- [Tworzenie środowiska Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)
 
