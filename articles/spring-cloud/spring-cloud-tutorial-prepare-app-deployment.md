---
title: Jak przygotować aplikację do wdrożenia w chmurze Azure wiosennej
description: Dowiedz się, jak przygotować aplikację do wdrożenia w chmurze Azure wiosennej.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c9d4356f5dc0b1eace586b741593b9c718c35caf
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945451"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Przygotowywanie aplikacji do wdrożenia w chmurze Azure wiosennej

::: zone pivot="programming-language-csharp"
Chmura sprężynowa platformy Azure oferuje niezawodne usługi do hostowania, monitorowania, skalowania i aktualizowania aplikacji steeltoe. W tym artykule przedstawiono sposób przygotowania istniejącej aplikacji steeltoe do wdrożenia w chmurze z usługą Azure wiosną. 

W tym artykule opisano zależności, konfigurację i kod, które są wymagane do uruchomienia aplikacji platformy .NET Core steeltoe w chmurze sieci szkieletowej platformy Azure. Aby uzyskać informacje na temat sposobu wdrażania aplikacji w chmurze Azure wiosennej, zobacz [wdrażanie pierwszej aplikacji w chmurze Azure wiosennej](spring-cloud-quickstart.md).

>[!Note]
> Obsługa steeltoe w chmurze z systemem Azure jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji o obsłudze wersji zapoznawczych, zapoznaj się z tematem [często zadawanych pytań](https://azure.microsoft.com/support/faq/) lub pliku [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

##  <a name="supported-versions"></a>Obsługiwane wersje

Chmura sprężynowa platformy Azure obsługuje:

* .NET Core 3,1
* Steeltoe 2,4

## <a name="dependencies"></a>Zależności

Zainstaluj pakiet [Microsoft. Azure. SpringCloud. Client](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) .

## <a name="update-programcs"></a>Aktualizacja Program.cs

W `Program.Main` metodzie Wywołaj `UseAzureSpringCloudService` metodę:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .UseAzureSpringCloudService();
```

## <a name="enable-eureka-server-service-discovery"></a>Włącz odnajdowanie usługi serwera Eureka

W źródle konfiguracji, który będzie używany, gdy aplikacja jest uruchamiana w chmurze Azure wiosennej, ustaw `spring.application.name` taką samą nazwę, jak aplikacja w chmurze Azure wiosny, do której zostanie wdrożony projekt.

Jeśli na przykład projekt .NET został wdrożony o nazwie `EurekaDataProvider` do aplikacji w chmurze Azure wiosennej o nazwie `planet-weather-provider` *appSettings.jsw* pliku powinien zawierać następujący kod JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Korzystanie z odnajdowania usług

Aby wywołać usługę przy użyciu odnajdywania usługi serwera Eureka, należy wykonać żądania HTTP, `http://<app_name>` gdzie `app_name` jest wartością `spring.application.name` aplikacji docelowej. Na przykład poniższy kod wywołuje `planet-weather-provider` usługę:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
W tym temacie przedstawiono sposób przygotowania istniejącej aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej. W przypadku poprawnego skonfigurowania chmurowa usługa Azure wiosenna zapewnia niezawodne usługi do monitorowania, skalowania i aktualizowania aplikacji w chmurze ze sprężyną Java.

Przed uruchomieniem tego przykładu możesz skorzystać z [podstawowego przewodnika Szybki Start](spring-cloud-quickstart.md).

Inne przykłady wyjaśniają sposób wdrażania aplikacji w chmurze Azure sprężynowej po skonfigurowaniu pliku pliku pom. 
* [Uruchamianie pierwszej aplikacji](spring-cloud-quickstart.md)
* [Kompilowanie i uruchamianie mikrousług](spring-cloud-quickstart-sample-app-introduction.md)

W tym artykule opisano wymagane zależności oraz sposób ich dodawania do pliku pliku pom.

## <a name="java-runtime-version"></a>Wersja środowiska uruchomieniowego języka Java

Tylko aplikacje sprężynowe/Java mogą działać w chmurze Azure wiosennej.

Chmura sprężynowa platformy Azure obsługuje języki Java 8 i Java 11. Środowisko hostingu zawiera najnowszą wersję programu Azul Zulu OpenJDK for Azure. Aby uzyskać więcej informacji na temat Azul Zulu OpenJDK for Azure, zobacz [Instalowanie programu JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Sprężynowe wersje chmurowe i sprężynowe

Aby przygotować istniejącą aplikację do rozruchu ze sprężyną w celu wdrożenia w chmurze Azure wiosennej, Dołącz zależności chmurowego rozruchowego i sprężynowego w pliku pliku pom aplikacji, jak pokazano w poniższych sekcjach.

Chmura sprężynowa platformy Azure obsługuje tylko aplikacje do rozruchu sprężynowego w wersji 2,1 lub 2,2. W poniższej tabeli wymieniono obsługiwane kombinacje chmurowych rozruchowych i sprężynowych:

Wersja sprężyny rozruchowej | Wersja chmury wiosennej
---|---
2.1 | Greenwich. RELEASE
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8

### <a name="dependencies-for-spring-boot-version-21"></a>Zależności w przypadku rozruchu sprężynowego w wersji 2,1

W przypadku rozruchu sprężynowego w wersji 2,1 Dodaj następujące zależności do pliku pliku pom aplikacji.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Zależności w przypadku rozruchu sprężynowego w wersji 2,2

W przypadku rozruchu sprężynowego w wersji 2,2 Dodaj następujące zależności do pliku pliku pom aplikacji.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>Zależności w przypadku rozruchu sprężynowego w wersji 2,3

W przypadku rozruchu sprężynowego w wersji 2,3 Dodaj następujące zależności do pliku pliku pom aplikacji.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Zależność klienta chmury ze sprężyną Azure

Usługa Azure wiosny Cloud hostuje i zarządza składnikami chmury Wiosnowej. Składniki obejmują rejestr usługi w chmurze ze sprężyną i wiosną serwerów konfiguracji chmury. Zalecane jest użycie sprężyny Boot 2,2 lub 2,3. W przypadku rozruchu sprężynowego 2,1 należy uwzględnić w zależnoście bibliotekę kliencką chmury Azure wiosny, aby umożliwić komunikację z wystąpieniem usługi w chmurze ze sprężyną na platformie Azure.

W poniższej tabeli wymieniono prawidłowe wersje chmurowe platformy Azure dla aplikacji, które korzystają z chmury rozruchowej i sprężyny.

Wersja sprężyny rozruchowej | Wersja chmury wiosennej | Wersja początkowa klienta usługi Azure wiosny Cloud
---|---|---
2.1. x | Greenwich. RELEASE | 2.1.2
2.2. x | Hoxton.SR8 | Nie jest wymagany
2.3. x | Hoxton.SR8 | Nie jest wymagany

Jeśli używasz sieci z rozruchem sprężynowym 2,1, Dołącz następujący dependenciy do pliku pom.xml.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```
> [!WARNING]
> Nie określaj `server.port` w konfiguracji. Chmura sprężynowa platformy Azure będzie overide to ustawienie na stały numer portu. Należy również przestrzegać tego ustawienia i nie określać portu serwera w kodzie.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Inne Zalecane zależności dotyczące włączania funkcji chmury wiosennej platformy Azure

Aby włączyć funkcje wbudowane w chmurze Azure wiosennej z rejestru usługi do rozproszonego śledzenia, należy również uwzględnić następujące zależności w aplikacji. Niektóre z tych zależności można usunąć, jeśli nie są potrzebne odpowiednie funkcje dla określonych aplikacji.

### <a name="service-registry"></a>Rejestr usługi

Aby użyć zarządzanej usługi rejestru usługi platformy Azure, należy uwzględnić `spring-cloud-starter-netflix-eureka-client` zależność w pliku pom.xml, jak pokazano poniżej:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Punkt końcowy serwera rejestru usługi jest automatycznie wprowadzany jako zmienne środowiskowe w aplikacji. Aplikacje mogą zarejestrować się na serwerze rejestru usługi i odnajdywać inne zależne mikrousługi.


#### <a name="enablediscoveryclient-annotation"></a>Adnotacja EnableDiscoveryClient

Dodaj następującą adnotację do kodu źródłowego aplikacji.
```java
@EnableDiscoveryClient
```
Na przykład zapoznaj się z aplikacją piggymetrics z wcześniejszych przykładów:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Konfiguracja rozproszona

Aby włączyć konfigurację rozproszoną, `spring-cloud-config-client` w sekcji zależności pliku pom.xml Uwzględnij następującą zależność:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nie określaj `spring.cloud.config.enabled=false` w konfiguracji ładowania początkowego. W przeciwnym razie aplikacja przestanie działać z serwerem konfiguracji.

### <a name="metrics"></a>Metryki

Uwzględnij `spring-boot-starter-actuator` zależność w sekcji zależności pliku pom.xml, jak pokazano poniżej:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metryki są okresowo ściągane z punktów końcowych JMX. Możesz wizualizować metryki przy użyciu Azure Portal.

 > [!WARNING]
 > Określ `spring.jmx.enabled=true` we właściwości konfiguracji. W przeciwnym razie metryki nie można wizualizować w Azure Portal.

### <a name="distributed-tracing"></a>Śledzenie rozproszone

Uwzględnij poniższe `spring-cloud-starter-sleuth` i `spring-cloud-starter-zipkin` zależności w sekcji zależności pliku pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Musisz również włączyć wystąpienie usługi Azure Application Insights, aby współpracowało z wystąpieniem usług w chmurze sieci Azure ze sprężyną. Aby uzyskać informacje o sposobach używania Application Insights z chmurą Azure wiosennej, zapoznaj się z [dokumentacją śledzenia rozproszonego](spring-cloud-tutorial-distributed-tracing.md).

## <a name="see-also"></a>Zobacz też
* [Analizowanie dzienników i metryk aplikacji](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Konfigurowanie serwera konfiguracji](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Korzystanie z rozproszonego śledzenia w chmurze Azure wiosennej](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Przewodnik szybkiego startu](https://spring.io/quickstart)
* [Dokumentacja dotycząca rozruchu sprężynowego](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Następne kroki

W tym temacie opisano sposób konfigurowania aplikacji ze sprężyną Java na potrzeby wdrożenia w chmurze sieci platformy Azure. Aby dowiedzieć się, jak skonfigurować wystąpienie serwera konfiguracji, zobacz [Konfigurowanie wystąpienia serwera konfiguracji](spring-cloud-tutorial-config-server.md).

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end
