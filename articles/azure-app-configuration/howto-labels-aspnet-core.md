---
title: Użyj konfiguracji dla środowiska
titleSuffix: Azure App Configuration
description: Używanie etykiet do udostępniania wartości konfiguracyjnych dla środowiska
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056797"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Użyj etykiet, aby włączyć różne konfiguracje dla różnych środowisk

Wiele aplikacji wymaga użycia różnych konfiguracji w różnych środowiskach. Załóżmy, że aplikacja ma wartość konfiguracji, która definiuje parametry połączenia, które będą używane dla wewnętrznej bazy danych. Deweloperzy aplikacji korzystają z innej bazy danych niż używana w środowisku produkcyjnym. Parametry połączenia bazy danych używane przez aplikację muszą ulec zmianie, gdy aplikacja przejdzie z programowania do produkcji.

W obszarze Konfiguracja aplikacji platformy Azure można używać *etykiet* do definiowania różnych wartości dla tego samego klucza. Na przykład można zdefiniować pojedynczy klucz z różnymi wartościami na potrzeby *programowania* i *produkcji*. Możesz określić, które etykiety mają zostać załadowane podczas łączenia z konfiguracją aplikacji.

Aby zademonstrować tę funkcję, zmodyfikujemy aplikację sieci Web utworzoną w [przewodniku szybki start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure](./quickstart-aspnet-core-app.md) , aby używać różnych ustawień konfiguracji do programowania i produkcji. Przed kontynuowaniem Ukończ Szybki Start.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Określ etykietę podczas dodawania wartości konfiguracji

W Azure Portal przejdź do **Eksploratora konfiguracji** i Znajdź klucz *TestApp: Settings: FONTCOLOR* utworzony w ramach przewodnika Szybki Start. Wybierz jego menu kontekstowe, a następnie kliknij przycisk **Dodaj wartość**.

> [!div class="mx-imgBorder"]
> ![Dodaj element menu wartość](media/labels-add-value.png)

Na ekranie **Dodaj wartość** wprowadź **wartość** **czerwoną** i **etykietę** **rozwoju**. Pozostaw pusty **Typ zawartości** . Wybierz przycisk **Zastosuj**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Ładowanie wartości konfiguracyjnych z określoną etykietą

Domyślnie usługa Azure App Configuration ładuje tylko wartości konfiguracji bez etykiety. Jeśli zdefiniowano etykiety dla wartości konfiguracji, należy określić etykiety, które będą używane podczas łączenia się z konfiguracją aplikacji.

W ostatniej sekcji utworzono inną wartość konfiguracji dla środowiska *deweloperskiego* . Ta `HostingEnvironment.EnvironmentName` zmienna służy do dynamicznego określania środowiska, w którym aplikacja jest aktualnie uruchomiona. Aby dowiedzieć się więcej, zobacz [Używanie wielu środowisk w ASP.NET Core](/aspnet/core/fundamentals/environments).

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
> Powyższy fragment kodu ładuje parametry połączenia z konfiguracją aplikacji ze zmiennej środowiskowej `AppConfigConnectionString`o nazwie. Upewnij się, że ta zmienna środowiskowa jest prawidłowo ustawiona.

`Select` Metoda jest wywoływana dwukrotnie. Po raz pierwszy ładuje wartości konfiguracyjne bez etykiety. Następnie ładuje wartości konfiguracji z etykietą odpowiadającą bieżącemu środowisku. Te wartości specyficzne dla środowiska zastępują wszelkie odpowiadające im wartości bez etykiet. Nie trzeba definiować wartości specyficznych dla środowiska dla każdego klucza. Jeśli klucz nie ma wartości z etykietą odpowiadającą bieżącemu środowisku, wówczas nie zostanie użyta wartość bez etykiety.

## <a name="testing-in-different-environments"></a>Testowanie w różnych środowiskach

Aby przetestować różne wartości konfiguracyjne, Otwórz `launchSettings.json` plik w `Properties` katalogu. Znajdź `config` wpis w obszarze `profiles`. W `environmentVariables` sekcji Ustaw `ASPNETCORE_ENVIRONMENT` zmienną na. `Production`

Po ustawieniu nowych wartości Kompiluj i uruchom aplikację.

```dotnetcli
dotnet build
dotnet run
```

Przejdź do `http://localhost:5000`programu przy użyciu przeglądarki sieci Web. Zauważ, że kolor czcionki jest czarny.

![Aplikacja sieci Web działająca z konfiguracją produkcyjną](media/labels-website-prod.png)

Teraz zaktualizuj `launchSettings.json` , `ASPNETCORE_ENVIRONMENT` aby ustawić zmienną na `Development`. Uruchom ponownie polecenie `dotnet run`. Zauważ, że kolor czcionki jest teraz czerwony. Dzieje się tak dlatego, `TestApp:Settings:FontColor` że aplikacja używa teraz wartości, która ma `Development` etykietę. Wszystkie inne wartości konfiguracyjne pozostają takie same jak ich wartości produkcyjne.

![Aplikacja sieci Web działająca z konfiguracją programistyczną](media/labels-website-dev.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja w ASP.NET Core](/aspnet/core/fundamentals/configuration/)
