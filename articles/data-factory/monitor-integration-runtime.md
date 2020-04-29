---
title: Monitorowanie środowiska Integration Runtime w Azure Data Factory
description: Dowiedz się, jak monitorować różne typy środowiska Integration Runtime w Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 6d2ea5c0b7354867086fc0cce43732f2d73c53ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398963"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitor an integration runtime in Azure Data Factory (Monitorowanie środowiska Integration Runtime w usłudze Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration Runtime** to infrastruktura obliczeniowa używana przez Azure Data Factory, która zapewnia różne możliwości integracji danych w różnych środowiskach sieciowych. Istnieją trzy typy środowisk Integration Runtime oferowanych przez Data Factory:

- Środowisko uruchomieniowe integracji Azure
- Infrastruktura Integration Runtime (Self-hosted)
- Azure SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby uzyskać stan wystąpienia środowiska Integration Runtime (IR), uruchom następujące polecenie programu PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Polecenie cmdlet zwraca różne informacje dla różnych typów środowiska Integration Runtime. W tym artykule opisano właściwości i Stany dla każdego typu środowiska Integration Runtime.  

## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure
Zasób obliczeniowy dla środowiska Azure Integration Runtime jest w pełni zarządzany elastycznie na platformie Azure. Poniższa tabela zawiera opisy właściwości zwracanych przez polecenie **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Właściwości
Poniższa tabela zawiera opisy właściwości zwracanych przez polecenie cmdlet dla środowiska Azure Integration Runtime:

| Właściwość | Opis |
-------- | ------------- | 
| Nazwa | Nazwa środowiska Azure Integration Runtime. |  
| Stan | Stan środowiska Azure Integration Runtime. | 
| Lokalizacja | Lokalizacja środowiska Azure Integration Runtime. Aby uzyskać szczegółowe informacje na temat lokalizacji środowiska Azure Integration Runtime, zobacz [wprowadzenie do środowiska Integration Runtime](concepts-integration-runtime.md). |
| DataFactoryName | Nazwa fabryki danych, do której należy środowisko Azure Integration Runtime. | 
| ResourceGroupName | Nazwa grupy zasobów, do której należy Fabryka danych.  |
| Opis | Opis środowiska Integration Runtime.  |

### <a name="status"></a>Stan
W poniższej tabeli przedstawiono możliwe stany środowiska Azure Integration Runtime:

| Stan | Komentarze/scenariusze | 
| ------ | ------------------ |
| Online | Środowisko Azure Integration Runtime jest w trybie online i gotowe do użycia. | 
| W trybie offline | Środowisko Azure Integration Runtime jest w trybie offline z powodu błędu wewnętrznego. |

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)
Ta sekcja zawiera opisy właściwości zwracanych przez polecenie cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Zwrócone właściwości i stan zawierają informacje o ogólnym środowisku Integration Runtime i każdym węźle w środowisku uruchomieniowym.  

### <a name="properties"></a>Właściwości

Poniższa tabela zawiera opisy właściwości monitorowania dla **każdego węzła**:

| Właściwość | Opis | 
| -------- | ----------- | 
| Nazwa | Nazwa własnego środowiska Integration Runtime i skojarzonych z nim węzłów. Węzeł to lokalna maszyna z systemem Windows, na której zainstalowano własne środowisko Integration Runtime. |  
| Stan | Stan ogólnego środowiska Integration Runtime i każdego węzła. Przykład: online/offline/Limited/itd. Aby uzyskać informacje o tych Stanach, zobacz następną sekcję. | 
| Wersja | Wersja własnego środowiska Integration Runtime i każdego węzła. Wersja własnego środowiska Integration Runtime jest określana na podstawie wersji większości węzłów w grupie. Jeśli istnieją węzły z różnymi wersjami w konfiguracji samodzielnego środowiska uruchomieniowego Integration Runtime, tylko węzły mające ten sam numer wersji co poprawnie obsługiwana funkcja środowiska Integration Runtime. Inne są w trybie ograniczonym i muszą zostać ręcznie zaktualizowane (tylko w przypadku niepowodzenia aktualizacji w przypadku awarii). | 
| Dostępna pamięć | Dostępna pamięć w węźle środowiska Integration Runtime w trybie własnym. Ta wartość jest migawką niemal w czasie rzeczywistym. | 
| Wykorzystanie procesora | Użycie procesora CPU w węźle środowiska Integration Runtime (własny). Ta wartość jest migawką niemal w czasie rzeczywistym. |
| Sieć (do/z) | Wykorzystanie sieci w węźle środowiska Integration Runtime (własny). Ta wartość jest migawką niemal w czasie rzeczywistym. | 
| Zadania współbieżne (uruchomione/ograniczone) | **Uruchomione**. Liczba zadań lub zadań uruchomionych w każdym węźle. Ta wartość jest migawką niemal w czasie rzeczywistym. <br/><br/>**Limit**. Wartość Ogranicz oznacza maksymalne zadania współbieżne dla każdego węzła. Ta wartość jest definiowana w zależności od rozmiaru maszyny. Można zwiększyć limit skalowania współbieżnego wykonywania zadań w zaawansowanych scenariuszach, gdy działania mają limit czasu, nawet gdy wykorzystanie procesora CPU, pamięci lub sieci jest w użyciu. Ta funkcja jest również dostępna w przypadku autonomicznego środowiska Integration Runtime w jednym węźle. |
| Rola | Istnieją dwa typy ról w ramach międzywęzłowego środowiska Integration Runtime — Dyspozytor i proces roboczy. Wszystkie węzły są pracownikami, co oznacza, że mogą być używane do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który służy do ściągania zadań/zadań z usług w chmurze i wysyłania ich do różnych węzłów procesu roboczego. Węzeł dyspozytora jest również węzłem procesu roboczego. |

