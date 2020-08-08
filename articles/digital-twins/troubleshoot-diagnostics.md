---
title: Konfigurowanie diagnostyki
titleSuffix: Azure Digital Twins
description: Zobacz jak włączyć rejestrowanie przy użyciu ustawień diagnostycznych.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: d47bb7cc868c5733c6e36290f097fec783764cd3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003579"
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
        
        Aby uzyskać więcej informacji na temat tych opcji, zobacz sekcję [*szczegóły kategorii*](#category-details) poniżej.
     * **Szczegóły lokalizacji docelowej**: Wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:
        - Wysyłanie do usługi Log Analytics
        - Zarchiwizuj na koncie magazynu
        - Przesyłaj strumieniowo do centrum zdarzeń

        Może zostać wyświetlony monit o podanie dodatkowych informacji, jeśli są one niezbędne do wyboru miejsca docelowego.  
    
4. Zapisz nowe ustawienia. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym z powrotem na stronie **Ustawienia diagnostyczne** dla danego wystąpienia. 

## <a name="category-details"></a>Szczegóły kategorii

Poniżej znajdują się dodatkowe szczegóły dotyczące kategorii dziennika, które można wybrać w obszarze **szczegóły kategorii** podczas konfigurowania ustawień diagnostycznych.

| Kategoria dziennika | Opis |
| --- | --- |
| ADTModelsOperation | Rejestruj wszystkie wywołania interfejsu API odnoszące się do modeli |
| ADTQueryOperation | Rejestruj wszystkie wywołania interfejsu API odnoszące się do zapytań |
| ADTEventRoutesOperation | Rejestruj wszystkie wywołania interfejsu API dotyczące tras zdarzeń oraz wychodzące zdarzenia z usługi Azure Digital bliźniaczych reprezentacji do usługi punktu końcowego, takiej jak Event Grid, Event Hubs i Service Bus |
| ADTDigitalTwinsOperation | Rejestruj wszystkie wywołania interfejsu API związane z usługą Azure Digital bliźniaczych reprezentacji |

Każda kategoria dziennika składa się z operacji zapisu, odczytu, usuwania i akcji.  Te mapy do wywołań interfejsu API REST są następujące:

| Typ zdarzenia | Operacje interfejsu API REST |
| --- | --- |
| Zapisywanie | PUT i PATCH |
| Odczyt | GET |
| Usuń | DELETE |
| Akcja | POST |

Poniżej znajduje się kompleksowa Lista operacji i odpowiednich [wywołań interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji](https://docs.microsoft.com/rest/api/azure-digitaltwins/) , które są rejestrowane w każdej kategorii. 

>[!NOTE]
> Każda kategoria dziennika zawiera kilka wywołań operacji/interfejsu API REST. W poniższej tabeli każda kategoria dziennika jest mapowana na wszystkie operacje/wywołania interfejsu API REST, dopóki nie zostanie wyświetlona Następna kategoria dziennika. 

| Kategoria dziennika | Operacja | Wywołania interfejsu API REST i inne zdarzenia |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modele/zapis | Interfejs API aktualizacji modeli dwuosiowych |
|  | Microsoft. DigitalTwins/modele/odczyt | Cyfrowe modele przędzy uzyskiwane według identyfikatorów i interfejsów API listy |
|  | Microsoft. DigitalTwins/modele/usuwanie | Cyfrowe modele dwuosiowe — Usuwanie interfejsu API |
|  | Microsoft. DigitalTwins/modele/akcja | Modele Digital bliźniaczy Add API |
| ADTQueryOperation | Microsoft. DigitalTwins/kwerenda/akcja | Interfejs API bliźniaczych reprezentacji zapytań |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Dodawanie interfejsu API tras zdarzeń |
|  | Microsoft. DigitalTwins/eventroutes/odczyt | Trasy zdarzeń Get według identyfikatorów i interfejsów API |
|  | Microsoft. DigitalTwins/eventroutes/Delete | Interfejs API usuwania tras zdarzeń |
|  | Microsoft. DigitalTwins/eventroutes/Action | Wyjście zdarzenia do usługi punktu końcowego (nie wywołania interfejsu API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digital bliźniaczych reprezentacji Add, Dodawanie relacji, aktualizowanie, aktualizowanie składnika |
|  | Microsoft. DigitalTwins/DigitalTwins/odczyt | Digital bliźniaczych reprezentacji Get według identyfikatora, Pobierz składnik, uzyskaj relację według identyfikatora, Wyświetl listę relacji przychodzących, relacje list |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Digital bliźniaczych reprezentacji Delete, usuwanie relacji |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Dane telemetryczne składnika Digital bliźniaczych reprezentacji Send, wysyłanie danych telemetrycznych |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [*zbieranie i korzystanie z danych dzienników z zasobów platformy Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*Rozwiązywanie problemów: wyświetlanie metryk z Azure monitor*](troubleshoot-metrics.md).
* Aby dowiedzieć się, jak włączyć alerty dla metryk, zobacz [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md).
