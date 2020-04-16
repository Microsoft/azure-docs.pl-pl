---
title: Monitorowanie środowiska wykonawczego integracji w usłudze Azure Data Factory
description: Dowiedz się, jak monitorować różne typy środowiska wykonawczego integracji w usłudze Azure Data Factory.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398963"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitor an integration runtime in Azure Data Factory (Monitorowanie środowiska Integration Runtime w usłudze Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Środowisko wykonawcze integracji** to infrastruktura obliczeniowa używana przez usługę Azure Data Factory w celu zapewnienia różnych możliwości integracji danych w różnych środowiskach sieciowych. Istnieją trzy typy uruchomień integracji oferowanych przez fabrykę danych:

- Środowisko uruchomieniowe integracji Azure
- Infrastruktura Integration Runtime (Self-hosted)
- Azure SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby uzyskać stan wystąpienia środowiska uruchomieniowego integracji (IR), uruchom następujące polecenie programu PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Polecenie cmdlet zwraca różne informacje dla różnych typów środowiska wykonawczego integracji. W tym artykule opisano właściwości i stany dla każdego typu środowiska wykonawczego integracji.  

## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure
Zasób obliczeniowy dla środowiska wykonawczego integracji platformy Azure jest w pełni zarządzany elastycznie na platformie Azure. Poniższa tabela zawiera opisy właściwości zwracanych przez polecenie **Get-AzDataFactoryV2IntegrationRuntime:**

### <a name="properties"></a>Właściwości
Poniższa tabela zawiera opisy właściwości zwracanych przez polecenie cmdlet dla środowiska wykonawczego integracji platformy Azure:

| Właściwość | Opis |
-------- | ------------- | 
| Nazwa | Nazwa środowiska wykonawczego integracji platformy Azure. |  
| Stan | Stan środowiska wykonawczego integracji platformy Azure. | 
| Lokalizacja | Lokalizacja środowiska wykonawczego integracji platformy Azure. Aby uzyskać szczegółowe informacje na temat lokalizacji środowiska wykonawczego integracji platformy Azure, zobacz [Wprowadzenie do środowiska wykonawczego integracji.](concepts-integration-runtime.md) |
| Nazwa danych | Nazwa fabryki danych, do której należy środowisko wykonawcze integracji platformy Azure. | 
| ResourceGroupName | Nazwa grupy zasobów, do której należy fabryka danych.  |
| Opis | Opis środowiska wykonawczego integracji.  |

### <a name="status"></a>Stan
Poniższa tabela zawiera możliwe stany środowiska wykonawczego integracji platformy Azure:

| Stan | Komentarze/scenariusze | 
| ------ | ------------------ |
| Online | Środowisko wykonawcze integracji platformy Azure jest w trybie online i gotowe do użycia. | 
| W trybie offline | Środowisko wykonawcze integracji platformy Azure jest w trybie offline z powodu błędu wewnętrznego. |

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)
Ta sekcja zawiera opisy właściwości zwracanych przez polecenie cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Zwrócone właściwości i stan zawierają informacje o ogólnym czasie wykonywania integracji hostowanego samodzielnie i każdym węźle w czasie wykonywania.  

### <a name="properties"></a>Właściwości

Poniższa tabela zawiera opisy właściwości monitorowania dla **każdego węzła:**