Niektóre ustawienia właściwości są bardziej zrozumiałe, jeśli istnieją co najmniej dwa węzły w środowisku Integration Runtime (w scenariuszu skalowania w poziomie).

#### <a name="concurrent-jobs-limit"></a>Limit zadań współbieżnych

Wartość domyślna limitu współbieżnych zadań jest ustawiana na podstawie rozmiaru maszyny. Czynniki używane do obliczania tej wartości zależą od ilości pamięci RAM i liczby rdzeni procesora CPU maszyny. Im więcej rdzeni i więcej pamięci, tym większy domyślny limit jednoczesnych zadań.

Skalowanie w poziomie przez zwiększenie liczby węzłów. Zwiększenie liczby węzłów powoduje, że limit zadań współbieżnych jest sumą współbieżnych wartości limitów zadań wszystkich dostępnych węzłów.  Na przykład jeśli jeden węzeł pozwala uruchamiać maksymalnie dwanaście współbieżnych zadań, dodanie trzech bardziej podobnych węzłów umożliwia uruchomienie maksymalnie 48 współbieżnych zadań (czyli 4 x 12). Zalecamy zwiększenie limitu zadań współbieżnych tylko wtedy, gdy widoczne są niskie użycie zasobów z wartościami domyślnymi w każdym węźle.

Obliczoną wartość domyślną można zastąpić w Azure Portal. Wybierz pozycję Utwórz > połączenia > środowiska Integration Runtime > Edytuj węzły > > zmodyfikuj wartość zadania współbieżnego na węzeł. Można również użyć polecenia [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) programu PowerShell.
  
### <a name="status-per-node"></a>Stan (na węzeł)
W poniższej tabeli przedstawiono możliwe stany własnego węzła Integration Runtime:

| Stan | Opis |
| ------ | ------------------ | 
| Online | Węzeł jest połączony z usługą Data Factory. |
| W trybie offline | Węzeł jest w trybie offline. |
| Unowocześnieni | Węzeł jest aktualizowany w sposób autouzupełniania. |
| Ograniczone | Z powodu problemu z łącznością. Może to być spowodowane problemem z portem HTTP 8050, problemem z łącznością usługi Service Bus lub problemem z synchronizacją poświadczeń. |
| Nieaktywne | Węzeł jest w konfiguracji innej niż Konfiguracja innych węzłów większości. |

Węzeł może być nieaktywny, jeśli nie może połączyć się z innymi węzłami.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stan (ogólne środowisko Integration Runtime)
W poniższej tabeli przedstawiono możliwe stany środowiska Integration Runtime (samodzielne). Ten stan zależy od stanu wszystkich węzłów należących do środowiska uruchomieniowego. 

| Stan | Opis |
| ------ | ----------- | 
| Potrzebna rejestracja | Nie zarejestrowano jeszcze żadnego węzła w tym środowisku Integration Runtime. |
| Online | Wszystkie węzły są w trybie online. |
| W trybie offline | Żaden węzeł nie jest w trybie online. |
| Ograniczone | Nie wszystkie węzły w tym samym środowisku Integration Runtime są w dobrej kondycji. Ten stan jest ostrzeżeniem, że niektóre węzły mogą nie działać. Ten stan może być spowodowany problemem z synchronizacją poświadczeń w węźle dyspozytora/proces roboczy. |

Użyj polecenia cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** , aby pobrać ładunek JSON zawierający szczegółowe właściwości samodzielnego środowiska Integration Runtime oraz ich wartości migawek w czasie wykonywania polecenia cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Przykładowe dane wyjściowe (zakłada, że istnieją dwa węzły skojarzone z tym własnym środowiskiem Integration Runtime):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS Integration Runtime
Azure-SSIS Integration Runtime to w pełni zarządzany klaster maszyn wirtualnych platformy Azure (lub węzłów) przeznaczonych do uruchamiania pakietów SSIS. Nie działają żadne inne działania Azure Data Factory. Po udostępnieniu programu można wykonywać zapytania dotyczące jego właściwości oraz monitorować jego wszystkie Stany.

### <a name="properties"></a>Właściwości

