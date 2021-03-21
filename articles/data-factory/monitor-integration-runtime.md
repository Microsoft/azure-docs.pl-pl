---
title: Monitorowanie środowiska Integration Runtime w Azure Data Factory
description: Dowiedz się, jak monitorować różne typy środowiska Integration Runtime w Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 1cb4fcaa51e1a59ee9d09eb178faf9b250173709
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740033"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitor an integration runtime in Azure Data Factory (Monitorowanie środowiska Integration Runtime w usłudze Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration Runtime** to infrastruktura obliczeniowa używana przez Azure Data Factory (ADF) do zapewniania różnych możliwości integracji danych w różnych środowiskach sieciowych. Istnieją trzy typy środowisk Integration Runtime oferowanych przez Data Factory:

- Środowisko uruchomieniowe integracji Azure
- Infrastruktura Integration Runtime (Self-hosted)
- Azure-SQL Server Integration Services (SSIS) Integration Runtime

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
| Tryb online | Środowisko Azure Integration Runtime jest w trybie online i gotowe do użycia. | 
| Tryb offline | Środowisko Azure Integration Runtime jest w trybie offline z powodu błędu wewnętrznego. |

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

Obliczoną wartość domyślną można zastąpić w Azure Portal. Wybierz pozycję Utwórz > połączenia > środowiska Integration Runtime > Edytuj węzły > > zmodyfikuj wartość zadania współbieżnego na węzeł. Można również użyć polecenia [Update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) programu PowerShell.
  
### <a name="status-per-node"></a>Stan (na węzeł)

W poniższej tabeli przedstawiono możliwe stany własnego węzła Integration Runtime:

| Stan | Opis |
| ------ | ------------------ | 
| Tryb online | Węzeł jest połączony z usługą Data Factory. |
| Tryb offline | Węzeł jest w trybie offline. |
| Uaktualnianie | Węzeł jest aktualizowany w sposób autouzupełniania. |
| Ograniczone | Z powodu problemu z łącznością. Może to być spowodowane problemem z portem HTTP 8060, problemem z łącznością usługi Service Bus lub problemem z synchronizacją poświadczeń. |
| Nieaktywne | Węzeł jest w konfiguracji innej niż Konfiguracja innych węzłów większości. |

Węzeł może być nieaktywny, jeśli nie może połączyć się z innymi węzłami.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stan (ogólne środowisko Integration Runtime)

W poniższej tabeli przedstawiono możliwe stany środowiska Integration Runtime (samodzielne). Ten stan zależy od stanu wszystkich węzłów należących do środowiska uruchomieniowego. 

| Stan | Opis |
| ------ | ----------- | 
| Potrzebna rejestracja | Nie zarejestrowano jeszcze żadnego węzła w tym środowisku Integration Runtime. |
| Tryb online | Wszystkie węzły są w trybie online. |
| Tryb offline | Żaden węzeł nie jest w trybie online. |
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

Azure-SSIS IR to w pełni zarządzany klaster maszyn wirtualnych platformy Azure (maszyn wirtualnych lub węzłów) przeznaczonych do uruchamiania pakietów usług SSIS. Można wywołać wykonywanie pakietów SSIS na Azure-SSIS IR przy użyciu różnych metod, na przykład za pośrednictwem platformy Azure SQL Server Data Tools (SSDT), AzureDTExec narzędzia wiersza polecenia, T-SQL w systemie SQL Server Management Studio (SSMS)/SQL Server Agent oraz wykonywać działania pakietu SSIS w potokach ADF. W Azure-SSIS IR nie są uruchamiane żadne inne działania ADF. Po zainicjowaniu obsługi można monitorować jego ogólne/właściwości i Stany zależne od Azure PowerShell, Azure Portal i Azure Monitor.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Monitoruj środowisko Azure-SSIS Integration Runtime przy użyciu Azure PowerShell

Użyj następującego polecenia cmdlet Azure PowerShell, aby monitorować właściwości i Stany Azure-SSIS IR specyficzne dla węzła.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Właściwości

Poniższa tabela zawiera opisy właściwości zwracanych przez powyższe polecenie cmdlet dla Azure-SSIS IR.

