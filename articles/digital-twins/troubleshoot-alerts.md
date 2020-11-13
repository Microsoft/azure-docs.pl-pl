---
title: Konfigurowanie alertów
titleSuffix: Azure Digital Twins
description: Zobacz, jak włączyć alerty dotyczące metryk usługi Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 057a1b08095a71e2fca6119f5b63bfdc787eea88
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616606"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: alerty

Usługa Azure Digital bliźniaczych reprezentacji zbiera [metryki](troubleshoot-metrics.md) dla wystąpienia usługi, które zawierają informacje o stanie zasobów. Za pomocą tych metryk można ocenić ogólną kondycję usługi Azure Digital bliźniaczych reprezentacji i połączone z nią zasoby.

**Alerty** z wyprzedzeniem powiadamiają użytkownika o znalezieniu w danych metryk ważnych warunków. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. Więcej informacji na temat alertów można znaleźć w temacie [*Omówienie alertów w Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).

## <a name="turn-on-alerts"></a>Włącz alerty

Poniżej przedstawiono sposób włączania alertów dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Z menu wybierz opcję **alerty** , a następnie pozycję **+ Nowa reguła alertu**.

3. Na stronie *Tworzenie reguły alertu* , która znajduje się poniżej, można postępować zgodnie z monitami, aby zdefiniować warunki, akcje, które mają być wyzwalane, oraz szczegóły alertu.     
    * Szczegóły **zakresu** powinny być wypełniane automatycznie przy użyciu szczegółów dla danego wystąpienia
    * Zdefiniuj **warunek** i szczegóły **grupy akcji** , aby dostosować wyzwalacze i odpowiedzi alertów.
    * W sekcji **szczegóły reguły alertu** wprowadź nazwę i opcjonalny opis reguły. Możesz zaznaczyć pole wyboru _Włącz regułę alertu przy tworzeniu_ , jeśli chcesz, aby alert stał się aktywny zaraz po jego utworzeniu.
        - W tym miejscu można również wybrać _grupę zasobów_ i poziom _ważności_ .

4. Wybierz przycisk _Utwórz regułę alertu_ , aby utworzyć regułę alertu.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie reguły alertów z sekcjami dotyczącymi zakresu, warunku, grupy akcji i szczegółów reguły alertu" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Aby zapoznać się z przewodnikiem dotyczącym wypełniania tych pól, zobacz [*Omówienie alertów w Microsoft Azure*](../azure-monitor/platform/alerts-overview.md). Poniżej przedstawiono kilka przykładów, które będą wyglądały tak, jak w przypadku usługi Azure Digital bliźniaczych reprezentacji.

### <a name="select-conditions"></a>Wybierz warunki

Poniżej znajduje się fragment z procesu *SELECT Condition* pokazujący typy sygnałów alertów, które są dostępne dla usługi Azure Digital bliźniaczych reprezentacji. Na tej stronie można filtrować typ sygnału i wybrać żądany sygnał z listy.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Zrzut ekranu przedstawiający stronę Pierwsza konfiguracja logiki sygnału. Wyróżnienie pola Typ sygnału umożliwia wybranie metryk lub dzienników aktywności oraz listę metryk poniżej, które mogą być wybrane.":::

Po wybraniu sygnału zostanie wyświetlony monit o skonfigurowanie logiki alertu. Można filtrować według wymiaru, ustawiać wartość progową dla alertu i ustawić częstotliwość sprawdzania dla warunku. Poniżej znajduje się przykład konfigurowania alertu w przypadku, gdy Metryka dotycząca średniego współczynnika błędów routingu spadnie powyżej 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Zrzut ekranu przedstawiający stronę drugiej konfiguracji logiki sygnałów. Na stronie zostanie wyświetlona historia metryk, która ma obszar do filtrowania według wymiaru, takiego jak Event Grid operacje, a sekcja definiowania logiki alertu, taka jak &quot;średnia jest większa niż 5&quot;":::

### <a name="verify-success"></a>Weryfikowanie sukcesu

Po skonfigurowaniu alertów zostaną one wyświetlone na stronie *alerty* dla danego wystąpienia.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Zrzut ekranu przedstawiający stronę alerty i przycisk do dodania. Skonfigurowano jeden alert" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat alertów z Azure Monitor, zobacz [*Omówienie alertów w programie Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*Rozwiązywanie problemów: wyświetlanie metryk z Azure monitor*](troubleshoot-metrics.md).
* Aby dowiedzieć się, jak włączyć rejestrowanie diagnostyczne dla metryk, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).
