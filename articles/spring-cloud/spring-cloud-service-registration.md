---
title: Automatyzowanie rejestru i odnajdywania usługi
description: Dowiedz się, jak zautomatyzować odnajdywanie i rejestrację usług przy użyciu rejestru wiosennej usługi w chmurze
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278856"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Odnajdywanie i rejestrowanie usług w chmurze wiosny

Odnajdowanie usług to kluczowy wymóg dla architektury opartej na mikrousługach.  Skonfigurowanie każdego klienta ręcznie zabiera czas i stanowi zagrożenie dla człowieka.  Rejestr usługi w chmurze Azure wiosennej rozwiązuje ten problem.  Po skonfigurowaniu serwer rejestru usługi będzie kontrolować rejestrację i odnajdywanie usługi dla mikrousług aplikacji. Serwer rejestru usługi przechowuje rejestr wdrożonych mikrousług, umożliwia Równoważenie obciążenia po stronie klienta i oddziela dostawców usług od klientów bez konieczności korzystania z systemu DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Rejestrowanie aplikacji przy użyciu rejestru wiosennej usługi w chmurze

Aby aplikacja mogła zarządzać rejestracją i odnajdywaniem usługi przy użyciu rejestru wiosennej usługi w chmurze, w pliku *pliku pom. XML* aplikacji musi być uwzględnionych kilka zależności.
Uwzględnij zależności dla sieci *wiosny-Cloud-Starter-Netflix-Eureka-Client* i *sprężyny-Cloud-Starter-Azure-sprężyny-Cloud-Client* z *pliku pom. XML*

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