| Właściwość/stan              | Opis                  |
| ---------------------------- | ---------------------------- |
| Czas trwania                   | Czas UTC utworzenia Azure-SSIS IR. |
| Węzły                        | Przydzielono lub dostępne węzły Azure-SSIS IR z Stanami specyficznymi dla węzła (początkowymi/dostępnymi/odtwarzanymi/niedostępnymi) i błędami do wykonania. |
| OtherErrors                  | Błędy niespecyficzne dla węzła w Azure-SSIS IR. |
| LastOperation                | Wynik ostatniej operacji uruchamiania/zatrzymywania na Azure-SSIS IR z błędami, które można wykonać, jeśli to się nie powiodło. |
| Stan                        | Ogólny stan (początkowy/początkowy/uruchomiony/zatrzymywanie/zatrzymana) Azure-SSIS IR. |
| Lokalizacja                     | Lokalizacja Azure-SSIS IR. |
| NodeSize                     | Rozmiar każdego węzła w Azure-SSIS IR. |
| NodeCount                    | Liczba węzłów w Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | Maksymalna liczba równoległych wykonań na węzeł w Azure-SSIS IR. |
| CatalogServerEndpoint        | Punkt końcowy istniejącego serwera Azure SQL Database lub wystąpienia zarządzanego do hostowania usług SSIS Catalog (SSISDB). |
| CatalogAdminUserName         | Nazwa użytkownika administratora dla istniejącego serwera Azure SQL Database lub wystąpienia zarządzanego. Funkcja ADF używa tych informacji do przygotowywania SSISDB w Twoim imieniu i zarządzania nimi. |
| CatalogAdminPassword         | Hasło administratora istniejącego serwera Azure SQL Database lub wystąpienia zarządzanego. |
| CatalogPricingTier           | Warstwa cenowa usługi SSISDB hostowana przez Azure SQL Database Server.  Nie dotyczy SSISDB hostingu wystąpienia zarządzanego Azure SQL. |
| VNetId                       | Identyfikator zasobu sieci wirtualnej dla Azure-SSIS IR do przyłączenia. |
| Podsieć                       | Nazwa podsieci do przyłączenia do Azure-SSIS IR. |
| ID (Identyfikator)                           | Identyfikator zasobu Azure-SSIS IR. |
| Typ                         | Typ IR (zarządzany/samoobsługowy) Azure-SSIS IR. |
| ResourceGroupName            | Nazwa grupy zasobów platformy Azure, w której zostały utworzone zasoby ADF i Azure-SSIS IR. |
| DataFactoryName              | Nazwa Twojego ADF. |
| Nazwa                         | Nazwa Azure-SSIS IR. |
| Opis                  | Opis Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Stan (na Azure-SSIS IR węzeł)

W poniższej tabeli przedstawiono możliwe stany węzła Azure-SSIS IR:

| Stan specyficzny dla węzła | Opis |
| -------------------- | ----------- | 
| Uruchamianie             | Trwa przygotowywanie tego węzła. |
| Dostępne            | Ten węzeł jest gotowy do wdrażania/wykonywania pakietów usług SSIS. |
| Konfigurowaln            | Ten węzeł jest naprawiany/uruchamiany ponownie. |
| Niedostępny          | Ten węzeł nie jest gotowy do wdrażania/wykonywania pakietów usług SSIS i zawiera błędy, które można rozwiązać. |

#### <a name="status-overall-azure-ssis-ir"></a>Stan (ogólne Azure-SSIS IR)

W poniższej tabeli przedstawiono możliwe ogólne Stany Azure-SSIS IR. Ogólny stan z kolei zależy od połączonych stanów wszystkich węzłów, które należą do Azure-SSIS IR. 

| Stan ogólny | Opis | 
| -------------- | ----------- | 
| Wartość początkowa        | Węzły Azure-SSIS IR nie zostały przydzieloną/przygotowane. | 
| Uruchamianie       | Węzły Azure-SSIS IR są przydzielenia/przygotowywane i rozliczenia rozpoczęte. |
| Rozpoczęto        | Węzły Azure-SSIS IR zostały przydzieloną/przygotowane i są gotowe do wdrażania/wykonywania pakietów SSIS. |
| Zatrzymywanie       | Węzły Azure-SSIS IR są udostępniane. |
| Zatrzymano        | Węzły Azure-SSIS IR zostały wydane i rozliczenia zostały zatrzymane. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Monitoruj środowisko Azure-SSIS Integration Runtime w Azure Portal

Aby monitorować Azure-SSIS IR w Azure Portal, przejdź do strony **Integration Runtimes** ( **monitorowanie** centrum w INTERFEJSie użytkownika funkcji ADF), gdzie można zobaczyć wszystkie środowiska Integration Runtime.

