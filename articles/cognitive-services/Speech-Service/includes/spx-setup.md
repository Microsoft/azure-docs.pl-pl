---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544756"
---
## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym jest subskrypcja usługi Azure Speech. Zapoznaj się z [przewodnikiem](../get-started.md#new-resource) dotyczącym tworzenia nowej subskrypcji, jeśli jeszcze jej nie masz.

## <a name="download-and-install"></a>Pobieranie i instalowanie

#### <a name="windows-install"></a>[Instalacja systemu Windows](#tab/windowsinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Windows:

1. Zainstaluj system [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) lub [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobrania i Wyodrębnij wymagany podkatalog ( `spx-net471` dla .NET Framework 4,7 lub `spx-netcore-win-x64` dla platformy .NET Core 3,0 na procesorze x64).

W wierszu polecenia Zmień katalog na tę lokalizację, a następnie wpisz polecenie, `spx` Aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

> [!NOTE]
> W systemie Windows interfejs wiersza polecenia mowy może wyświetlać tylko czcionki dostępne dla wiersza poleceń na komputerze lokalnym.
> [Terminal systemu Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) obsługuje wszystkie czcionki opracowane interaktywnie przez interfejs wiersza polecenia mowy.
> Jeśli dane wyjściowe są wyprowadzane do pliku, Edytor tekstu, taki jak Notatnik lub przeglądarka sieci Web, np. Microsoft Edge, może również wyświetlać wszystkie czcionki.

> [!NOTE]
> Program PowerShell nie sprawdza katalogu lokalnego podczas wyszukiwania polecenia. W programie PowerShell Zmień katalog na lokalizację `spx` i Wywołaj narzędzie, wprowadzając polecenie `.\spx` .
> Jeśli dodasz ten katalog do ścieżki, program PowerShell i wiersz polecenia systemu Windows znajdą się `spx` z dowolnego katalogu bez `.\` prefiksu.

#### <a name="linux-install"></a>[Instalacja systemu Linux](#tab/linuxinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Linux na PROCESORze x64:

1. Zainstaluj program [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobierania i Wyodrębnij `spx-netcore-30-linux-x64` go do nowego `~/spx` katalogu.
4. W terminalu wpisz następujące polecenia:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Wpisz `spx` , aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

***

## <a name="create-subscription-config"></a>Utwórz konfigurację subskrypcji

Aby rozpocząć korzystanie z interfejsu wiersza polecenia mowy, musisz najpierw wprowadzić informacje o kluczu subskrypcji mowy i regionie. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Gdy masz klucz subskrypcji i Identyfikator regionu (np. `eastus`, `westus` Uruchom następujące polecenia.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Twoje uwierzytelnianie subskrypcji jest teraz przechowywane dla przyszłych żądań SPX. Jeśli musisz usunąć jedną z tych przechowywanych wartości, uruchom polecenie `spx config @region --clear` lub `spx config @key --clear` .
