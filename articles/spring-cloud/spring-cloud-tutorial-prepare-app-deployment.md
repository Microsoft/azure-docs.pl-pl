---
title: Instrukcje — przygotowanie aplikacji Java Spring do wdrożenia w chmurze Azure Spring Cloud
description: W tym temacie należy przygotować aplikację Java Spring do wdrożenia w usłudze Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 16cee333d52765755b732c4de4dd8a6e092a130d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731177"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Przygotowanie aplikacji Java Spring do wdrożenia w chmurze Azure Spring Cloud

W tym temacie pokazano, jak przygotować istniejącą aplikację Java Spring do wdrożenia w usłudze Azure Spring Cloud. Jeśli usługa Azure Spring Cloud jest poprawnie skonfigurowana, zapewnia niezawodne usługi monitorowania, skalowania i aktualizowania aplikacji Java Spring Cloud.

Inne przykłady wyjaśniają, jak wdrożyć aplikację w usłudze Azure Spring Cloud, gdy plik POM jest skonfigurowany. 
* [Uruchamianie aplikacji za pomocą portalu Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Uruchamianie aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart-launch-app-cli.md)

W tym artykule wyjaśniono wymagane zależności i jak dodać je do pliku POM.

## <a name="java-runtime-version"></a>Wersja java runtime

Tylko aplikacje Wiosna/Java mogą działać w usłudze Azure Spring Cloud.

Usługa Azure Spring Cloud obsługuje zarówno oprogramowanie Java 8, jak i Java 11. Środowisko hostingowe zawiera najnowszą wersję programu Azul Zulu OpenJDK for Azure. Aby uzyskać więcej informacji na temat Azul Zulu OpenJDK for Azure, zobacz [Instalowanie pliku JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Wersje Spring Boot i Spring Cloud

Aby przygotować istniejącą aplikację Spring Boot do wdrożenia w usłudze Azure Spring Cloud, uwzględnij zależności spring boot i spring cloud w pliku POM aplikacji, jak pokazano w poniższych sekcjach.

Usługa Azure Spring Cloud obsługuje tylko aplikacje Spring Boot w wersji 2.1 lub 2.2. W poniższej tabeli wymieniono obsługiwane kombinacje Spring Boot i Spring Cloud:

Wersja Spring Boot | Wersja Spring Cloud
---|---
2.1 | Greenwich.RELEASE (WYDANIE)
2.2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Zależności dla spring boot w wersji 2.1

W przypadku rozruchu wiosennego w wersji 2.1 dodaj następujące zależności do pliku POM aplikacji.

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
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Zależności dla spring boot w wersji 2.2

W przypadku rozruchu wiosennego w wersji 2.2 dodaj następujące zależności do pliku POM aplikacji.

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
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Zależność klienta chmury usługi Azure Spring Cloud

Usługa Azure Spring Cloud obsługuje składniki spring cloud i zarządza nimi. Składniki obejmują Spring Cloud Service Registry i Spring Cloud Config Server. Dołącz bibliotekę klienta usługi Azure Spring Cloud do zależności, aby umożliwić komunikację z wystąpieniem usługi Azure Spring Cloud.

W poniższej tabeli wymieniono poprawne wersje usługi Azure Spring Cloud dla aplikacji korzystającej z spring boot i spring cloud.

Wersja Spring Boot | Wersja Spring Cloud | Wersja usługi Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE (WYDANIE) | 2.1
2.2 | Hoxton.RELEASE | 2.2

Dołącz jedną z następujących zależności w pliku pom.xml. Wybierz zależność, której wersja usługi Azure Spring Cloud jest zgodna z Twoją własną.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Zależność dla usługi Azure Spring Cloud w wersji 2.1

W przypadku rozruchu wiosennego w wersji 2.1 dodaj następującą zależność do pliku POM aplikacji.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Zależność dla usługi Azure Spring Cloud w wersji 2.2

W przypadku rozruchu wiosennego w wersji 2.2 dodaj następującą zależność do pliku POM aplikacji.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Inne wymagane zależności

Aby włączyć wbudowane funkcje usługi Azure Spring Cloud, aplikacja musi zawierać następujące zależności. To włączenie gwarantuje, że aplikacja konfiguruje się poprawnie z każdym składnikiem.

### <a name="enablediscoveryclient-annotation"></a>Adnotacja EnableDiscoveryClient

Dodaj następującą adnotację do kodu źródłowego aplikacji.
```java
@EnableDiscoveryClient
```
Na przykład zobacz piggymetrics aplikacji z wcześniejszych przykładów:
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

### <a name="service-registry-dependency"></a>Zależność rejestru usług

Aby korzystać z zarządzanej usługi `spring-cloud-starter-netflix-eureka-client` Azure Service Registry, należy uwzględnić zależność w pliku pom.xml, jak pokazano poniżej:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Punkt końcowy serwera rejestru usług jest automatycznie wstrzykiwany jako zmienne środowiskowe z aplikacją. Aplikacje mogą zarejestrować się na serwerze rejestru usług i odnajdywać inne mikrousług zależne.

### <a name="distributed-configuration-dependency"></a>Zależność konfiguracji rozproszonej

Aby włączyć konfigurację rozproszoną, dołącz następujące `spring-cloud-config-client` zależności w sekcji zależności pliku pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nie określaj `spring.cloud.config.enabled=false` w konfiguracji bootstrap. W przeciwnym razie aplikacja przestanie działać z config server.

### <a name="metrics-dependency"></a>Zależność metryk

Uwzględnij `spring-boot-starter-actuator` zależność w sekcji zależności pliku pom.xml, jak pokazano poniżej:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metryki są okresowo pobierane z punktów końcowych JMX. Metryki można wizualizować za pomocą witryny Azure portal.

### <a name="distributed-tracing-dependency"></a>Zależność śledzenia rozproszonego

Uwzględnij `spring-cloud-starter-sleuth` następujące `spring-cloud-starter-zipkin` zależności w sekcji zależności pliku pom.xml:

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

 Należy również włączyć wystąpienie usługi Azure Application Insights do pracy z wystąpieniem usługi Azure Spring Cloud. Przeczytaj [samouczek na temat śledzenia rozproszonego,](spring-cloud-tutorial-distributed-tracing.md) aby dowiedzieć się, jak korzystać z usługi Application Insights w usłudze Azure Spring Cloud.

## <a name="see-also"></a>Zobacz też
* [Analizowanie dzienników i metryk aplikacji](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Konfigurowanie serwera konfiguracji](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Korzystanie z śledzenia rozproszonego w usłudze Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Przewodnik szybki start sprężyny](https://spring.io/quickstart)
* [Dokumentacja rozruchu sprężynowego](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Następne kroki

W tym temacie dowiesz się, jak skonfigurować aplikację Java Spring do wdrożenia w usłudze Azure Spring Cloud. Aby dowiedzieć się, jak skonfigurować wystąpienie serwera config, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować wystąpienie serwera config](spring-cloud-tutorial-config-server.md)

Więcej przykładów jest dostępnych w usłudze GitHub: [Samples w chmurze w chmurze Azure Spring.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
