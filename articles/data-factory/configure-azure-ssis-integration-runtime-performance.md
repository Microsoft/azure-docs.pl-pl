---
title: Konfigurowanie wydajności dla Azure-SSIS Integration Runtime
description: Dowiedz się, jak skonfigurować właściwości Azure-SSIS Integration Runtime w celu uzyskania wysokiej wydajności
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: db50049675766d9fd8a018c8730f48ac34e23bfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276668"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurowanie Azure-SSIS Integration Runtime na potrzeby wysokiej wydajności

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime (IR) w celu uzyskania wysokiej wydajności. Azure-SSIS IR umożliwia wdrażanie i uruchamianie pakietów SQL Server Integration Services (SSIS) na platformie Azure. Aby uzyskać więcej informacji na temat Azure-SSIS IR, zobacz artykuł [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) . Aby uzyskać informacje na temat wdrażania i uruchamiania pakietów usług SSIS na platformie Azure, zobacz [Podnieś i przenieś SQL Server Integration Services obciążeń do chmury](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ten artykuł zawiera wyniki wydajności i obserwacje z testów wewnętrznych wykonanych przez członków zespołu programistycznego usług SSIS. Wyniki mogą się różnić. Wykonaj własne testy przed zakończeniem ustawień konfiguracji, które mają wpływ na koszt i wydajność.

## <a name="properties-to-configure"></a>Właściwości do skonfigurowania

W poniższej części skryptu konfiguracji przedstawiono właściwości, które można skonfigurować podczas tworzenia Azure-SSIS Integration Runtime. Aby uzyskać kompletny skrypt programu PowerShell i opis, zobacz [wdrażanie pakietów SQL Server Integration Services na platformie Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** jest lokalizacją dla węzła procesu roboczego Integration Runtime. Węzeł procesu roboczego utrzymuje stałe połączenie z bazą danych wykazu usług SSIS (SSISDB) w Azure SQL Database. Ustaw **AzureSSISLocation** na tę samą lokalizację co [logiczny serwer SQL Server](../azure-sql/database/logical-servers.md) , który hostuje SSISDB, co umożliwia wydajne działanie środowiska Integration Runtime.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, w tym Azure-SSIS IR, obsługuje następujące opcje:
-   Standardowa \_ a4 \_ v2
-   Standardowa \_ A8 \_ v2
-   Standardowa \_ D1 \_ v2
-   Standardowa \_ D2 \_ v2
-   Standardowa \_ D3, \_ wersja 2
-   Standardowa \_ D4 \_ v2
-   Standardowa \_ D2 \_ v3
-   Standardowa \_ D4 \_ v3
-   Standardowa \_ D8 \_ v3
-   Standardowa \_ D16 \_ v3
-   Standardowa \_ D32 \_ v3
-   Standardowa \_ D64 \_ v3
-   Standardowa \_ E2 \_ v3
-   Standardowa \_ E4 \_ v3
-   Standardowa \_ E8 \_ v3
-   Standardowa \_ E16 \_ v3
-   Standardowa \_ E32 \_ v3
-   Standardowa \_ E64 \_ v3

W nieoficjalnym teście prowadzonym przez zespół inżynierów usług SSIS seria D wydaje się bardziej odpowiednia dla wykonywania pakietów SSIS niż w przypadku serii.

-   Stosunek wydajności/ceny serii D jest wyższy niż seria, a stosunek wydajności/ceny dla serii v3 jest wyższy niż seria v2.
-   Przepływność dla serii D jest wyższa niż seria w tej samej cenie, a przepływność dla serii v3 jest wyższa niż seria v2 w tej samej cenie.
-   Węzły serii v2 Azure-SSIS IR nie są odpowiednie dla konfiguracji niestandardowej, dlatego należy zamiast tego użyć węzłów serii v3. Jeśli korzystasz już z węzłów serii w wersji 2, przejdź do korzystania z węzłów serii v3 najszybciej, jak to możliwe.
-   Seria E ma rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci, które zapewniają większy stosunek pamięci do procesora CPU niż inne maszyny. Jeśli pakiet wymaga dużej ilości pamięci, można rozważyć wybranie maszyny wirtualnej serii E.

### <a name="configure-for-execution-speed"></a>Skonfiguruj na potrzeby szybkości wykonywania
Jeśli nie masz wielu pakietów do uruchomienia i chcesz szybko uruchamiać pakiety, użyj informacji z poniższej tabeli, aby wybrać typ maszyny wirtualnej odpowiedni dla danego scenariusza.

Te dane przedstawiają wykonywanie pojedynczego pakietu na jednym węźle procesu roboczego. Pakiet ładuje 3 000 000 rekordów z imionami i kolumnami nazwiska z usługi Azure Blob Storage, generuje kolumnę pełna nazwa i zapisuje rekordy, które mają pełną nazwę o długości przekraczającej 20 znaków na platformie Azure Blob Storage.

Oś y to liczba pakietów, które ukończyły wykonywanie w ciągu godziny. Należy pamiętać, że jest to tylko wynik testu jednego pakietu zużywanego przez pamięć. Jeśli chcesz znać przepływność pakietu, zalecamy przeprowadzenie testu przez siebie.

![Szybkość wykonywania pakietu SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurowanie na potrzeby ogólnej przepływności

Jeśli masz wiele pakietów do uruchomienia i masz największe znaczenie dla ogólnej przepływności, użyj informacji z poniższej tabeli, aby wybrać typ maszyny wirtualnej odpowiedni dla danego scenariusza.

Oś y to liczba pakietów, które ukończyły wykonywanie w ciągu godziny. Należy pamiętać, że jest to tylko wynik testu jednego pakietu zużywanego przez pamięć. Jeśli chcesz znać przepływność pakietu, zalecamy przeprowadzenie testu przez siebie.

![W Integration Runtime SSIS maksymalna przepływność](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** dostosowuje skalowalność środowiska Integration Runtime. Przepływność środowiska Integration Runtime jest proporcjonalna do **AzureSSISNodeNumber**. W pierwszej kolejności ustaw **AzureSSISNodeNumber** na małą wartość, Monitoruj przepływność środowiska Integration Runtime, a następnie Dostosuj wartość dla danego scenariusza. Aby ponownie skonfigurować liczbę węzłów procesu roboczego, zobacz [Zarządzanie środowiskiem Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Gdy używasz już zaawansowanego węzła procesu roboczego do uruchamiania pakietów, zwiększenie **AzureSSISMaxParallelExecutionsPerNode** może zwiększyć ogólną przepływność środowiska Integration Runtime. Jeśli chcesz zwiększyć maksymalną wartość, musisz użyć Azure PowerShell, aby zaktualizować **AzureSSISMaxParallelExecutionsPerNode**. Można oszacować odpowiednią wartość w oparciu o koszt pakietu oraz następujące konfiguracje dla węzłów procesu roboczego. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych ogólnego przeznaczenia](../virtual-machines/windows/sizes-general.md).

| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardowa \_ D1 \_ v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standardowa \_ D2 \_ v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standardowa \_ D3, \_ wersja 2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standardowa \_ D4 \_ v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standardowa \_ a4 \_ v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standardowa \_ A8 \_ v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |
| Standardowa \_ D2 \_ v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| Standardowa \_ D4 \_ v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standardowa \_ D8 \_ v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standardowa \_ D16 \_ v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| Standardowa \_ D32 \_ v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16 000                                      |
| Standardowa \_ D64 \_ v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standardowa \_ E2 \_ v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| Standardowa \_ E4 \_ v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standardowa \_ E8 \_ v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standardowa \_ E16 \_ v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| Standardowa \_ E32 \_ v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16 000                                      |
| Standardowa \_ E64 \_ v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

Poniżej przedstawiono wskazówki dotyczące ustawiania prawidłowej wartości właściwości **AzureSSISMaxParallelExecutionsPerNode** : 

1. Najpierw ustaw na małą wartość.
2. Zwiększ go o niewielką ilość, aby sprawdzić, czy ogólna przepływność jest ulepszona.
3. Zatrzymaj zwiększenie wartości, gdy ogólna przepływność osiągnie wartość maksymalną.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** to warstwa cenowa bazy danych wykazu usług SSIS (SSISDB) w systemie Azure SQL Database. To ustawienie ma wpływ na maksymalną liczbę procesów roboczych w wystąpieniu środowiska IR, szybkość do kolejki wykonania pakietu oraz szybkość ładowania dziennika wykonania.

-   Jeśli nie masz opieki nad szybkością wykonywania pakietów w kolejce i załadowaniu dziennika wykonywania, możesz wybrać najniższą warstwę cenową bazy danych. Azure SQL Database z cenami podstawowymi obsługuje 8 procesów roboczych w wystąpieniu środowiska Integration Runtime.

-   Wybierz bardziej wydajną bazę danych niż podstawowa, jeśli liczba procesów roboczych jest większa niż 8, lub liczba rdzeni jest większa niż 50. W przeciwnym razie baza danych stanowi wąskie gardła wystąpienia środowiska Integration Runtime i ogólna wydajność ma negatywny wpływ.

-   Wybierz bardziej wydajną bazę danych, na przykład S3, jeśli poziom rejestrowania jest ustawiony na pełne. Zgodnie z naszymi nieoficjalnymi testami, warstwa cenowa S3 może obsługiwać wykonywanie pakietów SSIS z 2 węzłami, 128 liczbą równoległą i pełnym poziomem rejestrowania.

Warstwę cenową bazy danych można również dostosować na podstawie informacji o użyciu [jednostek transakcji bazy danych](../sql-database/sql-database-what-is-a-dtu.md) (DTU) dostępnych na Azure Portal.

## <a name="design-for-high-performance"></a>Projektowanie pod kątem wysokiej wydajności
Projektowanie pakietu usług SSIS do uruchamiania na platformie Azure różni się od projektowania pakietu do wykonania lokalnego. Zamiast łączyć wiele niezależnych zadań w tym samym pakiecie, rozdziel je na kilka pakietów, aby zwiększyć efektywność wykonywania w Azure-SSIS IR. Utwórz wykonanie pakietu dla każdego pakietu, aby nie musiały czekać na zakończenie każdego z nich. To podejście korzysta ze skalowalności środowiska Azure-SSIS Integration Runtime i zwiększa ogólną przepływność.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat Azure-SSIS Integration Runtime. Zobacz [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
