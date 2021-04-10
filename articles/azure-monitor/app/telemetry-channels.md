---
title: Kanały telemetrii na platformie Azure Application Insights | Microsoft Docs
description: Jak dostosować kanały telemetryczne w zestawach SDK Application Insights platformy Azure dla platform .NET i .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937360"
---
# <a name="telemetry-channels-in-application-insights"></a>Kanały telemetrii w Application Insights

Kanały telemetrii są integralną częścią [zestawów sdk Application Insights platformy Azure](./app-insights-overview.md). Zarządzają buforowaniem i przesyłaniem danych telemetrycznych do usługi Application Insights. Wersje .NET i .NET Core zestawów SDK mają dwa wbudowane kanały telemetrii: `InMemoryChannel` i `ServerTelemetryChannel` . W tym artykule opisano szczegółowo poszczególne kanały, w tym sposób dostosowywania zachowań kanału.

## <a name="what-are-telemetry-channels"></a>Co to są kanały telemetrii?

Kanały telemetrii są odpowiedzialne za buforowanie elementów telemetrycznych i wysyłanie ich do usługi Application Insights, gdzie są one przechowywane na potrzeby wykonywania zapytań i analizy. Kanał telemetrii jest klasą, która implementuje [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) interfejs.

`Send(ITelemetry item)`Metoda kanału telemetrii jest wywoływana po wywołaniu wszystkich inicjatorów telemetrii i procesorów telemetrycznych. W związku z tym wszystkie elementy usunięte przez procesor telemetrii nie docierają do kanału. `Send()` Zazwyczaj elementy do zaplecza nie są wysyłane natychmiastowo. Zwykle buforuje je w pamięci i wysyła je w partiach w celu wydajnej transmisji.

[Live Metrics Stream](live-stream.md) ma także niestandardowy kanał, który umożliwia przesyłanie strumieniowe danych telemetrycznych na żywo. Ten kanał jest niezależny od zwykłego kanału telemetrii i nie ma do niego zastosowania.

## <a name="built-in-telemetry-channels"></a>Wbudowane kanały telemetrii

Zestawy SDK Application Insights .NET i .NET Core są dostarczane z dwoma wbudowanymi kanałami:

* `InMemoryChannel`: Lekki kanał, który buforuje elementy w pamięci do momentu ich wysłania. Elementy są buforowane w pamięci i opróżniane co 30 sekund lub za każdym razem, gdy 500 elementy są buforowane. Ten kanał oferuje minimalne gwarancje niezawodności, ponieważ nie próbuje wysłać danych telemetrycznych po awarii. Ten kanał nie zachowuje także elementów na dysku, więc wszystkie niewysłane elementy są trwale tracone po zamknięciu aplikacji (bezpieczne lub nie). Ten kanał implementuje `Flush()` metodę, która może być używana w celu synchronicznego opróżniania wszystkich elementów telemetrycznych w pamięci. Ten kanał jest dobrze dostosowany do krótkoterminowych aplikacji, w których synchroniczne opróżnianie jest idealne.

    Ten kanał jest częścią większego pakietu NuGet Microsoft. ApplicationInsights i jest domyślnym kanałem, którego używa zestaw SDK, gdy nic innego nie jest skonfigurowane.

