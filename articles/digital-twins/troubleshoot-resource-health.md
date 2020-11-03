---
title: Informacje o kondycji zasobów
titleSuffix: Azure Digital Twins
description: Zobacz, jak używać Azure Resource Health, aby sprawdzić kondycję wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 9c31345a4ddaf9ac2b75204172dbc47606cb07db
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681731"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: Kondycja zasobów

[Azure Resource Health](../service-health/resource-health-overview.md) pomaga zdiagnozować i uzyskać pomoc techniczną dotyczącą problemów z usługami, które mają wpływ na zasoby platformy Azure. Raporty IT są raportowane w bieżącej i wcześniejszej kondycji zasobów.

W tym artykule pokazano, jak uzyskać informacje o **kondycji zasobów** dla wystąpień bliźniaczych reprezentacji cyfrowych platformy Azure.

## <a name="use-azure-resource-health"></a>Użyj Azure Resource Health

Azure Resource Health może pomóc w monitorowaniu, czy wystąpienie usługi Azure Digital bliźniaczych reprezentacji jest uruchomione. Można go również użyć, aby dowiedzieć się, czy regionalna awaria ma wpływ na kondycję wystąpienia.

Aby sprawdzić kondycję wystąpienia, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Z menu wystąpienia wybierz pozycję _**Kondycja zasobów**_ w obszarze *Pomoc techniczna i rozwiązywanie problemów*. Spowoduje to przejście do strony umożliwiającej wyświetlenie historii kondycji zasobów. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;kondycja zasobu&quot;. Istnieje sekcja &quot;Historia kondycji&quot; pokazująca dzienny Raport z ostatnich dziewięciu dni. Każdy dzień pokazuje stan &quot;dostępne&quot;.":::

Na powyższym obrazie to wystąpienie jest wyświetlane jako *dostępne* i było w ciągu ostatnich dziewięciu dni. Aby dowiedzieć się więcej na temat *dostępnego* stanu i innych typów stanu, które mogą się pojawić, zobacz [*Omówienie usługi Azure Resource Health*](../service-health/resource-health-overview.md).

Możesz również dowiedzieć się więcej o różnych kontrolach, które przechodzą do kondycji zasobów dla różnych typów zasobów platformy Azure w [*typach zasobów i kontrolach kondycji w usłudze Azure Resource Health*](../service-health/resource-health-checks-resource-types.md).

## <a name="next-steps"></a>Następne kroki

Przeczytaj o innych sposobach monitorowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji w następujących artykułach:
* [*Rozwiązywanie problemów: wyświetlanie metryk przy użyciu Azure Monitor*](troubleshoot-metrics.md)
* [*Rozwiązywanie problemów: Skonfiguruj diagnostykę*](troubleshoot-diagnostics.md).
* [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md)
