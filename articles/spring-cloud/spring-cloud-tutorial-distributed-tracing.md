---
title: Korzystanie z rozproszonego śledzenia w chmurze Azure wiosennej
description: Dowiedz się, jak używać śledzenia rozproszonego chmury wiosennej za pomocą usługi Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 30eb19e418292e74989be81d94ed684c917f6971
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088639"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Korzystanie z rozproszonego śledzenia w chmurze Azure wiosennej

Dzięki narzędziom do śledzenia rozproszonym w chmurze Azure wiosennej można łatwo debugować i monitorować złożone problemy. Chmura ze sprężyną Azure integruje się z [chmurą Sleuth](https://spring.io/projects/spring-cloud-sleuth) z platformą [Application Insights](../azure-monitor/app/app-insights-overview.md)Azure. Ta integracja zapewnia zaawansowane możliwości śledzenia rozproszonego na podstawie Azure Portal.

::: zone pivot="programming-language-csharp"
W tym artykule dowiesz się, jak włączyć śledzenie rozproszone przy użyciu aplikacji .NET Core steeltoe.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te procedury, potrzebna jest aplikacja steeltoe, która została już [przygotowana do wdrożenia w chmurze Azure wiosennej](spring-cloud-tutorial-prepare-app-deployment.md).

## <a name="dependencies"></a>Zależności

Zainstaluj następujące pakiety NuGet

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

## <a name="update-startupcs"></a>Aktualizacja Startup.cs

1. W `ConfigureServices` metodzie Wywołaj `AddDistributedTracing` metody i `AddZipkinExporter` .

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

1. W `Configure` metodzie Wywołaj `UseTracingExporter` metodę.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseTracingExporter();
   }
   ```

## <a name="update-configuration"></a>Aktualizowanie konfiguracji

Dodaj następujące ustawienia do źródła konfiguracji, które będą używane podczas uruchamiania aplikacji w chmurze Azure wiosną:

1. Ustaw wartość argumentu `management.tracing.alwaysSample` na true.

2. Jeśli chcesz zobaczyć zakresy śledzenia wysyłane między serwerem Eureka, serwerem konfiguracji i aplikacjami użytkownika: Ustaw wartość `management.tracing.egressIgnorePattern` "/API/v2/spans |/v2/Apps/.* /Permissions |/Eureka/.*| /oauth/.*".

Na przykład *appsettings.json* zawiera następujące właściwości:
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

Aby uzyskać więcej informacji na temat śledzenia rozproszonego w aplikacjach .NET Core steeltoe, zobacz [śledzenie rozproszone](https://steeltoe.io/docs/3/tracing/distributed-tracing) w dokumentacji steeltoe.
::: zone-end
::: zone pivot="programming-language-java"
W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Włącz śledzenie rozproszone w Azure Portal.
> * Dodaj wiosenną Sleuth chmurową do swojej aplikacji.
> * Wyświetl mapy zależności dla aplikacji mikrousług.
> * Przeszukaj dane śledzenia przy użyciu różnych filtrów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te procedury, potrzebna jest usługa w chmurze Azure wiosny, która jest już zainicjowana i uruchomiona. Ukończ Przewodnik Szybki Start dotyczący [wdrażania pierwszej aplikacji w chmurze Azure ze sprężyną](spring-cloud-quickstart.md) w celu aprowizacji i uruchamiania usługi w chmurze Azure wiosennej.

## <a name="add-dependencies"></a>Dodaj zależności

1. Dodaj następujący wiersz do pliku Application. Properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Po tej zmianie nadawca Zipkin może wysłać do sieci Web.

1. Pomiń ten krok, jeśli korzystasz [z naszego przewodnika przygotowującego aplikację w chmurze platformy Azure](spring-cloud-tutorial-prepare-app-deployment.md). W przeciwnym razie przejdź do lokalnego środowiska deweloperskiego i edytuj plik pom.xml, aby uwzględnić następującą zależność Sleuth chmury Wiosnowej:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Skompiluj i Wdróż ponownie dla usługi w chmurze Azure wiosny, aby odzwierciedlić te zmiany.

## <a name="modify-the-sample-rate"></a>Modyfikuj częstotliwość próbkowania

Możesz zmienić częstotliwość zbierania danych telemetrycznych, modyfikując częstotliwość próbkowania. Na przykład jeśli chcesz próbkować bardzo często, Otwórz plik Application. Properties i zmień następujący wiersz:

```xml
spring.sleuth.sampler.probability=0.5
```

Jeśli aplikacja została już skompilowana i wdrożona, można zmodyfikować częstotliwość próbkowania. Aby to zrobić, Dodaj poprzedni wiersz jako zmienną środowiskową w interfejsie wiersza polecenia platformy Azure lub w Azure Portal.
::: zone-end

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal.
1. Na stronie **monitorowanie** wybierz opcję **śledzenie rozproszone**.
1. Wybierz pozycję **Edytuj ustawienie** , aby edytować lub dodać nowe ustawienie.
1. Utwórz nowe zapytanie Application Insights lub Wybierz istniejące.
1. Wybierz kategorię rejestrowania, którą chcesz monitorować, a następnie określ czas przechowywania w dniach.
1. Wybierz pozycję **Zastosuj** , aby zastosować nowe śledzenie.

## <a name="view-the-application-map"></a>Wyświetlanie mapy aplikacji

Wróć do strony **śledzenie rozproszone** i wybierz pozycję **Wyświetl mapę aplikacji**. Przejrzyj wizualną reprezentację ustawień aplikacji i monitorowania. Aby dowiedzieć się, jak używać mapy aplikacji, zobacz Application [map: Klasyfikacja Distributed Applications](../azure-monitor/app/app-map.md).

## <a name="use-search"></a>Używanie wyszukiwania

Funkcja Search umożliwia wykonywanie zapytań dotyczących innych określonych elementów telemetrii. Na stronie **śledzenie rozproszone** wybierz pozycję **Wyszukaj**. Aby uzyskać więcej informacji na temat korzystania z funkcji wyszukiwania, zobacz [Używanie wyszukiwania w Application Insights](../azure-monitor/app/diagnostic-search.md).

## <a name="use-application-insights"></a>Użyj Application Insights

Application Insights udostępnia funkcje monitorowania oprócz mapy aplikacji i funkcji wyszukiwania. Wyszukaj w Azure Portal nazwę aplikacji, a następnie otwórz stronę Application Insights, aby znaleźć informacje dotyczące monitorowania. Aby uzyskać więcej wskazówek na temat korzystania z tych narzędzi, zapoznaj się z tematem [Azure monitor zapytania dziennika](/azure/data-explorer/kusto/query/).

## <a name="disable-application-insights"></a>Wyłącz Application Insights

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal.
1. W obszarze **monitorowanie**wybierz pozycję **śledzenie rozproszone**.
1. Wybierz pozycję **Wyłącz** , aby wyłączyć Application Insights.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i zrozumienia śledzenia rozproszonego w chmurze Azure wiosennej. Aby dowiedzieć się więcej na temat usługi Binding Services dla aplikacji, zobacz [BIND a Azure Cosmos DB Database w aplikacji w chmurze ze sprężyną na platformie Azure](spring-cloud-tutorial-bind-cosmos.md).