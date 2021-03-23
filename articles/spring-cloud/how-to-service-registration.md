---
title: Automatyzowanie rejestru i odnajdywania usługi
description: Dowiedz się, jak zautomatyzować odnajdywanie i rejestrację usług przy użyciu rejestru wiosennej usługi w chmurze
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e2ad6d8e3d81497eb7ebe612d01e60415790faea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878665"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Odnajdywanie i rejestrowanie usług w chmurze wiosny

Odnajdowanie usług to kluczowy wymóg dla architektury opartej na mikrousługach.  Skonfigurowanie każdego klienta ręcznie zabiera czas i stanowi zagrożenie dla człowieka.  Rejestr usługi w chmurze Azure wiosennej rozwiązuje ten problem.  Po skonfigurowaniu serwer rejestru usługi będzie kontrolować rejestrację i odnajdywanie usługi dla mikrousług aplikacji. Serwer rejestru usługi przechowuje rejestr wdrożonych mikrousług, umożliwia Równoważenie obciążenia po stronie klienta i oddziela dostawców usług od klientów bez konieczności korzystania z systemu DNS.

::: zone pivot="programming-language-csharp"
Aby uzyskać informacje o konfigurowaniu rejestracji usługi dla aplikacji steeltoe, zobacz [Przygotowywanie aplikacji Java wiosny do wdrożenia w chmurze Azure wiosennej](how-to-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Rejestrowanie aplikacji przy użyciu rejestru wiosennej usługi w chmurze

Aby aplikacja mogła zarządzać rejestracją i odnajdywaniem usługi przy użyciu rejestru wiosennej usługi w chmurze, w pliku *pom.xml* aplikacji musi być uwzględnionych kilka zależności.
Uwzględnij zależności dla sieci *wiosny-Cloud-Starter-Netflix-Eureka-Client* i *sprężyny-Cloud-Starter-Azure-sprężyny-Cloud-Client* do *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Aktualizowanie klasy najwyższego poziomu

Na koniec dodajemy adnotację do klasy najwyższego poziomu aplikacji

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Punkt końcowy serwera rejestru usługi w chmurze wiosny zostanie dodany jako zmienna środowiskowa w aplikacji.  Mikrousługi będą teraz mogły zarejestrować się na serwerze rejestru usługi i wykryć inne zależne mikrousługi.

Należy pamiętać, że może upłynąć kilka minut, zanim zmiany są propagowane z serwera do wszystkich mikrousług.
::: zone-end