![Monitoruj wszystkie środowiska Integration Runtime](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Następnie wybierz nazwę Azure-SSIS IR, aby otworzyć jej stronę monitorowania, w której można zobaczyć jej właściwości i Stany ogólne/specyficzne dla węzła. Na tej stronie, w zależności od sposobu konfigurowania ustawień ogólnych, wdrażania i zaawansowanego Azure-SSIS IR, znajdziesz różne kafelki informacyjne/funkcjonalne.

Kafelki informacyjne **typu** i **regionu** pokazują odpowiednio typ i region Azure-SSIS IR.

Kafelek informacje o **rozmiarze węzła** przedstawia jednostkę SKU (edition_VM SSIS tier_VM Series), liczbę rdzeni procesora CPU i rozmiar pamięci RAM dla węzła Azure-SSIS IR. 

Kafelek z informacjami o **uruchomionym/żądanym węźle** porównuje liczbę węzłów aktualnie uruchomionych z łączną liczbą węzłów, które wcześniej zażądały w odniesieniu do Azure-SSIS IR.

**Podwójna para w stanie wstrzymania/** kafelek z informacjami o rolach zawiera nazwę podwójnej pary Azure-SSIS IR w stanie wstrzymania, która działa w ramach synchronizacji z grupą trybu failover wystąpienia Azure SQL Database/zarządzanego w celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR) oraz bieżącą główną/pomocniczą rolę Azure-SSIS IR. W przypadku przełączenia SSISDB w tryb failover podstawowy i pomocniczy urząd platformy Azure — SSIS (zobacz [konfigurowanie Azure-SSIS IR dla BCDR](./configure-bcdr-azure-ssis-integration-runtime.md)).

Kafelki funkcjonalne są opisane poniżej.

![Monitoruj Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>Kafelek stanu

Na kafelku **stan** strony monitorowania Azure-SSIS IR można zobaczyć jej ogólny stan, na przykład **uruchomioną** lub **zatrzymaną**. Wybranie stanu **działania** powoduje wyczyszczenie okna z aktywnym przyciskiem **Zatrzymaj** , aby zatrzymać Azure-SSIS IR. Zaznaczenie pola stan **zatrzymania** powoduje wyczyszczenie okna z aktywnym przyciskiem **Start** , aby rozpocząć Azure-SSIS IR. W oknie podręcznym jest również dostępny przycisk **Wykonaj pakiet SSIS** , aby automatycznie wygenerować potok ADF z działaniem pakietu SSIS, które jest uruchamiane na Azure-SSIS IR (zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach APD](./how-to-invoke-ssis-package-ssis-activity.md)) i pole tekstowe **Identyfikator zasobu** , w którym można skopiować identyfikator zasobu Azure-SSIS IR ( `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` ). Sufiks identyfikatora zasobu Azure-SSIS IR, który zawiera pliki ADF i Azure-SSIS IR, stanowi identyfikator klastra, którego można użyć do zakupu dodatkowych składników usług SSIS/licencjonowanych w warstwie Premium od niezależnych dostawców oprogramowania (ISV) i powiązać je z Azure-SSIS IR (zobacz [Instalowanie składników Premium/licencjonowanych w Azure-SSIS IR](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![Monitorowanie kafelka Azure-SSIS IR-STATUS](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Kafelek punktu końcowego serwera SSISDB

W przypadku używania modelu wdrażania projektu, w którym pakiety są przechowywane w SSISDB hostowanym przez serwer Azure SQL Database lub wystąpienie zarządzane, na stronie monitorowanie Azure-SSIS IR można zobaczyć kafelek **punktu końcowego serwera SSISDB** (zobacz [konfigurowanie ustawień wdrożenia Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Na tym kafelku można wybrać łącze wskazujące serwer Azure SQL Database lub wystąpienie zarządzane, aby wyskakujące okno, w którym można skopiować punkt końcowy serwera z pola tekstowego i użyć go podczas łączenia się z programu SSMS w celu wdrażania, konfigurowania, uruchamiania i zarządzania pakietami. W oknie podręcznym możesz również wybrać łącze **Sprawdź ustawienia Azure SQL Database lub wystąpienia zarządzanego** , aby ponownie skonfigurować/zmienić rozmiar SSISDB w Azure Portal.

![Monitorowanie kafelka Azure-SSIS IR-SSISDB](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Kafelek PROXY/PRZEMIESZCZAnie

Jeśli pobierzesz, zainstalujesz i skonfigurujesz Self-Hosted IR (SHIR) jako serwer proxy dla Azure-SSIS IR dostępu do danych lokalnie, zobaczysz kafelek **serwer proxy/przemieszczanie** na stronie monitorowania Azure-SSIS IR (zobacz [Konfigurowanie SHIR jako serwera proxy dla Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)). Na tym kafelku możesz wybrać łącze wyznaczające SHIR, aby otworzyć jego stronę monitorowania. Możesz również wybrać inny link wskazujący Blob Storage platformy Azure na potrzeby przygotowania, aby ponownie skonfigurować jego połączoną usługę.

#### <a name="validate-vnet--subnet-tile"></a>Sprawdź poprawność kafelka sieci wirtualnej/podsieci

Jeśli dołączysz Azure-SSIS IR do sieci wirtualnej, zobaczysz kafelek **Weryfikuj sieć wirtualną/podsieć** na stronie monitorowania Azure-SSIS IR (zobacz [przyłączanie Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md)). Na tym kafelku można wybrać link wyznaczania sieci wirtualnej i podsieci, aby wyskakujące okno, w którym można skopiować identyfikator zasobu sieci wirtualnej ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` ) i nazwę podsieci z pól tekstowych, a także sprawdzić poprawność konfiguracji sieci wirtualnej i podsieci, aby upewnić się, że wymagane jest niezakłócony ruch przychodzący i niezależny oraz zarządzanie Azure-SSIS IRami.

![Monitoruj Azure-SSIS IR — Sprawdź poprawność kafelka](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>Diagnozuj kafelek łączności

Na kafelku **Diagnostyka łączności** na stronie monitorowania Azure-SSIS IR możesz wybrać link **Test connection** , aby wypróbować okno, w którym można sprawdzić połączenia między Azure-SSIS IR i odpowiednimi pakietami/konfiguracjami/magazynami danych, a także usługami zarządzania, za pośrednictwem ich w pełni kwalifikowanej nazwy domeny (FQDN)/IP i wystawionego portu (zobacz [Testowanie połączeń z Azure-SSIS IR](./ssis-integration-runtime-diagnose-connectivity-faq.md)).

![Monitorowanie kafelka Azure-SSIS IR-Diagnozuj](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Kafelek statyczne publiczne adresy IP

W przypadku wprowadzenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR zobaczysz kafelek **statyczne publiczne adresy IP** na stronie monitorowania Azure-SSIS IR (zobacz temat Przenoszenie [własnych statycznych adresów IP dla Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)). Na tym kafelku można wybrać linki wyznaczające pierwsze/drugie statyczne publiczne adresy IP dla Azure-SSIS IR, aby wyskakujące okno, w którym można skopiować swój identyfikator zasobu ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` ) z pola tekstowego. W oknie podręcznym możesz również wybrać łącze **Zobacz ustawienia pierwszego/drugiego statycznego publicznego adresu IP** , aby zarządzać pierwszym/drugim statycznym publicznym adresem ip w Azure Portal.

![Monitorowanie kafelka Azure-SSIS IR-STATIC](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Kafelek MAGAZYNów pakietów

Jeśli używasz modelu wdrażania pakietów, w którym pakiety są przechowywane w systemie plików/Azure Files/SQL Server Database (MSDB) hostowanym przez wystąpienie zarządzane usługi Azure SQL i zarządzane za pośrednictwem Azure-SSIS IR magazynów pakietów, zobaczysz kafelek **magazyny pakietów** na stronie monitorowania Azure-SSIS IR (zobacz [konfigurowanie ustawień wdrożenia Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Na tym kafelku można wybrać łącze wyznaczające liczbę magazynów pakietów dołączonych do Azure-SSIS IR, aby wyskakujące okno, w którym można ponownie skonfigurować odpowiednie połączone usługi dla Azure-SSIS IR magazynów pakietów w systemie plików/Azure Files/MSDB hostowanej przez wystąpienie zarządzane Azure SQL.

![Monitorowanie kafelka Azure-SSIS IR — pakiet](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>Kafelek błędów

Jeśli wystąpią problemy z uruchamianiem/zatrzymywaniem/konserwacją/uaktualnieniem Azure-SSIS IR, na stronie monitorowania Azure-SSIS IR zobaczysz dodatkową tabliczkę **błędów** . Na tym kafelku można wybrać łącze wyznaczające liczbę błędów wygenerowanych przez Azure-SSIS IR, aby wypróbować okno, w którym można zobaczyć te błędy w bardziej szczegółowych i skopiować je w celu znalezienia zalecanych rozwiązań w naszym przewodniku rozwiązywania problemów (zobacz [Rozwiązywanie problemów z Azure-SSIS IR](./ssis-integration-runtime-management-troubleshoot.md)).

![Monitoruj kafelek Azure-SSIS IR — błąd](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Monitoruj środowisko Azure-SSIS Integration Runtime przy użyciu Azure Monitor

Aby monitorować Azure-SSIS IR przy użyciu Azure Monitor, zobacz [monitorowanie operacji usług SSIS przy użyciu Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Więcej informacji na temat środowiska Azure SSIS Integration Runtime

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat środowiska Azure-SSIS Integration Runtime:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne dotyczące środowiska Integration Runtime, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](./tutorial-deploy-ssis-packages-azure.md). Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia Azure-SSIS IR i używania Azure SQL Database do hostowania wykazu usług SSIS (SSISDB). 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL do hostowania SSISDB. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób uruchamiania, zatrzymywania lub usuwania Azure-SSIS IR. Przedstawiono w nim również sposób skalowania go przez dodanie kolejnych węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera instrukcje dotyczące przyłączania Azure-SSIS IR do sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi monitorowania potoków na różne sposoby: 

- [Szybki Start: Tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md).
- [Używanie Azure Monitor do monitorowania potoków Data Factory](monitor-using-azure-monitor.md)
