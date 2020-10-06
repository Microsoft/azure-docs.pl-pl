---
title: Przestarzałe metryki dysku w Azure Portal | Microsoft Docs
description: Dowiedz się, które metryki dysku zostały wycofane i jak zaktualizować alerty metryki, aby korzystać z nowych metryk.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 623d9385b9ae6b13c8964f655fb973fe67a0918a
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743713"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Przestarzałe metryki dysku w Azure Portal

Przestarzałe metryki powiązane z dyskami zostaną wkrótce usunięte z Azure Portal. Dostępna jest nowa wersja każdej przestarzałej metryki do użycia. W tym artykule opisano, które metryki są nowe i jak aktualizować alerty metryki w celu ich używania.

## <a name="list-of-new-metrics"></a>Lista nowych metryk

Ta tabela mapuje każdą przestarzałą metrykę na odpowiadającą jej nową metrykę. 

|Przestarzała Metryka|Nowa (zastępowanie) Metryka|
|----|----|
|Głębokość kolejki dysku danych (przestarzałe)|Głębokość kolejki dysku danych (wersja zapoznawcza)|
|Bajty odczytu dysku danych/s (przestarzałe)|Bajty odczytu dysku danych/s (wersja zapoznawcza)|
|Operacje odczytu z dysku danych/s (przestarzałe)|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|
|Bajty zapisu na dysku danych/s (przestarzałe)|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|
|Operacje zapisu na dysku danych/s (przestarzałe)|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|
|OS głębokość kolejki (przestarzałe)|Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)|
|Bajty odczytu systemu operacyjnego/s (przestarzałe)|Bajty odczytu systemu operacyjnego/s (wersja zapoznawcza)|
|Operacje odczytu systemu operacyjnego/s (przestarzałe)|Operacje odczytu systemu operacyjnego/s (wersja zapoznawcza)|
|Bajty zapisu systemu operacyjnego/s (przestarzałe)|Bajty zapisu systemu operacyjnego/s (wersja zapoznawcza)|
|Operacje zapisu systemu operacyjnego/s (przestarzałe)|Operacje zapisu systemu operacyjnego/s (wersja zapoznawcza)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrowanie metryk w alertach metryk

Zaktualizuj alerty metryki, aby używać nowych metryk.

1. W Azure Portal Wyszukaj **alerty**. Następnie w sekcji **usługi** wybierz pozycję **alerty**.

   > [!div class="mx-imgBorder"]
   > ![Usługa alertu](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na stronie **alerty** wybierz przycisk **Zarządzaj regułami alertów** . 

   > [!div class="mx-imgBorder"]
   > ![Zarządzaj regułami alertów](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Z listy rozwijanej **Grupa zasobów** zaznacz pole wyboru **Virtual Machines** i na liście rozwijanej **Typ sygnału** zaznacz pole wyboru **metryki** . 

   > [!div class="mx-imgBorder"]
   > ![Filtrowanie alertów](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Na liście metryk Zidentyfikuj warunki, które odnoszą się do dysków. Kliknij nazwę reguły. 

   Nazwa jest wyświetlana jako hiperłącze w kolumnie **Nazwa** tabeli.

   > [!div class="mx-imgBorder"]
   > ![Znajdź warunki dysku](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. W sekcji **warunki** na stronie **Zarządzanie regułami** kliknij warunek alertu. 

   Warunek jest wyświetlany jako hiperłącze.  

   > [!div class="mx-imgBorder"]
   > ![Dostosuj warunki](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Zostanie wyświetlona strona **Konfigurowanie logiki sygnału** , a ustawienia warunku zostaną wyświetlone w sekcji **logika alertu** na tej stronie.

6. Zanotuj te ustawienia, ponieważ zostaną usunięte po usunięciu przestarzałej metryki.

   > [!div class="mx-imgBorder"]
   > ![Reguły warunków](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Rozważ przechwycenie tych ustawień na zrzucie ekranu lub w pliku tekstowym. 

7. Kliknij link **Wróć do wyboru sygnału** .

   > [!div class="mx-imgBorder"]
   > ![Wróć do wyboru sygnałów](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na stronie **Konfiguruj logikę sygnału** wybierz odpowiednią metrykę wymiany (Nowa Metryka). Użyj [tabeli](#update-metrics) , która występuje wcześniej w tym artykule, aby zidentyfikować nazwę nowej metryki.

   > [!TIP] 
   > Zacznij pisać na pasku wyszukiwania, aby zawęzić listę nazw metryk. 

   > [!div class="mx-imgBorder"]
   > ![Wybierz nową metrykę](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Wybierz przycisk **gotowe** . 

   > [!div class="mx-imgBorder"]
   > ![Ustaw nową metrykę](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Zatwierdź zmiany, wybierając przycisk **Zapisz** . 

    > [!div class="mx-imgBorder"]
    > ![Zapisz nową metrykę](./media/portal-disk-metrics-deprecation/save-new-metric.png)






