---
title: Użyj konfiguracji dla środowiska
titleSuffix: Azure App Configuration
description: Użyj etykiet, aby podać wartości konfiguracyjne dla środowiska.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929620"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Używanie etykiet do włączania konfiguracji dla różnych środowisk

Wiele aplikacji wymaga użycia różnych konfiguracji w różnych środowiskach. Załóżmy, że aplikacja ma wartość konfiguracji, która definiuje parametry połączenia, które będą używane dla wewnętrznej bazy danych. Deweloperzy aplikacji korzystają z innej bazy danych niż używana w środowisku produkcyjnym. Parametry połączenia z bazą danych używane przez aplikację muszą ulec zmianie, gdy aplikacja przejdzie z programowania do produkcji.

W obszarze Konfiguracja aplikacji platformy Azure można używać *etykiet* do definiowania różnych wartości dla tego samego klucza. Na przykład można zdefiniować pojedynczy klucz z różnymi wartościami na potrzeby programowania i produkcji. Możesz określić, która etykieta ma zostać załadowana podczas nawiązywania połączenia z konfiguracją aplikacji.

Aby zademonstrować tę funkcję, zmodyfikuj aplikację sieci Web utworzoną w [przewodniku szybki start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure](./quickstart-aspnet-core-app.md) w celu używania różnych ustawień konfiguracji na potrzeby programowania i produkcji. Przed kontynuowaniem Ukończ Szybki Start.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Określ etykietę podczas dodawania wartości konfiguracji

W Azure Portal przejdź do **Eksploratora konfiguracji** i Znajdź klucz *TestApp: Settings: FONTCOLOR* utworzony w ramach przewodnika Szybki Start. Wybierz jego menu kontekstowe, a następnie wybierz pozycję **Dodaj wartość**.

> [!div class="mx-imgBorder"]
> ![Dodaj element menu wartość](media/labels-add-value.png)

Na ekranie **Dodaj wartość** wprowadź **wartość** **czerwoną** i **etykietę** **rozwoju**. Pozostaw pusty **Typ zawartości** . Wybierz przycisk **Zastosuj**.

## <a name="load-configuration-values-with-a-specified-label"></a>Załaduj wartości konfiguracyjne z określoną etykietą

Domyślnie usługa Azure App Configuration ładuje tylko wartości konfiguracji bez etykiety. Jeśli zdefiniowano etykiety dla wartości konfiguracji, należy określić etykiety do użycia podczas łączenia się z konfiguracją aplikacji.

W poprzedniej sekcji utworzono inną wartość konfiguracji dla środowiska deweloperskiego. Ta zmienna służy `HostingEnvironment.EnvironmentName` do dynamicznego określania środowiska, w którym aktualnie działa aplikacja. Aby dowiedzieć się więcej, zobacz [Używanie wielu środowisk w ASP.NET Core](/aspnet/core/fundamentals/environments).

Załaduj wartości konfiguracji z etykietą odpowiadającą bieżącemu środowisku, przekazując nazwę środowiska do `Select` metody:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Poprzedni fragment kodu ładuje parametry połączenia z konfiguracją aplikacji ze zmiennej środowiskowej o nazwie `AppConfigConnectionString` . Upewnij się, że ta zmienna środowiskowa jest prawidłowo ustawiona.

`Select`Metoda jest wywoływana dwukrotnie. Po raz pierwszy ładuje wartości konfiguracyjne bez etykiety. Następnie ładuje wartości konfiguracji z etykietą odpowiadającą bieżącemu środowisku. Te wartości specyficzne dla środowiska zastępują wszelkie odpowiadające im wartości bez etykiet. Nie trzeba definiować wartości specyficznych dla środowiska dla każdego klucza. Jeśli klucz nie ma wartości z etykietą odpowiadającą bieżącemu środowisku, używa wartości bez etykiety.

## <a name="test-in-different-environments"></a>Testowanie w różnych środowiskach

Otwórz `launchSettings.json` plik w `Properties` katalogu. Znajdź `config` wpis poniżej `profiles` . W `environmentVariables` sekcji Ustaw `ASPNETCORE_ENVIRONMENT` zmienną na `Production` .

Po ustawieniu nowych wartości Kompiluj i uruchom aplikację.

```dotnetcli
dotnet build
dotnet run
```

Użyj przeglądarki sieci Web, aby przejść do `http://localhost:5000` . Zauważ, że kolor czcionki jest czarny.

![Aplikacja sieci Web działająca z konfiguracją produkcyjną](media/labels-website-prod.png)

Zaktualizuj `launchSettings.json` , aby ustawić wartość `ASPNETCORE_ENVIRONMENT` zmiennej `Development` . Uruchom ponownie polecenie `dotnet run`. 

Zauważ, że kolor czcionki jest teraz czerwony. Dzieje się tak dlatego, że aplikacja używa teraz wartości `TestApp:Settings:FontColor` , która ma `Development` etykietę. Wszystkie inne wartości konfiguracyjne pozostają takie same jak ich wartości produkcyjne.

![Aplikacja sieci Web działająca z konfiguracją programistyczną](media/labels-website-dev.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja w ASP.NET Core](/aspnet/core/fundamentals/configuration/)
