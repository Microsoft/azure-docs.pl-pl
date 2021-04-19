---
title: Jak przygotować aplikację do wdrożenia w programie Azure Spring Cloud
description: Dowiedz się, jak przygotować aplikację do wdrożenia w Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cabc4784dfb19f569212f4d0cb93e6838473e559
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714492"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Przygotowywanie aplikacji do wdrożenia w Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud zapewnia niezawodne usługi hostowania, monitorowania, skalowania i aktualizowania aplikacji Firmy Steeltoe. W tym artykule pokazano, jak przygotować istniejącą aplikację Zew do wdrożenia w Azure Spring Cloud. 

W tym artykule wyjaśniono zależności, konfigurację i kod, które są wymagane do uruchomienia aplikacji .NET Core Steeltoe w Azure Spring Cloud. Aby uzyskać informacje na temat sposobu wdrażania aplikacji w Azure Spring Cloud, zobacz Deploy your first Azure Spring Cloud application (Wdrażanie pierwszej [Azure Spring Cloud aplikacji).](spring-cloud-quickstart.md)

>[!Note]
> Obsługa techniczna dla Azure Spring Cloud jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty w publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalnym wydaniem.  Funkcje i usługi w publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji na temat obsługi w wersjach zapoznawczych, zobacz często zadawane pytania [lub](https://azure.microsoft.com/support/faq/) Support request [.](../azure-portal/supportability/how-to-create-azure-support-request.md)

##  <a name="supported-versions"></a>Obsługiwane wersje

Azure Spring Cloud obsługuje:

* .NET Core 3.1
* Steeltoe 2.4 i 3.0

## <a name="dependencies"></a>Zależności

W przypadku rozwiązania Steeltoe 2.4 dodaj najnowszy pakiet [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) do pliku projektu:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

W przypadku rozwiązania Steeltoe 3.0 dodaj najnowszy pakiet [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) do pliku projektu:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Aktualizowanie programu Program.cs

W `Program.Main` metodzie wywołaj `UseAzureSpringCloudService` metodę .

W przypadku steeltoe 2.4.4 wywołaj wywołanie po i `UseAzureSpringCloudService` `ConfigureWebHostDefaults` po, `AddConfigServer` jeśli zostanie wywołana:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

W przypadku oprogramowania Steeltoe 3.0.0 wywołaj przed i przed dowolnym kodem konfiguracji `UseAzureSpringCloudService` `ConfigureWebHostDefaults` Steeltoe:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Włączanie odnajdywania usług serwera Eureka

W źródle konfiguracji, które będzie używane, gdy aplikacja będzie uruchamiana w programie Azure Spring Cloud, ustaw na taką samą nazwę jak Azure Spring Cloud aplikacji, w której zostanie `spring.application.name` wdrożony projekt.

Na przykład w przypadku wdrożenia projektu .NET o nazwie w aplikacji Azure Spring Cloud o nazwieappSettings.jspliku powinien `EurekaDataProvider` `planet-weather-provider` zawierać następujący kod JSON: 

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Korzystanie z odnajdywania usług

Aby wywołać usługę przy użyciu odnajdywania usługi Serwera Eureka, wyślij żądania HTTP do miejsca, w którym jest `http://<app_name>` `app_name` `spring.application.name` wartością aplikacji docelowej. Na przykład następujący kod wywołuje `planet-weather-provider` usługę:

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
W tym temacie pokazano, jak przygotować istniejącą aplikację Java Spring do wdrożenia w Azure Spring Cloud. Jeśli konfiguracja jest prawidłowo skonfigurowana, Azure Spring Cloud niezawodne usługi do monitorowania, skalowania i aktualizowania aplikacji Java Spring Cloud aplikacji.

Przed uruchomieniem tego przykładu możesz wypróbować podstawowy [przewodnik Szybki start.](spring-cloud-quickstart.md)

Inne przykłady wyjaśniają sposób wdrażania aplikacji w Azure Spring Cloud podczas konfigurowania pliku POM. 
* [Uruchamianie pierwszej aplikacji](spring-cloud-quickstart.md)
* [Kompilowanie i uruchamianie mikrousług](spring-cloud-quickstart-sample-app-introduction.md)

W tym artykule opisano wymagane zależności i sposób ich dodawania do pliku POM.

## <a name="java-runtime-version"></a>Wersja środowiska uruchomieniowego Java

Tylko aplikacje Spring/Java mogą być uruchamiane w Azure Spring Cloud.

Azure Spring Cloud obsługuje zarówno środowisko Java 8, jak i Java 11. Środowisko hostingu zawiera najnowszą wersję zestawu OpenJDK Azul Zulu dla platformy Azure. Aby uzyskać więcej informacji na temat narzędzia Azul Zulu OpenJDK for Azure, zobacz [Install the JDK (Instalowanie pakietu JDK).](/azure/developer/java/fundamentals/java-jdk-install)

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot i Spring Cloud wersji

Aby przygotować istniejącą aplikację Spring Boot do wdrożenia w programie Azure Spring Cloud, uwzględnij zależności Spring Boot i Spring Cloud w pliku POM aplikacji, jak pokazano w poniższych sekcjach.

Azure Spring Cloud obsługuje Spring Boot wersji 2.2, 2.3, 2.4. W poniższej tabeli wymieniono obsługiwane Spring Boot i Spring Cloud kombinacji:

Spring Boot wersji | Spring Cloud wersji
---|---
2,2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1+ | 2020.0.0

> [!NOTE]
> Zidentyfikowaliśmy problem z protokołem Spring Boot 2.4.0 podczas uwierzytelniania TLS między aplikacjami i aplikacją Eureka. Użyj co najmniej 2.4.1. Zapoznaj się z naszymi często [zadawanymi](./spring-cloud-faq.md?pivots=programming-language-java#development) pytaniami, aby zapoznać się z obejściem problemu, jeśli nie chcesz używać programu 2.4.0.

### <a name="dependencies-for-spring-boot-version-2223"></a>Zależności dla Spring Boot wersji 2.2/2.3

Na Spring Boot wersji 2.2 dodaj następujące zależności do pliku POM aplikacji.

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

### <a name="dependencies-for-spring-boot-version-24"></a>Zależności dla Spring Boot wersji 2.4

Na Spring Boot wersji 2.2 dodaj następujące zależności do pliku POM aplikacji.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Nie określaj `server.port` w konfiguracji. Azure Spring Cloud przesłoni to ustawienie do stałego numeru portu. Należy również przestrzegać tego ustawienia i nie określać portu serwera w kodzie.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Inne zalecane zależności umożliwiające włączenie Azure Spring Cloud funkcji

Aby włączyć wbudowane funkcje usługi Azure Spring Cloud z rejestru usług do śledzenia rozproszonego, należy również uwzględnić w aplikacji następujące zależności. Niektóre z tych zależności można usunąć, jeśli nie potrzebujesz odpowiednich funkcji dla określonych aplikacji.

### <a name="service-registry"></a>Rejestr usług

Aby użyć zarządzanej usługi Azure Service Registry, uwzględnij zależność w `spring-cloud-starter-netflix-eureka-client` pom.xml, jak pokazano poniżej:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Punkt końcowy serwera usługi Service Registry jest automatycznie wstrzykiwany jako zmienne środowiskowe w aplikacji. Aplikacje mogą rejestrować się za pomocą serwera usługi Service Registry i odnajdywać inne zależne mikrousługi.


#### <a name="enablediscoveryclient-annotation"></a>Adnotacja EnableDiscoveryClient

Dodaj następującą adnotację do kodu źródłowego aplikacji.
```java
@EnableDiscoveryClient
```
Zobacz na przykład aplikację piggymetrics z wcześniejszych przykładów:
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

Aby włączyć konfigurację rozproszoną, uwzględnij następującą zależność w sekcji `spring-cloud-config-client` zależności pom.xml pliku:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nie należy określać `spring.cloud.config.enabled=false` w konfiguracji ładowania początkowego. W przeciwnym razie aplikacja przestanie działać z Config Server.

### <a name="metrics"></a>Metryki

Uwzględnij zależność w sekcji `spring-boot-starter-actuator` zależności pliku pom.xml, jak pokazano poniżej:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metryki są okresowo ściągane z punktów końcowych JMX. Metryki można wizualizować przy użyciu Azure Portal.

 > [!WARNING]
 > Określ wartość `spring.jmx.enabled=true` we właściwości konfiguracji. W przeciwnym razie metryk nie można wizualizować w Azure Portal.

### <a name="distributed-tracing"></a>Śledzenie rozproszone

Należy również włączyć usługę Azure Application Insights do pracy z wystąpieniem Azure Spring Cloud usługi. Aby uzyskać informacje na temat używania Application Insights z Azure Spring Cloud, zobacz dokumentację [dotyczącą śledzenia rozproszonego.](spring-cloud-tutorial-distributed-tracing.md)

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3
Uwzględnij następujące zależności i w sekcji `spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` zależności pom.xml pliku:

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

#### <a name="spring-boot-24"></a>Spring Boot 2.4
Uwzględnij `spring-cloud-sleuth-zipkin` następującą zależność w sekcji zależności pom.xml pliku:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Zobacz też
* [Analizowanie dzienników i metryk aplikacji](./diagnostic-services.md)
* [Konfigurowanie serwera konfiguracji](spring-cloud-tutorial-config-server.md)
* [Używanie śledzenia rozproszonego z Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Przewodnik Szybki start dotyczący programu Spring](https://spring.io/quickstart)
* [Spring Boot dokumentacji](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Następne kroki

W tym temacie opisano sposób konfigurowania aplikacji Java Spring do wdrażania w Azure Spring Cloud. Aby dowiedzieć się, jak skonfigurować Config Server, zobacz [Konfigurowanie Config Server wystąpienia](spring-cloud-tutorial-config-server.md).

Więcej przykładów można uzyskać w witrynie GitHub: [Azure Spring Cloud Przykłady](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end