| Właściwość | Opis | 
| -------- | ----------- | 
| Nazwa | Nazwa środowiska uruchomieniowego integracji hostowanego samodzielnie i skojarzonych z nim węzłów. Węzeł jest lokalnym komputerem z systemem Windows, który ma zainstalowane środowisko uruchomieniowe integracji hostowanego samodzielnie. |  
| Stan | Stan ogólnego środowiska uruchomieniowego integracji hostowanego samodzielnie i każdego węzła. Przykład: Online / Offline / Limited / itp. Aby uzyskać informacje na temat tych stanów, zobacz następną sekcję. | 
| Wersja | Wersja środowiska wykonawczego integracji hostowanego samodzielnie i każdego węzła. Wersja środowiska wykonawczego integracji hostowanego samodzielnie jest określana na podstawie wersji większości węzłów w grupie. Jeśli istnieją węzły z różnymi wersjami w konfiguracji środowiska uruchomieniowego integracji hostowanej samodzielnie, poprawnie działają tylko węzły o tym samym numerze wersji, co funkcja środowiska wykonawczego integracji logicznej. Inne są w trybie ograniczonym i muszą być ręcznie aktualizowane (tylko w przypadku, gdy automatyczna aktualizacja nie powiedzie się). | 
| Dostępna pamięć | Dostępna pamięć w węźle środowiska uruchomieniowego integracji hostowanej samodzielnie. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. | 
| Wykorzystanie procesora | Wykorzystanie procesora CPU w węźle środowiska uruchomieniowego integracji hostowanego przez własny host. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. |
| Sieć (wsuwanie/wyjęcie) | Wykorzystanie sieci węzła wykonawczego integracji hostowanego samodzielnie. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. | 
| Równoczesne zadania (uruchamianie/ limit) | **Bieganie**. Liczba zadań lub zadań uruchomionych w każdym węźle. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. <br/><br/>**Limit**. Limit oznacza maksymalną liczbę równoczesnych zadań dla każdego węzła. Ta wartość jest definiowana na podstawie rozmiaru maszyny. Można zwiększyć limit skalowania w górę równoczesnych zadań w zaawansowanych scenariuszach, gdy działania są limit czasu, nawet wtedy, gdy procesor CPU, pamięci lub sieci jest niedostateczone. Ta funkcja jest również dostępna w czasie wykonywania integracji z jednym węzłem. |
| Rola | Istnieją dwa typy ról w wielowęzłowym środowisko uruchomieniowe integracji hostowane przez własny — dyspozytor i pracownik. Wszystkie węzły są pracownikami, co oznacza, że wszystkie mogą być używane do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do ściągania zadań/zadań z usług w chmurze i wysyłania ich do różnych węzłów procesu roboczego. Węzeł dyspozytora jest również węzłem roboczym. |

Niektóre ustawienia właściwości mają większy sens, gdy istnieją dwa lub więcej węzłów w środowisku uruchomieniowym integracji hostowanego samodzielnie (czyli w scenariuszu skalowania w poziomie).

#### <a name="concurrent-jobs-limit"></a>Limit równoczesnych zadań

Domyślna wartość limitu równoczesnych zadań jest ustawiana na podstawie rozmiaru komputera. Czynniki używane do obliczania tej wartości zależą od ilości pamięci RAM i liczby rdzeni procesora komputera. Im więcej rdzeni i więcej pamięci, tym wyższy domyślny limit równoczesnych zadań.

Skalowanie w poziomie przez zwiększenie liczby węzłów. Po zwiększeniu liczby węzłów limit równoczesnych zadań jest sumą równoczesnych wartości granicznych zadań wszystkich dostępnych węzłów.  Na przykład jeśli jeden węzeł umożliwia uruchomienie maksymalnie dwunastu równoczesnych zadań, a następnie dodanie trzech podobnych węzłów umożliwia uruchomienie maksymalnie 48 równoczesnych zadań (czyli 4 x 12). Zaleca się zwiększenie limitu równoczesnych zadań tylko wtedy, gdy widzisz niskie użycie zasobów z wartościami domyślnymi w każdym węźle.

Obliczoną wartość domyślną można zastąpić w witrynie Azure Portal. Wybierz pozycję Autor > Connections > Integration Runtimes > Edit > Nodes > Modify concurrent job value per node . Można również użyć polecenia [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) programu PowerShell.
  
### <a name="status-per-node"></a>Stan (na węzeł)
Poniższa tabela zawiera możliwe stany węzła wykonawczego integracji hostowanego samodzielnie:

| Stan | Opis |
| ------ | ------------------ | 
| Online | Węzeł jest połączony z usługą Data Factory. |
| W trybie offline | Węzeł jest w trybie offline. |
| Uaktualnianie | Węzeł jest automatycznie aktualizowany. |
| Ograniczone | Z powodu problemu z łącznością. Może to być spowodowane problemem z portem HTTP 8050, problemem z łącznością magistrali usług lub problemem synchronizacji poświadczeń. |
| Nieaktywne | Węzeł jest w konfiguracji różni się od konfiguracji innych węzłów większościowych. |

