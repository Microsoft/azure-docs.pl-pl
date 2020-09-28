---
title: Programowanie za pomocą usługi Azure Signal ASP.NET
description: Przewodnik Szybki Start dotyczący używania usługi Azure Signal Service do tworzenia pokoju rozmów z platformą ASP.NET Framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 8c7d7f84e02cdd2cd4f53bc9eb42c78f936146ae
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408373"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Szybki Start: Tworzenie pokoju rozmów przy użyciu usługi ASP.NET i sygnalizującego

Usługa Azure Signal based jest oparta na [sygnale dla ASP.NET Core 2,1](https://docs.microsoft.com/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), który **nie** jest zgodny z 100% w przypadku sygnalizowania ASP.NET. Usługa sygnałów platformy Azure zaimplementował ASP.NET protokół danych sygnalizujący na podstawie najnowszych technologii ASP.NET Core. W przypadku korzystania z usługi Azure Signal Service dla sygnalizującego ASP.NET niektóre funkcje sygnałów ASP.NET nie są już obsługiwane, na przykład usługa Azure sygnalizująca nie powtarza komunikatów po ponownym nawiązaniu połączenia przez klienta. Ponadto nie są obsługiwane stałe transport ramek i JSONP. Niektóre zmiany kodu i poprawna wersja bibliotek zależnych są konieczne, aby aplikacja sygnalizująca ASP.NET działała z usługą sygnalizującej.

Zapoznaj się z [dokumentem różnice wersji](https://docs.microsoft.com/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) , aby zapoznać się z pełną listą porównania funkcji między sygnałem ASP.NET ASP.NET Core i modułem sygnalizującym.

W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z usługą ASP.NET i platformą Azure dla podobnej [aplikacji do pokoju rozmów](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET sygnalizujący](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Tryb *bezserwerowy* nie jest obsługiwany w przypadku aplikacji sygnalizujących ASP.NET. Zawsze używaj *domyślnego* lub *klasycznego* wystąpienia usługi Azure Signal Service.

Możesz również utworzyć zasoby platformy Azure używane w tym przewodniku Szybki Start i [utworzyć skrypt usługi sygnalizującej](scripts/signalr-cli-create-service.md).

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W czasie, gdy usługa jest wdrażana, przejdźmy do pracy z kodem. Sklonuj [przykładową aplikację z witryny GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ustaw parametry połączenia usługi SignalR Service i uruchom aplikację lokalnie.

1. Otwórz okno terminala usługi Git. Przejdź do folderu, w którym chcesz sklonować przykładowy projekt.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurowanie i uruchamianie aplikacji sieci Web pokoju rozmów

1. Uruchom program Visual Studio i Otwórz rozwiązanie w polu *ASPNET-Samples/ChatRoom/* folder sklonowanego repozytorium.

1. W przeglądarce, w której Azure Portal jest otwarty, Znajdź i wybierz utworzone wystąpienie.

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

1. Teraz ustaw parametry połączenia w pliku *web.config* .

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. W *Startup.cs*, zamiast wywoływania `MapSignalR()` , należy wywołać `MapAzureSignalR({YourApplicationName})` i przekazać parametry połączenia, aby aplikacja łączyła się z usługą zamiast sygnałów hostingowych. Zamień `{YourApplicationName}` na nazwę aplikacji. Ta nazwa jest unikatową nazwą odróżniającą tę aplikację od innych aplikacji. Można użyć `this.GetType().FullName` jako wartości.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Przed użyciem tych interfejsów API należy również odwołać się do zestawu SDK usługi. Otwórz **Narzędzia | Menedżer pakietów NuGet | Konsola Menedżera pakietów** i polecenie uruchamiania:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Inne niż te zmiany nie są takie same, ale nadal możesz użyć interfejsu centrum, który znasz już, aby napisać logikę biznesową.

    > [!NOTE]
    > W implementacji punkt końcowy `/signalr/negotiate` jest narażony na potrzeby negocjacji przez zestaw SDK usługi Azure Signal Service. Zostanie zwrócona specjalna odpowiedź dotycząca negocjacji, gdy klienci próbują nawiązać połączenie i przekierować klientów do punktu końcowego usługi zdefiniowanego w parametrach połączenia.

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt w trybie debugowania. Aplikacja jest uruchamiana lokalnie. Zamiast hostować środowisko uruchomieniowe sygnalizujące przez samą aplikację, teraz nawiązuje połączenie z usługą Azure Signal.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

![Usuń](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy zasób usługi Azure Signal Service i użył go z aplikacją sieci Web ASP.NET. Następnie Dowiedz się, jak opracowywać aplikacje w czasie rzeczywistym przy użyciu usługi Azure Signal Service z ASP.NET Core.

> [!div class="nextstepaction"]
> [Usługa sygnałów platformy Azure z ASP.NET Core](./signalr-quickstart-dotnet-core.md)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnet)
