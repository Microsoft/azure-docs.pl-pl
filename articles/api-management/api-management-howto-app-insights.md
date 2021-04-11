---
title: Integracja usługi Azure API Management z usługą Azure Application Insights
titleSuffix: Azure API Management
description: Dowiedz się, jak rejestrować i wyświetlać zdarzenia z usługi Azure API Management w usłudze Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564266"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Jak zintegrować usługę Azure API Management z usługą Azure Application Insights

Usługa Azure API Management pozwala na łatwą integrację z usługą Azure Application Insights — rozszerzalną usługę dla deweloperów sieci Web tworzących aplikacje i zarządzające nimi na wielu platformach. Ten przewodnik przeprowadzi Cię przez każdy krok integracji i opisuje strategie zmniejszania wpływu na wydajność wystąpienia usługi API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przewodnika, musisz mieć wystąpienie usługi Azure API Management. Jeśli go nie masz, najpierw Wypełnij [samouczek](get-started-create-service-instance.md) .

## <a name="create-an-application-insights-instance"></a>Tworzenie wystąpienia Application Insights

Przed rozpoczęciem korzystania z Application Insights należy najpierw utworzyć wystąpienie usługi. Aby uzyskać instrukcje dotyczące tworzenia wystąpienia przy użyciu Azure Portal, zobacz [zasoby Application Insights oparte na obszarze roboczym](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Utwórz połączenie między Application Insights i API Management

1. Przejdź do **wystąpienia usługi Azure API Management** w **Azure Portal**.
1. Wybierz pozycję **Application Insights** z menu po lewej stronie.
1. Kliknij pozycję **+ Dodaj**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Zrzut ekranu pokazujący, gdzie dodać nowe połączenie":::
1. Wybierz wcześniej utworzone wystąpienie **Application Insights** i podaj Krótki opis.
1. Kliknij pozycję **Utwórz**.
1. Właśnie utworzono Rejestrator Application Insights z kluczem Instrumentacji. Powinien teraz pojawić się na liście.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Zrzut ekranu przedstawiający miejsce wyświetlania nowo utworzonego rejestratora Application Insights z kluczem Instrumentacji":::

> [!NOTE]
> Za sceną jest tworzona jednostka [rejestratora](/rest/api/apimanagement/2019-12-01/logger/createorupdate) w wystąpieniu API Management, zawierająca klucz Instrumentacji wystąpienia Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Włączanie rejestrowania Application Insights dla interfejsu API

1. Przejdź do **wystąpienia usługi Azure API Management** w **Azure Portal**.
1. Wybierz pozycję **Interfejsy API** z menu po lewej stronie.
1. Kliknij swój interfejs API, w tym przypadku **interfejsu API konferencji demonstracyjnej**. W przypadku skonfigurowania wybierz wersję.
1. Przejdź do karty **Ustawienia** na górnym pasku.
1. Przewiń w dół do sekcji **dzienniki diagnostyczne** .  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Rejestrator usługi App Insights":::
1. Zaznacz pole **Włącz** .
1. Wybierz dołączony rejestrator w polu rozwijanym **miejsce docelowe** .
1. Wprowadź **100** jako **próbkowanie (%)** i zaznacz pole wyboru **zawsze Rejestruj błędy** .
1. Wybierz pozycję **Zapisz**.

> [!WARNING]
> Zastąpienie wartości domyślnej **0** w polu **liczba bajtów ładunku do zarejestrowania** może znacząco obniżyć wydajność interfejsów API.

> [!NOTE]
> Za sceną jest tworzona jednostka [diagnostyczna](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) o nazwie "ApplicationInsights" na poziomie interfejsu API.

| Nazwa ustawienia                        | Typ wartości                        | Opis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włącz                              | boolean                           | Określa, czy jest włączone rejestrowanie tego interfejsu API.                                                                                                                                                                                                                                                                                                |
| Element docelowy                         | Rejestrator Application Insights platformy Azure | Określa Rejestrator Application Insights platformy Azure do użycia                                                                                                                                                                                                                                                                                           |
| Próbkowanie (%)                        | decimal                           | Wartości od 0 do 100 (procent). <br/> Określa procent żądań, które będą rejestrowane w Application Insights. 0% próbkowanie oznacza, że zarejestrowano zero żądań, a próbkowanie 100% oznacza, że wszystkie żądania zostały zarejestrowane. <br/> Użyj tego ustawienia, aby zmniejszyć wpływ na wydajność podczas rejestrowania żądań do Application Insights. Zobacz [implikacje wydajności i próbkowanie dzienników](#performance-implications-and-log-sampling). |
| Zawsze Rejestruj błędy                   | boolean                           | W przypadku wybrania tego ustawienia wszystkie błędy będą rejestrowane w Application Insights, niezależnie od ustawienia **próbkowania** .   
| Adres IP klienta dziennika | |  W przypadku wybrania tego ustawienia adres IP klienta dla żądań interfejsu API zostanie zarejestrowany w Application Insights.                                         |
| Szczegółowość         |                                   | Określa poziom szczegółowości. Rejestrowane będą tylko niestandardowe dane śledzenia o wyższym poziomie ważności. Wartość domyślna: informacje.      | 
| Protokół korelacji |  |  Wybierz protokół używany do skorelowania telemetrii wysyłanej przez wiele składników. Domyślne: Starsza wersja <br/>Aby uzyskać więcej informacji, zobacz [korelacja telemetrii w Application Insights](../azure-monitor/app/correlation.md).  |
| Opcje podstawowe: nagłówki do rejestrowania              | list                              | Określa nagłówki, które będą rejestrowane w Application Insights na potrzeby żądań i odpowiedzi.  Wartość domyślna: żadne nagłówki nie są rejestrowane.                                                                                                                                                                                                             |
| Opcje podstawowe: liczba bajtów ładunku do zarejestrowania | liczba całkowita                           | Określa, ile pierwszych bajtów treści jest rejestrowanych do Application Insights na potrzeby żądań i odpowiedzi.  Wartość domyślna: 0.                                                                                                                                                                                                    |
| Opcje zaawansowane: żądanie frontonu  |                                   | Określa, czy i w jaki sposób *żądania frontonu* będą rejestrowane w Application Insights. *Żądanie frontonu* to żądanie przychodzące do usługi Azure API Management.                                                                                                                                                                        |
| Opcje zaawansowane: odpowiedź frontonu |                                   | Określa, czy i w jaki sposób *odpowiedzi frontonu* będą rejestrowane w Application Insights. *Odpowiedź frontonu* to odpowiedź wychodząca z usługi Azure API Management.                                                                                                                                                                   |
| Opcje zaawansowane: żądanie wewnętrznej bazy danych   |                                   | Określa, czy i w jaki sposób mają być rejestrowane *żądania zaplecza* Application Insights. *Żądanie zaplecza* to żądanie wychodzące z usługi Azure API Management.                                                                                                                                                                        |
| Opcje zaawansowane: odpowiedź zaplecza  |                                   | Określa, czy i w jaki sposób *odpowiedzi zaplecza* będą rejestrowane w Application Insights. *Odpowiedź zaplecza* to odpowiedź przychodząca do usługi Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Można określić rejestratory na różnych poziomach — pojedynczy Rejestrator interfejsu API lub Rejestrator dla wszystkich interfejsów API.
>  
> W przypadku określenia obu:
> + Jeśli są to różne rejestratory, zostaną użyte oba typy (dzienniki multipleksowania),
> + Jeśli są to te same rejestratory, ale mają różne ustawienia, to jeden dla jednego interfejsu API (bardziej szczegółowy) przesłoni jeden dla wszystkich interfejsów API.

## <a name="what-data-is-added-to-application-insights"></a>Jakie dane są dodawane do Application Insights

Application Insights odbiera:

+ *Żądaj* elementu telemetrii dla każdego żądania przychodzącego (*żądanie frontonu*, *odpowiedź frontonu*),
+ Element telemetrii *zależności* dla każdego żądania przekazanego do usługi zaplecza (*żądanie wewnętrznej bazy* danych, *odpowiedź zaplecza*),
+ Element telemetrii *wyjątku* dla każdego żądania zakończonego niepowodzeniem:
    + Niepowodzenie z powodu zamkniętego połączenia z klientem
    + wyzwolono sekcję *on-Error* zasad interfejsu API
    + ma odpowiedź zgodną z kodem stanu HTTP 4xx lub 5xx.
+ *Śledzenie* elementu telemetrii, jeśli skonfigurowano zasady [śledzenia](api-management-advanced-policies.md#Trace) . `severity`Ustawienie w `trace` zasadach musi mieć wartość równą lub większą od `verbosity` ustawienia w dzienniku Application Insights.

> [!NOTE]
> Aby uzyskać informacje o maksymalnym rozmiarze i liczbie metryk i zdarzeń na wystąpienie Application Insights, zobacz [limity Application Insights](../azure-monitor/service-limits.md#application-insights) .

## <a name="performance-implications-and-log-sampling"></a>Wpływ na wydajność i próbkowanie dzienników

> [!WARNING]
> Rejestrowanie wszystkich zdarzeń może mieć poważne konsekwencje dla wydajności, w zależności od szybkości żądań przychodzących.

Na podstawie wewnętrznych testów obciążenia włączenie tej funkcji spowodowało spadek przepływności o 40% – 50%, gdy liczba żądań przekroczyła 1 000 żądań na sekundę. Application Insights jest przeznaczony do oceny wydajności aplikacji przy użyciu analizy statystycznej. Nie jest to system audytu i nie jest przystosowany do rejestrowania każdego pojedynczego żądania dla interfejsów API.

Liczbę rejestrowanych żądań można manipulować przez dostosowanie ustawienia **próbkowania** (patrz poprzednie kroki). Wartość 100% oznacza, że wszystkie żądania są rejestrowane, natomiast 0% nie odzwierciedla rejestrowania. **Próbkowanie** pomaga zmniejszyć ilość danych telemetrycznych, co skutecznie uniemożliwia znaczne obniżenie wydajności, zachowując jednocześnie zalety rejestrowania.

Pomijanie rejestrowania nagłówków i treści żądań i odpowiedzi spowoduje również pozytywny wpływ na rozwiązywanie problemów z wydajnością.

## <a name="video"></a>Wideo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [usłudze Azure Application Insights](/azure/application-insights/).
+ Rozważ [zarejestrowanie się w usłudze Azure Event Hubs](api-management-howto-log-event-hubs.md).
