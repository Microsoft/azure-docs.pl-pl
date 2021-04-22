---
title: Opracowywanie za pomocą ASP.NET — Azure SignalR Service
description: Przewodnik Szybki start z Azure SignalR Service tworzenia pokoju rozmów przy użyciu ASP.NET framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 0ac17de3f73424994fc39ef0044d17ef83b501b7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866147"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Szybki start: tworzenie pokoju rozmów przy użyciu ASP.NET i SignalR Service

Azure SignalR Service jest oparta na uciece SignalR dla ASP.NET [Core 2.1,](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)który nie jest **w** 100% zgodny z ASP.NET SignalR. Azure SignalR Service ponownie zaimplementowane ASP.NET danych SignalR na podstawie najnowszych technologii ASP.NET Core. W przypadku korzystania z usługi Azure SignalR Service dla usługi ASP.NET SignalR niektóre funkcje usługi ASP.NET SignalR nie są już obsługiwane, na przykład usługa Azure SignalR nie odtwarza komunikatów po ponownym nawiązaniu połączenia z klientem. Ponadto transport Forever Frame i JSONP nie są obsługiwane. Niektóre zmiany kodu i odpowiednia wersja bibliotek zależnych są potrzebne do ASP.NET aplikacji SignalR z SignalR Service.

Zapoznaj się z dokumentów [różnicami wersji,](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) aby uzyskać pełną listę porównań funkcji między ASP.NET SignalR i ASP.NET Core SignalR.

W tym przewodniku Szybki start dowiesz się, jak rozpocząć pracę z ASP.NET i Azure SignalR Service podobnej aplikacji [pokoju rozmów.](./signalr-quickstart-dotnet-core.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET Framework 4.6.1](https://dotnet.microsoft.com/download/dotnet-framework/net461)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Tryb bez serwera* nie jest obsługiwany w przypadku ASP.NET SignalR. Zawsze używaj *wartości domyślnej* *lub* klasycznej Azure SignalR Service wystąpienia.

Zasoby platformy Azure używane w tym przewodniku Szybki start można również utworzyć za [pomocą SignalR Service skryptu](scripts/signalr-cli-create-service.md).

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsnet).

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W czasie, gdy usługa jest wdrażana, przejdźmy do pracy z kodem. Sklonuj [przykładową aplikację z witryny GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ustaw parametry połączenia usługi SignalR Service i uruchom aplikację lokalnie.

1. Otwórz okno terminala usługi Git. Przejdź do folderu, w którym chcesz sklonować przykładowy projekt.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsnet).

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurowanie i uruchamianie aplikacji internetowej pokoju rozmów

1. Uruchom Visual Studio i otwórz rozwiązanie w folderze *aspnet-samples/ChatRoom/* sklonowanego repozytorium.

1. W przeglądarce, w Azure Portal, znajdź i wybierz utworzone wystąpienie.

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

1. Teraz ustaw parametrów połączenia w *web.config* pliku.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. W *przypadku plików Startup.cs* zamiast wywoływania funkcji należy wywołać i przekazać ciąg połączenia, aby aplikacja łączyła się z usługą zamiast `MapSignalR()` `MapAzureSignalR({YourApplicationName})` hostować usługę SignalR samodzielnie. Zastąp `{YourApplicationName}` nazwą swojej aplikacji. Ta nazwa jest unikatową nazwą odróżniającą tę aplikację od innych aplikacji. Możesz użyć `this.GetType().FullName` wartości .

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Należy również odwołać się do zestawu SDK usługi przed użyciem tych interfejsów API. Otwórz okno **narzędzia | NuGet Menedżer pakietów | Menedżer pakietów konsoli i** uruchom polecenie:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Poza tymi zmianami wszystkie inne dane pozostają takie same. Do pisania logiki biznesowej można nadal używać znanego interfejsu centrum.

    > [!NOTE]
    > W implementacji punkt końcowy jest `/signalr/negotiate` ujawniony do negocjacji przez Azure SignalR Service SDK. Zostanie zwrócona specjalna odpowiedź negocjacji, gdy klienci spróbują nawiązać połączenie i przekierować klientów do punktu końcowego usługi zdefiniowanego w parametrach połączenia.

1. Naciśnij <kbd>klawisz F5,</kbd> aby uruchomić projekt w trybie debugowania. Możesz zobaczyć, że aplikacja działa lokalnie. Zamiast hostować środowisko uruchomieniowe SignalR przez aplikację, teraz łączy się z Azure SignalR Service.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

![Usuń](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy zasób Azure SignalR Service, który był używany z aplikacją ASP.NET internetową. Następnie dowiesz się, jak opracowywać aplikacje w czasie rzeczywistym przy użyciu Azure SignalR Service z ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR Service z programem ASP.NET Core](./signalr-quickstart-dotnet-core.md)
