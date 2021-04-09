---
title: Zapewnianie nadmiarowości geograficznej dla zadań Azure Stream Analytics
description: W tym artykule opisano sposób osiągnięcia nadmiarowości geograficznej Azure Stream Analytics zadań, a nie geograficznie trybu failover.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015525"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Zapewnianie nadmiarowości geograficznej dla zadań Azure Stream Analytics

Azure Stream Analytics nie zapewnia automatycznej pracy w trybie geograficznym, ale można uzyskać nadmiarowość geograficzną przez wdrożenie identycznych Stream Analytics zadań w wielu regionach świadczenia usługi Azure. Każde zadanie nawiązuje połączenie z lokalnymi źródłami danych wejściowych i lokalnymi. Jest odpowiedzialna aplikacja do przesyłania danych wejściowych do dwóch regionalnych wejść i uzgadniania między dwoma regionami regionalnymi. Zadania Stream Analytics są dwoma osobnymi jednostkami.

Na poniższym diagramie przedstawiono przykładowe geograficznie nadmiarowe wdrożenie zadania Stream Analytics przy użyciu danych wejściowych centrum zdarzeń i danych wyjściowych usługi Azure Database.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="Diagram geograficznie nadmiarowych zadań usługi Stream Analytics":::

## <a name="primarysecondary-strategy"></a>Strategia podstawowa/pomocnicza

Aplikacja musi zarządzać wyjściową bazą danych regionu, która jest uważana za podstawową i która jest traktowana jako pomocnicza. W przypadku awarii regionu podstawowego aplikacja przełącza się do pomocniczej bazy danych i rozpocznie odczytywanie aktualizacji z tej bazy danych. Rzeczywisty mechanizm, który pozwala zminimalizować odczyty duplikatów, zależy od aplikacji.Można uprościć ten proces, pisząc dodatkowe informacje w danych wyjściowych. Na przykład można dodać sygnaturę czasową lub identyfikator sekwencji do każdego danych wyjściowych, aby pominąć zduplikowane wiersze w prostej operacji. Po przywróceniu regionu podstawowego przechwytuje on do pomocniczej bazy danych przy użyciu podobnego Mechanics.

Chociaż różne typy danych wejściowych i wyjściowych umożliwiają używanie różnych opcji replikacji geograficznej, zalecamy użycie wzorca przedstawionego w tym artykule w celu zapewnienia nadmiarowości geograficznej, ponieważ zapewnia ona elastyczność i kontrolę nad zarówno producentami zdarzeń, jak i konsumentami zdarzeń.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie zadań Azure Stream Analytics i zarządzanie nimi za pomocą programu PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Debugowanie oparte na danych w Azure Stream Analytics](stream-analytics-job-diagram-with-metrics.md)