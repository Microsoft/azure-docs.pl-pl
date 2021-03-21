---
title: 'Samouczek: korzystanie z konfiguracji dynamicznej za pomocą odświeżania wypychanego w aplikacji .NET Core'
titleSuffix: Azure App Configuration
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji platformy .NET Core przy użyciu funkcji odświeżania wypychania
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 977982bf1a36b4b85524df2513f2272fe4a8d1bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701522"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Samouczek: korzystanie z konfiguracji dynamicznej za pomocą odświeżania wypychanego w aplikacji .NET Core

Biblioteka kliencka konfiguracji aplikacji platformy .NET Core obsługuje aktualizowanie konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Aplikację można skonfigurować w taki sposób, aby wykrywać zmiany w konfiguracji aplikacji przy użyciu jednego lub obu dwóch poniższych podejść.

1. Model sondowania: jest to domyślne zachowanie, które używa sondowania do wykrywania zmian w konfiguracji. Po wygaśnięciu wartości w pamięci podręcznej, następnym wywołaniu `TryRefreshAsync` lub `RefreshAsync` wysyła żądanie do serwera w celu sprawdzenia, czy konfiguracja została zmieniona, i w razie potrzeby pobiera zaktualizowaną konfigurację.

1. Model wypychania: służy do wykrywania zmian w konfiguracji przy użyciu [zdarzeń konfiguracji aplikacji](./concept-app-configuration-event.md) . Po skonfigurowaniu konfiguracji aplikacji do wysyłania zdarzeń zmiany wartości klucza do Azure Event Grid aplikacja może używać tych zdarzeń do optymalizowania łącznej liczby żądań, które są konieczne do zaktualizowania konfiguracji. Aplikacje mogą subskrybować te elementy bezpośrednio z Event Grid lub w przypadku jednego z [obsługiwanych zdarzeń](../event-grid/event-handlers.md) , takich jak element webhook, funkcja platformy Azure lub temat Service Bus.

Aplikacje mogą subskrybować te zdarzenia bezpośrednio z Event Grid lub za pośrednictwem elementu webhook lub przez funkcję przesyłania dalej zdarzeń do Azure Service Bus. Zestaw Azure Service Bus SDK udostępnia interfejs API umożliwiający zarejestrowanie programu obsługi komunikatów, który upraszcza ten proces w przypadku aplikacji, które nie mają punktu końcowego HTTP lub nie chcą w sposób ciągły sondować usługi Event Grid.

W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w kodzie przy użyciu funkcji odświeżania wypychania. Jest ona oparta na aplikacji wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem należy najpierw [utworzyć aplikację platformy .NET Core z konfiguracją aplikacji](./quickstart-dotnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj subskrypcję do wysyłania zdarzeń zmiany konfiguracji z konfiguracji aplikacji do tematu Service Bus
> * Skonfiguruj aplikację platformy .NET Core w celu zaktualizowania jej konfiguracji w odpowiedzi na zmiany w konfiguracji aplikacji.
> * Korzystaj z najnowszej konfiguracji w aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Konfigurowanie tematu Azure Service Bus i subskrypcji

W tym samouczku zawarto Service Bus integrację Event Grid, aby uprościć wykrywanie zmian konfiguracji aplikacji, które nie chcą w sposób ciągły wykrywać zmian w konfiguracji aplikacji. Zestaw SDK Azure Service Bus udostępnia interfejs API umożliwiający zarejestrowanie procedury obsługi komunikatów, która może służyć do aktualizowania konfiguracji w przypadku wykrycia zmian w konfiguracji aplikacji. Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal do utworzenia tematu Service Bus i subskrypcji](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) , aby utworzyć obszar nazw, temat i subskrypcję usługi Service Bus.

Po utworzeniu zasobów Dodaj następujące zmienne środowiskowe. Zostaną one użyte do zarejestrowania programu obsługi zdarzeń w celu zmiany konfiguracji w kodzie aplikacji.

| Klucz | Wartość |
|---|---|
| ServiceBusConnectionString | Parametry połączenia dla przestrzeni nazw usługi Service Bus |
| ServiceBusTopic | Nazwa tematu Service Bus |
| ServiceBusSubscription | Nazwa subskrypcji usługi Service Bus |

