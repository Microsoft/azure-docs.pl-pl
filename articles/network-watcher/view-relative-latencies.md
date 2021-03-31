---
title: Wyświetlanie względnych opóźnień w regionach platformy Azure z określonych lokalizacji
description: Dowiedz się, jak wyświetlać opóźnienia względne między dostawcami internetowymi w regionach platformy Azure z określonych lokalizacji.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737907"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Wyświetlanie względnego opóźnienia do regionów platformy Azure z określonych lokalizacji

> [!WARNING]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i nadal jest testowana pod kątem stabilności.

W tym samouczku dowiesz się, jak używać usługi Azure [Network Watcher](network-watcher-monitoring-overview.md) , aby określić region platformy Azure, w którym ma zostać wdrożona aplikacja lub usługa, w oparciu o dane demograficzne użytkownika. Ponadto można jej użyć do oszacowania połączeń dostawców usług z platformą Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Tworzenie obserwatora sieciowego

Jeśli masz już obserwatora sieci w co najmniej jednym [regionie](https://azure.microsoft.com/regions)platformy Azure, możesz pominąć zadania w tej sekcji. Utwórz grupę zasobów dla obserwatora sieci. W tym przykładzie grupa zasobów jest tworzona w regionie Wschodnie stany USA, ale grupy zasobów można utworzyć w dowolnym regionie świadczenia usługi Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Tworzenie obserwatora sieciowego. Należy utworzyć obserwatora sieci w co najmniej jednym regionie platformy Azure. W tym przykładzie zostanie utworzony obserwator sieci w regionie świadczenia usługi Azure Wschodnie stany USA.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porównywanie opóźnień sieci względem jednego regionu świadczenia usługi Azure z określonej lokalizacji

Oceń dostawców usług lub Rozwiąż problemy użytkownika, takie jak "powolna lokacja", z określonej lokalizacji w regionie świadczenia usługi Azure, w którym została wdrożona usługa. Na przykład następujące polecenie zwraca średnią względną liczbę opóźnień usługodawców internetowych między stanem Waszyngton w Stany Zjednoczone i regionem świadczenia usługi Azure zachodnie stany USA 2 między grudnia 13-15, 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Region określony w poprzednim poleceniu nie musi być tożsamy z regionem określonym podczas pobierania obserwatora sieciowego. Poprzednie polecenie po prostu wymaga określenia istniejącego obserwatora sieci. Obserwator sieci może znajdować się w dowolnym regionie. Jeśli określisz wartości dla `-Country` i `-State` , muszą one być prawidłowe. W wartościach jest rozróżniana wielkość liter. Dane są dostępne dla ograniczonej liczby krajów/regionów, stanów i miejscowości. Uruchom polecenia w [widoku dostępne kraje/regiony, Stany, miasta i dostawcy,](#view-available) aby wyświetlić listę dostępnych krajów/regionów, miast i Stanów do użycia z poprzednim poleceniem. 

> [!WARNING]
> Należy określić datę w ciągu ostatnich 30 dni dla `-StartTime` i `-EndTime` . Określenie wcześniejszej daty spowoduje zwrócenie danych.

Dane wyjściowe poprzedniego polecenia są następujące:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

W zwracanych danych wyjściowych wartość dla **oceny** jest względnym opóźnieniem między regionami i dostawcami. Wynik 1 to najgorszy (najwyższy) opóźnieniu, a 100 jest najmniejszym opóźnieniem. Wartości opóźnień względnych są uśredniane dla danego dnia. W poprzednim przykładzie, podczas gdy jest jasne, że opóźnienia były takie same w dniach i że istnieje mała różnica między opóźnieniami dwóch dostawców, jest również jasne, że opóźnienia dla obu dostawców są niskie na skalę 1-100. Chociaż jest to oczekiwane, ponieważ stan Waszyngton w Stany Zjednoczone jest fizycznie bliski regionu platformy Azure w regionie zachodnie stany USA 2, czasami wyniki nie są zgodne z oczekiwaniami. Im większy zakres dat, tym większa jest wartość średnia opóźnienia w czasie.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porównywanie opóźnień sieci w regionach platformy Azure z określonej lokalizacji

Jeśli zamiast określania względnych opóźnień między konkretną lokalizacją i określonym regionem świadczenia usługi Azure przy użyciu programu chcesz `-Location` określić względne opóźnienia dla wszystkich regionów świadczenia usługi Azure z określonej lokalizacji fizycznej, możesz to zrobić. Na przykład następujące polecenie pomaga w ocenie regionu platformy Azure, w którym ma zostać wdrożona usługa, jeśli użytkownicy podstawowi są Comcast w stanie Waszyngton:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> W przeciwieństwie do określania pojedynczej lokalizacji, jeśli nie określono lokalizacji lub określić wiele lokalizacji, takich jak "zachodnie stany USA 2", "zachodnie stany USA", podczas uruchamiania polecenia należy określić usługodawcę internetowego. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Wyświetl dostępne kraje/regiony, Stany, miasta i dostawców

Dane są dostępne dla określonych dostawców usług internetowych, krajów/regionów, stanów i miejscowości. Aby wyświetlić listę wszystkich dostępnych dostawców usług internetowych, krajów/regionów, stanów i miejscowości, dla których można wyświetlić dane, wprowadź następujące polecenie:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Dane są dostępne tylko dla krajów/regionów, stanów i miejscowości zwracanych przez poprzednie polecenie. Poprzednie polecenie wymaga określenia istniejącego obserwatora sieci. Przykład został określony dla obserwatora sieci *NetworkWatcher_eastus* w grupie zasobów o nazwie *NetworkWatcherRG*, ale można określić dowolny istniejący obserwator sieci. Jeśli nie masz istniejącego obserwatora sieciowego, utwórz go, wykonując zadania w obszarze [Tworzenie obserwatora sieciowego](#create-a-network-watcher). 

Po uruchomieniu poprzedniego polecenia można filtrować dane wyjściowe zwracane przez określenie prawidłowych wartości dla **kraju**, **województwa** i **miasta**, w razie potrzeby.  Aby na przykład wyświetlić listę dostawców usług internetowych dostępnych w Seattle, Waszyngton, w Stany Zjednoczone wprowadź następujące polecenie:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Wartość określona dla **kraju** musi być wielką i małą literą. Wartości podane dla **województwa** i **miasta** muszą być małymi literami. Wartości muszą być wymienione w danych wyjściowych zwracanych po uruchomieniu polecenia bez wartości dla **kraju**, **województwa** i **miasta**. Jeśli określisz nieprawidłową wielkość liter lub określisz wartość **kraju**, **województwa** lub **miasta** , która nie znajduje się w danych wyjściowych zwróconych po uruchomieniu polecenia bez wartości dla tych właściwości, zwracane dane wyjściowe są puste.
