---
title: Diagnozowanie błędów i wyjątków za pomocą usługi Azure Application Insights
description: Przechwytuj wyjątki z aplikacji ASP.NET wraz z danymi telemetrycznymi żądania.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 926516075c7d43e6e800403a69ff3ab8f1233fcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726995"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnozowanie wyjątków w aplikacjach internetowych za pomocą usługi Application Insights
Wyjątki w aplikacji sieci Web na żywo są zgłaszane przez [Application Insights](./app-insights-overview.md). Możliwe jest skorelowanie żądań zakończonych niepowodzeniem z wyjątkami i innymi zdarzeniami zarówno na kliencie, jak i na serwerze, dzięki czemu można szybko zdiagnozować przyczyny.

## <a name="set-up-exception-reporting"></a>Konfigurowanie raportowania wyjątków
* Aby były zgłoszone wyjątki z aplikacji serwera:
  * Aplikacje sieci Web platformy Azure: Dodawanie [rozszerzenia Application Insights](./azure-web-apps.md)
  * Maszyna wirtualna platformy Azure i zestaw skalowania maszyn wirtualnych platformy Azure — aplikacje hostowane: Dodaj [rozszerzenie monitorowania aplikacji](./azure-vm-vmss-apps.md)
  * Zainstaluj [Application Insights zestaw SDK](./asp-net.md) w kodzie aplikacji lub
  * Serwery sieci Web usług IIS: Uruchom [agenta Application Insights](./monitor-performance-live-website-now.md); oraz
  * Aplikacje sieci Web Java: Włączanie [agenta Java](./java-in-process-agent.md)