## <a name="set-up-event-subscription"></a>Konfigurowanie subskrypcji zdarzeń

1. Otwórz zasób konfiguracji aplikacji w Azure Portal, a następnie kliknij przycisk w `+ Event Subscription` `Events` okienku.

    ![Zdarzenia konfiguracji aplikacji](./media/events-pane.png)

1. Wprowadź nazwę `Event Subscription` i `System Topic` .

    ![Utwórz subskrypcję zdarzeń](./media/create-event-subscription.png)

1. Wybierz opcję `Endpoint Type` tak `Service Bus Topic` , aby wybrać temat Service Bus, a następnie kliknij pozycję włączone `Confirm Selection` .

    ![Punkt końcowy magistrali usługi subskrypcji zdarzeń](./media/event-subscription-servicebus-endpoint.png)

1. Kliknij pozycję Włącz `Create` , aby utworzyć subskrypcję zdarzeń.

1. Kliknij `Event Subscriptions` w okienku, `Events` Aby sprawdzić, czy subskrypcja została utworzona pomyślnie.

    ![Subskrypcje zdarzeń konfiguracji aplikacji](./media/event-subscription-view.png)

> [!NOTE]
> Gdy subskrybujesz zmiany konfiguracji, można użyć co najmniej jednego filtru, aby zmniejszyć liczbę zdarzeń wysyłanych do aplikacji. Można je skonfigurować jako [Event Grid filtry subskrypcji](../event-grid/event-filtering.md) lub [Service Bus filtry subskrypcji](../service-bus-messaging/topic-filters.md). Na przykład można użyć filtru subskrypcji, aby subskrybować zdarzenia dotyczące zmian w kluczu, który rozpoczyna się od określonego ciągu.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Zarejestruj procedurę obsługi zdarzeń w celu ponownego załadowania danych z konfiguracji aplikacji

Otwórz *program programy. cs* i zaktualizuj plik przy użyciu następującego kodu.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

Metoda [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) służy do ustawiania wartości pamięci podręcznej dla wartości kluczy zarejestrowanych do odświeżania jako zanieczyszczone. Pozwala to zagwarantować, że następne wywołanie `RefreshAsync` lub `TryRefreshAsync` ponownie zweryfikuje buforowane wartości przy użyciu konfiguracji aplikacji i aktualizuje je w razie potrzeby.

Do momentu, gdy wartość buforowana zostanie oznaczona jako zanieczyszczona, należy dodać losowe opóźnienie, aby zmniejszyć potencjalne ograniczenie przepustowości w przypadku odświeżenia wielu wystąpień jednocześnie. Domyślne maksymalne opóźnienie, zanim wartość buforowana zostanie oznaczona jako zanieczyszczona, wynosi 30 sekund, ale można je zastąpić przez przekazanie opcjonalnego `TimeSpan` parametru do `SetDirty` metody.

> [!NOTE]
> Aby zmniejszyć liczbę żądań do konfiguracji aplikacji podczas korzystania z funkcji odświeżania wypychania, ważne jest wywołanie `SetCacheExpiration(TimeSpan cacheExpiration)` z odpowiednią wartością `cacheExpiration` parametru. Umożliwia to kontrolowanie czasu wygaśnięcia pamięci podręcznej w celu odświeżenia i użycia jako sieci bezpieczeństwa w przypadku wystąpienia problemu z subskrypcją zdarzeń lub Service Bus subskrypcją. Zalecana wartość to `TimeSpan.FromDays(30)` .

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **AppConfigurationConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Uruchom następujące polecenie, aby skompilować aplikację konsolową:

    ```console
     dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

    ```console
     dotnet run
    ```

    ![Uruchom odświeżanie wypychane przed aktualizacją](./media/dotnet-core-app-pushrefresh-initial.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji** i zaktualizuj wartości następujących kluczy:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

1. Poczekaj 30 sekund, aby umożliwić przetworzenie zdarzenia i konfigurację aktualizacji.

    ![Uruchom odświeżanie po aktualizacji](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację .NET Core do dynamicznego odświeżania ustawień konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)