---
title: Rozwiązanie do rozwiązywania problemów z usługą Azure Signal Service
description: Informacje na temat rozwiązywania problemów z łącznością i dostarczaniem komunikatów
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: ed8775c6544791571746b0f3784a60ce2af0de7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726978"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Jak rozwiązywać problemy z dostarczaniem łączności i komunikatów

W tych wskazówkach przedstawiono kilka sposobów, które ułatwiają samodiagnostyki znalezienie głównej przyczyny lub zawężenie problemu. Wynik samodiagnostyki jest również przydatny podczas zgłaszania go nam do dalszej analizy.

Najpierw należy sprawdzić, czy Azure Portal, w którym [servicemode](./concept-service-mode.md) jest usługa Azure Signal (znana także jako **ASRS**).

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="Servicemode":::

* W przypadku `Default` trybu należy zapoznać się z [trybem domyślnym rozwiązywania problemów](#default_mode_tsg)

* W przypadku `Serverless` trybu, zobacz [Rozwiązywanie problemów z trybem bezserwerowym](#serverless_mode_tsg)

* Aby `Classic` zapoznać się z trybem, zobacz [Rozwiązywanie problemów w trybie klasycznym](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

[Masz problemy lub opinie na temat rozwiązywania problemów? Daj nam znać.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>Rozwiązywanie problemów z trybem domyślnym

Gdy **ASRS** jest w trybie *domyślnym* , istnieją **trzy** role: *Klient*, *serwer* i *Usługa*:

* *Klient*: *Klient* oznacza klientów podłączonych do usługi **ASRS**. Połączenia trwałe łączące się z klientem i **ASRS** są nazywane *połączeniami klienta* w tych wskazówkach.

* *Serwer*: *serwer* udostępnia serwer, który obsługuje negocjacje klienta i obsługuje `Hub` logikę sygnałów. A trwałe połączenia między *serwerem* i **ASRS** są nazywane *połączeniami serwera* w tych wskazówkach.

* *Usługa*: *Usługa* to krótka nazwa **ASRS** w tych wskazówkach.

Szczegółowe wprowadzenie całej architektury i przepływu pracy można znaleźć w tematach [wewnętrznych usługi Azure Signal Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Istnieje kilka sposobów, które mogą pomóc w zawężaniu problemu. 

* Jeśli problem będzie się powtarzać w sposób lub jest Odtwórz, prostym sposobem jest wyświetlenie ruchu. 

* Jeśli problem jest trudny do Odtwórz, dane śledzenia i dzienniki mogą pomóc.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Jak wyświetlić ruch i zawęzić problem

Przechwytywanie ruchu w toku to najbardziej prosty sposób na zawężenie problemu. [Ślady sieci](/aspnet/core/signalr/diagnostics#network-traces) można przechwytywać, korzystając z poniższych opcji:

* [Zbieranie danych śledzenia sieci za pomocą programu Fiddler](/aspnet/core/signalr/diagnostics#network-traces)

* [Zbieranie danych śledzenia sieci za pomocą tcpdump](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Zbieranie danych śledzenia sieci w przeglądarce](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Żądania klientów

W przypadku połączenia trwałego sygnalizującego należy najpierw znajdować się w `/negotiate` hostowanym serwerze aplikacji, a następnie przekierować do usługi Azure Signal, a następnie ustanowić rzeczywiste trwałe połączenie z usługą Azure Signal Service. Szczegółowe instrukcje znajdują się w sekcji [wewnętrzne usługi Azure Signal Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Korzystając z funkcji śledzenia sieci po stronie klienta, sprawdź, które żądanie nie powiedzie się z kodem stanu i odpowiedzią, i poszukaj rozwiązań w [przewodniku rozwiązywania problemów](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Żądania serwera

*Serwer* sygnalizujący utrzymuje *połączenie serwera* między *serwerem* a *usługą*. Po uruchomieniu serwera aplikacji uruchamia połączenie **protokołu WebSocket** z usługą Azure Signal Service. Wszystkie ruchy klienta są kierowane za pomocą usługi Azure Signal Service do tych *połączeń z serwerem*, a następnie wysyłane do usługi `Hub` . Gdy *połączenie z serwerem* spadnie, wpłynie to na klientów kierowanych do tego *połączenia z serwerem* . Nasz zestaw SDK usługi Azure Signal ma logikę "zawsze ponawianie", aby ponownie połączyć *połączenie z serwerem* z maksymalnie 1-minutowym opóźnieniem, aby zminimalizować wpływ.

*Połączenie serwera* s może porzucić ze względu na niestabilność sieci lub regularną konserwację usługi Azure sygnalizującej lub aktualizacje/konserwację hostowanego serwera aplikacji. Tak długo, jak po stronie klienta jest mechanizm rozłączania/ponownego łączenia, wpływ jest minimalny, podobnie jak w przypadku wszystkich operacji po stronie klienta spowodowanych rozłączeniem.

Wyświetl śledzenie sieci po stronie serwera, aby sprawdzić kod stanu i szczegóły błędu, dlaczego *połączenie z serwerem* zostało porzucone lub odrzucone przez *usługę*, i poszukaj głównej przyczyny w [przewodniku rozwiązywania problemów](./signalr-howto-troubleshoot-guide.md).

[Masz problemy lub opinie na temat rozwiązywania problemów? Daj nam znać.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Jak dodać dzienniki

Dzienniki mogą być przydatne do diagnozowania problemów i monitorowania stanu uruchomienia.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Jak włączyć dziennik po stronie klienta

Rejestrowanie po stronie klienta jest dokładnie takie samo, jak w przypadku korzystania z własnego sygnału.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Włącz rejestrowanie po stronie klienta dla programu `ASP.NET Core SignalR`

* [Rejestrowanie klientów JavaScript](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [Rejestrowanie klienta platformy .NET](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Włącz rejestrowanie po stronie klienta dla programu `ASP.NET SignalR`

* [Klient .NET](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Włączanie śledzenia w przypadku klientów z Windows Phone 8](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Włączanie śledzenia w kliencie JavaScript](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Jak włączyć dziennik po stronie serwera

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Włącz rejestrowanie po stronie serwera dla `ASP.NET Core SignalR`

Rejestrowanie po stronie serwera dla usługi `ASP.NET Core SignalR` integruje się `ILogger` z [rejestrowaniem](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&preserve-view=true&view=aspnetcore-2.1) opartym na systemie `ASP.NET Core` . Rejestrowanie po stronie serwera można włączyć za pomocą polecenia `ConfigureLogging` , korzystając z przykładowego użycia w następujący sposób:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Kategorie rejestratorów dla usługi Azure Signal zawsze zaczynają się od `Microsoft.Azure.SignalR` . Aby włączyć szczegółowe dzienniki z usługi Azure Signal, skonfiguruj poprzednie prefiksy do `Information` poziomu w **appsettings.jsw** pliku podobnym do poniższego:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Sprawdź, czy zarejestrowano nieprawidłowe dzienniki ostrzeżeń i błędów. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Włącz śledzenie po stronie serwera dla `ASP.NET SignalR`

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

Sprawdź, czy zarejestrowano nieprawidłowe dzienniki ostrzeżeń i błędów. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Jak włączyć dzienniki w usłudze Azure Signal Service

Możesz również [włączyć dzienniki diagnostyczne](./signalr-howto-diagnostic-logs.md) dla usługi Azure Signal, dzienniki te zawierają szczegółowe informacje o każdym połączeniu podłączonym do usługi Azure Signal.

<a name="serverless_mode_tsg"></a>

[Masz problemy lub opinie na temat rozwiązywania problemów? Daj nam znać.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Rozwiązywanie problemów z trybem bezserwerowym

Gdy **ASRS** jest w trybie *bezserwerowym* , tylko **ASP.NET Core sygnalizujący** obsługuje `Serverless` tryb, a **sygnalizujący ASP.NET** nie obsługuje tego trybu. 

Aby zdiagnozować problemy z łącznością w `Serverless` trybie, najbardziej prostym sposobem jest [wyświetlenie ruchu po stronie klienta](#view_traffic_client). Można także ułatwić korzystanie z [dzienników po stronie klienta](#add_logs_client) i [dzienników po stronie usług](#add_logs_server) .

<a name="classic_mode_tsg"></a>

[Masz problemy lub opinie na temat rozwiązywania problemów? Daj nam znać.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Rozwiązywanie problemów w trybie klasycznym

`Classic` tryb jest przestarzały i nie jest zachęcany do użycia. W tym trybie usługa Azure Signal Service używa *połączeń* połączonego serwera w celu ustalenia, czy bieżąca usługa znajduje się w `default` trybie czy w `serverless` trybie. Może to prowadzić do niektórych problemów z łącznością klienta, ponieważ w przypadku nagłego porzucenia wszystkich podłączonych *połączeń serwera*, na przykład ze względu na niestabilność sieci, usługa Azure sygnalizująca jest teraz przełączana do `serverless` trybu, a klienci połączeni w tym okresie nigdy nie będą kierowani do hostowanego serwera aplikacji. Włącz [dzienniki po stronie usługi](#add_logs_server) i sprawdź, czy istnieją jakieś Klienci zarejestrowani jako `ServerlessModeEntered` Jeśli masz serwer aplikacji hostowanej, jednak niektórzy klienci nigdy nie docierają po stronie serwera aplikacji. Jeśli istnieje, [Przerwij te połączenia klienckie](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) i pozwól, aby klienci mogli je ponownie uruchomić.

Rozwiązywanie problemów z `classic` łącznością i dostarczaniem komunikatów jest podobne do [rozwiązywania problemów z trybem domyślnym](#default_mode_tsg).

[Masz problemy lub opinie na temat rozwiązywania problemów? Daj nam znać.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Kondycja usługi

Można sprawdzić kondycję interfejsu API usługi kondycji.

* Żądanie: pobieranie `https://{instance_name}.service.signalr.net/api/v1/health`

* Kod stanu odpowiedzi:
  * 200: dobra kondycja.
  * 503: usługa jest w złej kondycji. Oto co możesz zrobić:
    * Zaczekaj kilka minut na Autoodzyskiwanie.
    * Sprawdź, czy adres IP jest taki sam, jak w przypadku adresu IP z portalu.
    * Lub Uruchom ponownie wystąpienie.
    * Jeśli wszystkie powyższe opcje nie działają, skontaktuj się z nami, dodając nowe żądanie obsługi w Azure Portal.

Więcej informacji na temat [odzyskiwania po awarii](./signalr-concept-disaster-recovery.md).

[Masz problemy lub opinie na temat rozwiązywania problemów? Daj nam znać.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku zawarto informacje na temat rozwiązywania problemów z łącznością i dostarczaniem komunikatów. Możesz również dowiedzieć się, jak obsłużyć typowe problemy. 

> [!div class="nextstepaction"]
> [Przewodnik rozwiązywania problemów](./signalr-howto-troubleshoot-guide.md)