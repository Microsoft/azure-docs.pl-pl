---
title: 'Usługa komunikacji równorzędnej Azure: jak zmierzyć dane telemetryczne połączenia '
description: W tym samouczku dowiesz się, jak zmierzyć dane telemetryczne połączenia.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022500"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Samouczek: mierzenie danych telemetrycznych połączenia usługi Komunikacja równorzędna

 W tym samouczku dowiesz się, jak zmierzyć dane telemetryczne dla połączeń usług komunikacji równorzędnej.
 
 Dane telemetryczne połączenia udostępniają szczegółowe informacje zebrane na potrzeby łączności między lokalizacją klienta a siecią firmy Microsoft. W tym artykule dowiesz się, jak wyświetlić raport o opóźnieniu dla określonego połączenia usługi Komunikacja równorzędna Azure. 

Aby zmierzyć dane telemetryczne połączenia usługi komunikacji równorzędnej, należy zarejestrować połączenie usługi komunikacji równorzędnej w Azure Portal. Aby dowiedzieć się, jak zarejestrować połączenie, zobacz [Rejestrowanie połączenia z usługą komunikacji równorzędnej — Azure Portal](azure-portal.md).


## <a name="view-a-latency-report"></a>Wyświetlanie raportu o opóźnieniu

Aby wyświetlić raport o opóźnieniu dla określonego połączenia z usługą komunikacji równorzędnej, wykonaj następujące kroki.

1. W lewym okienku wybierz pozycję **wszystkie zasoby** , a następnie wybierz pozycję połączenie usługi Komunikacja równorzędna. Następnie wybierz pozycję **Otwórz** w obszarze **prefiksy**. 

   ![Wybierz połączenie usługi Komunikacja równorzędna](./media/peering-service-measure/peering-service-measure-menu.png)

2. Zostanie wyświetlona strona raportu o opóźnieniu dla wszystkich prefiksów skojarzonych z tym połączeniem usługi komunikacji równorzędnej. 

      ![Strona raportu o opóźnieniu](./media/peering-service-measure/peering-service-latency-report.png)

3. Domyślnie raport jest aktualizowany dla każdej godziny, która jest wyświetlana na tej stronie. Aby wyświetlić raport dla różnych osi czasu, wybierz odpowiednią opcję z opcji **Pokaż dane dla ostatnich**. 

4. Aby wyświetlić zdarzenia dla określonego prefiksu, wybierz nazwę prefiksu i wybierz pozycję **zdarzenia prefiksu** w okienku po lewej stronie. Zostaną wyświetlone przechwycone zdarzenia.


   ![Zdarzenia prefiksu](./media/peering-service-measure/peering-service-prefix-event.png)

 Niektóre z możliwych zdarzeń, które są przechwytywane na liście **zdarzenia prefiksu** , są wyświetlane w tym miejscu.

| **Zdarzenia prefiksu** | **Typ zdarzenia**|**Rozsądkiem**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Informacje|Otrzymano powiadomienie o prefiksie|
| PrefixWithdrawalEvent|Ostrzeżenie| Odebrano cofnięcie prefiksu |
| PrefixBackupRouteAnnouncementEvent |Informacje|Odebrano anons dotyczący trasy kopii zapasowej |
| PrefixBackupRouteWithdrawalEvent|Ostrzeżenie|Odebrano wycofanie trasy kopii zapasowej |
| PrefixActivePath |Informacje| Aktywna trasa bieżącego prefiksu   |
| PrefixBackupPath | Informacje|Bieżąca trasa kopii zapasowej z prefiksem   |
| PrefixOriginAsChangeEvent|Krytyczne| Dokładny prefiks odebrany z innym numerem systemu autonomicznego pochodzenia (dla aktywnej trasy)| 
| PrefixBackupRouteOriginAsChangeEvent  | Błąd|Odebrano prefiks z innym numerem systemu autonomicznego pochodzenia (dla trasy tworzenia kopii zapasowej)  |

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniu z usługą komunikacji równorzędnej, zobacz [nawiązywanie połączenia z usługą komunikacji równorzędnej](connection.md).
- Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).