Węzeł może być nieaktywny, gdy nie może połączyć się z innymi węzłami.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stan (ogólne środowisko uruchomieniowe integracji hostowanego samodzielnie)
Poniższa tabela zawiera możliwe stany środowiska uruchomieniowego integracji hostowanego samodzielnie. Ten stan zależy od stanów wszystkich węzłów, które należą do środowiska wykonawczego. 

| Stan | Opis |
| ------ | ----------- | 
| Potrzebujesz rejestracji | Żaden węzeł nie jest jeszcze zarejestrowany w tym własnym czasie wykonywania integracji. |
| Online | Wszystkie węzły są w trybie online. |
| W trybie offline | Żaden węzeł nie jest w trybie online. |
| Ograniczone | Nie wszystkie węzły w tym własnym czasie wykonywania integracji są w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzły mogą być w dół. Ten stan może być spowodowany problemem synchronizacji poświadczeń w węźle dyspozytor/pracownik. |

Użyj polecenia cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetricMetric,** aby pobrać ładunek JSON zawierający szczegółowe właściwości środowiska uruchomieniowego integracji hostowanego samodzielnie i ich wartości migawki w czasie wykonywania polecenia cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Przykładowe dane wyjściowe (zakłada, że istnieją dwa węzły skojarzone z tym środowiskom uruchomieniowym integracji hostowanego samodzielnie):

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
Środowisko uruchomieniowe integracji platformy Azure-SSIS to w pełni zarządzany klaster maszyn wirtualnych (lub węzłów) platformy Azure przeznaczony do uruchamiania pakietów SSIS. Nie uruchamia żadnych innych działań usługi Azure Data Factory. Po zainicjowaniu obsługi administracyjnej można zbadać jego właściwości i monitorować jego stany ogólne/specyficzne dla węzła.

### <a name="properties"></a>Właściwości

| Właściwość/stan | Opis |
| --------------- | ----------- |
| Czas tworzenia | Czas UTC podczas tworzenia środowiska wykonawczego integracji azure-SSIS. |
| Węzły | Przydzielone/dostępne węzły środowiska wykonawczego integracji azure-SSIS ze stanami specyficznymi dla węzła (uruchamianie/dostępne/recykling/niedostępne) i błędy zasłaniające. |
| InneErrory | Błędy niespecyfowalne specyficzne dla węzła w czasie wykonywania integracji usługi Azure-SSIS. |
| OstatniOperacja | Wynik ostatniej operacji start/stop w czasie wykonywania integracji azure-SSIS z błędami podlegania działaniu, jeśli nie powiodło się. |
| Stan | Ogólny stan (początkowy/początkowy/uruchomiony/zatrzymywany/zatrzymany) środowiska wykonawczego integracji platformy Azure-SSIS. |
| Lokalizacja | Lokalizacja środowiska wykonawczego integracji platformy Azure-SSIS. |
| Rozmiar węzła | Rozmiar każdego węzła środowiska wykonawczego integracji azure-SSIS. |
| Liczba węzłów | Liczba węzłów w czasie wykonywania integracji usługi Azure-SSIS. |
| MaxParallelWykonańPrzezNożenie | Liczba równoległych wykonań na węzeł w czasie wykonywania integracji platformy Azure-SSIS. |
| Punkt katalogowyServerEndpoint | Punkt końcowy istniejącego serwera usługi Azure SQL Database/Managed Instance do hostowania usługi SSISDB. |
| Nazwa katalogowaAdminUsername | Nazwa użytkownika administratora istniejącego serwera usługi Azure SQL Database/Managed Instance. Usługa Data Factory używa tych informacji do przygotowania usługi SSISDB i zarządzania nią w Twoim imieniu. |
| KatalogAdminPassword | Hasło administratora istniejącego serwera usługi Azure SQL Database/Managed Instance. |
| CatalogPricingTier (Serwer cen katalogowych) | Warstwa cenowa dla usługi SSISDB hostowana przez istniejący serwer bazy danych SQL azure.  Nie dotyczy wystąpienia zarządzanego usługi Azure SQL Database obsługującego usługę SSISDB. |
| Wirtualnajdz. | Identyfikator zasobu sieci wirtualnej dla środowiska wykonawczego integracji platformy Azure-SSIS do dołączenia. |
| Podsieć | Nazwa podsieci środowiska wykonawczego integracji platformy Azure-SSIS do dołączenia. |
| ID | Identyfikator zasobu środowiska wykonawczego integracji platformy Azure-SSIS. |
| Typ | Typ (zarządzany/hostowany samodzielnie) środowiska wykonawczego integracji platformy Azure-SSIS. |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w której utworzono fabrykę danych i środowisko uruchomieniowe integracji platformy Azure-SSIS. |
| Nazwa danych | Nazwa fabryki danych platformy Azure. |
| Nazwa | Nazwa środowiska wykonawczego integracji azure-SSIS. |
| Opis | Opis środowiska wykonawczego integracji platformy Azure-SSIS. |

  
### <a name="status-per-node"></a>Stan (na węzeł)

