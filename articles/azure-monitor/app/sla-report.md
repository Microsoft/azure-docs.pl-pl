---
title: Skoroszyt przestoju, SLA i przestoju — Application Insights
description: Oblicz i Zgłoś umowę SLA dla testów sieci Web za pomocą jednego okienka Glass w ramach zasobów Application Insights i subskrypcji platformy Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: 884d65d10ab0360957360efa8bf9dc4cac59949b
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989398"
---
# <a name="downtime-sla-and-outages-workbook"></a>Nieprzerwana, umowa SLA i skoroszyt przestoju

Wprowadzenie prostego sposobu obliczania i zgłaszania umów SLA (Umowa dotycząca poziomu usług) dla testów sieci Web za pośrednictwem jednego okienka Glass w ramach zasobów Application Insights i subskrypcji platformy Azure. Raport przestoje i awarie zawiera zaawansowane, wstępnie skompilowane zapytania i wizualizacje danych, dzięki czemu można wzmocnić zrozumienie łączności klienta, typowego czasu reakcji aplikacji i napotkanego czasu.

Szablon skoroszytu SLA jest dostępny za pomocą Galerii skoroszytów w zasobie Application Insights lub za pomocą karty dostępność, wybierając pozycję **raporty SLA** w górnej części strony.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Wyświetlona zrzut ekranu karty dostępność z wyróżnionymi raportami umów SLA." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Zrzut ekranu galerii skoroszytów z wyróżnionym czasem przestoju i skoroszytu" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Elastyczność parametrów

Parametry ustawione w skoroszycie wpływają na resztę raportu.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Zrzut ekranu przedstawiający kartę Parametry przestoju/konserwacji w skoroszycie przestoje i Przestój." lightbox ="./media/sla-report/outages.png":::

`Subscriptions``App Insights Resources`Parametry, i `Web Test` określają opcje zasobów wysokiego poziomu. Parametry te są oparte na zapytaniach usługi log Analytics i używane w każdej kwerendzie raportu.

`Failure Threshold` i `Outage Window` umożliwiają ustalenie własnych kryteriów przestoju usługi, na przykład kryteriów dla alertu dostępności w usłudze App Insights na podstawie licznika niepomyślnych lokalizacji w wybranym okresie. Typowy próg to trzy lokalizacje w oknie 5-minutowym.

`Maintenance Period` umożliwia wybranie typowej częstotliwości konserwacji i `Maintenance Window` jest selektorem DateTime dla przykładowego okresu konserwacji. Wszystkie dane, które pojawiają się w określonym okresie, zostaną zignorowane w wynikach.

`Availability Target 9s` Określa docelowy cel 9 z dwóch 9 do pięciu 9.

## <a name="overview-page"></a>Strona omówienia

Strona przegląd zawiera ogólne informacje o całkowitej liczbie umów SLA (z wyjątkiem okresów konserwacji, jeśli zostały zdefiniowane), kompleksowe wystąpienia przestoju i przestoju aplikacji. Wystąpienia przestojów są definiowane przez, gdy test zakończy się niepowodzeniem, dopóki nie zostanie on wyświetlony na podstawie parametrów przestoju. Jeśli test kończy się niepowodzeniem o godzinie 8:00 i kończy się powodzeniem o 10:00 am, wówczas cały okres danych jest uznawany za tę samą awarię.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" Obraz GIF strony przeglądowej przedstawiający tabelę przeglądu według testu." lightbox="./media/sla-report/overview.gif":::

Możesz również zbadać najdłuższe przestoje, które wystąpiły w okresie raportowania.

Niektóre testy są z powrotem połączone z Application Insights zasobem w celu dalszej analizy, ale jest to możliwe tylko w przypadku [zasobów Application Insights opartych na obszarze roboczym](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Przestój, przestoje i awarie

Karta **awaria i przestoje** zawiera informacje o całkowitej liczbie wystąpień przestojów i łącznym czasie w dół w wyniku testu. Karta **błędy według lokalizacji** ma mapę geograficzną zakończonych niepowodzeniem lokalizacji testowania, aby ułatwić zidentyfikowanie potencjalnych obszarów połączeń problemów.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" Karta GIF na kartach czas przestoju i przestój oraz niepowodzenie według lokalizacji." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Edytowanie raportu

Raport można edytować jak każdy inny [Azure monitor skoroszyt](../platform/workbooks-overview.md). Możesz dostosować zapytania lub wizualizacje w zależności od potrzeb zespołu.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF, wybierając przycisk Edytuj, aby zmienić wizualizację na wykres kołowy." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

Wszystkie zapytania można uruchamiać w [log Analytics](../log-query/log-analytics-overview.md) i używać ich w innych raportach lub pulpitach nawigacyjnych. Usuń ograniczenie parametru i ponownie użyj podstawowego zapytania.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF zapytania dziennika." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Dostęp i udostępnianie

Raport może być współużytkowany z zespołami, liderem lub przypięty do pulpitu nawigacyjnego w celu dodatkowego użycia. Użytkownik musi mieć uprawnienia do odczytu i dostęp do zasobu usługi Application Insights, w którym przechowywany jest rzeczywisty skoroszyt.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Zrzut ekranu przedstawiający udostępnianie tego szablonu." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Następne kroki

- [Log Analytics wskazówki dotyczące optymalizacji zapytań](../log-query/query-optimization.md).
- Dowiedz się, jak [utworzyć wykres w skoroszytach](../platform/workbooks-chart-visualizations.md).
- Dowiedz się, jak monitorować witrynę sieci Web za pomocą [testów dostępności](monitor-web-app-availability.md).
