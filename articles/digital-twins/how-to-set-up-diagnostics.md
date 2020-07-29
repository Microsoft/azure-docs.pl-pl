---
title: Konfigurowanie diagnostyki
titleSuffix: Azure Digital Twins
description: Zobacz jak włączyć rejestrowanie przy użyciu ustawień diagnostycznych.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374568"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Włączanie rejestrowania przy użyciu ustawień diagnostycznych

Możesz wysłać dane metryk do [log Analytics](../azure-monitor/log-query/get-started-portal.md), punktu końcowego [Event Hubs](../event-hubs/event-hubs-about.md) lub [usługi Azure Storage](../storage/blobs/storage-blobs-overview.md) , włączając rejestrowanie z ustawieniami diagnostycznymi dla danego wystąpienia.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Włączanie ustawień diagnostycznych przy użyciu Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Z menu wybierz pozycję **Ustawienia diagnostyczne** , a następnie **Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

3. Na następnej stronie wypełnij następujące wartości:
 * **Nazwa ustawienia diagnostycznego**: nadaj nazwę ustawień diagnostycznych.
 * **Szczegóły kategorii**: Wybierz operacje, które chcesz monitorować, i zaznacz pola wyboru, aby włączyć diagnostykę tych operacji. Dla operacji, które mogą być zgłaszane przez ustawienia diagnostyczne, są następujące:
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - Queryoperation obiektu DataService
    - AllMetrics
 * **Szczegóły lokalizacji docelowej**: Wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:
    - Wysyłanie do usługi Log Analytics
    - Zarchiwizuj na koncie magazynu
    - Przesyłaj strumieniowo do centrum zdarzeń

    Może zostać wyświetlony monit o podanie dodatkowych informacji, jeśli są one niezbędne do wyboru miejsca docelowego.  
    
4. Zapisz nowe ustawienia. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym z powrotem na stronie **Ustawienia diagnostyczne** dla danego wystąpienia. 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [*zbieranie i korzystanie z danych dzienników z zasobów platformy Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [ *How to: View Metrics with Azure monitor*](how-to-view-metrics.md)