---
title: Konfigurowanie diagnostyki
titleSuffix: Azure Digital Twins
description: Zobacz jak włączyć rejestrowanie przy użyciu ustawień diagnostycznych.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 0376a57e3f2c1158e9da97fb291a28c99ce2463c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903967"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: rejestrowanie diagnostyczne

Usługa Azure Digital bliźniaczych reprezentacji zbiera [metryki](troubleshoot-metrics.md) dla wystąpienia usługi, które zawierają informacje o stanie zasobów. Za pomocą tych metryk można ocenić ogólną kondycję usługi Azure Digital bliźniaczych reprezentacji i połączone z nią zasoby. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z usługą Azure Digital bliźniaczych reprezentacji, oraz jak przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

W tym artykule pokazano, jak włączyć **rejestrowanie diagnostyczne** dla danych metryk z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Dzienników tych można użyć do rozwiązywania problemów z usługą i konfigurowania ustawień diagnostycznych w celu wysyłania metryk usługi Azure Digital bliźniaczych reprezentacji do różnych miejsc docelowych. Więcej informacji o tych ustawieniach można znaleźć w temacie [*Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Włączanie ustawień diagnostycznych przy użyciu Azure Portal

Poniżej przedstawiono sposób włączania ustawień diagnostycznych dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Z menu wybierz pozycję **Ustawienia diagnostyczne** , a następnie **Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

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
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*Rozwiązywanie problemów: wyświetlanie metryk z Azure monitor*](troubleshoot-metrics.md).
* Aby dowiedzieć się, jak włączyć alerty dla metryk, zobacz [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md).
