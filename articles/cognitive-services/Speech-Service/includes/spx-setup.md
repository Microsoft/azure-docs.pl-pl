---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6a38590d2511afaac6bd9213f53a8c69d0264eb4
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714692"
---
## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym jest subskrypcja usługi Azure Speech. Zapoznaj się z [przewodnikiem](../get-started.md#new-resource) dotyczącym tworzenia nowej subskrypcji, jeśli jeszcze jej nie masz.

## <a name="download-and-install"></a>Pobieranie i instalowanie

#### <a name="windows-install"></a>[Instalacja systemu Windows](#tab/windowsinstall)

Wykonaj następujące kroki, aby zainstalować narzędzie SPX w systemie Windows:

1. Zainstaluj system [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) lub [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)narzędzia dla SPX, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobrania i Wyodrębnij wymagany podkatalog ( `spx-net471` dla .NET Framework 4,7 lub `spx-netcore-win-x64` dla platformy .NET Core 3,0 na procesorze x64).

W wierszu polecenia Zmień katalog na tę lokalizację, a następnie wpisz, `spx` Aby wyświetlić pomoc dla narzędzia SPX.

#### <a name="linux-install"></a>[Instalacja systemu Linux](#tab/linuxinstall)

Wykonaj następujące kroki, aby zainstalować narzędzie SPX w systemie Linux na PROCESORze x64:

1. Zainstaluj program [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)narzędzia dla SPX, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobierania i Wyodrębnij `spx-netcore-30-linux-x64` go do nowego `~/spx` katalogu.
4. W terminalu wpisz następujące polecenia:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Wpisz `spx` , aby wyświetlić pomoc dla narzędzia SPX.

***

## <a name="create-subscription-config"></a>Utwórz konfigurację subskrypcji

Aby rozpocząć korzystanie z usługi SPX, musisz najpierw wprowadzić klucz subskrypcji mowy i informacje o regionie. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Gdy masz klucz subskrypcji i Identyfikator regionu (np. `eastus`, `westus` Uruchom następujące polecenia.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Twoje uwierzytelnianie subskrypcji jest teraz przechowywane dla przyszłych żądań SPX. Jeśli musisz usunąć jedną z tych przechowywanych wartości, uruchom polecenie `spx config @region --clear` lub `spx config @key --clear` .
