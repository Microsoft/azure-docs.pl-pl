---
title: 'Samouczek: używanie konfiguracji dynamicznej w aplikacji .NET Core'
titleSuffix: Azure App Configuration
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji platformy .NET Core
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: 95c48bded224a40879328dba5861b3564f0ebcf8
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979865"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji .NET Core

Biblioteka kliencka konfiguracji aplikacji platformy .NET Core obsługuje aktualizowanie konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Można to wdrożyć, najpierw pobierając wystąpienie z `IConfigurationRefresher` opcji dla dostawcy konfiguracji, a następnie wywołując je `TryRefreshAsync` w dowolnym miejscu w kodzie.

Aby zachować zaktualizowane ustawienia i uniknąć zbyt wielu wywołań w magazynie konfiguracji, dla każdego ustawienia zostanie użyta pamięć podręczna. Do momentu wygaśnięcia zbuforowanej wartości ustawienia operacja odświeżania nie aktualizuje wartości, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund, ale w razie potrzeby można go zastąpić.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Jest ona oparta na aplikacji wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem należy najpierw [utworzyć aplikację platformy .NET Core z konfiguracją aplikacji](./quickstart-dotnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację platformy .NET Core w celu zaktualizowania jej konfiguracji w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Korzystaj z najnowszej konfiguracji w aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

Otwórz *program.cs* i zaktualizuj plik, aby dodać odwołanie do `System.Threading.Tasks` przestrzeni nazw, aby określić konfigurację odświeżania w `AddAzureAppConfiguration` metodzie i wyzwalać ręczne odświeżanie przy użyciu `TryRefreshAsync` metody.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

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

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

Ta `ConfigureRefresh` Metoda służy do określania ustawień służących do aktualizowania danych konfiguracji z magazynem konfiguracji aplikacji w przypadku wyzwolenia operacji odświeżania. Wystąpienie `IConfigurationRefresher` można pobrać przez wywołanie `GetRefresher` metody w opcjach dostarczonych do `AddAzureAppConfiguration` metody, a `TryRefreshAsync` Metoda w tym wystąpieniu może służyć do wyzwalania operacji odświeżania wszędzie w kodzie.
    
> [!NOTE]
> Domyślny czas wygaśnięcia pamięci podręcznej dla ustawienia konfiguracji wynosi 30 sekund, ale można go zastąpić, wywołując `SetCacheExpiration` metodę z inicjatora opcji przekazaną jako argument do `ConfigureRefresh` metody.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Uruchom następujące polecenie, aby skompilować aplikację konsolową:

    ```console
     dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

    ```console
     dotnet run
    ```

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji** i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Naciśnij klawisz ENTER, aby wyzwolić odświeżanie i wydrukować zaktualizowaną wartość w wierszu polecenia lub w oknie programu PowerShell.

    ![Lokalne odświeżanie aplikacji z przewodnika Szybki start](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Ponieważ czas wygaśnięcia pamięci podręcznej został ustawiony na 10 sekund przy użyciu `SetCacheExpiration` metody podczas określania konfiguracji dla operacji odświeżania, wartość ustawienia konfiguracji będzie aktualizowana tylko wtedy, gdy od czasu ostatniego odświeżenia tego ustawienia upłynie co najmniej 10 sekund.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację .NET Core do dynamicznego odświeżania ustawień konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
