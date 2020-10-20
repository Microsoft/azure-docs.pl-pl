---
title: Sesje usługi Remote Rendering
description: Opisuje, co jest zdalna sesja renderowania
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 8f2adc846247c4f06c9356f482501fd01c5463bf
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202688"
---
# <a name="remote-rendering-sessions"></a>Sesje usługi Remote Rendering

W przypadku renderowania zdalnego (ARR) na platformie Azure *sesja* jest najważniejszym pojęciem. W tym artykule wyjaśniono, co dokładnie jest sesja.

## <a name="overview"></a>Omówienie

Zdalne renderowanie na platformie Azure działa przez przeciążanie złożonych zadań renderowania do chmury. Te zadania renderowania nie mogą być spełnione przez tylko każdy serwer, ponieważ większość serwerów w chmurze nie ma procesorów GPU. Ze względu na ilość danych, których to dotyczy, oraz twardą wymaganą możliwość tworzenia wyników w oparciu o interaktywne szybkości klatek, odpowiedzialność, która z nich nie może być przekazana na inny komputer w locie, jak może być bardziej typowym ruchem w sieci Web.

Oznacza to, że w przypadku korzystania z zdalnego renderowania platformy Azure serwer w chmurze z wymaganymi możliwościami sprzętowymi musi być zarezerwowany wyłącznie do obsługi żądań renderowania. *Sesja* odnosi się do wszystkich elementów powiązanych z tym serwerem. Rozpoczyna się od wstępnego żądania zarezerwowania (*dzierżawy*) maszyny do użycia, program kontynuuje wszystkie polecenia do ładowania i manipulowania modelami oraz zakończy się zwalnianiem dzierżawy na serwerze w chmurze.

## <a name="managing-sessions"></a>Zarządzanie sesjami

Istnieje wiele sposobów, aby zarządzać sesjami i korzystać z nich. Niezależny od języka sposób tworzenia, aktualizowania i zamykania sesji odbywa się za pomocą [interfejsu API REST zarządzania sesją](../how-tos/session-rest-api.md). W językach C# i C++ te operacje są ujawniane za pomocą klas `AzureFrontend` i `AzureSession` . W przypadku aplikacji aparatu Unity dostępne są dalsze funkcje narzędziowe udostępniane przez `ARRServiceUnity` składnik.

Po *nawiązaniu połączenia* z aktywną sesją operacje, takie jak [ładowanie modeli](models.md) i współdziałanie z sceną, są ujawniane za pomocą `AzureSession` klasy.

### <a name="managing-multiple-sessions-simultaneously"></a>Jednoczesne zarządzanie wieloma sesjami

Nie można w pełni *łączyć* się z wieloma sesjami z jednego urządzenia. Można jednak tworzyć, obserwować i zamykać dowolną liczbę sesji z pojedynczej aplikacji. Tak długo, jak aplikacja nie jest przeznaczona do łączenia się z sesją, nie musi działać na urządzeniu, takim jak HoloLens 2. Przypadek użycia dla takiej implementacji może być, jeśli chcesz sterować sesjami za pomocą mechanizmu centralnego. Na przykład jedna z nich może utworzyć aplikację sieci Web, w której może się zalogować wiele tabletów i urządzeń HoloLens. Następnie aplikacja może wyświetlać opcje na tabletach, takie jak model CAD, który ma być wyświetlany. Jeśli użytkownik dokona wyboru, te informacje są przekazywane do wszystkich urządzeń HoloLens, aby utworzyć środowisko udostępnione.

## <a name="session-phases"></a>Etapy sesji

Każda sesja jest poddawana wielu etapom.

### <a name="session-startup"></a>Uruchamianie sesji

