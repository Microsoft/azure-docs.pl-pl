---
title: Diagnostyka zwrotnego serwera proxy Service Fabric platformy Azure
description: Dowiedz się, jak monitorować i diagnozować przetwarzanie żądań na zwrotnym serwerze proxy dla aplikacji Service Fabric platformy Azure.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645467"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorowanie i diagnozowanie przetwarzania żądań na odwrotnym serwerze proxy

Począwszy od wersji 5,7 Service Fabric, zdarzenia zwrotnego serwera proxy są dostępne dla kolekcji. Zdarzenia są dostępne w dwóch kanałach — jeden z błędami przetwarzania żądania na odwrotnym serwerze proxy i drugim kanale zawierającym pełne zdarzenia z wpisami dla żądań zakończonych powodzeniem i niepowodzeniem.

Zapoznaj się z tematem [zbieranie zdarzeń zwrotnego serwera proxy](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) , aby włączyć zbieranie zdarzeń z tych kanałów w klastrach lokalnych i Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Rozwiązywanie problemów przy użyciu dzienników diagnostycznych
Poniżej przedstawiono kilka przykładów interpretacji typowych dzienników błędów, które mogą wystąpić:

1. Zwrotny serwer proxy zwraca kod stanu odpowiedzi 504 (limit czasu).

    Przyczyną może być niepowodzenie odpowiedzi usługi w okresie limitu czasu żądania.
   Pierwsze wydarzenie poniżej rejestruje szczegóły żądania otrzymanego na odwrotnym serwerze proxy. 
   Drugie zdarzenie wskazuje, że żądanie nie powiodło się podczas przesyłania dalej do usługi z powodu "błędu wewnętrznego = ERROR_WINHTTP_TIMEOUT" 

    Ładunek obejmuje:

   * **traceId**: ten identyfikator GUID może służyć do skorelowania wszystkich zdarzeń odpowiadających pojedynczemu żądaniu. W poniższych dwóch zdarzeniach traceId = **2f87b722-e254-4ac2-A802-fd315c1a0271**, co oznacza, że należą one do tego samego żądania.
   * **requestUrl**: adres URL (zwrotny adres URL serwera proxy), do którego wysłano żądanie.
   * **zlecenie**: zlecenie http.
   * **remoteAddress**: adres klienta wysyłającego żądanie.
   * **resolvedServiceUrl**: adres URL punktu końcowego usługi, do którego zostało rozwiązane żądanie przychodzące. 
   * **errorDetails**: dodatkowe informacje o błędzie.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Zwrotny serwer proxy zwraca kod stanu odpowiedzi 404 (nie znaleziono). 
    
    Oto przykładowe zdarzenie, w przypadku którego zwrotny serwer proxy zwraca 404, ponieważ nie może znaleźć pasującego punktu końcowego usługi.
    Poniżej znajdują się poniższe wpisy ładunku:
   * **processRequestPhase**: wskazuje fazę podczas przetwarzania żądania, gdy wystąpił błąd, ***TryGetEndpoint*** tj. podczas próby pobrania punktu końcowego usługi do usługi. 
   * **errorDetails**: Wyświetla listę kryteriów wyszukiwania punktów końcowych. W tym miejscu można zobaczyć, że określony odbiornik to **FrontEndListener** , a lista punktów końcowych repliki zawiera odbiornik o nazwie **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Innym przykładem, jeśli zwrotny serwer proxy może zwrócić 404 nie znaleziono: parametr konfiguracji ApplicationGateway\Http **SecureOnlyMode** jest ustawiony na wartość true przy użyciu zwrotnego serwera proxy nasłuchujący na **protokole https**, jednak wszystkie punkty końcowe repliki są niezabezpieczone (NAsłuchiwanie w protokole HTTP).
     Zwrotny serwer proxy zwraca 404, ponieważ nie może znaleźć punktu końcowego nasłuchującego na protokole HTTPS, aby przesłać żądanie. Analizowanie parametrów w ładunku zdarzeń ułatwia zawężenie problemu:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Żądanie do zwrotnego serwera proxy kończy się niepowodzeniem z powodu błędu limitu czasu. 
    Dzienniki zdarzeń zawierają zdarzenie z odebranymi szczegółami żądania (nie są tu wyświetlane).
    Następne zdarzenie pokazuje, że usługa odpowiedziała za pomocą kodu stanu 404 i zwrotny serwer proxy inicjuje ponowne rozwiązanie. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Podczas zbierania wszystkich zdarzeń zobaczysz pociąg zdarzeń pokazujący każdą próbę rozwiązania problemu i przesyłania dalej.
    Ostatnie zdarzenie w serii pokazuje, że przetwarzanie żądania nie powiodło się z powodu przekroczenia limitu czasu, wraz z liczbą udanych prób rozpoznania.
    
    > [!NOTE]
    > Zaleca się, aby pełne zbieranie zdarzeń kanałów były domyślnie wyłączone i włączane na potrzeby rozwiązywania problemów.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Jeśli zbieranie danych jest włączone tylko dla zdarzeń krytycznych/błędów, zobaczysz jedno zdarzenie ze szczegółowymi informacjami o limicie czasu i liczbie prób rozwiązania. 
    
    Usługi, które zamierzają wysłać kod stanu 404 z powrotem do użytkownika, powinien dodać nagłówek "X-servicefabric" w odpowiedzi. Po dodaniu nagłówka do odpowiedzi zwrotny serwer proxy przekazuje kod stanu z powrotem do klienta.  

4. Przypadki, gdy klient odłączył żądanie.

    Następujące zdarzenie jest rejestrowane, gdy zwrotny serwer proxy przesyła odpowiedź do klienta, ale klient rozłącza:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Zwrotny serwer proxy zwraca 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    Błąd FABRIC_E_SERVICE_DOES_NOT_EXIST jest zwracany, jeśli nie określono schematu identyfikatora URI dla punktu końcowego usługi w manifeście usługi.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Aby rozwiązać ten problem, określ schemat identyfikatora URI w manifeście.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Zdarzenia związane z przetwarzaniem żądań protokołu WebSocket nie są obecnie rejestrowane. Ta wartość zostanie dodana w następnej wersji.

## <a name="next-steps"></a>Następne kroki
* [Agregacja i zbieranie zdarzeń przy użyciu Diagnostyka Azure systemu Windows](service-fabric-diagnostics-event-aggregation-wad.md) do włączania zbierania dzienników w klastrach platformy Azure.
* Aby wyświetlić zdarzenia Service Fabric w programie Visual Studio, zobacz temat [monitorowanie i diagnozowanie lokalne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Zapoznaj się z tematem [Konfigurowanie zwrotnego serwera proxy w celu nawiązania połączenia z bezpiecznymi usługami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) dla przykładów szablonów Azure Resource Manager, aby skonfigurować bezpieczny zwrotny serwer proxy przy użyciu różnych opcji weryfikacji certyfikatu usługi
* Przeczytaj [Service Fabric odwrotny serwer proxy](service-fabric-reverseproxy.md) , aby dowiedzieć się więcej.
