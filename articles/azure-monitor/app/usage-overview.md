---
title: Analiza użycia za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zapoznaj się z użytkownikami i Dowiedz się, co robią z Twoją aplikacją.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 633d35ec16f5eb9de664421c38cd4c824dc240cf
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233852"
---
# <a name="usage-analysis-with-application-insights"></a>Analiza użycia za pomocą usługi Application Insights

Które funkcje aplikacji sieci Web lub mobilnej są najbardziej popularne? Czy użytkownicy uzyskują swoje cele przy użyciu swojej aplikacji? Czy są one porzucane w określonych punktach i są zwracane później?  [Usługa Azure Application Insights](./app-insights-overview.md) pozwala uzyskać zaawansowane informacje dotyczące sposobu korzystania z aplikacji przez użytkowników. Za każdym razem, gdy aktualizujesz aplikację, możesz ocenić, jak dobrze działa dla użytkowników. Korzystając z tej wiedzy, można podejmować decyzje oparte na danych dotyczące kolejnych cykli tworzenia oprogramowania.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Wysyłanie danych telemetrycznych z aplikacji

Najlepsze środowisko jest uzyskiwane przez zainstalowanie Application Insights zarówno w kodzie serwera aplikacji, jak i na stronach sieci Web. Składniki klienta i serwera aplikacji wysyłają dane telemetryczne z powrotem do Azure Portal na potrzeby analizy.

1. **Kod serwera:** Zainstaluj odpowiedni moduł dla [ASP.NET](./asp-net.md), [platformy Azure](./app-insights-overview.md), [środowiska Java](./java-get-started.md), [Node.js](./nodejs.md)lub [innej](./platforms.md) aplikacji.

    * *Nie chcesz instalować kodu serwera? Po prostu [Utwórz zasób usługi Azure Application Insights](./create-new-resource.md).*

2. **Kod strony sieci Web:** Przed zamknięciem Dodaj następujący skrypt do strony sieci Web ``</head>`` . Zastąp klucz Instrumentacji odpowiednią wartością dla Application Insights zasobu:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Aby dowiedzieć się więcej na temat zaawansowanych konfiguracji monitorowania witryn sieci Web, zapoznaj się z [artykułem dotyczącym zestawu SDK języka JavaScript](./javascript.md).

3. **Kod aplikacji mobilnej:** Użyj zestawu SDK App Center, aby zbierać zdarzenia z aplikacji, a następnie wysyłać kopie tych zdarzeń do Application Insights do analizy, [postępując zgodnie z tym przewodnikiem](../learn/mobile-center-quickstart.md).

4. **Pobierz dane telemetryczne:** Uruchom projekt w trybie debugowania przez kilka minut, a następnie wyszukaj wyniki w bloku przegląd w Application Insights.

    Opublikuj swoją aplikację, aby monitorować wydajność aplikacji i dowiedzieć się, co użytkownicy robią z aplikacją.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Dołącz identyfikator użytkownika i sesji do telemetrii
Aby śledzić użytkowników w czasie, Application Insights wymaga ich zidentyfikowania. Narzędzie zdarzenia jest jedynym narzędziem do użycia, które nie wymaga identyfikatora użytkownika lub identyfikatora sesji.

Rozpocznij wysyłanie identyfikatorów użytkowników i sesji przy użyciu [tego procesu](./usage-send-user-context.md).

## <a name="explore-usage-demographics-and-statistics"></a>Eksplorowanie danych demograficznych dotyczących użycia i statystyk
Dowiedz się, w jaki sposób użytkownicy korzystają z Twojej aplikacji, jakie strony są najbardziej interesujące, w których znajdują się Twoje osoby, których przeglądarek i systemów operacyjnych używają. 

Raporty Użytkownicy i sesje filtrują dane według stron lub zdarzeń niestandardowych i segmentują je według właściwości, takich jak lokalizacja, środowisko i strona. Możesz również dodać własne filtry.

![Przechwytywanie ekranu pokazuje stronę przegląd użytkowników dla fikcyjnej firmy.](./media/usage-overview/users.png)  

Szczegółowe informacje o interesujących wzorcach z prawej strony zestawu danych.  

* Raport **Użytkownicy** liczy liczbę unikatowych użytkowników, którzy uzyskują dostęp do stron w wybranych okresach. W przypadku aplikacji sieci Web użytkownicy są zliczane przy użyciu plików cookie. Jeśli ktoś uzyskuje dostęp do witryny z różnymi przeglądarkami lub komputerami klienckimi lub czyści pliki cookie, zostaną one zliczone więcej niż jeden raz.
* Raport **sesje** liczy liczbę sesji użytkowników, które uzyskują dostęp do witryny. Sesja to okres działania użytkownika zakończony przez okres nieaktywności dłuższy niż pół godziny.

