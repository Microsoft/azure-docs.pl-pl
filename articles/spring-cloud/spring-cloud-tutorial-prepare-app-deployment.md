---
title: Jak przygotować aplikację ze sprężyną Java do wdrożenia w chmurze Azure wiosennej
description: Dowiedz się, jak przygotować aplikację ze sprężyną Java do wdrożenia w chmurze z systemem Azure wiosną.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2ae94da3d9b2dee62bc031c4a32d17b43be00a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021277"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Przygotowywanie aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej

W tym temacie przedstawiono sposób przygotowania istniejącej aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej. W przypadku poprawnego skonfigurowania chmurowa usługa Azure wiosenna zapewnia niezawodne usługi do monitorowania, skalowania i aktualizowania aplikacji w chmurze ze sprężyną Java.

Inne przykłady wyjaśniają sposób wdrażania aplikacji w chmurze Azure sprężynowej po skonfigurowaniu pliku pliku pom. 
* [Uruchom aplikację przy użyciu Azure Portal](spring-cloud-quickstart-launch-app-portal.md)
* [Uruchamianie aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart-launch-app-cli.md)

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
2.2 | Hoxton. RELEASE
2.3 | Hoxton.SR5

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
                <version>Greenwich.SR4</version>
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
                <version>Hoxton.SR1</version>
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
                <version>Hoxton.SR5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Zależność klienta chmury ze sprężyną Azure

Usługa Azure wiosny Cloud hostuje i zarządza składnikami chmury Wiosnowej. Składniki obejmują rejestr usługi w chmurze ze sprężyną i wiosną serwerów konfiguracji chmury. Uwzględnij w zależnościach bibliotekę kliencką chmury platformy Azure, aby umożliwić komunikację z wystąpieniem usługi w chmurze ze sprężyną Azure.

W poniższej tabeli wymieniono prawidłowe wersje chmurowe platformy Azure dla aplikacji, które korzystają z chmury rozruchowej i sprężyny.

Wersja sprężyny rozruchowej | Wersja chmury wiosennej | Wersja chmury wiosennej platformy Azure
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2.2 | Hoxton. RELEASE | 2.2
2.3 | Hoxton.SR5 | 2.3

Uwzględnij jedną z następujących zależności w pliku pom.xml. Wybierz zależność, której wersja chmury sieci Azure ze sprężyną jest zgodna z własnymi.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Zależność dla chmury wiosennej platformy Azure w wersji 2,1

W przypadku rozruchu sprężynowego w wersji 2,1 Dodaj następujący zależność do pliku pliku pom aplikacji.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Zależność dla chmury wiosennej platformy Azure w wersji 2,2

W przypadku rozruchu sprężynowego w wersji 2,2 Dodaj następujący zależność do pliku pliku pom aplikacji.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.1</version>
</dependency>
```

W przypadku rozruchu sprężynowego w wersji 2,3 Dodaj następujący zależność do pliku pliku pom aplikacji.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.3.0</version>
</dependency>
```

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
