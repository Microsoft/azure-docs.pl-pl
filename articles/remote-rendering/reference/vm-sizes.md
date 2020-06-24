---
title: Rozmiary maszyn wirtualnych
description: Opisuje różne rozmiary maszyn wirtualnych, które mogą być przydzielone
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: aab914caa2647146639aa366f558c80bebcfde54
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810208"
---
# <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

Usługa renderowania może działać na dwóch różnych typach maszyn na platformie Azure, nazywanych `Standard` i `Premium` .

## <a name="polygon-limits"></a>Limity wielokąta

Istnieje stałe ograniczenie **20 000 000 wielokątów** dla `Standard` rozmiaru maszyny wirtualnej. Nie ma takiego ograniczenia dotyczącego `Premium` rozmiaru.

Gdy moduł renderowania dla standardowej maszyny wirtualnej osiągnie to ograniczenie, przełącza renderowanie do tła w szachownicy:

![Szachownic](media/checkerboard.png)

## <a name="allocate-the-vm"></a>Przydziel maszynę wirtualną

Wymagany typ maszyny wirtualnej musi być określony podczas inicjowania sesji renderowania. Nie można go zmienić w uruchomionej sesji. W poniższym przykładzie kodu pokazano miejsce, w którym musi zostać określony rozmiar maszyny wirtualnej:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

W przypadku [przykładowych skryptów programu PowerShell](../samples/powershell-example-scripts.md)rozmiar maszyny wirtualnej musi być określony w `arrconfig.json` pliku:

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

Liczba wielokątów, które są brane pod uwagę w teście ograniczenia to liczba wielokątów, które są w rzeczywistości przesyłane do modułu renderowania. Ta geometria jest zwykle sumą wszystkich modeli skonkretyzowanych, ale istnieją także wyjątki. **Nie dołączono**następującej geometrii:
* Załadowane wystąpienia modelu, które są w pełni poza widokiem frustum.
* Modele lub części modelu, które są przełączane do niewidoczne, przy użyciu [składnika zastąpienia stanu hierarchicznego](../overview/features/override-hierarchical-state.md).

W związku z tym można napisać aplikację, która jest przeznaczona dla `standard` rozmiaru ładującego wiele modeli z liczbą wielokątów zbliżoną do limitu dla każdego pojedynczego modelu. Gdy aplikacja wyświetla tylko jeden model w danym momencie, szachownica nie jest wyzwalana.

### <a name="how-to-determine-the-number-of-polygons"></a>Jak ustalić liczbę wielokątów

Istnieją dwa sposoby określenia liczby wielokątów modelu lub sceny, które przyczyniają się do limitu budżetu `standard` maszyny wirtualnej o rozmiarze:
* Na stronie Konwersja modelu Pobierz [wyjściowy plik JSON konwersji](../how-tos/conversion/get-information.md)i sprawdź `numFaces` wpis w [sekcji *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Jeśli aplikacja dotyczy zawartości dynamicznej, liczba renderowanych wielokątów może być dynamicznie przetwarzana w czasie wykonywania. Użyj [zapytania oceny wydajności](../overview/features/performance-queries.md#performance-assessment-queries) i sprawdź, czy `polygonsRendered` element członkowski znajduje się w `FrameStatistics` strukturze. Tło z szachownicą jest zawsze wyblakłe z pewnym opóźnieniem, aby można było wykonać akcję użytkownika po wykonaniu tej kwerendy asynchronicznej. Akcja użytkownika może na przykład ukryć lub usunąć wystąpienia modelu.

## <a name="pricing"></a>Cennik

Szczegółowy podział cen dla każdego typu maszyn wirtualnych można znaleźć na stronie [cennika renderowania zdalnego](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Następne kroki
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
* [Konwersja modelu](../how-tos/conversion/model-conversion.md)