* Zainstaluj [fragment kodu JavaScript](./javascript.md) na stronach sieci Web, aby przechwycić wyjątki przeglądarki.
* W niektórych strukturach aplikacji lub z niektórymi ustawieniami należy wykonać kilka dodatkowych kroków, aby przechwycić więcej wyjątków:
  * [Formularze sieci Web](#web-forms)
  * [MVC](#mvc)
  * [Interfejs API sieci Web 1. *](#web-api-1x)
  * [Web API 2. *](#web-api-2x)
  * [WCF](#wcf)

  Ten artykuł dotyczy głównie aplikacji .NET Framework z perspektywy przykładowego kodu. Niektóre metody, które działają dla .NET Framework są przestarzałe w zestaw .NET Core SDK. Zapoznaj się z [dokumentacją zestaw .NET Core SDK](./asp-net-core.md) , jeśli masz aplikację .NET Core.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnozowanie wyjątków przy użyciu programu Visual Studio
Otwórz rozwiązanie aplikacji w programie Visual Studio, aby pomóc w debugowaniu.

Uruchom aplikację na serwerze lub na komputerze deweloperskim przy użyciu klawisza F5.

Otwórz okno wyszukiwania Application Insights w programie Visual Studio i ustaw je, aby wyświetlało zdarzenia z aplikacji. Podczas debugowania możesz to zrobić, klikając przycisk Application Insights.

![Kliknij prawym przyciskiem myszy projekt, a następnie wybierz Application Insights, Otwórz.](./media/asp-net-exceptions/34.png)

Należy zauważyć, że można filtrować raport, aby pokazać tylko wyjątki.

*Nie pokazywane są wyjątki? Zobacz [wyjątki przechwytywania](#exceptions).*

Kliknij raport o wyjątku, aby wyświetlić jego ślad stosu.
Kliknij odwołanie do wiersza w śladzie stosu, aby otworzyć odpowiedni plik kodu.

W kodzie należy zauważyć, że CodeLens wyświetla dane dotyczące wyjątków:

![CodeLens powiadomienia o wyjątkach.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnozowanie błędów przy użyciu Azure Portal
Application Insights udostępnia nadzorowane środowisko APM ułatwiające diagnozowanie błędów w monitorowanych aplikacjach. Aby rozpocząć, kliknij opcję błędy w menu zasobów Application Insights, które znajdują się w sekcji Zbadaj.
Powinien zostać wyświetlony widok pełny ekran, który pokazuje trendy częstotliwości awarii dla Twoich żądań, ile z nich kończy się niepowodzeniem i ilu użytkowników ma wpływ. Po prawej stronie zobaczysz niektóre z najbardziej przydatnych dystrybucji specyficznych dla wybranej operacji zakończonych niepowodzeniem, w tym trzy najważniejsze kody odpowiedzi, trzy najważniejsze typy wyjątków i trzy pierwsze niepowodzenie typów zależności.

![Widok Klasyfikacja błędów (karta Operacje)](./media/asp-net-exceptions/failures0719.png)

W jednym kliknięciem można przejrzeć reprezentatywne przykłady dla każdego z tych podzestawów operacji. W szczególności aby zdiagnozować wyjątki, można kliknąć licznik konkretnego wyjątku, który ma zostać przedstawiony z kartą szczegółów transakcji end-to-end, na przykład:

![Karta Szczegóły z końca do końca transakcji](./media/asp-net-exceptions/end-to-end.png)

**Alternatywnie,** zamiast wyszukiwać wyjątki dla konkretnej nieudanej operacji, można zacząć od ogólnego widoku wyjątków, przełączając się na kartę Wyjątki u góry. W tym miejscu możesz zobaczyć wszystkie wyjątki zebrane dla monitorowanej aplikacji.

*Nie pokazywane są wyjątki? Zobacz [wyjątki przechwytywania](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Śledzenie niestandardowe i dane dziennika
Aby uzyskać dane diagnostyczne specyficzne dla aplikacji, możesz wstawić kod do wysyłania własnych danych telemetrycznych. Ta wartość jest wyświetlana w przeszukiwaniu diagnostycznym obok żądania, widoku strony i innych automatycznie zbieranych danych.

Istnieje kilka rozwiązań:

* [Poleceń trackEvent ()](./api-custom-events-metrics.md#trackevent) jest zwykle używany do monitorowania wzorców użycia, ale dane wysyłane również są wyświetlane w obszarze niestandardowe zdarzenia w przeszukiwaniu diagnostycznym. Zdarzenia mają nazwę i mogą mieć właściwości ciągów i metryki liczbowe, na których można [filtrować wyszukiwania diagnostyczne](./diagnostic-search.md).
* [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) umożliwia wysyłanie dłuższych danych, takich jak informacje o publikacji.
* Traceexception [()](#exceptions) wysyła ślady stosu. [Więcej informacji o wyjątkach](#exceptions).
* Jeśli używasz już platformy rejestrowania, takiej jak Log4Net lub NLog, możesz [przechwytywać te dzienniki](asp-net-trace-logs.md) i wyświetlać je w przeszukiwaniu diagnostycznym obok danych żądania i wyjątków.

Aby wyświetlić te zdarzenia, Otwórz [Wyszukiwanie](./diagnostic-search.md) w menu po lewej stronie, wybierz **typy zdarzeń** menu rozwijanego, a następnie wybierz zdarzenie niestandardowe, śledzenie lub wyjątek.

![Przeglądanie szczegółowe](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Jeśli aplikacja generuje wiele danych telemetrycznych, moduł próbkowania adaptacyjnego będzie automatycznie redukować ilość danych wysyłanych do portalu, wysyłając tylko ich reprezentatywną część. Zdarzenia, które są częścią tej samej operacji, zostaną wybrane lub odwybrane jako Grupa, aby można było nawigować między powiązanymi zdarzeniami. [Więcej informacji na temat próbkowania.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak zobaczyć dane POST żądania
Szczegóły żądania nie obejmują danych wysyłanych do aplikacji w wywołaniu POST. Aby zgłosić te dane:

* [Zainstaluj zestaw SDK](./asp-net.md) w projekcie aplikacji.
* Wstaw kod w aplikacji, aby wywołać metodę [Microsoft. ApplicationInsights. TrackTrace ()](./api-custom-events-metrics.md#tracktrace). Wyślij dane POST w parametrze Message. Limit dozwolonego rozmiaru, dlatego należy spróbować wysłać tylko istotne dane.
* Podczas badania niepomyślnego żądania Znajdź skojarzone ślady.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Przechwytywanie wyjątków i powiązanych danych diagnostycznych
Na początku nie zobaczysz w portalu wszystkich wyjątków, które powodują błędy w aplikacji. Zobaczysz dowolne wyjątki przeglądarki (Jeśli używasz [zestawu JavaScript SDK](./javascript.md) na stronach sieci Web). Jednak większość wyjątków serwera są przechwytywane przez usługi IIS i trzeba napisać bit kodu, aby je zobaczyć.

Oto co możesz zrobić:

* **Rejestruj wyjątki jawnie** przez wstawianie kodu w obsłudze wyjątków w celu zgłoszenia wyjątków.
* **Automatycznie Przechwytuj wyjątki** przez skonfigurowanie platformy ASP.NET. Niezbędne Dodatki są różne dla różnych typów platform.

## <a name="reporting-exceptions-explicitly"></a>Raportowanie wyjątków jawnie
Najprostszym sposobem jest wstawienie wywołania metody Trackexception () w programie obsługi wyjątków.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Parametry właściwości i pomiarów są opcjonalne, ale są przydatne do [filtrowania i dodawania](./diagnostic-search.md) dodatkowych informacji. Na przykład jeśli masz aplikację, która może uruchamiać kilka gier, możesz znaleźć wszystkie raporty dotyczące wyjątków powiązane z konkretną grą. Możesz dodać dowolną liczbę elementów do każdego słownika.

## <a name="browser-exceptions"></a>Wyjątki przeglądarki
Wszystkie wyjątki przeglądarki są zgłaszane.

Jeśli strona sieci Web zawiera pliki skryptów z sieci dostarczania zawartości lub innych domen, upewnij się, że tag skryptu ma atrybut ```crossorigin="anonymous"``` i że serwer wysyła [nagłówki CORS](https://enable-cors.org/). Pozwoli to na uzyskanie śledzenia i szczegółów stosu dla nieobsłużonych wyjątków JavaScript z tych zasobów.

## <a name="reuse-your-telemetry-client"></a>Ponowne używanie klienta telemetrii

> [!NOTE]
> Zaleca się utworzenie wystąpienia TelemetryClient raz i ponowne użycie przez cały czas życia aplikacji.

Poniżej przedstawiono przykład użycia TelemetryClient prawidłowo.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Formularze sieci Web
W przypadku formularzy sieci Web moduł HTTP będzie mógł zbierać wyjątki, gdy nie ma żadnych przekierowań skonfigurowanych dla usługi CustomErrors.

Ale jeśli masz aktywne przekierowania, Dodaj następujące wiersze do funkcji Application_Error w elemencie Global. asax. cs. (Dodaj plik Global. asax, jeśli jeszcze go nie masz).

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Począwszy od Application Insights zestawu SDK sieci Web w wersji 2,6 (beta3 i nowszych), Application Insights zbiera Nieobsłużone wyjątki, które zostały automatycznie zgłoszone w metodach kontrolerów MVC 5. Jeśli wcześniej dodano niestandardową procedurę obsługi do śledzenia takich wyjątków (zgodnie z opisem w poniższych przykładach), można ją usunąć, aby zapobiec podwójnemu śledzeniu wyjątków.

Istnieją różne przypadki, w których nie można obsłużyć filtrów wyjątków. Na przykład:

* Zgłoszone wyjątki przez konstruktorów kontrolerów.
* Zgłoszone wyjątki przez programy obsługi komunikatów.
* Zgłoszone wyjątki podczas routingu.
* Zgłoszone wyjątki podczas serializacji zawartości odpowiedzi.
* Zgłoszony wyjątek podczas uruchamiania aplikacji.
* Zgłoszony wyjątek w zadaniach w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie.
Nieobsłużone wyjątki pochodzące z kontrolerów zwykle powodują, że odpowiedź 500 "wewnętrzny błąd serwera". Jeśli taka odpowiedź jest tworzona ręcznie w wyniku obsłużonego wyjątku (lub bez wyjątku), jest śledzona w odpowiedniej telemetrii żądania z `ResultCode` 500, Application Insights jednak zestaw SDK nie może śledzić odpowiadającego wyjątku.

### <a name="prior-versions-support"></a>Obsługa wcześniejszych wersji
W przypadku używania MVC 4 (i wcześniejszych) Application Insights Web SDK 2,5 (i starszych) zapoznaj się z poniższymi przykładami, aby śledzić wyjątki.

Jeśli konfiguracja [customErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) ma wartość `Off` , wyjątki będą dostępne dla [modułu http](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) do zbierania. Jednak jeśli jest `RemoteOnly` (domyślnie) lub `On` , wyjątek zostanie wyczyszczony i nie będzie dostępny do Application Insights do automatycznego zbierania. Można to naprawić, zastępując [klasę System. Web. MVC. HandleErrorAttribute](/dotnet/api/system.web.mvc.handleerrorattribute)i stosując przesłoniętą klasę, jak pokazano w różnych wersjach MVC poniżej ([Źródło GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Zastąp atrybut HandleError nowym atrybutem na kontrolerach.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Przykład](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Zarejestruj `AiHandleErrorAttribute` jako filtr globalny w Global. asax. cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Przykład](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Zarejestruj AiHandleErrorAttribute jako filtr globalny w FilterConfig. cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Przykład](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Interfejs API sieci Web
Począwszy od Application Insights zestawu SDK sieci Web w wersji 2,6 (beta3 i nowszych), Application Insights zbiera Nieobsłużone wyjątki, które są automatycznie generowane w metodach kontrolera dla WebAPI 2 +. Jeśli wcześniej dodano niestandardową procedurę obsługi do śledzenia takich wyjątków (zgodnie z opisem w poniższych przykładach), można ją usunąć, aby zapobiec podwójnemu śledzeniu wyjątków.

Istnieją różne przypadki, w których nie można obsłużyć filtrów wyjątków. Na przykład:

* Zgłoszone wyjątki przez konstruktorów kontrolerów.
* Zgłoszone wyjątki przez programy obsługi komunikatów.
* Zgłoszone wyjątki podczas routingu.
* Zgłoszone wyjątki podczas serializacji zawartości odpowiedzi.
* Zgłoszony wyjątek podczas uruchamiania aplikacji.
* Zgłoszony wyjątek w zadaniach w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie.
Nieobsłużone wyjątki pochodzące z kontrolerów zwykle powodują, że odpowiedź 500 "wewnętrzny błąd serwera". Jeśli taka odpowiedź jest tworzona ręcznie w wyniku obsłużonego wyjątku (lub bez wyjątku), jest śledzona w odpowiedniej telemetrii żądania z `ResultCode` 500, Application Insights jednak zestaw SDK nie może śledzić odpowiadającego wyjątku.

### <a name="prior-versions-support"></a>Obsługa wcześniejszych wersji
Jeśli używasz WebAPI 1 (i starszych) Application Insights Web SDK 2,5 (i starszych), zapoznaj się z poniższymi przykładami, aby śledzić wyjątki.

#### <a name="web-api-1x"></a>Interfejs API sieci Web 1. x
Zastąp system. Web. http. filters. ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Można dodać ten zastąpiony atrybut do określonych kontrolerów lub dodać go do konfiguracji filtru globalnego w klasie WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Przykład](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2. x
Dodaj implementację interfejsu iexceptionlogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Dodaj to do usług w WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Przykład](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Jako alternatywy, możesz:

1. Zastąp jedyną ExceptionHandlerą niestandardową implementacją IExceptionHandler. Jest to możliwe tylko wtedy, gdy struktura nadal może wybrać komunikat odpowiedzi do wysłania (nie w przypadku przerwania połączenia dla wystąpienia)
2. Filtry wyjątków (zgodnie z opisem w sekcji dotyczącej kontrolerów Web API 1. x powyżej) — nie są wywoływane we wszystkich przypadkach.

## <a name="wcf"></a>WCF
Dodaj klasę rozszerzającą atrybut i implementującą IErrorHandler i IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Przykład](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Liczniki wydajności wyjątków
Jeśli na serwerze [zainstalowano agenta Application Insights](./monitor-performance-live-website-now.md) , można uzyskać wykres częstotliwości wyjątków mierzony przez platformę .NET. Dotyczy to zarówno obsłużonych, jak i nieobsługiwanych wyjątków platformy .NET.

Otwórz kartę Eksplorator metryk, Dodaj nowy wykres i wybierz pozycję **częstotliwość wyjątków**, która znajduje się w obszarze liczniki wydajności.

Program .NET Framework oblicza stawkę, zliczając liczbę wyjątków w interwale i dzieląc je na długość interwału.

Różni się to od licznika "Exceptions" obliczonego w raportach zliczania portalu Application Insights. Interwały próbkowania są różne, a zestaw SDK nie wysyła raportów usługi Trackexception dla wszystkich obsłużonych i nieobsłużonych wyjątków.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie REST, SQL i innych wywołań zależności](./asp-net-dependencies.md)
* [Monitorowanie czasów ładowania strony, wyjątków przeglądarki i wywołań AJAX](./javascript.md)
* [Monitorowanie liczników wydajności](./performance-counters.md)