| Właściwość/stan | Opis |
| --------------- | ----------- |
| Czas trwania | Czas UTC utworzenia środowiska Azure-SSIS Integration Runtime. |
| Węzły | Przydzielono lub dostępne węzły środowiska Azure-SSIS Integration Runtime ze Stanami specyficznymi dla węzła (uruchamianie/dostępne/odtwarzanie/niedostępne) i błędami do wykonania. |
| OtherErrors | Błędy bez określonego węzła w środowisku Azure-SSIS Integration Runtime. |
| LastOperation | Wynik ostatniej operacji uruchamiania/zatrzymywania na platformie Azure-SSIS Integration Runtime z błędami, które można wykonać, jeśli nie powiodło się. |
| Stan | Ogólny stan (początkowy/początkowy/uruchomiony/zatrzymywanie/zatrzymana) środowiska Azure-SSIS Integration Runtime. |
| Lokalizacja | Lokalizacja środowiska Azure-SSIS Integration Runtime. |
| NodeSize | Rozmiar każdego węzła środowiska Azure-SSIS Integration Runtime. |
| NodeCount | Liczba węzłów w środowisku Azure-SSIS Integration Runtime. |
| MaxParallelExecutionsPerNode | Liczba równoległych wykonań na węzeł w środowisku Azure-SSIS Integration Runtime. |
| CatalogServerEndpoint | Punkt końcowy istniejącego serwera wystąpienia Azure SQL Databaseowego/zarządzanego, który będzie hostować SSISDB. |
| CatalogAdminUserName | Nazwa użytkownika administratora istniejącego serwera wystąpienia Azure SQL Database/zarządzanego. Usługa Data Factory używa tych informacji do przygotowania i zarządzania usługą SSISDB w Twoim imieniu. |
| CatalogAdminPassword | Hasło administratora istniejącego serwera wystąpienia Azure SQL Database/zarządzanego. |
| CatalogPricingTier | Warstwa cenowa usługi SSISDB hostowana przez istniejący serwer Azure SQL Database.  Nie ma zastosowania do Azure SQL Database hostingu wystąpienia zarządzanego SSISDB. |
| VNetId | Identyfikator zasobu sieci wirtualnej dla środowiska Azure-SSIS Integration Runtime do przyłączenia. |
| Podsieć | Nazwa podsieci dla środowiska Azure-SSIS Integration Runtime do przyłączenia. |
| ID | Identyfikator zasobu środowiska Azure-SSIS Integration Runtime. |
| Typ | Typ (zarządzany/własny) środowiska Azure-SSIS Integration Runtime. |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w której utworzono fabrykę danych i środowisko Azure-SSIS Integration Runtime. |
| DataFactoryName | Nazwa usługi Azure Data Factory. |
| Nazwa | Nazwa środowiska Azure-SSIS Integration Runtime. |
| Opis | Opis środowiska Azure-SSIS Integration Runtime. |

  
### <a name="status-per-node"></a>Stan (na węzeł)

| Stan | Opis |
| ------ | ----------- | 
| Uruchamianie | Trwa przygotowywanie tego węzła. |
| Dostępne | Ten węzeł jest gotowy do wdrażania/wykonywania pakietów usług SSIS. |
| Konfigurowaln | Ten węzeł jest naprawiany/uruchamiany ponownie. |
| Niedostępny | Ten węzeł nie jest gotowy do wdrażania/wykonywania pakietów usług SSIS i zawiera błędy, które można rozwiązać. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stan (ogólne środowisko Azure-SSIS Integration Runtime)

| Stan ogólny | Opis | 
| -------------- | ----------- | 
| Wartość początkowa | Węzły środowiska Azure-SSIS Integration Runtime nie zostały przydzieloną/przygotowane. | 
| Uruchamianie | Węzły środowiska Azure-SSIS Integration Runtime są przydzieleni/przygotowane i rozliczenia zostały rozpoczęte. |
| Rozpoczęto | Węzły środowiska Azure-SSIS Integration Runtime zostały przyłączone/przygotowane i są gotowe do wdrażania/wykonywania pakietów SSIS. |
| Zatrzymywanie  | Są udostępniane węzły środowiska Azure-SSIS Integration Runtime. |
| Zatrzymano | Węzły środowiska Azure-SSIS Integration Runtime zostały wydane i rozliczenia zostały zatrzymane. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitoruj środowisko Azure-SSIS Integration Runtime w Azure Portal

Poniższe zrzuty ekranu pokazują, jak wybrać Azure-SSIS IR do monitorowania, i podać przykład wyświetlanych informacji.

![Wybierz środowisko Azure-SSIS Integration Runtime do monitorowania](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Wyświetlanie informacji o środowisku Azure-SSIS Integration Runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorowanie środowiska Azure-SSIS Integration Runtime przy użyciu programu PowerShell

Użyj skryptu, takiego jak Poniższy przykład, aby sprawdzić stan Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Więcej informacji na temat środowiska Azure SSIS Integration Runtime

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat środowiska Azure-SSIS Integration Runtime:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne dotyczące środowiska Integration Runtime, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL Database i dołączania do sieci wirtualnej. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Przedstawiono w nim również kroki Azure Portal służące do konfigurowania sieci wirtualnej w taki sposób, aby Azure-SSIS IR mógł dołączyć do sieci wirtualnej. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi monitorowania potoków na różne sposoby: 

- [Szybki Start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md).
- [Używanie Azure Monitor do monitorowania potoków Data Factory](monitor-using-azure-monitor.md)
