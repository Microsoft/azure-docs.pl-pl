---
title: Odporność konfiguracji aplikacji platformy Azure i odzyskiwanie po awarii
description: Sposób implementacji odporności i odzyskiwania po awarii przy użyciu konfiguracji aplikacji platformy Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 5c62f10d67345d68cde27af7d0a7663b22d978a0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002429"
---
# <a name="resiliency-and-disaster-recovery"></a>Odporność i odzyskiwanie po awarii

Obecnie konfiguracja aplikacji platformy Azure to usługa regionalna. Każdy magazyn konfiguracji jest tworzony w określonym regionie świadczenia usługi Azure. Awaria całego regionu ma wpływ na wszystkie sklepy w tym regionie. Konfiguracja aplikacji nie oferuje automatycznej pracy awaryjnej w innym regionie. Ten artykuł zawiera ogólne wskazówki dotyczące korzystania z wielu magazynów konfiguracji w regionach platformy Azure w celu zwiększenia odporności aplikacji na geograficzną.

## <a name="high-availability-architecture"></a>Architektura wysokiej dostępności

Aby zrealizować nadmiarowość między regionami, należy utworzyć wiele magazynów konfiguracji aplikacji w różnych regionach. W przypadku tej konfiguracji aplikacja ma co najmniej jeden dodatkowy magazyn konfiguracji do przywrócenia, jeśli magazyn podstawowy stał się niedostępny. Na poniższym diagramie przedstawiono topologię między aplikacją i jej podstawową i pomocniczą magazynem konfiguracji:

![Sklepy geograficznie nadmiarowe](./media/geo-redundant-app-configuration-stores.png)

Aplikacja ładuje swoją konfigurację zarówno ze sklepu podstawowego, jak i pomocniczego. Zwiększa to prawdopodobieństwo pomyślnego pobrania danych konfiguracyjnych. Użytkownik jest odpowiedzialny za przechowywanie danych w obu sklepach w synchronizacji. W poniższych sekcjach opisano sposób tworzenia odporności geograficznej w aplikacji.

## <a name="failover-between-configuration-stores"></a>Przełączanie w tryb failover między magazynami konfiguracji

Technicznie aplikacja nie wykonuje przejścia w tryb failover. Podjęto próbę pobrania tego samego zestawu danych konfiguracji z dwóch magazynów konfiguracji aplikacji jednocześnie. Rozmieść kod w taki sposób, aby został załadowany z magazynu pomocniczego jako pierwszy, a następnie do magazynu podstawowego. Takie podejście gwarantuje, że dane konfiguracyjne w magazynie podstawowym mają pierwszeństwo za każdym razem, gdy jest dostępne. Poniższy fragment kodu przedstawia sposób wdrożenia tego rozmieszczenia w programie .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Zwróć uwagę na `optional` parametr przekazywany do `AddAzureAppConfiguration` funkcji. Gdy ta wartość `true` jest ustawiona na, ten parametr zapobiega niepowodzeniu działania aplikacji w przypadku, gdy funkcja nie może załadować danych konfiguracyjnych.

## <a name="synchronization-between-configuration-stores"></a>Synchronizacja między magazynami konfiguracji

Ważne jest, aby wszystkie dane z geograficznie nadmiarowych magazynów miały ten sam zestaw danych. Istnieją dwa sposoby osiągnięcia tego celu:

### <a name="backup-manually-using-the-export-function"></a>Ręczne tworzenie kopii zapasowej przy użyciu funkcji eksportu

Aby skopiować dane z magazynu podstawowego do pomocniczego na żądanie, można użyć funkcji **eksportu** w konfiguracji aplikacji. Ta funkcja jest dostępna za pomocą zarówno Azure Portal, jak i interfejsu wiersza polecenia.

W Azure Portal można wypchnąć zmianę do innego magazynu konfiguracji, wykonując następujące kroki.

1. Przejdź do karty **Importuj/Eksportuj** , a następnie wybierz pozycję **Eksportuj**  >  **App Configuration**  >  **element docelowy** konfiguracji aplikacji  >  **Wybierz zasób**.

1. W nowym bloku, który zostanie otwarty, określ subskrypcję, grupę zasobów i nazwę zasobu magazynu pomocniczego, a następnie wybierz pozycję **Zastosuj**.

1. Interfejs użytkownika zostanie zaktualizowany, aby można było wybrać dane konfiguracji, które mają zostać wyeksportowane do magazynu pomocniczego. Domyślną wartością czasu można pozostawić jako wartość i ustawić zarówno **z etykiety** , jak i **etykiety** na tę samą wartość. Wybierz pozycję **Zastosuj**. Powtórz tę czynność dla wszystkich etykiet w magazynie podstawowym.

1. Powtórz poprzednie kroki przy każdej zmianie konfiguracji.

Proces eksportowania można również uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure. Następujące polecenie pokazuje, jak wyeksportować wszystkie konfiguracje z magazynu podstawowego do pomocniczego:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Utwórz kopię zapasową automatycznie przy użyciu Azure Functions

Proces tworzenia kopii zapasowej można zautomatyzować za pomocą Azure Functions. Wykorzystuje ona integrację z Azure Event Grid w konfiguracji aplikacji. Po skonfigurowaniu usługi Konfiguracja aplikacji będzie publikować zdarzenia do Event Grid dla wszelkich zmian wprowadzonych w kluczowych wartościach w magazynie konfiguracji. W związku z tym aplikacja Azure Functions może nasłuchiwać tych zdarzeń i odpowiednio tworzyć dane kopii zapasowej. Aby uzyskać szczegółowe informacje, zobacz Samouczek dotyczący [sposobu automatycznego tworzenia kopii zapasowych magazynów konfiguracji aplikacji](./howto-backup-config-store.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rozszerzania aplikacji w celu uzyskania odporności geograficznej w czasie wykonywania w celu skonfigurowania aplikacji. Możesz również osadzić dane konfiguracji z konfiguracji aplikacji podczas kompilacji lub czasu wdrożenia. Aby uzyskać więcej informacji, zobacz [Integrowanie z potokiem](./integrate-ci-cd-pipeline.md)ciągłej integracji/ciągłego wdrażania.
