---
title: Szybki Start — wprowadzenie do przykładowej aplikacji — chmura Wiosenna platformy Azure
description: Zawiera opis przykładowej aplikacji używanej w tej serii przewodników szybki start dotyczących wdrażania w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90903580"
---
# <a name="introduction-to-the-sample-app"></a>Wprowadzenie do aplikacji przykładowej

::: zone pivot="programming-language-csharp"
Ta seria przewodników szybki start używa przykładowej aplikacji składającej się z dwóch mikrousług, aby dowiesz się, jak wdrożyć aplikację .NET Core steeltoe w usłudze w chmurze platformy Azure. Będziesz korzystać z możliwości chmury Azure wiosny, takich jak odnajdowanie usług, serwer konfiguracji, dzienniki, metryki i śledzenie rozproszone.

## <a name="functional-services"></a>Usługi funkcjonalne

Przykładowa aplikacja składa się z dwóch mikrousług:

* `planet-weather-provider`Usługa zwraca tekst Pogoda w odpowiedzi na żądanie HTTP, które określa nazwę globalnej. Na przykład może zwracać "bardzo grzane" dla rtęci globalnej. Pobiera dane pogody z serwera konfiguracji. Serwer konfiguracji pobiera dane pogody z pliku YAML w repozytorium git, na przykład:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather`Usługa zwraca dane z czterech planety w odpowiedzi na żądanie HTTP. Pobiera dane, tworząc cztery żądania HTTP `planet-weather-provider` . Używa usługi odnajdywania serwera Eureka do wywoływania `planet-weather-provider` . Zwraca kod JSON, na przykład:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Na poniższym diagramie przedstawiono przykładową architekturę aplikacji:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Przykładowy diagram aplikacji":::

## <a name="code-repository"></a>Repozytorium kodu

Przykładowa aplikacja znajduje się w folderze [steeltoe-](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) Samples for Azure-Samples/Azure-sprężyn-Cloud-Samples w witrynie GitHub.

Instrukcje w następujących przewodnikach szybki start odnoszą się do kodu źródłowego w zależności od potrzeby.

::: zone-end

::: zone pivot="programming-language-java"
W tym przewodniku szybki start użyjemy przykładowych finansów prywatnych o nazwie PiggyMetrics, aby dowiesz się, jak wdrożyć aplikację w usłudze w chmurze Azure wiosennej. PiggyMetrics demonstruje wzorzec architektury mikrousług i wyróżnia podział usług. Zobaczysz, w jaki sposób jest wdrażany na platformie Azure z zaawansowanymi możliwościami chmury Wiosnowej platformy Azure, w tym odnajdywania usług, serwera konfiguracji, dzienników, metryk i śledzenia rozproszonego.

Aby postępować zgodnie z przykładami wdrożenia chmury ze sprężyną na platformie Azure, potrzebujesz tylko lokalizacji kodu źródłowego, który jest dostarczany w razie potrzeby.

## <a name="functional-services"></a>Usługi funkcjonalne

PiggyMetrics jest rozłożony na trzy podstawowe mikrousługi. Wszystkie z nich są zależnymi do wdrożenia aplikacjami zorganizowanymi według domen firmy.

* **Usługa konta (do wdrożenia)**: zawiera ogólną logikę i sprawdzanie poprawności danych wejściowych użytkownika: wyniki i elementy wydatków, oszczędności i ustawienia konta.
* **Usługa statystyki (Nieużyta w tym przewodniku Szybki Start)**: wykonuje obliczenia dotyczące głównych parametrów statystyk i przechwytuje serie czasowe dla każdego konta. Punkt danych zawiera wartości, znormalizowany do podstawowej waluty i okresu. Te dane służą do śledzenia dynamiki przepływów pieniężnych w okresie istnienia konta.
* **Usługa powiadomień (Nieużyta w tym przewodniku Szybki Start)**: przechowuje informacje kontaktowe użytkowników i ustawienia powiadomień, takie jak Przypomnij i częstotliwość tworzenia kopii zapasowych. Zaplanowany proces roboczy zbiera wymagane informacje z innych usług i wysyła wiadomości e-mail do subskrybowanych klientów.

## <a name="infrastructure-services"></a>Usługi infrastruktury

W systemach rozproszonych istnieje kilka popularnych wzorców, które ułatwiają działanie usług podstawowych. Chmura sprężynowa platformy Azure oferuje zaawansowane narzędzia, które rozszerzają zachowanie aplikacji z rozruchem sprężynowym w celu wdrożenia tych wzorców: 

* **Usługa konfiguracji (hostowana w chmurze Azure wiosennej)**: Konfiguracja chmury sieci Azure ze sprężyną jest skalowalną w poziomie scentralizowaną usługą konfiguracji dla systemów rozproszonych. Używa repozytorium podłączane, które aktualnie obsługuje magazyn lokalny, Git i Subversion.
* **Odnajdowanie usług (hostowane w chmurze Azure wiosennej)**: umożliwia automatyczne wykrywanie lokalizacji sieciowych dla wystąpień usług, które mogą mieć dynamicznie przypisane adresy z powodu automatycznego skalowania, niepowodzeń i uaktualnień.
* **Usługa uwierzytelniania (do wdrożenia)** Obowiązki autoryzacji są całkowicie wyodrębniane na oddzielny serwer, który przyznaje tokeny OAuth2 dla usług zasobów zaplecza. Serwer uwierzytelniania wykonuje autoryzację użytkownika i bezpieczną komunikację między maszynami w obrębie obwodu.
* **Brama interfejsu API (do wdrożenia)**: trzy podstawowe usługi uwidaczniają zewnętrzny interfejs API dla klienta. W systemach rzeczywistych liczba funkcji może szybko rosnąć z złożonością systemu. Setki usług mogą być wykorzystywane do renderowania jednej złożonej strony sieci Web. Brama interfejsu API jest pojedynczym punktem wejścia do systemu, używanym do obsługi żądań i kierowania ich do odpowiedniej usługi zaplecza lub do wywołania wielu usług zaplecza, agregowania wyników. 

## <a name="sample-usage-of-piggymetrics"></a>Przykładowe użycie PiggyMetrics

Aby uzyskać szczegółowe informacje na temat pełnej implementacji, zobacz [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). Próbki odwołują się do kodu źródłowego zgodnie z wymaganiami.
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Udostępnianie wystąpienia chmury Azure wiosny](spring-cloud-quickstart-provision-service-instance.md)
