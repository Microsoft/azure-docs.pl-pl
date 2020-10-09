---
title: Monitorowanie opublikowanych interfejsów API w usłudze Azure API Management | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak monitorować interfejs API w usłudze Azure API Management.
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 7f6c7a651e133122dab86d6ed81572f239718b43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243243"
---
# <a name="monitor-published-apis"></a>Monitorowanie opublikowanych interfejsów API

Dzięki usłudze Azure Monitor możesz wykonywać wizualizacje i zapytania, ustalać trasy, archiwizować i podejmować działania dotyczące metryk lub dzienników pochodzących z zasobów platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie dzienników aktywności
> * Wyświetlanie dzienników zasobów
> * Wyświetlanie metryk interfejsu API 
> * Konfigurowanie reguły alertu, gdy interfejs API odbiera nieautoryzowane wywołania

W poniższym filmie wideo pokazano, jak monitorować usługę API Management przy użyciu usługi Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Wyświetlanie metryk interfejsów API

Usługa API Management emituje metryki co minutę, oferując wgląd w stan i kondycję interfejsów API w czasie zbliżonym do rzeczywistego. Poniżej znajdują się dwa najczęściej używane metryki. Aby uzyskać listę wszystkich dostępnych metryk, zobacz [obsługiwane metryki](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* Pojemność: ułatwia podejmowanie decyzji dotyczących uaktualniania/obniżania wersji usług APIM. Metryka jest emitowana co minutę i odzwierciedla pojemność bramy w momencie raportowania. Wartość metryki należy do zakresu od 0 do 100 i jest obliczana w oparciu o zasoby bramy, takie jak wykorzystanie procesora i pamięci.
* Żądania: ułatwia analizowanie ruchu interfejsu API przechodzącego przez usługi APIM. Metryka jest emitowana na minutę i zgłasza liczbę żądań bramy z wymiarami, w tym kodami odpowiedzi, lokalizacjami, nazwą hosta i błędami. 

> [!IMPORTANT]
> Następujące metryki zostały wycofane z maja 2019 i zostaną wycofane w sierpniu 2023: całkowita liczba żądań bramy, pomyślne żądania bramy, nieautoryzowane żądania bramy, Nieudane żądania bramy, inne żądania bramy. Przeprowadź migrację do metryki żądań, która zapewnia równoważną funkcjonalność.

![wykres metryk](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Aby uzyskać dostęp do metryk:

1. Wybierz pozycję **Metryki** w menu w dolnej części strony.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Z listy rozwijanej wybierz interesujące Cię metryki. Na przykład **żądania**. 
3. Wykres przedstawia łączną liczbę wywołań interfejsu API.
4. Wykres może być filtrowany przy użyciu wymiarów metryk **żądań** . Na przykład kliknij pozycję **Dodaj filtr**, wybierz pozycję **kod odpowiedzi zaplecza**, wprowadź 500 jako wartość. Teraz wykres pokazuje liczbę żądań, które zakończyły się niepowodzeniem w zapleczu interfejsu API.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurowanie reguły alertu na potrzeby nieautoryzowanego żądania

Można skonfigurować odbieranie alertów w oparciu o metryki i dzienniki aktywności. Usługa Azure Monitor umożliwia skonfigurowanie alertu powodującego wykonywanie następujących czynności po jego wyzwoleniu:

* Wysyłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować alerty:

1. Wybierz pozycję **alerty** na pasku menu w dolnej części strony.

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. Kliknij **nową regułę alertu** dla tego alertu.
3. Kliknij pozycję **Dodaj warunek**.
4. Wybierz pozycję **metryki** na liście rozwijanej Typ sygnału.
5. Wybierz pozycję **nieautoryzowane żądanie bramy** jako sygnał do monitorowania.

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. W widoku **Konfiguracja logiki sygnału** Określ próg, po którym alert powinien zostać wyzwolony, a następnie kliknij pozycję **gotowe**.

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. Wybierz istniejącą grupę akcji lub Utwórz nową. W poniższym przykładzie wiadomość e-mail zostanie wysłana do administratorów. 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. Podaj nazwę, opis reguły alertu i wybierz poziom ważności. 
9. Naciśnij pozycję **Utwórz regułę alertu**.
10. Teraz spróbuj wywołać interfejs API konferencji bez klucza interfejsu API. Alert zostanie wyzwolony, a wiadomość e-mail zostanie wysłana do administratorów. 

## <a name="activity-logs"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do usług API Management. Korzystając z tych dzienników, można określić rodzaj, użytkownika i czas każdej operacji zapisu (PUT, POST, DELETE) wykonanej względem usług API Management.

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET) ani operacji wykonywanych w witrynie Azure Portal albo przy użyciu oryginalnych interfejsów API zarządzania.

Dostęp do dzienników aktywności można uzyskać w usłudze API Management, a dostęp do wszystkich zasobów platformy Azure — w usłudze Azure Monitor. 

![dzienniki aktywności](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Dziennik aktywności**.

    ![dziennik aktywności](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Wybierz żądany zakres filtrowania i kliknij przycisk **Zastosuj**.

## <a name="resource-logs"></a>Dzienniki zasobów

Dzienniki zasobów zawierają bogate informacje o operacjach i błędach, które są ważne do celów inspekcji, a także do rozwiązywania problemów. Dzienniki zasobów różnią się od dzienników aktywności. Dzienniki aktywności udostępniają szczegółowe informacje o operacjach wykonywanych na zasobach platformy Azure. Dzienniki zasobów zapewniają wgląd w operacje wykonywane przez zasób.

Aby skonfigurować dzienniki zasobów:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Ustawienia diagnostyczne**.

    ![Dzienniki zasobów](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Kliknij pozycję **Włącz diagnostykę**. Dzienniki zasobów oraz metryki można archiwizować na koncie magazynu, przesyłać strumieniowo do centrum zdarzeń lub wysyłać do dzienników Azure Monitor. 

API Management obecnie udostępnia dzienniki zasobów (wsadowe co godzinę) dotyczące poszczególnych żądań interfejsu API z każdym wpisem, który ma następujący schemat:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Właściwość  | Typ | Opis |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | Wartość true, jeśli żądanie HTTP zostało zakończone z kodem stanu odpowiedzi z zakresu 2xx lub 3xx |
| time | data i godzina | Sygnatura czasowa rozpoczęcia przetwarzania żądania przez bramę |
| operationName | ciąg | Wartość stała „Microsoft.ApiManagement/GatewayLogs” |
| category | ciąg | Wartość stała „GatewayLogs” |
| durationMs | liczba całkowita | Liczba milisekund od momentu odebrania żądania przez bramę do momentu, gdy odpowiedź na chwilę zostanie wysłana w całości. Obejmuje to clienTime, cacheTime i backendTime. |
| callerIpAddress | ciąg | Adres IP bezpośredniego modułu wywołującego bramy (może być pośrednik) |
| correlationId | ciąg | Unikatowy identyfikator żądania HTTP przypisany przez usługę API Management |
| location | ciąg | Nazwa regionu platformy Azure, w którym znajdowała się brama przetwarzająca żądanie |
| httpStatusCodeCategory | ciąg | Kategoria kodu stanu odpowiedzi HTTP: Powodzenie (301 lub mniej albo 304 lub 307), Bez autoryzacji (401, 403, 429), Błąd (400, od 500 do 600), Inne |
| resourceId | ciąg | Identyfikator zasobu API Management/SUBSCRIPTIONS/ \<subscription> /RESOURCEGROUPS/ \<resource-group> /providers/Microsoft. APIMANAGEMENT/SERVICE/\<name> |
| properties | object | Właściwości bieżącego żądania |
| method | ciąg | Metoda HTTP żądania przychodzącego |
| url | ciąg | Adres URL żądania przychodzącego |
| clientProtocol | ciąg | Wersja protokołu HTTP żądania przychodzącego |
| responseCode | liczba całkowita | Kod stanu odpowiedzi HTTP wysłanej do klienta |
| backendMethod | ciąg | Metoda HTTP żądania wysłanego do zaplecza |
| backendUrl | ciąg | Adres URL żądania wysłanego do zaplecza |
| backendResponseCode | liczba całkowita | Kod odpowiedzi HTTP odebranej z zaplecza |
| backendProtocol | ciąg | Wersja protokołu HTTP żądania wysłanego do zaplecza | 
| requestSize | liczba całkowita | Liczba bajtów odebranych od klienta podczas przetwarzania żądania | 
| responseSize | liczba całkowita | Liczba bajtów wysłanych do klienta podczas przetwarzania żądania | 
| cache | ciąg | Stan zaangażowania pamięci podręcznej usługi API Management w przetwarzanie żądania (tj. trafienie, chybienie, brak) | 
| cacheTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy pamięci podręcznej w usłudze API Management (łączenie, wysyłanie i odbieranie bajtów) | 
| backendTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy zaplecza (łączenie, wysyłanie i odbieranie bajtów) | 
| clientTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy klienta (łączenie, wysyłanie i odbieranie bajtów) | 
| apiId | ciąg | Identyfikator jednostki interfejsu API dla bieżącego żądania | 
| operationId | ciąg | Identyfikator jednostki operacji dla bieżącego żądania | 
| productId | ciąg | Identyfikator jednostki produktu dla bieżącego żądania | 
| userId | ciąg | Identyfikator jednostki użytkownika dla bieżącego żądania | 
| apimSubscriptionId | ciąg | Identyfikator jednostki subskrypcji dla bieżącego żądania | 
| backendId | ciąg | Identyfikator jednostki zaplecza dla bieżącego żądania | 
| LastError | object | Ostatni błąd przetwarzania żądania | 
| elapsed | liczba całkowita | Liczba milisekund upływających od momentu odebrania żądania przez bramę i momentu wystąpienia błędu | 
| source | string | Nazwa wewnętrznej procedury obsługi przetwarzania lub zasad, które spowodowały błąd | 
| scope | ciąg | Zakres dokumentu zasad zawierający zasady, które spowodowały błąd | 
| section | ciąg | Sekcja dokumentu zasad zawierająca zasady, które spowodowały błąd | 
| reason | ciąg | Przyczyna błędu | 
| message | ciąg | Komunikat o błędzie | 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie dzienników aktywności
> * Wyświetlanie dzienników zasobów
> * Wyświetlanie metryk interfejsu API
> * Konfigurowanie reguły alertu, gdy interfejs API odbiera nieautoryzowane wywołania

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Śledzenie wywołań](api-management-howto-api-inspector.md)