[Więcej informacji o narzędziach użytkownicy, sesje i zdarzenia](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Przechowywanie — ilu użytkowników wraca?

Przechowywanie ułatwia zrozumienie, jak często użytkownicy zwracają się do korzystania z aplikacji, w oparciu o kohorty użytkowników, którzy wykonali pewne działania biznesowe w określonym przedziale czasu. 

- Informacje o konkretnych funkcjach, które powodują, że użytkownicy mogą wrócić więcej niż inne 
- Formy poformowania w oparciu o prawdziwe dane użytkownika 
- Określanie, czy przechowywanie jest problemem w produkcie 

![Przechwytywanie ekranu zawiera stronę przegląd przechowywania, która zawiera informacje o tym, jak często użytkownicy zwracają się do korzystania z aplikacji.](./media/usage-overview/retention.png) 

Kontrolki przechowywania na górze umożliwiają zdefiniowanie określonych zdarzeń i zakresu czasu w celu obliczenia okresu przechowywania. Wykres w środku daje wizualną reprezentację ogólnej wartości procentowej przechowywania według określonego zakresu czasu. Wykres na dole reprezentuje indywidualne przechowywanie w danym okresie czasu. Ten poziom szczegółowości pozwala zrozumieć, co robią Użytkownicy, i co może wpłynąć na zwracanie użytkowników w celu uzyskania bardziej szczegółowego stopnia szczegółowości.  

[Więcej informacji o narzędziu do przechowywania](usage-retention.md)

## <a name="custom-business-events"></a>Niestandardowe zdarzenia biznesowe

Aby jasno zrozumieć, co użytkownicy robią z aplikacją, warto wstawić wiersze kodu do rejestrowania zdarzeń niestandardowych. Zdarzenia te mogą śledzić wszystkie szczegółowe czynności użytkownika, takie jak kliknięcie określonych przycisków, do bardziej znaczących wydarzeń, takich jak zakup lub przegranie gry.

Możesz również użyć [wtyczki Autocollection analizy](javascript-click-analytics-plugin.md) do zbierania zdarzeń niestandardowych.

Chociaż w niektórych przypadkach wyświetlenia stron mogą reprezentować przydatne zdarzenia, nie jest to ogólnie prawdziwe. Użytkownik może otworzyć stronę produktu bez kupowania produktu. 

W przypadku określonych zdarzeń firmy można stworzyć wykres postępów użytkowników w witrynie. Możesz sprawdzić preferencje dotyczące różnych opcji oraz miejsce ich porzucenia lub mieć problemy. Korzystając z tej wiedzy, można podejmować świadome decyzje dotyczące priorytetów w zaległościach związanych z programowaniem.

Zdarzenia mogą być rejestrowane po stronie klienta aplikacji:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Lub po stronie serwera:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Można dołączyć wartości właściwości do tych zdarzeń, aby można było filtrować lub dzielić zdarzenia podczas inspekcji w portalu. Ponadto do każdego zdarzenia jest dołączony standardowy zestaw właściwości, taki jak identyfikator użytkownika anonimowego, który umożliwia śledzenie sekwencji działań poszczególnych użytkowników.

Dowiedz się więcej na temat [niestandardowych zdarzeń](./api-custom-events-metrics.md#trackevent) i [Właściwości](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Zdarzenia wycinków i indeksów

W narzędziach użytkownicy, sesje i zdarzenia można wydzielić zdarzenia niestandardowe i indeksy według użytkownika, nazwy zdarzenia i właściwości.
![Przechwytywanie ekranu pokazuje stronę przegląd użytkowników dla fikcyjnej firmy.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Zaprojektuj telemetrię przy użyciu aplikacji

Podczas projektowania każdej funkcji aplikacji należy wziąć pod uwagę sposób mierzenia jej sukcesu z użytkownikami. Zdecyduj, jakie zdarzenia biznesowe należy nagrać, i Zakoduj wywołania śledzenia dla tych zdarzeń w aplikacji od początku.

## <a name="a--b-testing"></a>A | Testowanie B
Jeśli nie wiesz, która odmiana funkcji będzie bardziej pomyślna, zwolnij oba z nich, udostępniając każdy dostęp różnym użytkownikom. Zmierz sukces każdego z nich, a następnie przejdź do ujednoliconej wersji.

W tej metodzie należy dołączyć różne wartości właściwości do wszystkich danych telemetrycznych wysyłanych przez poszczególne wersje aplikacji. Można to zrobić przez zdefiniowanie właściwości w aktywnym TelemetryContext. Te domyślne właściwości są dodawane do każdej wiadomości telemetrycznej wysyłanej przez aplikację — nie tylko wiadomości niestandardowych, ale również dla standardowej telemetrii.

W portalu Application Insights Przefiltruj i Podziel dane na wartości właściwości, aby porównać różne wersje.

W tym celu należy [skonfigurować inicjator telemetrii](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**Aplikacje ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

W inicjatorze aplikacji sieci Web, np. Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplikacje platformy ASP.NET Core**

> [!NOTE]
> Dodawanie inicjatora przy użyciu `ApplicationInsights.config` lub użycie `TelemetryConfiguration.Active` nie jest prawidłowe dla aplikacji ASP.NET Core. 

W przypadku aplikacji [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) dodanie nowego `TelemetryInitializer` jest wykonywane przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Jest to wykonywane w `ConfigureServices` metodzie `Startup.cs` klasy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Wszystkie nowe TelemetryClients automatycznie dodają określoną wartość właściwości. Poszczególne zdarzenia telemetrii mogą przesłonić wartości domyślne.

## <a name="next-steps"></a>Następne kroki
   - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
   - [Lejki](usage-funnels.md)
   - [Przechowywanie](usage-retention.md)
   - [Przepływy użytkownika](usage-flows.md)
   - [Skoroszyty](../platform/workbooks-overview.md)
   - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)

