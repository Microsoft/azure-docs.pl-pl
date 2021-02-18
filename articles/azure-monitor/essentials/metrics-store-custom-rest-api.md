---
title: Wysyłanie metryk do bazy danych metryk Azure Monitor przy użyciu interfejsu API REST
description: Wysyłanie metryk niestandardowych dla zasobu platformy Azure do magazynu metryk Azure Monitor przy użyciu interfejsu API REST
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: c315cc8c9dfcfa66999ff263fab95f414061e54e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615090"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Wysyłanie metryk niestandardowych dla zasobu platformy Azure do magazynu metryk Azure Monitor przy użyciu interfejsu API REST

W tym artykule pokazano, jak wysyłać niestandardowe metryki dla zasobów platformy Azure do magazynu metryk Azure Monitor za pośrednictwem interfejsu API REST. Gdy metryki znajdują się w Azure Monitor, można wykonać wszystkie czynności z nimi za pomocą standardowych metryk. Przykłady to wykresy, alerty i trasy do innych narzędzi zewnętrznych.  

>[!NOTE]  
>Interfejs API REST zezwala tylko na wysyłanie metryk niestandardowych dla zasobów platformy Azure. Aby wysyłać metryki dla zasobów w różnych środowiskach lub lokalnie, można użyć [Application Insights](../app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Utwórz i Autoryzuj jednostkę usługi, aby emitować metryki 

Utwórz nazwę główną usługi w dzierżawie Azure Active Directory przy użyciu instrukcji znajdujących się w temacie [Create a Service Principal](../../active-directory/develop/howto-create-service-principal-portal.md). 

Podczas tego procesu wykonaj następujące czynności: 

- Możesz wprowadzić dowolny adres URL dla adresu URL logowania.  
- Utwórz nowy klucz tajny klienta dla tej aplikacji.  
- Zapisz klucz i identyfikator klienta do użycia w dalszych krokach.  

Nadaj aplikacji utworzoną jako część kroku 1, monitorowanie wydawcy metryk, uprawnienia do zasobu, dla którego chcesz emitować metryki. Jeśli planujesz używać aplikacji do emitowania metryk niestandardowych względem wielu zasobów, możesz przyznać te uprawnienia na poziomie grupy zasobów lub subskrypcji. 

## <a name="get-an-authorization-token"></a>Pobieranie tokenu autoryzacji
Otwórz wiersz polecenia i uruchom następujące polecenie:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Zapisz token dostępu z odpowiedzi.

![Token dostępu](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emituj metrykę za pośrednictwem interfejsu API REST 

1. Wklej poniższy kod JSON do pliku i Zapisz go jako **custommetric.jsna** komputerze lokalnym. Zaktualizuj parametr Time w pliku JSON: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. W oknie wiersza polecenia Opublikuj dane metryk: 
   - **azureRegion**. Musi być zgodna z regionem wdrożenia zasobu, dla którego są emitowane metryki. 
   - Identyfikator **zasobu**.  Identyfikator zasobu platformy Azure, względem którego jest śledzona Metryka.  
   - **AccessToken**. Wklej wcześniej uzyskany token.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Zmień sygnaturę czasową i wartości w pliku JSON. 
1. Powtarzaj dwa poprzednie kroki kilka razy, aby dane były dostępne przez kilka minut.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Jeśli zostanie wyświetlony komunikat o błędzie z częścią procesu, należy wziąć pod uwagę następujące informacje dotyczące rozwiązywania problemów:

1. Nie można wystawić metryk dla subskrypcji lub grupy zasobów jako zasobu platformy Azure. 
1. Nie można umieścić metryki w sklepie przekraczającym 20 minut. Magazyn metryk jest zoptymalizowany pod kątem alertów i wykresów w czasie rzeczywistym. 
2. Liczba nazw wymiarów powinna być zgodna z wartościami i na odwrót. Sprawdź wartości. 
2. Mogą być emitowane metryki z regionem, który nie obsługuje metryk niestandardowych. Zobacz [Obsługiwane regiony](./metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Wyświetl metryki 

1. Zaloguj się do witryny Azure Portal. 

1. W menu po lewej stronie wybierz pozycję **monitorowanie**. 

1. Na stronie **monitorowanie** wybierz pozycję **metryki**. 

   ![Wybierz metryki](./media/metrics-store-custom-rest-api/metrics.png) 

1. Zmień okres agregacji na **ostatni 30 minut**.  

1. Z menu rozwijanego **zasób** wybierz zasób, względem którego wyemitowano metrykę.  

1. W menu rozwijanym **obszary nazw** wybierz pozycję **QueueProcessing**. 

1. W menu rozwijanym **metryk** wybierz pozycję **QueueDepth**.  

 
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](./metrics-custom-overview.md).

