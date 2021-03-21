---
title: 'Samouczek .NET Framework: Konfiguracja dynamiczna w konfiguracji aplikacji platformy Azure'
description: W ramach tego samouczka nauczysz się dynamicznie aktualizować dane konfiguracyjne .NET Framework aplikacji przy użyciu usługi Azure App Configuration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932765"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji .NET Framework

Biblioteka klienta platformy .NET konfiguracji aplikacji obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Można to wdrożyć, najpierw pobierając wystąpienie z `IConfigurationRefresher` opcji dla dostawcy konfiguracji, a następnie wywołując je `TryRefreshAsync` w dowolnym miejscu w kodzie.

Aby zachować zaktualizowane ustawienia i uniknąć zbyt wielu wywołań w magazynie konfiguracji, dla każdego ustawienia zostanie użyta pamięć podręczna. Do momentu wygaśnięcia zbuforowanej wartości ustawienia operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Jest ona oparta na aplikacji wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji .NET Frameworkej z konfiguracją aplikacji](./quickstart-dotnet-app.md) .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację .NET Framework, aby zaktualizować jej konfigurację w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Wstrzyknąć najnowszą konfigurację w aplikacji.
## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 lub nowszy](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz kolejno pozycje **Eksplorator konfiguracji**  >  **+ Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

8. Wybierz przycisk **Zastosuj**.

## <a name="create-a-net-framework-console-app"></a>Tworzenie aplikacji konsolowej .NET Framework

1. Uruchom program Visual Studio i wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. W obszarze **Utwórz nowy projekt** odfiltruj typ projektu **konsoli** i kliknij pozycję **Aplikacja konsolowa (.NET Framework)**. Kliknij przycisk **Dalej**.

1. W obszarze **Konfigurowanie nowego projektu** wprowadź nazwę projektu. W obszarze **Struktura** wybierz pozycję **.NET Framework 4.7.1** lub wyższy. Kliknij pozycję **Utwórz**.

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration
1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj *Microsoft.Extensions.Configwersja. AzureAppConfiguration* pakiet NuGet do projektu. Jeśli nie możesz go znaleźć, zaznacz pole wyboru **Uwzględnij wersję wstępną** .

1. Otwórz *program System. cs* i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Dodaj dwie zmienne do przechowywania obiektów związanych z konfiguracją.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Zaktualizuj `Main` metodę, aby połączyć się z konfiguracją aplikacji przy użyciu określonych opcji odświeżania.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    Ta `ConfigureRefresh` Metoda służy do określania ustawień służących do aktualizowania danych konfiguracji z magazynem konfiguracji aplikacji w przypadku wyzwolenia operacji odświeżania. Wystąpienie `IConfigurationRefresher` można pobrać przez wywołanie `GetRefresher` metody w opcjach dostarczonych do `AddAzureAppConfiguration` metody, a `TryRefreshAsync` Metoda w tym wystąpieniu może służyć do wyzwalania operacji odświeżania wszędzie w kodzie.

    > [!NOTE]
    > Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 sekund, ale można go zastąpić, wywołując `SetCacheExpiration` metodę z inicjatora opcji przekazaną jako argument do `ConfigureRefresh` metody.

1. Dodaj metodę o nazwie `PrintMessage()` , która wyzwala ręczne odświeżanie danych konfiguracji z konfiguracji aplikacji.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Uruchom ponownie program Visual Studio, aby zmiany zaczęły obowiązywać. 

1. Naciśnij kombinację klawiszy CTRL + F5, aby skompilować i uruchomić aplikację konsolową.

    ![Lokalne uruchamianie aplikacji](./media/dotnet-app-run.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji** i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Wróć do uruchomionej aplikacji, naciśnij klawisz ENTER, aby wyzwolić odświeżanie i wydrukować zaktualizowaną wartość w wierszu polecenia lub w oknie programu PowerShell.

    ![Lokalne odświeżanie aplikacji](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony na 10 sekund przy użyciu `SetCacheExpiration` metody podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji będzie aktualizowana tylko wtedy, gdy od czasu ostatniego odświeżenia tego ustawienia upłynie co najmniej 10 sekund.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację .NET Framework do dynamicznego odświeżania ustawień konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
