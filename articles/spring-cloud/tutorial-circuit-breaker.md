---
title: Samouczek — korzystanie z pulpitu nawigacyjnego wyłącznika ze sprężyną Azure
description: Dowiedz się, jak używać pulpitu nawigacyjnego wyłącznika z chmurą usługi Azure wiosną.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9fbd137f8fa36a7b0526b25d664fceac795ecd81
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879208"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Samouczek: korzystanie z pulpitu nawigacyjnego wyłącznika z chmurą Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java

[Turbina Netflix Cloud w chmurze](https://github.com/Netflix/Turbine) jest szeroko używana do agregowania wielu strumieni metryk [Hystrix](https://github.com/Netflix/Hystrix) , dzięki czemu strumienie mogą być monitorowane w jednym widoku przy użyciu pulpitu nawigacyjnego Hystrix. W tym samouczku pokazano, jak używać ich w chmurze Azure wiosennej.
> [!NOTE]
> Netflix Hystrix jest szeroko używany w wielu istniejących aplikacjach w chmurze, ale nie jest już aktywnym programowaniem. W przypadku tworzenia nowego projektu należy użyć zamiast nich aplikacji wyłącznika, takich jak [resilience4j](https://github.com/resilience4j/resilience4j). Inaczej niż w przypadku platformy turbin pokazanej w tym samouczku, Nowa struktura wyłącznika chmurowego sprężyny łączy wszystkie implementacje potoku danych metryk w Micrometer. Nadal pracujemy nad obsługą micrometer w chmurze Azure wiosennej, dlatego nie będzie ona objęta tym samouczkiem.

## <a name="prepare-your-sample-applications"></a>Przygotowywanie przykładowych aplikacji
Przykład jest rozwidleniem z tego [repozytorium](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine).

Klonuj repozytorium przykładowe do środowiska projektowego:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Utwórz 3 aplikacje, które będą używane w tym samouczku:
* Użytkownik-usługa: Prosta usługa REST z pojedynczym punktem końcowym/Personalized/{ID}
* zalecenie-usługa: Prosta usługa REST z pojedynczym punktem końcowym/Recommendations, która będzie wywoływana przez usługę User-Service.
* Hystrix-turbin: usługa pulpitu nawigacyjnego Hystrix do wyświetlania strumieni Hystrix i usługi turbinowej agregowania strumienia metryk Hystrix z innych usług.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Inicjowanie obsługi administracyjnej wystąpienia chmury Azure wiosny
Postępuj zgodnie z procedurą, aby [zainicjować obsługę wystąpienia usługi w interfejsie wiersza polecenia platformy Azure](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Wdrażanie aplikacji w chmurze Azure wiosennej
Te aplikacje nie korzystają z **serwera konfiguracji**, więc nie ma potrzeby konfigurowania **serwera konfiguracji** dla chmury wiosennej platformy Azure.  Utwórz i Wdróż w następujący sposób:
```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Weryfikowanie aplikacji
Gdy wszystkie aplikacje są uruchomione i wykrywalne, uzyskaj dostęp do `user-service` ścieżki https:// <username> -User-Service.azuremicroservices.IO/Personalized/1 z przeglądarki. Jeśli użytkownik może uzyskać dostęp do usługi `recommendation-service` , należy uzyskać następujące dane wyjściowe. Odśwież stronę sieci Web kilka razy, jeśli nie działa.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Dostęp do Hystrix pulpitu nawigacyjnego i metryki
Sprawdź przy użyciu publicznych punktów końcowych lub prywatnych punktów końcowych testów.

### <a name="using-public-endpoints"></a>Używanie publicznych punktów końcowych
Dostęp do Hystrix-turbiny ze ścieżką `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` z przeglądarki.  Na poniższej ilustracji przedstawiono pulpit nawigacyjny Hystrix uruchomiony w tej aplikacji.

![Pulpit nawigacyjny Hystrix](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Skopiuj adres URL strumienia turbiny `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` do pola tekstowego, a następnie kliknij pozycję **Monitoruj strumień**.  Spowoduje to wyświetlenie pulpitu nawigacyjnego. Jeśli w podglądzie nie ma niczego, naciśnij `user-service` punkty końcowe, aby wygenerować strumienie.

![Strumień Hystrix ](media/spring-cloud-circuit-breaker/hystrix-stream.png) teraz można eksperymentować z pulpitem nawigacyjnym wyłącznika.
> [!NOTE] 
> W środowisku produkcyjnym nie należy ujawniać w Internecie strumienia Hystrix i metryk.

### <a name="using-private-test-endpoints"></a>Korzystanie z punktów końcowych testów prywatnych
Strumienie metryk Hystrix są również dostępne z programu `test-endpoint` . Jako usługa zaplecza nie przypisała publicznego punktu końcowego dla `recommendation-service` , ale możemy wyświetlić metryki z punktem końcowym test-at `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Test Hystrix — strumień punktu końcowego](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Jako aplikacja internetowa Hystrix pulpit nawigacyjny powinien działać na `test-endpoint` . Jeśli nie działa prawidłowo, może być dwa powody: najpierw przy użyciu `test-endpoint` zmienionego podstawowego adresu URL z `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` , lub sekund, aplikacja sieci Web używa ścieżki bezwzględnej dla zasobu statycznego. Aby pracować nad tym `test-endpoint` , może być konieczne ręczne edytowanie <base> w plikach frontonu.

## <a name="next-steps"></a>Następne kroki
* [Inicjowanie obsługi administracyjnej wystąpienia usługi w interfejsie wiersza polecenia platformy Azure](spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Przygotowywanie aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej](how-to-prepare-app-deployment.md)

