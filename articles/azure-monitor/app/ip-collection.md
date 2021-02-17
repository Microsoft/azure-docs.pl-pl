---
title: Kolekcja adresów IP Application Insights platformy Azure | Microsoft Docs
description: Zrozumienie, jak adresy IP i geolokalizacja są obsługiwane za pomocą usługi Azure Application Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584076"
---
# <a name="geolocation-and-ip-address-handling"></a>Obsługa geolokalizacji i adresów IP

W tym artykule wyjaśniono, jak funkcja wyszukiwania geolokalizacji i obsługa adresów IP działają w Application Insights oraz jak modyfikować zachowanie domyślne.

## <a name="default-behavior"></a>Zachowanie domyślne

Domyślnie adresy IP są zbierane tymczasowo, ale nie są przechowywane w Application Insights. Podstawowy proces przebiega w następujący sposób:

Gdy dane telemetryczne są wysyłane do platformy Azure, adres IP służy do przeszukiwania geolokalizacji przy użyciu [GeoLite2 z MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Wyniki tego wyszukiwania są używane do wypełniania pól `client_City` , `client_StateOrProvince` i `client_CountryOrRegion` . Adres jest następnie odrzucony i `0.0.0.0` jest zapisywana w `client_IP` polu.

* Telemetria przeglądarki: tymczasowo zbieramy adres IP nadawcy. Adres IP jest obliczany przez punkt końcowy pozyskiwania.
* Dane telemetryczne serwera: moduł telemetrii Application Insights tymczasowo zbiera adres IP klienta. Adres IP nie jest zbierany lokalnie po `X-Forwarded-For` ustawieniu nagłówka.

Takie zachowanie jest zaprojektowane w celu uniknięcia niepotrzebnej kolekcji danych osobowych. Zawsze, gdy jest to możliwe, zalecamy uniknięcie zbierania danych osobowych. 

## <a name="overriding-default-behavior"></a>Zastępowanie zachowania domyślnego

Domyślnie nie są zbierane adresy IP. Nadal zapewniamy elastyczność, aby przesłonić to zachowanie. Jednak zalecamy sprawdzenie, czy kolekcja nie przerywa żadnych wymagań dotyczących zgodności ani lokalnych przepisów. 

Aby dowiedzieć się więcej na temat obsługi danych osobowych w Application Insights, zapoznaj się ze [wskazówkami dotyczącymi danych osobowych](../logs/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>Przechowywanie danych adresów IP

Aby umożliwić zbieranie i przechowywanie adresów IP, `DisableIpMasking` Właściwość składnika Application Insights musi być ustawiona na wartość `true` . Tę właściwość można ustawić za pomocą szablonów Azure Resource Manager lub przez wywołanie interfejsu API REST. 

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Jeśli musisz zmodyfikować zachowanie dla pojedynczego zasobu Application Insights, użyj Azure Portal. 

1. Przejdź do szablonu eksportu > **usługi Automatyzacja** zasobów Application Insights  >   

2. Wybierz pozycję **Wdróż**

    ![Przycisk z wyróżnionym słowem "Deploy" w kolorze czerwonym](media/ip-collection/deploy.png)

3. Wybierz pozycję **Edytuj szablon**.

    ![Przycisk z wyróżnionym słowem "Edytuj" w kolorze czerwonym](media/ip-collection/edit-template.png)

4. Wprowadź następujące zmiany w formacie JSON dla zasobu, a następnie wybierz pozycję **Zapisz**:

    ![Zrzut ekranu dodaje przecinek po "IbizaAIExtension" i dodaje nowy wiersz poniżej z "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Jeśli wystąpi błąd informujący, że **_Grupa zasobów znajduje się w lokalizacji, która nie jest obsługiwana przez co najmniej jeden zasób w szablonie. Wybierz inną grupę zasobów._** Tymczasowo wybierz inną grupę zasobów z listy rozwijanej, a następnie ponownie wybierz oryginalną grupę zasobów, aby rozwiązać ten problem.

5. Wybierz opcję **zgadzam** się na  >  **zakup**. 

    ![Zaznaczone pole z wyrazami "Zgadzam się na warunki i postanowienia wymienione powyżej" wyróżnione na czerwono nad przyciskiem z wyrazem "zakup" wyróżnionym kolorem czerwonym.](media/ip-collection/purchase.png)

    W takim przypadku nie jest faktycznie kupowane żadne nowe. Aktualizujemy tylko konfigurację istniejącego zasobu Application Insights.

6. Po zakończeniu wdrażania nowe dane telemetryczne zostaną zarejestrowane.

    Jeśli wybierzesz i zmodyfikujesz szablon ponownie, zobaczysz tylko szablon domyślny bez nowo dodanej właściwości. Jeśli nie widzisz danych adresów IP i chcesz potwierdzić, że `"DisableIpMasking": true` jest ustawione, uruchom następujące polecenie programu PowerShell: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Lista właściwości zostanie zwrócona w wyniku. Jedna z właściwości powinna zostać odczytana `DisableIpMasking: true` . Jeśli program PowerShell zostanie uruchomiony przed wdrożeniem nowej właściwości z Azure Resource Manager, właściwość nie będzie istnieć.

### <a name="rest-api"></a>Interfejs API REST

Ładunek [interfejsu API REST](/rest/api/azure/) , aby wprowadzić takie same modyfikacje, jest następujący:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicjator telemetrii

Jeśli potrzebujesz bardziej elastycznej alternatywy niż `DisableIpMasking` , możesz użyć [inicjatora telemetrii](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , aby skopiować całość lub część adresu IP do pola niestandardowego. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Jeśli nie możesz uzyskać dostępu `ISupportProperties` , sprawdź i upewnij się, że korzystasz z najnowszej stabilnej wersji zestawu SDK Application Insights. `ISupportProperties` są przeznaczone do wartości o dużej kardynalności, natomiast `GlobalProperties` są bardziej odpowiednie dla niskich wartości kardynalnych, takich jak nazwa regionu, Nazwa środowiska itp. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Włącz inicjatora telemetrii dla ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Włącz inicjatora telemetrii dla ASP.NET Core

Można utworzyć inicjatora telemetrii w taki sam sposób jak w przypadku ASP.NET Core jako ASP.NET, ale w celu włączenia inicjatora należy użyć następującego przykładu dla odwołania:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[Język JavaScript po stronie klienta](#tab/javascript)

### <a name="client-side-javascript"></a>Język JavaScript po stronie klienta

W przeciwieństwie do zestawów SDK po stronie serwera, zestaw SDK JavaScript po stronie klienta nie oblicza adresu IP. Domyślnie Obliczanie adresów IP dla danych telemetrycznych po stronie klienta odbywa się w punkcie końcowym pozyskiwania na platformie Azure. 

Jeśli chcesz obliczyć adres IP bezpośrednio po stronie klienta, musisz dodać własną logikę niestandardową i użyć wyniku, aby ustawić `ai.location.ip` tag. Gdy `ai.location.ip` jest ustawiony, obliczenia adresów IP nie są wykonywane przez punkt końcowy pozyskiwania, a podany adres IP jest używany do wyszukiwania geolokalizacji. W tym scenariuszu adres IP będzie nadal wyzerowany domyślnie. 

Aby zachować cały adres IP obliczany na podstawie logiki niestandardowej, można użyć inicjatora telemetrii, który skopiuje dane adresu IP podane w `ai.location.ip` oddzielnym polu niestandardowym. Jednak w przeciwieństwie do zestawów SDK po stronie serwera, bez polegania na bibliotekach innych firm lub własnej logiki kolekcji niestandardowych, zestaw SDK po stronie klienta nie będzie obliczał tego adresu.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Jeśli dane po stronie klienta przechodzą przez serwer proxy przed przekazaniem do punktu końcowego pozyskiwania, podczas obliczania adresu IP może być wyświetlany adres IP serwera proxy, a nie klient. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Wyświetlanie wyników inicjatora telemetrii

Jeśli wysyłasz nowy ruch do lokacji i poczekaj kilka minut. Następnie można uruchomić zapytanie w celu potwierdzenia, że kolekcja działa:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nowo zebrane adresy IP pojawią się w `customDimensions_client-ip` kolumnie. Kolumna Domyślna `client-ip` nadal będzie zawierać wszystkie cztery oktety, które są zerowane. 

W przypadku testowania z hosta lokalnego i wartości dla parametru `customDimensions_client-ip` is jest `::1` to oczekiwane zachowanie. `::1` reprezentuje adres sprzężenia zwrotnego w protokole IPv6. Jest to odpowiednik `127.0.0.1` w protokole IPv4.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zbieraniu danych osobowych](../logs/personal-data-mgmt.md) w Application Insights.

* Dowiedz się więcej o sposobie działania [zbierania adresów IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) w Application Insights. (W tym artykule znajduje się starszy, zewnętrzny wpis w blogu zapisany przez jednego z naszych inżynierów. Jest to wstępnie bieżące zachowanie domyślne, w którym adres IP jest rejestrowany jako `0.0.0.0` , ale jest bardziej głębokością Mechanics wbudowanego `ClientIpHeaderTelemetryInitializer` .)
