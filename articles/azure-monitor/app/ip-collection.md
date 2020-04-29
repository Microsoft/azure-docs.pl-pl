---
title: Kolekcja adresów IP Application Insights platformy Azure | Microsoft Docs
description: Zrozumienie, jak adresy IP i geolokalizacja są obsługiwane za pomocą usługi Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77656521"
---
# <a name="geolocation-and-ip-address-handling"></a>Obsługa geolokalizacji i adresów IP

W tym artykule wyjaśniono, w jaki sposób wyszukiwanie geolokalizacji i obsługa adresów IP odbywają się w Application Insights oraz jak modyfikować zachowanie domyślne.

## <a name="default-behavior"></a>Zachowanie domyślne

Domyślnie adresy IP są zbierane tymczasowo, ale nie są przechowywane w Application Insights. Podstawowy proces przebiega w następujący sposób:

Adresy IP są wysyłane do Application Insights w ramach danych telemetrycznych. Po osiągnięciu punktu końcowego pozyskiwania na platformie Azure adres IP jest używany do przeszukiwania geolokalizacji przy użyciu [GeoLite2 z MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Wyniki tego wyszukiwania są używane do wypełniania następujących pól `client_City`, `client_StateOrProvince`,. `client_CountryOrRegion` W tym momencie adres IP zostanie odrzucony i `0.0.0.0` zapisany w `client_IP` polu.

* Telemetria przeglądarki: tymczasowo zbieramy adres IP nadawcy. Adres IP jest obliczany przez punkt końcowy pozyskiwania.
* Dane telemetryczne serwera: moduł Application Insights tymczasowo zbiera adres IP klienta. Nie jest zbierane, jeśli `X-Forwarded-For` jest ustawiony.

Takie zachowanie jest zaprojektowane w celu uniknięcia niepotrzebnej kolekcji danych osobowych. Zawsze, gdy jest to możliwe, zalecamy uniknięcie zbierania danych osobowych. 

## <a name="overriding-default-behavior"></a>Zastępowanie zachowania domyślnego

Mimo że domyślne zachowanie polega na zminimalizowaniu kolekcji danych osobowych, firma Microsoft oferuje elastyczność zbierania i przechowywania danych adresów IP. Przed wybraniem opcji przechowywania danych osobowych, takich jak adresy IP, zdecydowanie zalecamy sprawdzenie, czy nie są to wymagania dotyczące zgodności ani lokalne przepisy, które mogą podlegać. Aby dowiedzieć się więcej na temat obsługi danych osobowych w Application Insights, zapoznaj się ze [wskazówkami dotyczącymi danych osobowych](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Przechowywanie danych adresów IP

Aby można było włączyć zbieranie i przechowywanie adresów IP, `DisableIpMasking` właściwość składnika Application Insights musi być ustawiona na `true`wartość. Tę właściwość można ustawić za pomocą szablonów Azure Resource Manager lub przez wywołanie interfejsu API REST. 

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

Jeśli musisz tylko zmodyfikować zachowanie dla pojedynczego zasobu Application Insights najłatwiejszym sposobem osiągnięcia tego problemu jest za pośrednictwem Azure Portal.  

1. Przejdź do**szablonu eksportu** **ustawień** > > zasobów Application Insights 

    ![Eksportuj szablon](media/ip-collection/export-template.png)

2. Wybierz pozycję **Wdróż**

    ![Przycisk Wdróż wyróżniony na czerwono](media/ip-collection/deploy.png)

3. Wybierz pozycję **Edytuj szablon**. (Jeśli szablon zawiera dodatkowe właściwości lub zasoby, które nie są wyświetlane w tym przykładowym szablonie, należy zachować ostrożność, aby upewnić się, że wszystkie zasoby zaakceptują wdrożenie szablonu jako przyrostową zmianę/aktualizację).

    ![Edytowanie szablonu](media/ip-collection/edit-template.png)

4. Wprowadź następujące zmiany w formacie JSON dla zasobu, a następnie kliknij przycisk **Zapisz**:

    ![Zrzut ekranu dodaje przecinek po "IbizaAIExtension" i dodaje nowy wiersz poniżej z "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Jeśli wystąpi błąd informujący, że ** _Grupa zasobów znajduje się w lokalizacji, która nie jest obsługiwana przez co najmniej jeden zasób w szablonie. Wybierz inną grupę zasobów._** Tymczasowo wybierz inną grupę zasobów z listy rozwijanej, a następnie ponownie wybierz oryginalną grupę zasobów, aby rozwiązać ten problem.

5. Wybierz opcję **zgadzam** > się na**zakup**. 

    ![Edytowanie szablonu](media/ip-collection/purchase.png)

    W takim przypadku nie jest kupowane żadne nowe, ale właśnie aktualizujemy konfigurację istniejącego zasobu Application Insights.

6. Po zakończeniu wdrożenia nowe dane telemetryczne zostaną zarejestrowane.

    Jeśli użytkownik chce wybrać i edytować szablon ponownie, zobaczysz tylko szablon domyślny i nie zobaczysz nowo dodanej właściwości i skojarzonej z nią wartości. Jeśli nie widzisz danych adresów IP i chcesz potwierdzić, że `"DisableIpMasking": true` jest ustawiony. Uruchom następujące środowisko PowerShell: (Zamień `Fabrikam-dev` na odpowiednią nazwę zasobu i grupy zasobów).
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Lista właściwości zostanie zwrócona w wyniku. Jedna z właściwości powinna zostać odczytana `DisableIpMasking: true`. Jeśli program PowerShell zostanie uruchomiony przed wdrożeniem nowej właściwości z Azure Resource Manager, właściwość nie będzie istnieć.

### <a name="rest-api"></a>Interfejs API REST

Ładunek [interfejsu API REST](https://docs.microsoft.com/rest/api/azure/) , aby wprowadzić takie same modyfikacje, jest następujący:

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

Jeśli potrzebujesz bardziej elastycznej alternatywy niż `DisableIpMasking` w przypadku rejestrowania wszystkich lub części adresów IP, możesz użyć [inicjatora telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) , aby skopiować całość lub część adresu IP do pola niestandardowego. 

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
> Jeśli nie możesz uzyskać dostępu `ISupportProperties`, sprawdź i upewnij się, że korzystasz z najnowszej stabilnej wersji zestawu SDK Application Insights. `ISupportProperties`są przeznaczone do wartości o dużej kardynalności, `GlobalProperties` natomiast są bardziej odpowiednie dla niskich wartości kardynalnych, takich jak nazwa regionu, Nazwa środowiska itp. 

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

### <a name="client-side-javascript"></a>Język JavaScript po stronie klienta

W przeciwieństwie do zestawów SDK po stronie serwera, zestaw JavaScript SDK po stronie klienta nie oblicza adresu IP. Domyślnie Obliczanie adresów IP dla danych telemetrycznych po stronie klienta jest wykonywane w punkcie końcowym pozyskiwania danych telemetrycznych na platformie Azure. Oznacza to, że w przypadku wysyłania danych po stronie klienta do serwera proxy, a następnie przekazywania do punktu końcowego pozyskiwania, obliczenia adresu IP mogą zawierać adres IP serwera proxy, a nie klienta. Jeśli nie jest używany żaden serwer proxy, nie powinien to być problem.

Jeśli chcesz obliczyć adres IP bezpośrednio po stronie klienta, należy dodać własną logikę niestandardową, aby wykonać to obliczenie, a następnie użyć wyniku, aby ustawić `ai.location.ip` tag. Gdy `ai.location.ip` jest ustawiony, obliczenia adresów IP nie są wykonywane przez punkt końcowy pozyskiwania, a podany adres IP jest uznawany za używany do wykonywania wyszukiwania geograficznego. W tym scenariuszu adres IP będzie nadal wyzerowany domyślnie. 

Aby zachować cały adres IP obliczony na podstawie logiki niestandardowej, można użyć inicjatora telemetrii, który skopiuje dane adresu IP podane w `ai.location.ip` oddzielnym polu niestandardowym. Jednak w przeciwieństwie do zestawów SDK po stronie serwera, bez polegania na bibliotekach innych firm lub własnej niestandardowej logiki zbierania adresów IP po stronie klienta, zestaw SDK klienta nie będzie obliczany przez użytkownika.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Wyświetlanie wyników inicjatora telemetrii

Jeśli następnie Wyzwalasz nowy ruch dla lokacji i poczekaj około 2-5 minut, aby upewnić się, że miało to miejsce, możesz uruchomić zapytanie Kusto, aby sprawdzić, czy kolekcja adresów IP działa:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nowo zebrane adresy IP powinny pojawić się `customDimensions_client-ip` w kolumnie. Kolumna Domyślna `client-ip` nadal będzie zawierać wszystkie 4 oktety albo zero lub tylko wyświetlanie pierwszych trzech oktetów w zależności od sposobu skonfigurowania zbierania adresów IP na poziomie składnika. W przypadku testowania lokalnego po wdrożeniu inicjatora telemetrii, gdy wartość widoczna `customDimensions_client-ip` dla `::1` jest to oczekiwane zachowanie. `::1`reprezentuje adres sprzężenia zwrotnego w protokole IPv6. Jest on odpowiednikiem `127.0.01` w protokole IPv4, a wynik jest wyświetlany podczas testowania z hosta lokalnego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zbieraniu danych osobowych](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) w Application Insights.

* Dowiedz się więcej o sposobie działania [zbierania adresów IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) w Application Insights. (Jest to starszy wpis w blogu zewnętrznym zapisany przez jednego z naszych inżynierów. Jest to wstępnie bieżące zachowanie domyślne, w którym adres IP jest rejestrowany jako `0.0.0.0`, ale jest bardziej głębokością Mechanics wbudowanego `ClientIpHeaderTelemetryInitializer`.)
