---
title: Szybki Start — wprowadzenie do metryk Piggy przykładowej aplikacji — chmura Wiosenna platformy Azure
description: Zawiera opis przykładowej aplikacji Piggy Metrics używanej w chmurze Azure wiosny.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d833c8f136a71d563ce10240f03e2c68e9131687
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951894"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Wprowadzenie do przykładowej aplikacji Piggy Metrics

W tym przewodniku szybki start będziemy używać przykładowych rodzajów finansów z weryfikacją osobistą o nazwie Piggy Metrics, aby dowiesz się, jak wdrożyć aplikację w usłudze w chmurze Azure wiosennej. Piggy Metrics ilustruje wzorzec architektury mikrousług, a w poniższych sekcjach wyróżniono podział usług. Zobaczysz, w jaki sposób jest wdrożony na platformie Azure, dzięki zaawansowanemu funkcjom chmury platformy Azure ze sprężyny za pomocą funkcji odnajdowania usług, konfigurowania serwera do rejestrowania, metryk i śledzenia rozproszonego.

Aby postępować zgodnie z przykładami wdrożenia chmury ze sprężyną na platformie Azure, potrzebujesz tylko lokalizacji kodu źródłowego, który jest dostarczany w razie potrzeby.

## <a name="functional-services"></a>Usługi funkcjonalne
Metryki Piggy są rozłożone na trzy podstawowe mikrousługi. Wszystkie z nich są zależne od siebie aplikacje, zorganizowane wokół domen firmowych.

* **Usługa konta (do wdrożenia)**: zawiera ogólną logikę i sprawdzanie poprawności danych wejściowych użytkownika: wyniki i elementy wydatków, oszczędności i ustawienia konta.
* **Usługa statystyki (Nieużyta w tym przewodniku Szybki Start)**: wykonuje obliczenia dotyczące głównych parametrów statystyk i przechwytuje serie czasowe dla każdego konta. Punkt danych zawiera wartości, znormalizowany do podstawowej waluty i okresu. Te dane służą do śledzenia dynamiki przepływów pieniężnych w okresie istnienia konta.
* **Usługa powiadomień (Nieużyta w tym przewodniku Szybki Start)**: przechowuje informacje kontaktowe użytkowników i ustawienia powiadomień, takie jak Przypomnij i częstotliwość tworzenia kopii zapasowych. Zaplanowany proces roboczy zbiera wymagane informacje z innych usług i wysyła wiadomości e-mail do subskrybowanych klientów.

## <a name="infrastructure-services"></a>Usługi infrastruktury
W systemach rozproszonych istnieje kilka popularnych wzorców, które ułatwiają działanie usług podstawowych. Chmura sprężynowa platformy Azure oferuje zaawansowane narzędzia, które rozszerzają zachowanie aplikacji z rozruchem sprężynowym w celu wdrożenia tych wzorców: 

* **Usługa konfiguracji (hostowana w chmurze Azure wiosennej)**: Konfiguracja chmury sieci Azure ze sprężyną jest skalowalną w poziomie scentralizowaną usługą konfiguracji dla systemów rozproszonych. Używa repozytorium podłączane, które aktualnie obsługuje magazyn lokalny, Git i Subversion.
* **Odnajdowanie usług (hostowane w chmurze Azure wiosennej)**: umożliwia automatyczne wykrywanie lokalizacji sieciowych dla wystąpień usług, które mogą mieć dynamicznie przypisane adresy z powodu automatycznego skalowania, niepowodzeń i uaktualnień.
* **Usługa uwierzytelniania (zostanie wdrożona)** Obowiązki autoryzacji są całkowicie wyodrębniane na oddzielny serwer, który przyznaje tokeny OAuth2 dla usług zasobów zaplecza. Serwer uwierzytelniania wykonuje autoryzację użytkownika i bezpieczną komunikację między maszynami w obrębie obwodu.
* **Brama interfejsu API (zostanie wdrożona)**: trzy podstawowe usługi uwidaczniają zewnętrzny interfejs API dla klienta. W systemach rzeczywistych liczba funkcji może szybko rosnąć z złożonością systemu. Setki usług mogą być wykorzystywane do renderowania jednej złożonej strony sieci Web. Brama interfejsu API jest pojedynczym punktem wejścia do systemu, używanym do obsługi żądań i kierowania ich do odpowiedniej usługi zaplecza lub do wywołania wielu usług zaplecza, agregowania wyników. 

## <a name="sample-usage-of-piggy-metrics"></a>Przykładowe użycie metryk Piggy
Aby uzyskać pełne szczegóły implementacji, zobacz [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics). Próbki odwołują się do kodu źródłowego zgodnie z wymaganiami.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Udostępnianie wystąpienia chmury Azure wiosny](spring-cloud-quickstart-provision-service-instance.md)