* `ServerTelemetryChannel`: Bardziej zaawansowany kanał, który ma zasady ponawiania prób i możliwość przechowywania danych na dysku lokalnym. Ten kanał ponawia próbę wysłania danych telemetrycznych w przypadku wystąpienia błędów przejściowych. Ten kanał używa również lokalnego magazynu dyskowego do przechowywania elementów na dysku w czasie awarii sieci lub dużej ilości danych telemetrycznych. Ze względu na te mechanizmy ponawiania prób i magazyn na dysku lokalnym ten kanał jest uznawany za bardziej niezawodny i jest zalecany we wszystkich scenariuszach produkcyjnych. Ten kanał jest wartością domyślną dla aplikacji [ASP.NET](./asp-net.md) i [ASP.NET Core](./asp-net-core.md) skonfigurowanych zgodnie z oficjalną dokumentacją. Ten kanał jest zoptymalizowany pod kątem scenariuszy serwera z długotrwałymi procesami. [`Flush()`](#which-channel-should-i-use)Metoda zaimplementowana przez ten kanał nie jest synchroniczna.

    Ten kanał jest dostarczany jako pakiet NuGet Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel i zostaje automatycznie pobrany w przypadku korzystania z pakietu NuGet Microsoft. ApplicationInsights. Web lub Microsoft. ApplicationInsights. AspNetCore.

## <a name="configure-a-telemetry-channel"></a>Konfigurowanie kanału telemetrii

Kanał telemetrii można skonfigurować przez ustawienie aktywnej konfiguracji telemetrii. W przypadku aplikacji ASP.NET konfiguracja wymaga ustawienia wystąpienia kanału telemetrii na `TelemetryConfiguration.Active` lub przez modyfikację `ApplicationInsights.config` . W przypadku aplikacji ASP.NET Core konfiguracja obejmuje dodanie kanału do kontenera iniekcji zależności.

W poniższych sekcjach przedstawiono przykłady konfigurowania `StorageFolder` Ustawienia dla kanału w różnych typach aplikacji. `StorageFolder` jest tylko jednym z konfigurowalnych ustawień. Aby zapoznać się z pełną listą ustawień konfiguracji, zobacz [sekcję Ustawienia](#configurable-settings-in-channels) w dalszej części tego artykułu.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguracja przy użyciu ApplicationInsights.config aplikacji ASP.NET

W poniższej sekcji w [ApplicationInsights.config](configuration-with-applicationinsights-config.md) przedstawiono `ServerTelemetryChannel` kanał skonfigurowany z `StorageFolder` ustawioną lokalizacją niestandardową:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguracja w kodzie dla aplikacji ASP.NET

Poniższy kod konfiguruje wystąpienie "ServerTelemetryChannel" z `StorageFolder` ustawioną jako lokalizacją niestandardową. Dodaj ten kod na początku aplikacji, zazwyczaj w `Application_Start()` metodzie w Global. aspx. cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguracja w kodzie dla aplikacji ASP.NET Core

Zmodyfikuj `ConfigureServices` metodę `Startup.cs` klasy, jak pokazano poniżej:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Konfigurowanie kanału przy użyciu `TelemetryConfiguration.Active` nie jest zalecane w przypadku aplikacji ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguracja w kodzie dla aplikacji konsoli .NET/.NET Core

W przypadku aplikacji konsolowych kod jest taki sam dla programów .NET i .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Szczegóły operacyjne elementu ServerTelemetryChannel

`ServerTelemetryChannel` przechowuje elementy w buforze w pamięci. Elementy są serializowane, kompresowane i przechowywane w `Transmission` wystąpieniu co 30 sekund lub w przypadku buforowania elementów 500. Pojedyncze `Transmission` wystąpienie zawiera maksymalnie 500 elementów i reprezentuje partię danych telemetrycznych wysyłanych przez pojedyncze wywołanie HTTPS do usługi Application Insights.

Domyślnie maksymalnie 10 `Transmission` wystąpień może być wysyłanych równolegle. Jeśli dane telemetryczne docierają przy szybszych szybkościach lub w przypadku powolnej sieci lub Application Insights zaplecza, `Transmission` wystąpienia są przechowywane w pamięci. Domyślna pojemność tego buforu w pamięci `Transmission` wynosi 5 MB. Po przekroczeniu pojemności w pamięci `Transmission` wystąpienia są przechowywane na dysku lokalnym do limitu 50 MB. `Transmission` wystąpienia są przechowywane na dysku lokalnym, również w przypadku problemów z siecią. Awaria aplikacji dotyczy tylko tych elementów, które są przechowywane na dysku lokalnym. Są one wysyłane za każdym razem, gdy aplikacja zostanie uruchomiona ponownie.

## <a name="configurable-settings-in-channels"></a>Ustawienia konfigurowalne w kanałach

Aby uzyskać pełną listę konfigurowalnych ustawień dla każdego kanału, zobacz:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Poniżej przedstawiono najczęściej używane ustawienia programu `ServerTelemetryChannel` :

1. `MaxTransmissionBufferCapacity`: Maksymalna ilość pamięci (w bajtach) używanej przez kanał do buforowania przesyłania w pamięci. Po osiągnięciu tej pojemności nowe elementy są przechowywane bezpośrednio na dysku lokalnym. Wartość domyślna to 5 MB. Ustawienie wyższej wartości powoduje mniejsze użycie dysku, ale należy pamiętać, że w przypadku awarii aplikacji elementy w pamięci zostaną utracone.

1. `MaxTransmissionSenderCapacity`: Maksymalna liczba `Transmission` wystąpień, które będą wysyłane do Application Insights w tym samym czasie. Wartość domyślna to 10. To ustawienie można skonfigurować na wyższą liczbę, która jest zalecana w przypadku generowania dużej ilości danych telemetrycznych. Wysoki wolumin zwykle występuje podczas testowania obciążenia lub gdy próbkowanie jest wyłączone.

1. `StorageFolder`: Folder używany przez kanał do przechowywania elementów na dysk w razie potrzeby. W systemie Windows jest używana wartość% LOCALAPPDATA% lub% TEMP%, jeśli nie określono jawnie żadnej innej ścieżki. W środowiskach innych niż Windows należy określić prawidłową lokalizację lub dane telemetryczne nie będą przechowywane na dysku lokalnym.

## <a name="which-channel-should-i-use"></a>Którego kanału należy użyć?

`ServerTelemetryChannel` jest zalecany w przypadku większości scenariuszy produkcyjnych obejmujących długotrwałe aplikacje. `Flush()`Metoda zaimplementowana przez `ServerTelemetryChannel` nie jest synchroniczna i nie gwarantuje wysyłania wszystkich oczekujących elementów z pamięci lub dysku. Jeśli ten kanał jest używany w scenariuszach, w których aplikacja zostanie ZAMKNIĘTA, zalecamy wprowadzenie pewnej opóźnienia po wywołaniu `Flush()` . Dokładna ilość opóźnienia, która może być wymagana, nie jest przewidywalna. Jest to zależne od czynników, takich jak liczba elementów lub `Transmission` wystąpień w pamięci, liczba przechowywanych na dysku, liczba danych przesyłanych do zaplecza, a także to, czy kanał jest w trakcie wykładniczych scenariuszy wycofywania.

Jeśli musisz wykonać synchroniczne opróżnianie, zalecamy korzystanie z programu `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Czy Application Insights kanał gwarantuje dostarczenie telemetrii? Jeśli nie, jakie są scenariusze, w których można utracić dane telemetryczne?

Krótka odpowiedź polega na tym, że żaden z wbudowanych kanałów nie oferuje gwarancji typu transakcji dostarczania danych telemetrycznych do zaplecza. `ServerTelemetryChannel` jest bardziej zaawansowana w porównaniu `InMemoryChannel` do niezawodnego dostarczania, ale zapewnia także najlepszą próbę wysłania telemetrii. Dane telemetryczne mogą być nadal tracone w kilku sytuacjach, łącznie z następującymi typowymi scenariuszami:

1. Elementy w pamięci są tracone po awarii aplikacji.

1. Dane telemetryczne są tracone w trakcie dłuższych okresów problemów z siecią. Dane telemetryczne są przechowywane na dysku lokalnym podczas awarii sieci lub w przypadku wystąpienia problemów z zapleczem Application Insights. Jednak elementy starsze niż 48 godzin są odrzucane.

1. Domyślne lokalizacje dysków do przechowywania danych telemetrycznych w systemie Windows to% LOCALAPPDATA% lub% TEMP%. Te lokalizacje są zwykle lokalne dla komputera. Jeśli aplikacja jest migrowana fizycznie z jednej lokalizacji do innej, wszystkie dane telemetryczne przechowywane w oryginalnej lokalizacji zostaną utracone.

1. W Web Apps w systemie Windows domyślną lokalizacją przechowywania na dysku jest D:\local\LocalAppData. Ta lokalizacja nie jest utrwalona. Jest wyczyszczone w ramach ponownych uruchomień aplikacji, skalowania i innych operacji, co prowadzi do utraty wszelkich przechowywanych danych telemetrycznych. Można przesłonić domyślne i określić magazyn do utrwalonej lokalizacji, takiej jak D:\home. Takie utrwalone lokalizacje są jednak obsługiwane przez Magazyn zdalny i dlatego mogą być powolne.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Czy ServerTelemetryChannel pracuje z systemami innymi niż Windows?

Chociaż nazwa pakietu i przestrzeni nazw zawiera "WindowsServer", ten kanał jest obsługiwany w systemach innych niż Windows, z wyjątkiem. W systemach innych niż Windows kanał nie domyślnie tworzy lokalnego folderu magazynu. Należy utworzyć lokalny folder magazynu i skonfigurować kanał do korzystania z niego. Po skonfigurowaniu magazynu lokalnego kanał działa tak samo jak w przypadku wszystkich systemów.

> [!NOTE]
> W przypadku wersji 2.15.0-beta3 i nowszej magazyn lokalny jest teraz automatycznie tworzony dla systemów Linux, Mac i Windows. W przypadku systemów innych niż Windows zestaw SDK automatycznie utworzy lokalny folder magazynu na podstawie następującej logiki:
> - `${TMPDIR}` -Jeśli `${TMPDIR}` zmienna środowiskowa jest ustawiona, Ta lokalizacja jest używana.
> - `/var/tmp` — Jeśli poprzednia lokalizacja nie istnieje, spróbuj ponownie `/var/tmp` .
> - `/tmp` — Jeśli obie poprzednie lokalizacje nie istnieją, spróbujmy `tmp` . 
> - Jeśli żadna z tych lokalizacji nie istnieje, Magazyn lokalny nie jest tworzony, a Konfiguracja ręczna jest nadal wymagana. [Aby uzyskać pełne szczegóły implementacji](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Czy zestaw SDK tworzy tymczasowy magazyn lokalny? Czy dane są szyfrowane w magazynie?

Zestaw SDK przechowuje elementy telemetrii w lokalnym magazynie podczas rozwiązywania problemów z siecią lub podczas ograniczania. Te dane nie są szyfrowane lokalnie.

W przypadku systemów Windows zestaw SDK automatycznie tworzy tymczasowy folder lokalny w katalogu% TEMP% lub% LOCALAPPDATA% i ogranicza dostęp do administratorów i tylko do bieżącego użytkownika.

W przypadku systemów innych niż Windows żaden magazyn lokalny nie jest tworzony automatycznie przez zestaw SDK i dlatego żadne dane nie są domyślnie przechowywane lokalnie.

> [!NOTE]
> W przypadku wersji 2.15.0-beta3 i nowszej magazyn lokalny jest teraz automatycznie tworzony dla systemów Linux, Mac i Windows. 

 Możesz samodzielnie utworzyć katalog magazynu i skonfigurować kanał, aby go używać. W takim przypadku użytkownik jest odpowiedzialny za zapewnienie, że katalog jest zabezpieczony.
Przeczytaj więcej na temat [ochrony i prywatności danych](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Zestaw SDK open source
Podobnie jak w przypadku każdego zestawu SDK dla Application Insights, kanały to typu open source. Zapoznaj się z kodem lub zgłoś problemy w [oficjalnym repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](./sampling.md)
* [Rozwiązywanie problemów dotyczących zestawu SDK](./asp-net-troubleshoot-no-data.md)

