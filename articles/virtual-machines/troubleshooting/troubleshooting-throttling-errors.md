---
title: Rozwiązywanie problemów z błędami ograniczania na platformie Azure | Microsoft Docs
description: Błędy ograniczania, ponawianie prób i wycofywania w usłudze Azure COMPUTE.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: b1cc8a43423ecd33218948aaa001fc34877eac60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074278"
---
# <a name="troubleshooting-api-throttling-errors"></a>Rozwiązywanie problemów dotyczących błędów ograniczania przepływności interfejsu API 

Żądania obliczeniowe platformy Azure mogą być ograniczone w ramach subskrypcji i dla poszczególnych regionów, aby pomóc w ogólnej wydajności usługi. Firma Microsoft gwarantuje, że wszystkie wywołania do dostawcy zasobów obliczeniowych platformy Azure (CRP), które zarządzają zasobami w ramach tego obiektu, nie przekraczają maksymalnej dozwolonej szybkości żądania interfejsu API. W tym dokumencie opisano ograniczanie interfejsu API, szczegółowe informacje na temat rozwiązywania problemów z ograniczeniami oraz najlepsze rozwiązania w celu uniknięcia ograniczenia przepustowości.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Ograniczanie przez Azure Resource Manager dostawców zasobów  

Jako przód do platformy Azure Azure Resource Manager wykonuje uwierzytelnianie i sprawdzanie poprawności i ograniczanie wszystkich przychodzących żądań interfejsu API. W [tym miejscu](../../azure-resource-manager/management/request-limits-and-throttling.md)opisano Azure Resource Manager limity szybkości wywołań i powiązane nagłówki HTTP odpowiedzi diagnostycznej.
 
Gdy klient interfejsu API platformy Azure uzyska błąd ograniczania przepustowości, stan HTTP to 429 zbyt wiele żądań. Aby zrozumieć, czy ograniczenie żądania jest wykonywane przez Azure Resource Manager lub dostawcę zasobów, takich jak CRP, sprawdź, czy są to żądania `x-ms-ratelimit-remaining-subscription-reads` Get i for `x-ms-ratelimit-remaining-subscription-writes` Response dla żądań nieget. Jeśli pozostała liczba wywołań zbliża się do 0, osiągnięto ogólny limit wywołań dla subskrypcji zdefiniowany przez Azure Resource Manager. Działania wszystkich klientów subskrypcji są zliczane razem. W przeciwnym razie ograniczanie przepływności pochodzi od docelowego dostawcy zasobów (jeden adres `/providers/<RP>` URL żądania). 

## <a name="call-rate-informational-response-headers"></a>Nagłówki odpowiedzi informacyjnych o szybkości wywołania 

| Header                            | Format wartości                           | Przykład                               | Opis                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-MS-ratelimit — pozostało-zasób |```<source RP>/<policy or bucket>;<count>```| Microsoft. COMPUTE/HighCostGet3Min; 159 | Pozostała liczba wywołań interfejsu API dla zasad ograniczania przepływności obejmujących zasobnik zasobów lub grupę operacji, w tym element docelowy tego żądania                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Liczba wywołań "naliczona" dla tego żądania HTTP w kierunku limitu obowiązujących zasad. Zwykle jest to 1. Żądania usługi Batch, takie jak skalowanie zestawu skalowania maszyn wirtualnych, mogą obciążać wiele liczników. |


Należy zauważyć, że żądanie interfejsu API może podlegać wielu zasadom ograniczania przepustowości. Dla każdej zasady zostanie oddzielny `x-ms-ratelimit-remaining-resource` nagłówek. 

Oto przykładowa odpowiedź na usunięcie żądania zestawu skalowania maszyn wirtualnych.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Szczegóły błędu ograniczania przepustowości

Stan HTTP 429 jest często używany do odrzucania żądania, ponieważ został osiągnięty limit liczby wywołań. Typowa odpowiedź dotycząca błędu dławienia od dostawcy zasobów obliczeniowych będzie wyglądać podobnie do poniższego przykładu (wyświetlane są tylko odpowiednie nagłówki):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Zasady o pozostałej liczbie wywołań równej 0 to ta, z powodu której zwracany jest błąd ograniczający. W tym przypadku jest to `HighCostGet30Min` . Ogólny format treści odpowiedzi to ogólny format błędu interfejsu API Azure Resource Manager (zgodny z OData). Główny kod błędu, `OperationNotAllowed` , jest jednym z dostawców zasobów obliczeniowych używa do raportowania błędów ograniczania (między innymi rodzajami błędów klienta). `message`Właściwość błędów wewnętrznych zawiera serializowaną strukturę JSON z szczegółowymi informacjami o naruszeniu ograniczenia przepustowości.

