---
title: Konfigurowanie diagnostyki
titleSuffix: Azure Digital Twins
description: Zobacz jak włączyć rejestrowanie przy użyciu ustawień diagnostycznych.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1a349883c8144aaff4aed70ba46ad0eaab04fe69
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388441"
---
# <a name="troubleshooting-azure-digital-twins-with-diagnostics-logging"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji z rejestrowaniem diagnostyki

Usługa Azure Digital bliźniaczych reprezentacji zbiera [metryki](how-to-view-metrics.md) dla wystąpienia usługi, które zawierają informacje o stanie zasobów. Za pomocą tych metryk można ocenić ogólną kondycję usługi Azure Digital bliźniaczych reprezentacji i połączone z nią zasoby. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z usługą Azure Digital bliźniaczych reprezentacji, oraz jak przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

W tym artykule pokazano, jak włączyć **rejestrowanie diagnostyczne** dla danych metryk z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Te dzienniki mogą pomóc w rozwiązywaniu problemów z usługą.

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym z powrotem na stronie **Ustawienia diagnostyczne** dla danego wystąpienia. 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [*zbieranie i korzystanie z danych dzienników z zasobów platformy Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*How to: View Metrics with Azure monitor*](how-to-view-metrics.md).