Po zaproszeniu ARR w celu [utworzenia nowej sesji](../how-tos/session-rest-api.md#create-a-session)pierwszy z nich ma zwrócić [identyfikator UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)sesji. Ten identyfikator UUID umożliwia wykonywanie zapytań dotyczących informacji o sesji. Identyfikator UUID i niektóre podstawowe informacje o sesji są utrwalane przez 30 dni, aby można było wysyłać zapytania do tych informacji nawet po zatrzymaniu sesji. W tym momencie **stan sesji** będzie raportowany jako **uruchamiany**.

Następnie zdalne renderowanie na platformie Azure próbuje znaleźć serwer, który może hostować daną sesję. Dla tego wyszukiwania istnieją dwa parametry. Po pierwsze spowoduje to zarezerwowanie serwerów w Twoim [regionie](../reference/regions.md). Wynika to z faktu, że opóźnienie sieci między regionami może być zbyt wysokie, aby zapewnić znośnego środowisko pracy. Drugim czynnikiem jest żądany *rozmiar* określony przez użytkownika. W każdym regionie istnieje ograniczona liczba serwerów, które mogą spełnić żądanie rozmiaru [*standardowego*](../reference/vm-sizes.md) lub [*Premium*](../reference/vm-sizes.md) . W związku z tym, jeśli wszystkie serwery o żądanym rozmiarze są obecnie używane w Twoim regionie, Tworzenie sesji zakończy się niepowodzeniem. Przyczyną niepowodzenia [może być zapytanie](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Jeśli zażądasz *standardowego* rozmiaru serwera i żądanie zakończy się niepowodzeniem z powodu wysokiego zapotrzebowania, nie oznacza to, że żądanie serwera w *warstwie Premium* nie powiedzie się. Dlatego jeśli jest to opcja dla Ciebie, możesz spróbować wrócić do rozmiaru serwera w *warstwie Premium* .

Gdy usługa odnajdzie odpowiedni serwer, musi skopiować do niego odpowiednią maszynę wirtualną, aby przekształcić ją w Host renderowania zdalnego platformy Azure. Ten proces trwa kilka minut. Następnie maszyna wirtualna jest uruchamiana i przechodzi **stan sesji** na **gotowe**.

W tym momencie serwer jest w stanie oczekiwać wyłącznie na dane wejściowe. Jest to również punkt, z którego naliczane są opłaty za usługę.

### <a name="connecting-to-a-session"></a>Nawiązywanie połączenia z sesją

Gdy sesja będzie *gotowa*, możesz *nawiązać z nią połączenie* . Po nawiązaniu połączenia urządzenie może wysyłać polecenia w celu załadowania i zmodyfikowania modeli. Każdy host ARR obsługuje tylko jedno urządzenie klienckie w danym momencie, więc gdy klient nawiązuje połączenie z sesją, ma wyłączną kontrolę nad renderowaną zawartością. Oznacza to również, że wydajność renderowania nigdy nie będzie się różnić z powodów poza formantem.

> [!IMPORTANT]
> Chociaż tylko jeden klient może *łączyć* się z sesją, podstawowe informacje o sesjach, takie jak ich bieżący stan, mogą być badane bez łączenia.

Gdy urządzenie jest połączone z sesją, próby nawiązania połączenia przez inne urządzenia zakończą się niepowodzeniem. Jednak po rozłączeniu urządzenia połączonego, a także dobrowolnie lub z powodu niepowodzenia, sesja zaakceptuje inne żądanie połączenia. Wszystkie poprzednie Stany (załadowane modele i takie) są odrzucane w taki sposób, że następne łączące się urządzenie uzyskuje czysty. W związku z tym sesje mogą być ponownie używane wielokrotnie, przez różne urządzenia i może być możliwe ukrycie obciążeń uruchamiania sesji od użytkownika końcowego w większości przypadków.

> [!IMPORTANT]
> Serwer zdalny nigdy nie zmienia stanu danych po stronie klienta. Wszystkie mutacje danych (takich jak aktualizacje transformacji i żądania załadowania) muszą być wykonywane przez aplikację kliencką. Wszystkie akcje natychmiast aktualizują stan klienta.

### <a name="session-end"></a>Zakończenie sesji

W przypadku żądania nowej sesji należy określić *Maksymalny czas dzierżawy*, zazwyczaj w zakresie od 1 do ośmiu godzin. Jest to czas, przez który host akceptuje dane wejściowe.

Istnieją dwa zwykłe powody, aby sesja była zakończona. Ręcznie Żądaj, aby sesja została zatrzymana lub maksymalny czas dzierżawy wygaśnie. W obu przypadkach wszystkie aktywne połączenia z hostem są od razu zamknięte, a usługa zostaje wyłączona na tym serwerze. Serwer jest następnie podawany do puli platformy Azure i może zostać zamierzony do innych celów. Zatrzymywanie sesji nie może zostać cofnięte lub anulowane. Wykonanie zapytania o **stan sesji** w zatrzymanej sesji zwróci wartość **zatrzymaną** lub **wygasłą**, w zależności od tego, czy został on ręcznie zamknięty, czy został osiągnięty maksymalny czas dzierżawy.

Sesja może być również zatrzymana z powodu pewnego błędu.

We wszystkich przypadkach nie będą naliczane dalsze opłaty po zatrzymaniu sesji.

> [!WARNING]
> Bez względu na to, czy nawiązujesz połączenie z sesją, jak długo nie wpływają na rozliczenia. Opłaty za usługę są zależne od *czasu trwania sesji*, czyli czasu, przez który serwer jest zarezerwowany wyłącznie dla Ciebie, i żądanych możliwości sprzętu ( [przydzielonego rozmiaru](../reference/vm-sizes.md)). Jeśli uruchomisz sesję, Połącz się przez pięć minut, a następnie nie zatrzymasz sesji, tak aby nadal działała do momentu wygaśnięcia dzierżawy, zostanie naliczona stawka za pełny czas dzierżawy sesji. Z drugiej strony *Maksymalny czas dzierżawy* jest przede wszystkim w sieci zabezpieczeń. Nie ma znaczenia, czy użytkownik żąda sesji z upływem ośmiu godzin dzierżawy, a następnie będzie go używać tylko przez pięć minut, w przypadku ręcznego zatrzymania sesji.

#### <a name="extend-a-sessions-lease-time"></a>Zwiększ czas dzierżawy sesji

Możesz [wydłużyć czas dzierżawy](../how-tos/session-rest-api.md#modify-and-query-session-properties) aktywnej sesji, jeśli okaże się to konieczne dłużej.

## <a name="example-code"></a>Przykładowy kod

W poniższym kodzie przedstawiono prostą implementację uruchomienia sesji, która oczekuje na stan *gotowości* , połączenie, a następnie ponowne połączenie i wyłączenie.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

`AzureFrontend` `AzureSession` Można utrzymywać wiele wystąpień, manipulować nimi i wykonywać zapytania z kodu. Ale tylko jedno urządzenie może połączyć się `AzureSession` jednocześnie.

Okres istnienia maszyny wirtualnej nie jest powiązany z `AzureFrontend` wystąpieniem ani `AzureSession` wystąpieniem. `AzureSession.StopAsync` musi być wywołana, aby zatrzymać sesję.

W przypadku identyfikatora sesji trwałej można wykonywać zapytania `AzureSession.SessionUUID()` i buforować lokalnie. Przy użyciu tego identyfikatora aplikacja może wywołać `AzureFrontend.OpenSession` powiązanie z tą sesją.

Gdy `AzureSession.IsConnected` ma wartość true, `AzureSession.Actions` zwraca wystąpienie `RemoteManager` , które zawiera funkcje do [ładowania modeli](models.md), manipulowania [jednostkami](entities.md)i [zapytania o informacje](../overview/features/spatial-queries.md) o renderowanej scenie.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa AzureSession języka C#](/dotnet/api/microsoft.azure.remoterendering.azuresession)
* [C# AzureFrontend. CreateNewRenderingSessionAsync ()](/dotnet/api/microsoft.azure.remoterendering.azurefrontend.createnewrenderingsessionasync)
* [C# AzureFrontend. OpenRenderingSession ()](/dotnet/api/microsoft.azure.remoterendering.azurefrontend.openrenderingsession)
* [Klasa C++ AzureSession](/cpp/api/remote-rendering/azuresession)
* [C++ AzureFrontend:: CreateNewRenderingSessionAsync](/cpp/api/remote-rendering/azurefrontend#createnewrenderingsessionasync)
* [C++ AzureFrontend:: OpenRenderingSession](/cpp/api/remote-rendering/azurefrontend#openrenderingsession)

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Modele](models.md)