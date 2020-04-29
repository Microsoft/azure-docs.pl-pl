---
title: Debugowanie renderowania
description: Przegląd efektów renderowania debugowania po stronie serwera
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868157"
---
# <a name="debug-rendering"></a>Debugowanie renderowania

Interfejs API renderowania debugowania udostępnia wiele opcji globalnych umożliwiających zmianę renderowania po stronie serwera z różnymi efektami debugowania.

## <a name="available-debug-rendering-effects"></a>Dostępne efekty renderowania debugowania

|Ustawienie                          | Efekt                               |
|---------------------------------|:-------------------------------------|
|Licznik ramek                    | Renderuje nakładkę tekstu w lewym górnym rogu ramki. Tekst pokazuje bieżący identyfikator ramki po stronie serwera, który jest stale zwiększany w miarę renderowania. |
|Liczba wielokątów                    | Renderuje nakładkę tekstu w lewym górnym rogu ramki. Tekst pokazuje aktualnie wyrenderowaną ilość wielokątów, taką samą wartość jak [zapytania wydajności po stronie serwera](performance-queries.md)| 
|Diagram szkieletowy                        | Jeśli ta funkcja jest włączona, wszystkie geometrie obiektów załadowane na serwerze będą renderowane w trybie szkieletu. Tylko krawędzie wielokątów będą rasteryzowane w tym trybie. |

Poniższy kod umożliwia wykonywanie tych efektów debugowania:

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
> Wszystkie efekty renderowania debugowania to globalne ustawienia, które mają wpływ na całą ramkę.

## <a name="use-cases"></a>Przypadki zastosowań

Interfejs API renderowania debugowania jest przeznaczony dla prostych zadań debugowania, takich jak sprawdzenie, czy połączenie z usługą zostało prawidłowo uruchomione. Opcje renderowania tekstu wpływają bezpośrednio na ramki wideo w dół. Włączenie tych funkcji sprawdza, czy są odbierane nowe ramki i poprawnie zdekodowane wideo.

Jednak podane efekty nie dają żadnych szczegółowych introspekcji w zakresie kondycji usługi. [Zapytania wydajności po stronie serwera](performance-queries.md) są zalecane dla tego przypadku użycia.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

* Włączenie nakładek tekstu jest nieco nieznacznie niezwiązane z wydajnością.
* Włączenie trybu szkieletu wiąże się z nieprostym obciążeniem wydajności, ale może się to różnić w zależności od sceny. W przypadku złożonych scen ten tryb może spowodować spadek szybkości klatek poniżej wartości docelowej 60-Hz.

## <a name="next-steps"></a>Następne kroki

* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania wydajności po stronie serwera](performance-queries.md)
