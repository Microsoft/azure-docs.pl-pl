---
title: Debugowanie renderowania
description: Przegląd efektów renderowania debugowania po stronie serwera
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591739"
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

```cs
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
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

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [C++ RenderingConnection::D ebugRenderingSettings ()](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>Następne kroki

* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania wydajności po stronie serwera](performance-queries.md)