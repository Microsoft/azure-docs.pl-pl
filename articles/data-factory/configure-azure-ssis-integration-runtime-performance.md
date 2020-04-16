---
title: Konfigurowanie wydajności środowiska wykonawczego integracji usługi Azure-SSIS
description: Dowiedz się, jak skonfigurować właściwości środowiska wykonawczego integracji azure-SSIS w celu uzyskania wysokiej wydajności
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: ca88e42438c7cb48b062aa67d82053afbb9244bf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418290"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurowanie środowiska wykonawczego integracji azure-SSIS w celu uzyskania wysokiej wydajności

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


W tym artykule opisano sposób konfigurowania środowiska wykonawczego integracji platformy Azure-SSIS (IR) pod kątem wysokiej wydajności. Usługa Azure-SSIS IR umożliwia wdrażanie i uruchamianie pakietów usług integracji programu SQL Server (SSIS) na platformie Azure. Aby uzyskać więcej informacji na temat usługi Azure-SSIS IR, zobacz artykuł [środowiska wykonawczego integracji.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Aby uzyskać informacje na temat wdrażania i uruchamiania pakietów SSIS na platformie Azure, zobacz [Podnoszenie i przenoszenie obciążeń usług SQL Server Integration Services do chmury.](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

> [!IMPORTANT]
> Ten artykuł zawiera wyniki wydajności i obserwacje z wewnętrznych testów przeprowadzonych przez członków zespołu programistycznego SSIS. Wyniki mogą się różnić. Wykonaj własne testy przed sfinalizowaniem ustawień konfiguracji, które mają wpływ zarówno na koszt, jak i wydajność.

## <a name="properties-to-configure"></a>Właściwości do skonfigurowania

W poniższej części skryptu konfiguracji przedstawiono właściwości, które można skonfigurować podczas tworzenia środowiska wykonawczego integracji azure-SSIS. Aby uzyskać pełny skrypt i opis programu PowerShell, zobacz [Wdrażanie pakietów usług integracji programu SQL Server na platformie Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

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
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>Usługa AzureSSISLokowanie
**AzureSSISLocation** jest lokalizacją dla węzła procesu roboczego środowiska wykonawczego integracji. Węzeł procesu roboczego utrzymuje stałe połączenie z bazą danych wykazu SSIS (SSISDB) w bazie danych SQL platformy Azure. Ustaw **AzureSSISLocation** do tej samej lokalizacji co serwer bazy danych SQL, który obsługuje SSISDB, który umożliwia środowisko uruchomieniowe integracji do pracy tak wydajnie, jak to możliwe.

## <a name="azuressisnodesize"></a>Rozmiar usługi AzureSSISNodeSize
Fabryka danych, w tym azure-SSIS IR, obsługuje następujące opcje:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

W nieoficjalnych wewnętrznych testach przeprowadzanych przez zespół inżynierów SSIS seria D wydaje się być bardziej odpowiednia do wykonywania pakietów SSIS niż seria A.

-   Stosunek wydajności do ceny serii D jest wyższy niż w serii A, a stosunek wydajności do ceny serii v3 jest wyższy niż w serii v2.
-   Przepływność dla serii D jest wyższa niż seria A w tej samej cenie, a przepustowość dla serii v3 jest wyższa niż w serii v2 w tej samej cenie.
-   Węzły serii v2 usługi Azure-SSIS IR nie są odpowiednie dla konfiguracji niestandardowej, więc zamiast tego należy użyć węzłów serii v3. Jeśli korzystasz już z węzłów serii v2, przełącz się, aby jak najszybciej użyć węzłów serii v3.
-   Seria E to zoptymalizowane pod kątem pamięci rozmiary maszyn wirtualnych, które zapewniają wyższy stosunek pamięci do procesora niż inne maszyny. Jeśli pakiet wymaga dużo pamięci, można rozważyć wybranie maszyny wirtualnej z serii E.

### <a name="configure-for-execution-speed"></a>Konfiguracja pod kątem szybkości wykonywania
Jeśli nie masz wiele pakietów do uruchomienia i chcesz, aby pakiety były uruchamiane szybko, użyj informacji na poniższym wykresie, aby wybrać typ maszyny wirtualnej odpowiedni dla danego scenariusza.

Te dane reprezentują wykonanie pojedynczego pakietu w węźle pojedynczego procesu roboczego. Pakiet ładuje 3 miliony rekordów z kolumnami imienia i nazwiska z usługi Azure Blob Storage, generuje pełną kolumnę name i zapisuje rekordy, które mają pełną nazwę dłuższą niż 20 znaków do usługi Azure Blob Storage.

![Szybkość wykonywania pakietu SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurowanie dla ogólnej przepływności

Jeśli masz wiele pakietów do uruchomienia i najbardziej zależy Ci na ogólnej przepływności, użyj informacji na poniższym wykresie, aby wybrać typ maszyny wirtualnej odpowiedni dla danego scenariusza.

![Maksymalna całkowita przepływność integracji SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>Numer AzureSSISNodeNumber

**AzureSSISNodeNumber** dostosowuje skalowalność środowiska wykonawczego integracji. Przepływność środowiska wykonawczego integracji jest proporcjonalna do **liczby AzureSSISNodeNumber**. Najpierw ustaw **numer AzureSSISNodeNumber** na małą wartość, monitoruj przepływność środowiska wykonawczego integracji, a następnie dostosuj wartość dla twojego scenariusza. Aby ponownie skonfigurować liczbę węzłów procesu [roboczego, zobacz Zarządzanie środowiskami uruchomieniowymi integracji platformy Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelWykonawyzostańNikzrój

Gdy już używasz węzła procesu roboczego zaawansowanego do uruchamiania pakietów, zwiększenie **AzureSSISMaxParallelExecutionsPerNode** może zwiększyć ogólną przepływność środowiska wykonawczego integracji. Dla Standard_D1_v2 węzłów obsługiwane są 1-4 równoległe wykonania na węzeł. Dla wszystkich innych typów węzłów obsługiwane są 1-max(2 x liczba rdzeni, 8) równoległe wykonania na węzeł. Jeśli chcesz **AzureSSISMaxParallelExecutionsPerNode** poza maksymalną wartość, którą obsługujemy, można otworzyć bilet pomocy technicznej i możemy zwiększyć wartość maksymalną dla Ciebie, a następnie trzeba użyć azure powershell do aktualizacji **AzureSSISMaxParallelExecutionsPerNode**.
Można oszacować odpowiednią wartość na podstawie kosztu pakietu i następujących konfiguracji dla węzłów procesu roboczego. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych ogólnego przeznaczenia](../virtual-machines/windows/sizes-general.md).

| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16 000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16 000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Oto wskazówki dotyczące ustawiania odpowiedniej wartości dla **właściwości AzureSSISMaxParallelExecutionsPerNode:** 

1. Najpierw ustaw go na małą wartość.
2. Zwiększ go o niewielką kwotę, aby sprawdzić, czy ogólna przepustowość jest lepsza.
3. Zatrzymaj zwiększenie wartości, gdy całkowita przepływność osiągnie wartość maksymalną.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** jest warstwą cenową bazy danych wykazu SSIS (SSISDB) w bazie danych SQL platformy Azure. To ustawienie ma wpływ na maksymalną liczbę pracowników w wystąpieniu podczerwenia, szybkość kolejkowania wykonania pakietu i szybkość ładowania dziennika wykonywania.

-   Jeśli nie dbasz o szybkość wykonywania pakietu kolejki i załadować dziennik wykonywania, można wybrać najniższą warstwę cenową bazy danych. Usługa Azure SQL Database z cennikiem basic obsługuje 8 pracowników w wystąpieniu środowiska wykonawczego integracji.

-   Wybierz bardziej zaawansowana baza danych niż Podstawowa, jeśli liczba pracowników jest większa niż 8 lub liczba rdzeni jest większa niż 50. W przeciwnym razie baza danych staje się wąskim gardłem wystąpienia środowiska wykonawczego integracji i ogólna wydajność ma negatywny wpływ.

-   Wybierz bardziej zaawansowana baza danych, taka jak s3, jeśli poziom rejestrowania jest ustawiony na pełny. Według naszych nieoficjalnych testów wewnętrznych warstwa cenowa s3 może obsługiwać wykonywanie pakietów SSIS z 2 węzłami, 128 równoległymi liczbami i poziomem szczegółowego rejestrowania.

Można również dostosować warstwę cenową bazy danych na podstawie informacji o użyciu [jednostki transakcji bazy danych](../sql-database/sql-database-what-is-a-dtu.md) (DTU) dostępnych w witrynie Azure portal.

## <a name="design-for-high-performance"></a>Projektowanie pod kątem wysokiej wydajności
Projektowanie pakietu SSIS do uruchomienia na platformie Azure różni się od projektowania pakietu do wykonywania lokalnego. Zamiast łączyć wiele niezależnych zadań w tym samym pakiecie, należy podzielić je na kilka pakietów w celu zwiększenia wydajności wykonania w usłudze Azure-SSIS IR. Utwórz wykonanie pakietu dla każdego pakietu, tak aby nie trzeba czekać na siebie nawzajem, aby zakończyć. Takie podejście korzysta ze skalowalności środowiska uruchomieniowego integracji azure-SSIS i poprawia ogólną przepływność.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o środowiskou uruchomieniowym integracji azure-SSIS. Zobacz [Środowisko uruchomieniowe integracji platformy Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
