---
title: Renderowanie debugowania
description: Omówienie efektów renderowania debugowania po stronie serwera
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394295"
---
# <a name="debug-rendering"></a>Renderowanie debugowania

Interfejs API renderowania debugowania udostępnia szereg opcji globalnych, aby zmienić renderowanie po stronie serwera z różnymi efektami debugowania.

## <a name="available-debug-rendering-effects"></a>Dostępne efekty renderowania debugowania

|Ustawienie                          | Efekt                               |
|---------------------------------|:-------------------------------------|
|Licznik ramek                    | Renderuje nakładkę tekstową w lewym górnym rogu ramki. Tekst pokazuje bieżący identyfikator ramki po stronie serwera, który jest stale zwiększany w miarę dalszego renderowania. |
|Liczba wielokątów                    | Renderuje nakładkę tekstową w lewym górnym rogu ramki. Tekst pokazuje aktualnie renderowane ilości wielokątów, taką samą wartość jak kwerendy [wydajności po stronie serwera](performance-queries.md)| 
|Szkielet                        | Jeśli ta opcja jest włączona, cała geometria obiektu załadowana na serwer będzie renderowana w trybie szkieletowym. Tylko krawędzie wielokątów będą rasteryzowane n w tym trybie. |

Poniższy kod umożliwia te efekty debugowania:

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![Renderowanie debugowania](./media/debug-rendering.png)

> [!NOTE]
> Wszystkie efekty renderowania debugowania są ustawieniami globalnymi, które wpływają na całą ramkę.

## <a name="use-cases"></a>Przypadki zastosowań

Interfejs API renderowania debugowania jest przeznaczony do prostych zadań debugowania, takich jak sprawdzanie, czy połączenie z usługą jest faktycznie uruchomione i działa poprawnie. Opcje renderowania tekstu mają bezpośredni wpływ na klatki wideo przesyłane strumieniowo w dół. Włączenie ich sprawdza, czy nowe klatki są odbierane i poprawnie dekodowane wideo.

Jednak pod warunkiem, że efekty nie dają żadnych szczegółowych introspekcji do kondycji służby. Kwerendy [wydajności po stronie serwera](performance-queries.md) są zalecane dla tego przypadku użycia.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

* Włączenie nakładek tekstu wiąże się z niewielkim lub żadnym obciążeniem na wydajność.
* Ponadto włączenie nakładki powoduje obciążenie wydajności nietrywialne, choć może się różnić w zależności od sceny. W przypadku złożonych scen ten tryb może spowodować spadek liczby klatek na sekundę poniżej celu 60-Hz.

## <a name="next-steps"></a>Następne kroki

* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania wydajności po stronie serwera](performance-queries.md)
