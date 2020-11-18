---
title: Przewodnik rozwiązywania problemów z usługą Azure SignalR Service
description: Dowiedz się, jak rozwiązywać typowe problemy
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 11ea348a80bc226b6a96bea1e7c023ee9c06b13a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684121"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Przewodnik rozwiązywania problemów z typowymi problemami dotyczącymi usługi Azure Signal

Te wskazówki mają na celu zapewnienie przydatnego przewodnika rozwiązywania problemów w oparciu o typowe problemy, które klienci spełnią i rozwiązane w ciągu ostatnich lat.

## <a name="access-token-too-long"></a>Token dostępu jest zbyt długi

### <a name="possible-errors"></a>Możliwe błędy:

* Po stronie klienta `ERR_CONNECTION_`
* 414 URI zbyt długi
* ładunek 413 zbyt duży
* Token dostępu nie może być dłuższy niż 4K. Jednostka żądania 413 jest zbyt duża

### <a name="root-cause"></a>Główna przyczyna:

W przypadku protokołu HTTP/2 Maksymalna długość pojedynczego nagłówka to **4 K**, więc jeśli używasz przeglądarki do uzyskiwania dostępu do usługi Azure, wystąpi błąd `ERR_CONNECTION_` dla tego ograniczenia.

W przypadku klientów z protokołem HTTP/1.1 lub C# Maksymalna długość identyfikatora URI to **12 k**, Maksymalna długość nagłówka to **16 k**.

W przypadku zestawu SDK w wersji **1.0.6** lub nowszej program zgłasza, `/negotiate` `413 Payload Too Large` gdy wygenerowany token dostępu będzie większy niż **4 K**.

### <a name="solution"></a>Rozwiązanie:

Domyślnie oświadczenia z `context.User.Claims` są uwzględniane podczas generowania tokenu dostępu JWT do **ASRS**(Zure **s** ignal **R** **s** nazwa), dzięki czemu oświadczenia są zachowywane i mogą być przekazywane z **ASRS** do,**A** `Hub` gdy klient łączy się z `Hub` .

W niektórych przypadkach `context.User.Claims` są wykorzystywane do przechowywania dużej ilości informacji dla serwera aplikacji, z których większość nie jest używana przez usługi `Hub` s, ale przez inne składniki.

Wygenerowany token dostępu jest przesyłany przez sieć i dla połączeń WebSocket/SSE tokeny dostępu są przesyłane przez ciągi zapytań. W związku z tym najlepszym rozwiązaniem jest sugerujemy tylko przekazywanie **niezbędnych** oświadczeń od klienta za pośrednictwem usługi **ASRS** do serwera aplikacji, gdy centrum potrzebuje.

Istnieje `ClaimsProvider` możliwość dostosowania oświadczeń przekazujących do **ASRS** wewnątrz tokenu dostępu.

Dla ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Dla ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>Wymagany protokół TLS 1,2

### <a name="possible-errors"></a>Możliwe błędy:

* Błąd ASP.NET "Brak dostępnego serwera" [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "połączenie nie jest aktywne, nie można wysłać danych do usługi". [#324](https://github.com/Azure/azure-signalr/issues/324) błędów
* "Wystąpił błąd podczas wykonywania żądania HTTP do https:// <API endpoint> . Ten błąd może być spowodowany faktem, że certyfikat serwera nie jest poprawnie skonfigurowany przy użyciu HTTP.SYS w przypadku protokołu HTTPS. Ten błąd może być również spowodowany niezgodnością powiązania zabezpieczeń między klientem a serwerem ".

### <a name="root-cause"></a>Główna przyczyna:

Usługa platformy Azure obsługuje tylko protokół TLS 1.2 pod kątem problemów z zabezpieczeniami. W przypadku programu .NET Framework istnieje możliwość, że protokół TLS 1.2 nie jest domyślnym protokołem. W związku z tym nie można pomyślnie nawiązać połączenia z serwerem ASRS.

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

1. Jeśli ten błąd może być odtwarzany lokalnie, usuń zaznaczenie *tylko mój kod* i Zgłoś wszystkie wyjątki CLR i Debuguj lokalnie serwer aplikacji, aby zobaczyć, jakie błędy zgłasza wyjątek.
    * Usuń zaznaczenie *tylko mój kod*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Usuń zaznaczenie Tylko mój kod":::

    * Generuj wyjątki CLR

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Generuj wyjątki CLR":::

    * Zobacz zgłaszanie wyjątków podczas debugowania kodu po stronie serwera aplikacji:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Zgłasza wyjątek":::

2. W przypadku ASP.NET można także dodać do niego następujący kod, `Startup.cs` Aby włączyć szczegółowe śledzenie i wyświetlić błędy z dziennika.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Rozwiązanie:

Dodaj następujący kod do uruchamiania:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400 Nieprawidłowe żądanie zwrócone dla żądań klientów

### <a name="root-cause"></a>Główna przyczyna

Sprawdź, czy żądanie klienta ma wiele `hub` ciągów zapytań. `hub` jest zachowanym parametrem zapytania i 400, jeśli usługa wykryje więcej niż jeden `hub` w zapytaniu.

## <a name="401-unauthorized-returned-for-client-requests"></a>Błąd 401 — brak autoryzacji zwracany w przypadku żądań klientów

### <a name="root-cause"></a>Główna przyczyna

Obecnie wartość domyślna okresu istnienia tokenu JWT to 1 godzina.

W przypadku ASP.NET Core sygnalizującego, gdy używa typu transportu WebSocket, jest to OK.

W przypadku innego typu transportu dla usługi ASP.NET Core sygnalizującego i sondowania długotrwałego oznacza to, że domyślnie połączenie może utrzymywać się maksymalnie przez 1 godzinę.

W przypadku sygnalizującego ASP.NET klient wysyła `/ping` do usługi żądanie utrzymywania ruchu w czasie, gdy `/ping` zakończy się niepowodzeniem, klient **przerywa** połączenie i nigdy nie nawiązuje połączenia ponownie. Oznacza to, że dla sygnalizującego ASP.NET, domyślny okres istnienia tokenu powoduje, że połączenie jest **wykonywane przez maksymalnie** 1 godzinę dla wszystkich typów transportu.

### <a name="solution"></a>Rozwiązanie

Ze względów bezpieczeństwa nie jest zalecane zwiększenie czasu wygaśnięcia. Zalecamy dodanie logiki reconnect z klienta, aby ponownie uruchomić połączenie, gdy wystąpi takie 401. Po ponownym uruchomieniu połączenia klient będzie negocjować z serwerem aplikacji, aby ponownie uzyskać token JWT i uzyskać odnowiony token.

Zapoznaj [się](#restart_connection) z tematem jak ponownie uruchomić połączenia klientów.

## <a name="404-returned-for-client-requests"></a>Błąd 404 zwracany w przypadku żądań klientów

W przypadku połączenia trwałego sygnalizującego najpierw to `/negotiate` usługa Azure sygnalizująca, a następnie nawiązuje rzeczywiste połączenie z usługą Azure Signal Service.

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

* Poniżej przedstawiono [sposób wyświetlania żądań wychodzących](#view_request) w celu uzyskania żądania od klienta do usługi.
* Sprawdź adres URL żądania, gdy występuje 404. Jeśli adres URL jest przeznaczony dla aplikacji sieci Web, a podobny do `{your_web_app}/hubs/{hubName}` , sprawdź, czy klient `SkipNegotiation` jest `true` . W przypadku korzystania z usługi Azure Signal klient otrzymuje adres URL przekierowania, gdy najpierw negocjuje z serwerem aplikacji. Klient **nie** powinien pomijać negocjacji w przypadku korzystania z usługi Azure Signal.
* Kolejna 404 może wystąpić, gdy żądanie połączenia jest obsługiwane więcej niż **5** sekund po `/negotiate` wywołaniu. Sprawdź sygnaturę czasową żądania klienta i Otwórz do nas problem, jeśli żądanie do usługi ma powolne odpowiedzi.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 zwrócone dla żądania reconnecter ASP.NET

W przypadku sygnalizującego ASP.NET, gdy [połączenie z klientem zostanie porzucane](#client_connection_drop), ponownie nawiązuje połączenie przy użyciu tego samego `connectionId` przez trzy razy przed zatrzymaniem połączenia. `/reconnect` może pomóc w przypadku porzucenia połączenia z powodu sporadycznych problemów z siecią, które `/reconnect` mogą pomyślnie przywrócić trwałe połączenie. Na przykład połączenie z klientem zostało odrzucone z powodu porzucenia połączenia z serwerem kierowanym lub usługa sygnalizująca ma pewne błędy wewnętrzne, takie jak ponowne uruchomienie wystąpienia/przejście w tryb failover/wdrożenie, połączenie już nie istnieje, więc `/reconnect` zwraca wartość `404` . Jest to oczekiwane zachowanie dla `/reconnect` i po trzykrotnym ponownym uruchomieniu połączenia. Zalecamy stosowanie logiki [ponownego uruchamiania połączenia](#restart_connection) po zatrzymaniu połączenia.

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (zbyt wiele żądań) zostało zwróconych dla żądań klientów

429 zwraca wartość, jeśli liczba połączeń **współbieżnych** przekracza limit.

W przypadku **bezpłatnych** wystąpień limit liczby połączeń **współbieżnych** wynosi 20 dla wystąpień **standardowych** , limit liczby połączeń **współbieżnych** **na jednostkę** to 1 K, co oznacza, że Unit100 umożliwia współbieżne połączenia 100-K.

Połączenia obejmują zarówno połączenia klienta, jak i serwera. Sprawdź [,](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-messages-and-connections#how-connections-are-counted) czy są zliczane połączenia.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 błąd podczas negocjowania: usługa Azure Signal nie jest jeszcze połączona, spróbuj ponownie później.

### <a name="root-cause"></a>Główna przyczyna

Ten błąd jest zgłaszany, gdy nie ma połączenia z serwerem do usługi Azure sygnalizującej.

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Włącz śledzenie po stronie serwera, aby sprawdzić szczegóły błędu, gdy serwer próbuje nawiązać połączenie z usługą Azure Signal Service.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Włącz rejestrowanie po stronie serwera dla ASP.NET Core sygnalizującego

Rejestrowanie po stronie serwera dla sygnalizującego ASP.NET Core jest integrowane z `ILogger` [rejestrowaniem](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) na podstawie podanym w strukturze ASP.NET Core. Rejestrowanie po stronie serwera można włączyć za pomocą polecenia `ConfigureLogging` , korzystając z przykładowego użycia w następujący sposób:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Kategorie rejestratorów dla usługi Azure Signal zawsze zaczynają się od `Microsoft.Azure.SignalR` . Aby włączyć szczegółowe dzienniki z usługi Azure Signal, skonfiguruj poprzednie prefiksy do `Debug` poziomu w **appsettings.jsw** pliku podobnym do poniższego:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Włącz śledzenie po stronie serwera dla sygnalizującego ASP.NET

W przypadku korzystania z zestawu SDK >= `1.0.0` można włączyć ślady, dodając następujące polecenie do `web.config` : ([szczegóły](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>Porzucanie połączenia klienta

Gdy klient jest połączony z usługą Azure sygnalizująca, trwałe połączenie między klientem a usługą Azure sygnalizujące może czasami porzucić z różnych powodów. W tej sekcji opisano kilka możliwości spowodowanych tym połączeniem i przedstawiono wskazówki dotyczące sposobu identyfikowania głównej przyczyny.

### <a name="possible-errors-seen-from-the-client-side"></a>Możliwe błędy widoczne po stronie klienta

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Główna przyczyna:

Połączenia klienckie mogą być porzucane w różnych sytuacjach:
* `Hub`W przypadku zgłaszania wyjątków w żądaniu przychodzącym.
* Gdy połączenie z serwerem, do którego kierowany jest klient, spadnie, zobacz sekcję poniżej, aby uzyskać szczegółowe informacje na temat [porzucania połączeń z serwerem](#server_connection_drop).
* Gdy wystąpi problem z łącznością sieciową między usługą Klient i usługa sygnalizująca.
* Gdy usługa sygnalizująca zawiera pewne błędy wewnętrzne, takie jak ponowne uruchomienie wystąpienia, tryb failover, wdrożenie i tak dalej.

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

1. Otwórz dziennik po stronie serwera aplikacji, aby sprawdzić, czy nastąpiło coś nietypowego
2. Sprawdź dziennik zdarzeń po stronie serwera aplikacji, aby sprawdzić, czy serwer aplikacji został uruchomiony ponownie
3. Utwórz problem z podaniem przedziału czasu, a następnie Wyślij do nas wiadomość e-mail z nazwą zasobu


## <a name="client-connection-increases-constantly"></a>Ciągłe zwiększenie połączenia klienta

Przyczyną może być nieprawidłowe użycie połączenia z klientem. Jeśli ktoś zapomni zaprzestać/Dispose klienta sygnalizującego, połączenie pozostaje otwarte.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Możliwe błędy występujące na metrykach sygnalizujących znajdujących się w sekcji monitorowanie menu zasobów Azure Portal

Połączenia klienckie rosną przez długi czas w metrykach usługi Azure Signal.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Ciągłe zwiększenie połączenia klienta":::

### <a name="root-cause"></a>Główna przyczyna:

`DisposeAsync`Nigdy nie wywołano połączenia klienta sygnalizującego, połączenie pozostaje otwarte.

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

1. Sprawdź, czy klient sygnalizujący **nigdy nie** jest zamknięty.

### <a name="solution"></a>Rozwiązanie

Sprawdź, czy połączenie zostało zamknięte. Ręcznie Wywołaj `HubConnection.DisposeAsync()` , aby zatrzymać połączenie po jego użyciu.

Przykład:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Typowy niewłaściwy sposób użycia połączenia klienta

#### <a name="azure-function-example"></a>Przykład funkcji platformy Azure 

Ten problem występuje często, gdy ktoś nawiąże połączenie z klientem przy użyciu metody funkcji platformy Azure zamiast przydzielenia mu statyczną składową klasy funkcji. Można oczekiwać, że jest ustanowione tylko jedno połączenie klienta, ale liczba połączeń klienta stale rośnie w temacie metryki, które znajdują się w sekcji monitorowanie menu zasobów Azure Portal, wszystkie te połączenia są wyświetlane dopiero po ponownym uruchomieniu usługi Azure Function lub Azure Signaler. Jest to spowodowane tym, że dla **każdego** żądania funkcja platformy Azure tworzy **jedno** połączenie klienta, jeśli nie zatrzymasz połączenia klienta w metodzie funkcji, klient utrzymuje połączenia z usługą Azure Signal Service.

#### <a name="solution"></a>Rozwiązanie

* Pamiętaj, aby zamknąć połączenie z klientem, jeśli używasz klientów sygnalizujących w funkcji platformy Azure lub używasz klienta sygnalizującego jako pojedynczej.
* Zamiast korzystać z klientów sygnalizujących w funkcji platformy Azure, można utworzyć klientów sygnalizujących w innym miejscu i użyć [powiązań Azure Functions dla usługi Azure Signal Service](https://github.com/Azure/azure-functions-signalrservice-extension) do [negocjowania](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) klienta z usługą Azure signaler. Można również użyć powiązania do [wysyłania wiadomości](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Przykłady do negocjowania klienta i wysyłania komunikatów można znaleźć [tutaj](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples). Więcej informacji można znaleźć [tutaj](https://github.com/Azure/azure-functions-signalrservice-extension).
* W przypadku korzystania z klientów sygnalizujących w funkcji platformy Azure może istnieć lepsza architektura w danym scenariuszu. Sprawdź, czy zaprojektowano właściwą architekturę bezserwerową. Można odwołać się do [aplikacji bezserwerowych w czasie rzeczywistym za pomocą powiązań usługi sygnalizującego w Azure Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>Porzucanie połączenia z serwerem

Gdy serwer aplikacji zostanie uruchomiony w tle, zestaw Azure SDK zaczyna inicjować połączenia z serwerem do zdalnego sygnalizującego platformy Azure. Zgodnie z opisem w [części wewnętrznej usługi Azure sygnalizujący usługa](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)Azure Signal kieruje przychodzące ruch klientów do tych połączeń serwera. Po usunięciu połączenia z serwerem zostaną również zamknięte wszystkie połączenia klienta, które obsługuje.

Połączenia między serwerem aplikacji a usługą sygnalizującym są połączeniami trwałymi, ale mogą występować problemy z łącznością sieciową. W zestawie SDK serwera **zawsze należy ponownie połączyć** strategię z połączeniami z serwerem. Zgodnie z najlepszymi rozwiązaniami Zachęcamy również użytkowników do dodawania do klientów ciągłej logiki ponownego połączenia z losowym opóźnieniem, aby uniknąć ogromnych równoczesnych żądań do serwera.

W regularnych odstępach czasu dostępne są nowe wersje usługi Azure Signal Service, a czasami poprawki lub uaktualnienia systemu operacyjnego na poziomie platformy Azure lub sporadyczne zakłócenia z naszych usług zależnych. Może to spowodować przerwanie przerwy w działaniu usługi, ale jeśli po stronie klienta jest mechanizm rozłączenia/ponownego połączenia, wpływ jest minimalny, podobnie jak w przypadku wszystkich nieprzerwanych po stronie klienta.

W tej sekcji opisano kilka możliwości prowadzących do porzucenia połączenia z serwerem i przedstawiono wskazówki dotyczące sposobu identyfikowania głównej przyczyny.

### <a name="possible-errors-seen-from-server-side"></a>Możliwe błędy widoczne po stronie serwera:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Główna przyczyna:

Połączenie z usługą serwera zostało zamknięte przez **ASRS**(**Zure** **s** ignal **R** **s** nazwa).

### <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

1. Otwórz dziennik po stronie serwera aplikacji, aby sprawdzić, czy nastąpiło coś nietypowego
2. Sprawdź dziennik zdarzeń po stronie serwera aplikacji, aby sprawdzić, czy serwer aplikacji został uruchomiony ponownie
3. Utwórz problem z podaniem przedziału czasu, a następnie Wyślij do nas wiadomość e-mail z nazwą zasobu

## <a name="tips"></a>Porady

<a name="view_request"></a>

* Jak wyświetlić wychodzące żądanie z klienta?
Wykonaj ASP.NET Core jeden na przykład (ASP.NET jeden jest podobny):
    * Z przeglądarki:

        Zrób to na przykład, możesz użyć **klawisza F12** , aby otworzyć okno konsoli i przełączyć się na kartę **Sieć** . Może być konieczne odświeżenie strony przy użyciu klawisza **F5** w celu przechwycenia sieci z bardzo początku.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Przeglądarka Chrome View Network":::

    * Z poziomu klienta języka C#:

        Możesz wyświetlić lokalne ruch internetowy za pomocą [programu Fiddler](https://www.telerik.com/fiddler). Ruch WebSocket jest obsługiwany od programu Fiddler 4,5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Programu Fiddler Wyświetlanie sieci" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Jak ponownie uruchomić połączenie z klientem?
    
    Poniżej przedstawiono [przykładowe kody](https://github.com/Azure/azure-signalr/tree/dev/samples) zawierające logikę ponownego połączenia z *zawsze strategią ponawiania próby* :

    * [ASP.NET Core klienta C#](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [Klient ASP.NET Core JavaScript](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [Klient języka C# ASP.NET](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [Klient JavaScript ASP.NET](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku wyjaśniono, jak obsłużyć typowe problemy. Możesz również zapoznać się z bardziej ogólnymi metodami rozwiązywania problemów. 

> [!div class="nextstepaction"]
> [Jak rozwiązywać problemy z dostarczaniem łączności i komunikatów](./signalr-howto-troubleshoot-method.md)
