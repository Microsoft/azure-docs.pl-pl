---
title: Rozmiary serwerów
description: Opisuje różne rozmiary serwera, które mogą być przydzielone
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594404"
---
# <a name="server-sizes"></a>Rozmiary serwerów

Zdalne renderowanie na platformie Azure jest dostępne w dwóch konfiguracjach serwera: `Standard` i `Premium` .

## <a name="polygon-limits"></a>Limity wielokąta

Renderowanie zdalne z `Standard` serwerem rozmiaru ma maksymalny rozmiar sceny wielokąta 20 000 000. Renderowanie zdalne z `Premium` rozmiarem nie wymusza ostatecznej wartości maksymalnej, ale wydajność może być obniżona, jeśli zawartość przekracza możliwości renderowania usługi.

Gdy moduł renderowania na serwerze "standardowy" osiągnie to ograniczenie, włącza renderowanie do tła szachownicy:

![Zrzut ekranu przedstawia siatkę czarno-białych kwadratów z menu Narzędzia.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Określ rozmiar serwera

Wymagany typ konfiguracji serwera należy określić podczas inicjowania sesji renderowania. Nie można go zmienić w uruchomionej sesji. W poniższym przykładzie kodu pokazano miejsce, w którym należy określić rozmiar serwera:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

W przypadku [przykładowych skryptów programu PowerShell](../samples/powershell-example-scripts.md)żądany rozmiar serwera musi być określony w `arrconfig.json` pliku:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Jak moduł renderujący oblicza liczbę wielokątów

Liczba wielokątów, które są brane pod uwagę w teście ograniczenia to liczba wielokątów, które są w rzeczywistości przesyłane do modułu renderowania. Ta geometria jest zwykle sumą wszystkich modeli skonkretyzowanych, ale istnieją także wyjątki. **Nie dołączono** następującej geometrii:
* Załadowane wystąpienia modelu, które są w pełni poza widokiem frustum.
* Modele lub części modelu, które są przełączane do niewidoczne, przy użyciu [składnika zastąpienia stanu hierarchicznego](../overview/features/override-hierarchical-state.md).

W związku z tym można napisać aplikację, która jest przeznaczona dla `standard` rozmiaru ładującego wiele modeli z liczbą wielokątów zbliżoną do limitu dla każdego pojedynczego modelu. Gdy aplikacja wyświetla tylko jeden model w danym momencie, szachownica nie jest wyzwalana.

### <a name="how-to-determine-the-number-of-polygons"></a>Jak ustalić liczbę wielokątów

Istnieją dwa sposoby określenia liczby wielokątów modelu lub sceny, które przyczyniają się do limitu budżetu dla `standard` rozmiaru konfiguracji:
* Na stronie Konwersja modelu Pobierz [wyjściowy plik JSON konwersji](../how-tos/conversion/get-information.md)i sprawdź `numFaces` wpis w [sekcji *inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Jeśli aplikacja dotyczy zawartości dynamicznej, liczba renderowanych wielokątów może być dynamicznie przetwarzana w czasie wykonywania. Użyj [zapytania oceny wydajności](../overview/features/performance-queries.md#performance-assessment-queries) i sprawdź, czy `polygonsRendered` element członkowski znajduje się w `FrameStatistics` strukturze. `PolygonsRendered`Pole zostanie ustawione na, `bad` gdy moduł renderowania trafi na ograniczenie wielokąta. Tło z szachownicą jest zawsze wyblakłe z pewnym opóźnieniem, aby można było wykonać akcję użytkownika po wykonaniu tej kwerendy asynchronicznej. Akcja użytkownika może na przykład ukryć lub usunąć wystąpienia modelu.

## <a name="pricing"></a>Ceny

Szczegółowy podział cen dla każdego typu konfiguracji można znaleźć na stronie [cennika renderowania zdalnego](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Następne kroki
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
* [Konwersja modelu](../how-tos/conversion/model-conversion.md)