Jak pokazano powyżej, każdy błąd ograniczania przepustowości obejmuje `Retry-After` nagłówek, który zapewnia minimalną liczbę sekund oczekiwania przez klienta przed ponowieniem próby wykonania żądania. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Analizator błędów dławienia i szybkości wywołań interfejsu API
Wersja zapoznawcza funkcji rozwiązywania problemów jest dostępna dla interfejsu API dostawcy zasobów obliczeniowych. Te polecenia cmdlet programu PowerShell zapewniają dane statystyczne o szybkości żądania interfejsu API na czas dla naruszeń operacji i ograniczania przepustowości na grupę operacji (zasady):
-   [Export-AzLogAnalyticRequestRateByInterval](/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Statystyki wywołań interfejsu API mogą zapewniać doskonałe informacje o zachowaniu klientów subskrypcji i umożliwiają łatwą identyfikację wzorców wywołań, które powodują ograniczenie przepustowości.

Ograniczenie analizatora w czasie polega na tym, że nie zlicza żądań dotyczących dysków i typów zasobów migawek (w ramach obsługi dysków zarządzanych). Ponieważ zbiera dane z telemetrii CRP, nie może również pomóc w identyfikowaniu błędów ograniczania przepustowości z usługi ARM. Ale mogą one być identyfikowane łatwo w oparciu o charakterystyczne nagłówki odpowiedzi ARM, jak opisano wcześniej.

Polecenia cmdlet programu PowerShell używają interfejsu API usługi REST, który może być łatwo wywoływany bezpośrednio przez klientów (choć bez formalnej pomocy technicznej). Aby wyświetlić format żądania HTTP, uruchom polecenia cmdlet z poleceniem-Debug Switch lub podsłuchiwaniem na ich wykonaniu za pomocą programu Fiddler.


## <a name="best-practices"></a>Najlepsze rozwiązania 

- Nie ponowij ponownie błędów interfejsu API usługi platformy Azure bez warunku i/lub natychmiast. Typowym wystąpieniem jest powstawanie kodu klienta w pętli szybkiej ponownej próby w przypadku wystąpienia błędu, który nie jest ponownie możliwy. Ponowne próby spowodują wyczerpanie dozwolonego limitu wywołań dla grupy operacji docelowej i wpływają na innych klientów subskrypcji. 
- W przypadku funkcji automatyzacji w dużej liczbie interfejsów API należy rozważyć zaimplementowanie samoczynnej samodzielnej przepustowości po stronie klienta, gdy liczba dostępnych wywołań dla docelowej grupy operacji spadnie poniżej pewnego dolnego progu. 
- Podczas śledzenia operacji asynchronicznych należy przestrzegać wskazówek dotyczących nagłówków Retry-After. 
- Jeśli kod klienta wymaga informacji o określonej maszynie wirtualnej, należy wykonać zapytanie dotyczące bezpośrednio tej maszyny wirtualnej zamiast wyświetlania wszystkich maszyn wirtualnych w zawierającej ją grupy zasobów lub całej subskrypcji, a następnie wybrać wymaganą maszynę wirtualną po stronie klienta. 
- Jeśli kod klienta wymaga maszyn wirtualnych, dysków i migawek z określonej lokalizacji platformy Azure, użyj formularza opartego na lokalizacji zapytania zamiast zapytania o wszystkie maszyny wirtualne subskrypcji, a następnie Przefiltruj według lokalizacji na stronie klienta: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` zapytanie do regionalnych punktów końcowych dostawcy zasobów obliczeniowych. 
-   W przypadku tworzenia lub aktualizowania zasobów interfejsu API w określonych maszynach wirtualnych i zestawach skalowania maszyn wirtualnych jest znacznie bardziej wydajna śledzenie zwracanej asynchronicznej operacji do ukończenia niż sondowanie samego adresu URL zasobu (na podstawie `provisioningState` ).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wskazówek dotyczących ponownych prób dla innych usług na platformie Azure, zobacz [wskazówki dotyczące ponawiania prób dla określonych usług](/azure/architecture/best-practices/retry-service-specific)
