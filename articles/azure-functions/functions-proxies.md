---
title: Współdziałanie z serwerami proxy w Azure Functions
description: Omówienie sposobu używania serwery proxy usługi Azure Functions
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 5e756258bb92d7def195959d909068e87e765c0f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562070"
---
# <a name="work-with-azure-functions-proxies"></a>Pracuj z serwery proxy usługi Azure Functions

W tym artykule opisano sposób konfigurowania serwery proxy usługi Azure Functions i pracy z nią. Za pomocą tej funkcji można określić punkty końcowe w aplikacji funkcji, które są implementowane przez inny zasób. Za pomocą tych serwerów proxy można podzielić duży interfejs API na wiele aplikacji funkcji (podobnie jak w przypadku architektury mikrousług), przy jednoczesnym podaniu jednej powierzchni interfejsu API dla klientów.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Rozliczenia funkcji standardowych dotyczą wykonań serwera proxy. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Tworzenie serwera proxy

W tej sekcji pokazano, jak utworzyć serwer proxy w portalu funkcji.

1. Otwórz [Azure Portal], a następnie przejdź do swojej aplikacji funkcji.
2. W lewym okienku wybierz pozycję **nowy serwer proxy**.
3. Podaj nazwę serwera proxy.
4. Skonfiguruj punkt końcowy, który jest udostępniany w tej aplikacji funkcji, określając **szablon trasy** i **metody http**. Te parametry zachowują się zgodnie z regułami dla [wyzwalaczy http].
5. Ustaw **adres URL zaplecza** na inny punkt końcowy. Ten punkt końcowy może być funkcją w innej aplikacji funkcji lub być innym interfejsem API. Wartość nie musi być statyczna i może odwoływać się do [ustawień] i parametrów aplikacji [z oryginalnego żądania klienta].
6. Kliknij przycisk **Utwórz**.

Twój serwer proxy istnieje teraz jako nowy punkt końcowy w aplikacji funkcji. Z punktu widzenia klienta jest on równoznaczny z HttpTrigger w Azure Functions. Możesz wypróbować nowy serwer proxy przez skopiowanie adresu URL serwera proxy i przetestowanie go za pomocą ulubionego klienta HTTP.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Modyfikuj żądania i odpowiedzi

Za pomocą serwery proxy usługi Azure Functions można modyfikować żądania do i odpowiedzi z zaplecza. Te przekształcenia mogą używać zmiennych zdefiniowanych w [zmiennych use].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Modyfikowanie żądania zaplecza

Domyślnie żądanie zaplecza jest inicjowane jako kopia oryginalnego żądania. Oprócz ustawiania adresu URL zaplecza można wprowadzać zmiany w metodzie HTTP, nagłówkach i parametrach ciągu zapytania. Zmodyfikowane wartości mogą odwoływać się do [ustawień] i [parametrów aplikacji z oryginalnego żądania klienta].

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *przesłonięcie żądania* na stronie szczegółów serwera proxy. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Modyfikowanie odpowiedzi

Domyślnie odpowiedź klienta jest inicjowana jako kopia odpowiedzi wewnętrznej bazy danych. Można wprowadzać zmiany w kodzie stanu odpowiedzi, frazie przyczyny, nagłówkach i treści. Zmodyfikowane wartości mogą odwoływać się do [ustawień aplikacji], [parametrów z oryginalnego żądania klienta]i [parametrów z odpowiedzi zaplecza].

Żądania zaplecza można modyfikować w portalu, rozszerzając sekcję *przesłonięcie odpowiedzi* na stronie szczegółów serwera proxy. 

## <a name="use-variables"></a><a name="using-variables"></a>Używanie zmiennych

Konfiguracja serwera proxy nie musi być statyczna. Można warunkować Używanie zmiennych z oryginalnego żądania klienta, odpowiedzi wewnętrznej lub ustawień aplikacji.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Odwołania do funkcji lokalnych
Można użyć `localhost` , aby odwoływać się do funkcji w tej samej aplikacji funkcji bezpośrednio, bez żądania dwukierunkowego serwera proxy.