| Stan | Opis |
| ------ | ----------- | 
| Uruchamianie | Ten węzeł jest przygotowywany. |
| Dostępne | Ten węzeł jest gotowy do wdrażania/wykonywania pakietów SSIS. |
| Recyklingu | Ten węzeł jest naprawiany/uruchamiany ponownie. |
| Niedostępny | Ten węzeł nie jest gotowy do wdrożenia/wykonania pakietów SSIS i ma błędy/problemy, które można rozwiązać. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stan (ogólny czas wykonywania integracji platformy Azure-SSIS)

| Ogólny stan | Opis | 
| -------------- | ----------- | 
| Wartość początkowa | Węzły środowiska wykonawczego integracji azure-SSIS nie zostały przydzielone/przygotowane. | 
| Uruchamianie | Węzły środowiska wykonawczego integracji platformy Azure-SSIS są przydzielane/przygotowywane i rozpoczyna się rozliczanie. |
| Rozpoczęto | Węzły środowiska wykonawczego integracji platformy Azure-SSIS zostały przydzielone/przygotowane i są gotowe do wdrożenia/wykonania pakietów SSIS. |
| Zatrzymywanie  | Węzły środowiska wykonawczego integracji azure-SSIS są zwalniane. |
| Zatrzymano | Węzły środowiska wykonawczego integracji platformy Azure-SSIS zostały zwolnione, a rozliczenia zostały zatrzymane. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorowanie środowiska uruchomieniowego integracji azure-SSIS w witrynie Azure portal

Poniższe zrzuty ekranu pokazują, jak wybrać identyfikator Azure-SSIS IR do monitorowania i podać przykład informacji, które są wyświetlane.

![Wybierz środowisko uruchomieniowe integracji platformy Azure-SSIS do monitorowania](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Wyświetlanie informacji o środowiskou wykonywania integracji azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorowanie środowiska uruchomieniowego integracji platformy Azure-SSIS za pomocą programu PowerShell

Użyj skryptu, takiego jak w poniższym przykładzie, aby sprawdzić stan usługi Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Więcej informacji o środowiskou uruchomieniowym integracji azure-SSIS

Zobacz następujące artykuły, aby dowiedzieć się więcej o środowiskou uruchomieniowym integracji platformy Azure-SSIS:

- [Środowisko uruchomieniowe integracji platformy Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje koncepcyjne dotyczące środowiska wykonawczego integracji w ogóle, w tym azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozwija się w samouczku i zawiera instrukcje dotyczące korzystania z wystąpienia zarządzanego bazy danych SQL azure i dołączania do podczerwenia do sieci wirtualnej. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Zawiera również kroki, aby użyć witryny Azure portal, aby skonfigurować sieć wirtualną, tak aby usługa Azure-SSIS IR mogła dołączyć do sieci wirtualnej. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły do monitorowania potoków na różne sposoby: 

- [Szybki start: tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md).
- [Monitorowanie potoków fabryki danych za pomocą usługi Azure Monitor](monitor-using-azure-monitor.md)
