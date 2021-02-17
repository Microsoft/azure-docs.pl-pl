---
title: Identyfikatory kontekstu użytkownika do śledzenia aktywności — Application Insights platformy Azure
description: Śledź, w jaki sposób użytkownicy przechodzą przez usługę, przypisując każdemu z nich unikatowy, trwały ciąg identyfikatora w Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: 021c76bcd03bbe35eabec5611fe0cc1e2c7c4427
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583341"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Wyślij identyfikatory kontekstu użytkownika, aby włączyć środowiska użycia na platformie Azure Application Insights

## <a name="tracking-users"></a>Śledzenie użytkowników

Application Insights umożliwia monitorowanie i śledzenie użytkowników za pomocą zestawu narzędzi do użycia produktów:

- [Użytkownicy, sesje, zdarzenia](./usage-segmentation.md)
- [Lejki](./usage-funnels.md)
- [Przechowywanie](./usage-retention.md) Kohorty
- [Skoroszyty](../visualize/workbooks-overview.md)

Aby śledzić, co użytkownik robi w czasie, Application Insights wymaga identyfikatora dla każdego użytkownika lub sesji. Uwzględnij następujące identyfikatory w każdym zdarzeniu niestandardowym lub w dowolnym widoku strony.

- Użytkownicy, Lejki, przechowywanie i kohorty: zawierają identyfikator użytkownika.
- Sesje: Uwzględnij identyfikator sesji.

> [!NOTE]
> Jest to zaawansowany artykuł zawierający opis ręcznych kroków śledzenia aktywności użytkowników przy użyciu Application Insights. W przypadku wielu aplikacji sieci Web **te czynności mogą nie być wymagane**, ponieważ domyślne zestawy SDK po stronie serwera, które są używane w połączeniu z [zestawem JavaScript klienta/po stronie przeglądarki](./website-monitoring.md), są często wystarczające do automatycznego śledzenia aktywności użytkownika. Jeśli nie skonfigurowano [monitorowania po stronie klienta](./website-monitoring.md) oprócz zestawu SDK po stronie serwera, należy najpierw wykonać te czynności i sprawdzić, czy narzędzia analityczne zachowania użytkownika działają zgodnie z oczekiwaniami.

## <a name="choosing-user-ids"></a>Wybieranie identyfikatorów użytkowników

Identyfikatory użytkowników powinny być przechowywane między sesjami użytkowników w celu śledzenia sposobu zachowania użytkowników w czasie. Istnieją różne podejścia do utrwalania tego identyfikatora.

- Definicja użytkownika, który już istnieje w usłudze.
- Jeśli usługa ma dostęp do przeglądarki, może przekazać do przeglądarki plik cookie z IDENTYFIKATORem. Identyfikator będzie trwały, o ile plik cookie pozostanie w przeglądarce użytkownika.
- W razie potrzeby można użyć nowego identyfikatora każdej sesji, ale wyniki dotyczące użytkowników będą ograniczone. Na przykład nie będzie można zobaczyć, jak zmienia się zachowanie użytkownika w czasie.

Identyfikator powinien być GUID lub innym złożonym ciągiem, aby identyfikować każdy użytkownik w unikatowy sposób. Na przykład może to być długa liczba losowa.

Jeśli identyfikator zawiera informacje osobiste dotyczące użytkownika, nie jest to odpowiednia wartość do wysłania do Application Insights jako identyfikator użytkownika. Ten identyfikator można wysłać jako [Identyfikator użytkownika uwierzytelnionego](./api-custom-events-metrics.md#authenticated-users), ale nie spełnia on wymagań dotyczących identyfikatora użytkownika dla scenariuszy użycia.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET Apps: Ustawianie kontekstu użytkownika w ITelemetryInitializer

Utwórz inicjator telemetrii, jak opisano [tutaj](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)szczegółowo. Przekaż identyfikator sesji za pomocą telemetrii żądania, a następnie ustaw Context.User.Id i Context.Session.Id.

Ten przykład ustawia identyfikator użytkownika na identyfikator, który wygasa po sesji. Jeśli to możliwe, należy użyć identyfikatora użytkownika, który utrzymuje się między sesjami.

### <a name="telemetry-initializer"></a>Inicjator telemetrii

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby włączyć środowiska użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](./api-custom-events-metrics.md#trackevent) lub [wyświetleń stron](./api-custom-events-metrics.md#page-views).
- Jeśli masz już wysłane zdarzenia niestandardowe lub widoki stron, zapoznaj się z narzędziami użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Przegląd użycia](usage-overview.md)
    - [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [Noszą](usage-retention.md)
    - [Skoroszyty](../visualize/workbooks-overview.md)

