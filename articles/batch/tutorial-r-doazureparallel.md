---
title: Samouczek — uruchamianie równoległej symulacji języka R przy użyciu Azure Batch
description: Samouczek — Instrukcje krok po kroku umożliwiające uruchomienie symulacji finansowej Monte Carlo w usłudze Azure Batch za pomocą pakietu języka R doAzureParallel
ms.devlang: r
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 7e74a3b02be35b40e1a92bef269a3aed0b2313e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491779"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Samouczek: równoległe uruchamianie symulacji języka R w usłudze Azure Batch

Możesz uruchamiać równoległe obciążenia języka R na dużą skalę za pomocą lekkiego pakietu języka R, [doAzureParallel](https://www.github.com/Azure/doAzureParallel), umożliwiającego używanie usługi Azure Batch bezpośrednio z poziomu sesji języka R. Pakiet doAzureParallel został zbudowany na podstawie popularnego pakietu języka R [foreach](https://cran.r-project.org/web/packages/foreach/index.html). Pakiet doAzureParallel przesyła poszczególne iteracje pętli pakietu foreach jako zadania podrzędne usługi Azure Batch.

Z tego samouczka dowiesz się, jak wdrożyć pulę usługi Azure Batch i uruchomić równoległe zadania języka R w usłudze Azure Batch bezpośrednio z poziomu programu RStudio. Omawiane kwestie:


> [!div class="checklist"]
> * Instalowanie pakietu doAzureParallel i konfigurowanie go w celu uzyskania dostępu do kont usług Batch i Storage
> * Tworzenie puli usługi Batch jako równoległego zaplecza dla sesji języka R
> * Uruchamianie równoległe przykładowej symulacji w puli

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstalowana dystrybucja języka [R](https://www.r-project.org/), na przykład [Microsoft R Open](https://mran.microsoft.com/open). Należy użyć języka R w wersji 3.3.1 lub nowszej.

* Program [RStudio](https://www.rstudio.com/) w wersji komercyjnej lub w wersji [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) typu open source.

* Konto usługi Azure Batch i Azure Storage. Aby utworzyć te konta, skorzystaj z przewodników Szybki start dla usługi Batch i [witryny Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]
## <a name="install-doazureparallel"></a>Instalowanie pakietu doAzureParallel

Przy użyciu konsoli programu RStudio zainstaluj [pakiet doAzureParallel z witryny GitHub](https://www.github.com/Azure/doAzureParallel). Następujące polecenia umożliwiają pobranie i zainstalowanie pakietu oraz jego zależności w bieżącej sesji języka R:

```R
# Install the devtools package
install.packages("devtools")

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch")

# Install the doAzureParallel package
devtools::install_github("Azure/doAzureParallel")

# Load the doAzureParallel library
library(doAzureParallel)
```
Instalacja może potrwać kilka minut.

Aby skonfigurować pakiet doAzureParallel przy użyciu wcześniej uzyskanych poświadczeń konta, wygeneruj plik konfiguracyjny o nazwie *credentials.json* w katalogu roboczym:

```R
generateCredentialsConfig("credentials.json")
```

W pliku wprowadź nazwy i klucze kont usługi Batch i Storage. Pozostaw niezmienione ustawienie `githubAuthenticationToken`.

Gotowy plik poświadczeń powinien wyglądać mniej więcej następująco:

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Zapisz plik. Następnie uruchom poniższe polecenie, aby ustawić poświadczenia dla bieżącej sesji języka R:

```R
setCredentials("credentials.json")
```

## <a name="create-a-batch-pool"></a>Tworzenie puli usługi Batch

Pakiet doAzureParallel zawiera funkcję generującą pulę (klastrer) usługi Azure Batch w celu równoległego uruchamiania zadań języka R. Na węzłach jest uruchamiana [maszyna wirtualna do analizy danych na platformie Azure](../machine-learning/data-science-virtual-machine/overview.md), oparta na systemie Ubuntu. W tym obrazie jest wstępnie zainstalowana dystrybucja Microsoft R Open wraz z popularnymi pakietami języka R. Możesz wyświetlać i dostosowywać niektóre ustawienia klastra, na przykład liczbę i rozmiar węzłów.

Aby wygenerować plik JSON z konfiguracją klastra w katalogu roboczym:

```R
generateClusterConfig("cluster.json")
```

Otwórz plik, aby wyświetlić konfigurację domyślną, obejmującą 3 węzły dedykowane oraz 3 węzły [o niskim priorytecie](batch-low-pri-vms.md). Są to tylko przykładowe ustawienia. Możesz je modyfikować i eksperymentować z nimi. Węzły dedykowane są zarezerwowane dla Twojej puli. Węzły o niskim priorytecie są oferowane w obniżonej cenie i korzystają z nadwyżek pojemności maszyn wirtualnych na platformie Azure. Węzły o niskim priorytecie staną się niedostępne, jeśli pojemność platformy Azure będzie niewystarczająca.

Na potrzeby tego samouczka wprowadź w konfiguracji następujące zmiany:

* Zwiększ wartość parametru `taskSlotsPerNode` do *2*, aby móc korzystać z obu rdzeni w każdym węźle.
* Ustaw wartość parametru `dedicatedNodes` na *0*, aby wypróbować maszyny wirtualne o niskim priorytecie na potrzeby usługi Batch. Ustaw wartość `min` parametru `lowPriorityNodes` na *5*. Ustaw wartość `max` tego parametru na *10*. W razie potrzeby możesz wybrać mniejsze wartości.

Pozostaw domyślne wartości pozostałych ustawień i zapisz plik. Zawartość okna powinna wyglądać mniej więcej tak:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "taskSlotsPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Teraz możesz utworzyć klaster. Usługa Batch tworzy pulę natychmiast, ale przydzielenie i uruchomienie węzłów obliczeniowych może potrwać kilka minut. Gdy klaster będzie już dostępny, zarejestruj go jako równoległe zaplecze dla sesji języka R.

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json")

# Register your parallel backend
registerDoAzureParallel(cluster)

# Check that the nodes are running
getDoParWorkers()
```

W danych wyjściowych zobaczysz liczbę „procesów roboczych wykonania” dla pakietu doAzureParallel. Jest to liczba węzłów pomnożona przez wartość parametru `taskSlotsPerNode`. W przypadku zmodyfikowania konfiguracji klastra zgodnie z powyższym opisem będzie to *10*.

## <a name="run-a-parallel-simulation"></a>Uruchamianie równoległej symulacji

Po utworzeniu klastra możesz uruchomić pętlę foreach przy użyciu zarejestrowanego zaplecza równoległego (puli usługi Azure Batch). Na przykład możesz uruchomić symulację finansową Monte Carlo — najpierw lokalnie, za pomocą standardowej pętli foreach, a następnie uruchamiając pakiet foreach w usłudze Batch. Ten przykład to uproszczona wersja prognozowania ceny akcji przez symulację dużej liczby różnych wyników po 5 latach.

Załóżmy, że cena akcji spółki Contoso Corporation codziennie wzrasta 1,001 raza w stosunku do ceny otwarcia, ale zmienność (odchylenie standardowe) kursu wynosi 0,01. Mając daną cenę początkową 100 USD, użyj symulacji cen Monte Carlo, aby przewidzieć cenę akcji spółki Contoso po 5 latach.

Parametry symulacji Monte Carlo:

```R
mean_change = 1.001
volatility = 0.01
opening_price = 100
```

Aby wykonać symulację cen zamknięcia, zdefiniuj następującą funkcję:

```R
getClosingPrice <- function() {
  days <- 1825 # ~ 5 years
  movement <- rnorm(days, mean=mean_change, sd=volatility)
  path <- cumprod(c(opening_price, movement))
  closingPrice <- path[days]
  return(closingPrice)
}
```

Najpierw uruchom 10 000 symulacji lokalnie za pomocą standardowej pętli foreach, używając słowa kluczowego `%do%`:

```R
start_s <- Sys.time()
# Run 10,000 simulations in series
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% {
  replicate(1000, getClosingPrice())
}
end_s <- Sys.time()
```


Wykreśl ceny zamknięcia na histogramie, aby zobaczyć rozkład wyników:

```R
hist(closingPrices_s)
```

Dane wyjściowe będą podobne do następujących:

![Zrzut ekranu przedstawia histogram cen zamknięcia](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Lokalna symulacja zostanie wykonana w maksymalnie kilka sekund:

```R
difftime(end_s, start_s)
```

Szacowany czas wykonywania 10 milionów wyników lokalnie, za pomocą przybliżenia liniowego, wynosi ok. 30 minut:

```R
1000 * difftime(end_s, start_s, unit = "min")
```


Teraz uruchom kod za pomocą pętli `foreach` ze słowem kluczowym `%dopar%`, aby porównać ten czas z czasem wykonywania 10 milionów symulacji na platformie Azure. Aby wykonać symulację równoległą przy użyciu usługi Batch, uruchom 100 iteracji po 100 000 symulacji:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10)
start_p <- Sys.time()
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% {
  replicate(100000, getClosingPrice())
}
end_p <- Sys.time()
```

Symulacja rozdziela zadania podrzędne do węzłów w puli usługi Batch. Działanie jest widoczne na mapie cieplnej dla puli w Azure Portal. Przejdź do pozycji **konta wsadowe**  >  *myBatchAccount*. Kliknij pozycję **Pule** moja  >  *Pula*.

![Mapa cieplna puli z uruchomionymi równoległymi zadaniami języka R](media/tutorial-r-doazureparallel/pool.png)

Po kilku minutach symulacja zostanie ukończona. Pakiet automatycznie scala wyniki i pobiera je z węzłów. Następnie możesz użyć wyników w sesji języka R.

```R
hist(closingPrices_p)
```

Dane wyjściowe będą podobne do następujących:

![Rozkład cen zamknięcia](media/tutorial-r-doazureparallel/closing-prices.png)

Jak długo trwało wykonywanie równoległych symulacji?

```R
difftime(end_p, start_p, unit = "min")
```

Uruchomienie symulacji w puli usługi Batch powinno spowodować zauważalne zwiększenie wydajności i skrócenie czasu w porównaniu z lokalnym wykonywaniem symulacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zadanie zostanie automatycznie usunięte po zakończeniu. Jeśli klaster nie będzie już potrzebny, wywołaj funkcję `stopCluster` w pakiecie doAzureParallel, aby go usunąć:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> Instalowanie pakietu doAzureParallel i konfigurowanie go w celu uzyskania dostępu do kont usług Batch i Storage
> * Tworzenie puli usługi Batch jako równoległego zaplecza dla sesji języka R
> * Uruchamianie równoległe przykładowej symulacji w puli


Aby uzyskać więcej informacji na temat pakietu doAzureParallel, zobacz dokumentację i przykłady w witrynie GitHub.

> [!div class="nextstepaction"]
> [Pakiet doAzureParallel](https://github.com/Azure/doAzureParallel/)