`"backendurl": "https://localhost/api/httptriggerC#1"`odwołuje się do lokalnej funkcji wyzwalanej przez protokół HTTP w marszrucie`/api/httptriggerC#1`

 
>[!Note]  
>Jeśli funkcja używa poziomów autoryzacji *funkcji, administratora lub sys* , należy podać kod i clientId, zgodnie z oryginalnym adresem URL funkcji. W takim przypadku odwołanie będzie wyglądać następująco: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` zalecamy przechowywanie tych kluczy w [ustawieniach aplikacji] i odwoływanie się do tych z serwerów proxy. Pozwala to uniknąć przechowywania wpisów tajnych w kodzie źródłowym. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametry żądania odwołania

Parametrów żądania można użyć jako danych wejściowych właściwości adresu URL zaplecza lub w ramach modyfikowania żądań i odpowiedzi. Niektóre parametry można powiązać z szablonem trasy określonym w podstawowej konfiguracji serwera proxy, a inne mogą pochodzić z właściwości żądania przychodzącego.

#### <a name="route-template-parameters"></a>Parametry szablonu trasy
Parametry, które są używane w szablonie trasy, są dostępne do przywoływania przez nazwę. Nazwy parametrów są ujęte w nawiasy klamrowe ({}).

Na przykład, jeśli serwer proxy ma szablon trasy, `/pets/{petId}`na przykład adres URL zaplecza może zawierać wartość `{petId}`, jak w. `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` Jeśli szablon trasy kończy się w symbolu wieloznacznym, na `/api/{*restOfPath}`przykład, `{restOfPath}` jest to ciąg reprezentujący pozostałe segmenty ścieżki z żądania przychodzącego.

#### <a name="additional-request-parameters"></a>Dodatkowe parametry żądania
Oprócz parametrów szablonu trasy do wartości konfiguracji można używać następujących wartości:

* **{Request. Method}**: Metoda http, która jest używana w oryginalnym żądaniu.
* **{Request. Heads\< . Nagłówekname\>}**: nagłówek, który można odczytać z oryginalnego żądania. Zastąp * \<nagłówek\> nagłówkowy* nazwą nagłówka, który ma zostać odczytany. Jeśli nagłówek nie jest uwzględniony w żądaniu, wartość będzie ciągiem pustym.
* **{Request. QueryString.\< ParameterName\>}**: parametr ciągu zapytania, który można odczytać z oryginalnego żądania. Zastąp * \<parametrname\> * nazwą parametru, który ma zostać odczytany. Jeśli parametr nie jest uwzględniony w żądaniu, wartość będzie ciągiem pustym.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Parametry odpowiedzi wewnętrznej bazy danych

Parametry odpowiedzi mogą służyć jako część modyfikacji odpowiedzi dla klienta. W wartościach konfiguracji można używać następujących wartości:

* **{zaplecze. Response. StatusCode}**: kod stanu HTTP zwracany w odpowiedzi wewnętrznej.
* **{zaplecze. Response. statusReason}**: fraza przyczyny http, która jest zwracana w odpowiedzi wewnętrznej.
* **{zaplecze. Response. Heads.\< HeaderName\>}**: nagłówek, który można odczytać z odpowiedzi wewnętrznej bazy danych. Zastąp * \<nagłówekname\> * nazwą nagłówka, który ma zostać odczytany. Jeśli nagłówek nie jest uwzględniony w odpowiedzi, wartość będzie ciągiem pustym.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Ustawienia aplikacji referencyjnych

Można także odwoływać się [do ustawień aplikacji zdefiniowanych dla aplikacji funkcji](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) , otaczając nazwę ustawienia ze znakami procentu (%).

Na przykład adres URL *https://%ORDER_PROCESSING_HOST%/api/orders* zaplecza "% ORDER_PROCESSING_HOST%" został zastąpiony wartością ustawienia ORDER_PROCESSING_HOST.

> [!TIP] 
> Ustawienia aplikacji dla hostów zaplecza należy używać w przypadku wielu wdrożeń lub środowisk testowych. Dzięki temu można upewnić się, że zawsze mówisz do odpowiedniej zaplecza dla tego środowiska.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Rozwiązywanie problemów z serwerami proxy

Po dodaniu flagi `"debug":true` do dowolnego serwera proxy w `proxies.json` ramach użytkownika zostanie włączone rejestrowanie debugowania. Dzienniki są przechowywane w `D:\home\LogFiles\Application\Proxies\DetailedTrace` i dostępne za pomocą narzędzi zaawansowanych (kudu). Wszystkie odpowiedzi HTTP zawierają również `Proxy-Trace-Location` nagłówek z adresem URL, aby uzyskać dostęp do pliku dziennika.

Można debugować serwer proxy po stronie klienta, dodając do `Proxy-Trace-Enabled` `true`niego nagłówek. Spowoduje to również zarejestrowanie śledzenia w systemie plików i zwrócenie adresu URL śledzenia jako nagłówka w odpowiedzi.

### <a name="block-proxy-traces"></a>Blokuj ślady serwera proxy

Ze względów bezpieczeństwa możesz nie chcieć, aby ktoś wywołujący usługę w celu wygenerowania śladu. Nie będą oni mogli uzyskać dostępu do zawartości śladu bez poświadczeń logowania, ale generowanie śledzenia zużywa zasoby i uwidacznia, że są używane usługi proxy funkcji.

Całkowicie wyłącz ślady poprzez `"debug":false` dodanie do każdego określonego serwera proxy `proxies.json`w.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Konfigurowane serwery proxy są przechowywane w pliku *proxy. JSON* , który znajduje się w katalogu głównym katalogu aplikacji funkcji. Możesz ręcznie edytować ten plik i wdrożyć go w ramach aplikacji, korzystając z dowolnej z [metod wdrażania](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) obsługiwanych przez funkcje. 

> [!TIP] 
> Jeśli nie skonfigurowano jednej z metod wdrażania, można również współdziałać z plikiem *proxy. JSON* w portalu. Przejdź do aplikacji funkcji, wybierz pozycję **funkcje platformy**, a następnie wybierz pozycję **Edytor usługi App Service**. Dzięki temu można wyświetlić całą strukturę plików aplikacji funkcji, a następnie wprowadzić zmiany.

*Proxy. JSON* jest zdefiniowany przez obiekt proxy, który składa się z nazwanych serwerów proxy i ich definicji. Opcjonalnie, jeśli edytor obsługuje tę funkcję, można odwoływać się do [schematu JSON](http://json.schemastore.org/proxies) w celu uzupełniania kodu. Przykładowy plik może wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Każdy serwer proxy ma przyjazną nazwę, taką jak *Proxy1* w poprzednim przykładzie. Odpowiedni obiekt definicji serwera proxy jest zdefiniowany przez następujące właściwości:

* **matchCondition**: wymagane--obiekt definiujący żądania, które wyzwalają wykonywanie tego serwera proxy. Zawiera dwie właściwości, które są współużytkowane z [wyzwalaczami http]:
    * _metody_: tablica metod http, na które odpowiada serwer proxy. Jeśli nie zostanie określony, serwer proxy odpowie na wszystkie metody HTTP w marszrucie.
    * _trasa_: wymagane — definiuje szablon trasy, kontrolujący adresy URL żądań, na które odpowiada serwer proxy. W przeciwieństwie do wyzwalaczy HTTP, nie ma wartości domyślnej.
* **backendUri**: adres URL zasobu zaplecza, do którego należy serwer proxy żądania. Ta wartość może odwoływać się do ustawień i parametrów aplikacji z oryginalnego żądania klienta. Jeśli ta właściwość nie jest uwzględniona, Azure Functions reaguje na HTTP 200 OK.
* **requestOverrides**: obiekt definiujący przekształcenia do żądania zaplecza. Zobacz [Definiowanie obiektu requestOverrides].
* **responseOverrides**: obiekt definiujący przekształcenia do odpowiedzi klienta. Zobacz [Definiowanie obiektu responseOverrides].

> [!NOTE] 
> Właściwość *Route* w serwery proxy usługi Azure Functions nie przestrzega właściwości *routePrefix* konfiguracji hosta aplikacja funkcji. Jeśli chcesz uwzględnić prefiks, taki jak `/api`, musi być uwzględniony we właściwości *trasy* .

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Wyłącz indywidualne serwery proxy

Poszczególne serwery proxy można wyłączyć, dodając `"disabled": true` je do serwera proxy w `proxies.json` pliku. Spowoduje to, że wszystkie żądania, które spełniają matchCondition, zwracają 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a>Ustawienia aplikacji

Zachowanie serwera proxy może być kontrolowane przez kilka ustawień aplikacji. Są one opisane w [dokumentacji dotyczącej ustawień aplikacji funkcji](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Znaki zastrzeżone (formatowanie ciągu)

Serwery proxy odczytują wszystkie ciągi z pliku JSON przy użyciu znaku \ jako symbolu ucieczki. Serwery proxy interpretują również nawiasy klamrowe. Zapoznaj się z pełnym zestawem przykładów poniżej.

|Znak|Znak ucieczki|Przykład|
|-|-|-|
|oraz|{{lub}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Zdefiniuj obiekt requestOverrides

Obiekt requestOverrides definiuje zmiany wprowadzone w żądaniu w przypadku wywołania zasobu zaplecza. Obiekt jest zdefiniowany przez następujące właściwości:

* **zaplecze. Request. Method**: Metoda http, która jest używana do wywoływania zaplecza.
* **zaplecze. Request. QueryString. \<: parametr ciągu zapytania, który można ustawić dla wywołania zaplecza.\>** Zastąp * \<parametrname\> * nazwą parametru, który ma zostać ustawiony. Należy pamiętać, że jeśli podano pusty ciąg, parametr nadal jest zawarty w żądaniu zaplecza.
* **zaplecze. Request. Heads. \<: nagłówek, który można ustawić dla wywołania zaplecza.\>** Zastąp * \<nagłówekname\> * nazwą nagłówka, który ma zostać ustawiony. Należy pamiętać, że jeśli podano pusty ciąg, parametr nadal jest zawarty w żądaniu zaplecza.

Wartości mogą odwoływać się do ustawień i parametrów aplikacji z oryginalnego żądania klienta.

Przykładowa konfiguracja może wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Zdefiniuj obiekt responseOverrides

Obiekt requestOverrides definiuje zmiany wprowadzane do odpowiedzi, które są przekazywane z powrotem do klienta. Obiekt jest zdefiniowany przez następujące właściwości:

* **Response. StatusCode**: kod stanu HTTP do zwrócenia klientowi.
* **Response. statusReason**: fraza przyczyny http, która ma zostać zwrócona do klienta.
* **Response. Body**: ciąg reprezentujący treść, która ma zostać zwrócona do klienta.
* **Response. Heads. \<: nagłówek, który można ustawić dla odpowiedzi dla\>** klienta. Zastąp * \<nagłówekname\> * nazwą nagłówka, który ma zostać ustawiony. Jeśli podano pusty ciąg, nagłówek nie zostanie uwzględniony w odpowiedzi.

Wartości mogą odwoływać się do ustawień aplikacji, parametrów z oryginalnego żądania klienta i parametrów z odpowiedzi zaplecza.

Przykładowa konfiguracja może wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> W tym przykładzie treść odpowiedzi jest ustawiana bezpośrednio, więc żadna właściwość `backendUri` nie jest wymagana. W przykładzie pokazano, jak można użyć serwery proxy usługi Azure Functions do tworzenia interfejsów API.

[Azure Portal]: https://portal.azure.com
[Wyzwalacze HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Zdefiniuj obiekt requestOverrides]: #requestOverrides
[Zdefiniuj obiekt responseOverrides]: #responseOverrides
[Ustawienia aplikacji]: #use-appsettings
[Używanie zmiennych]: #using-variables
[parametry z oryginalnego żądania klienta]: #request-parameters
[parametry z odpowiedzi wewnętrznej bazy danych]: #response-parameters
