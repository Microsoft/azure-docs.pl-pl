---
title: Szybki Start — wprowadzenie do metryk Piggy przykładowej aplikacji — chmura Wiosenna platformy Azure
description: Zawiera opis przykładowej aplikacji Piggy Metrics używanej w chmurze Azure wiosny.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046786"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Wprowadzenie do przykładowej aplikacji Piggy Metrics

W tym przewodniku szybki start używamy przykładowych finansów prywatnych o nazwie Piggy Metrics, aby dowiesz się, jak wdrożyć aplikację w usłudze w chmurze Azure wiosennej. Piggy Metrics ilustruje wzorzec architektury mikrousług i wyróżnia podział usług. Zobaczysz, w jaki sposób jest wdrażany na platformie Azure z zaawansowanymi możliwościami chmury Wiosnowej platformy Azure, w tym odnajdywania usług, serwera konfiguracji, dzienników, metryk i śledzenia rozproszonego.

Aby postępować zgodnie z przykładami wdrożenia chmury ze sprężyną na platformie Azure, potrzebujesz tylko lokalizacji kodu źródłowego, który jest dostarczany w razie potrzeby.

## <a name="functional-services"></a>Usługi funkcjonalne
Metryki Piggy są rozłożone na trzy podstawowe mikrousługi. Wszystkie z nich są zależnymi do wdrożenia aplikacjami zorganizowanymi według domen firmy.

* **Usługa konta (do wdrożenia)**: zawiera ogólną logikę i sprawdzanie poprawności danych wejściowych użytkownika: wyniki i elementy wydatków, oszczędności i ustawienia konta.
* **Usługa statystyki (Nieużyta w tym przewodniku Szybki Start)**: wykonuje obliczenia dotyczące głównych parametrów statystyk i przechwytuje serie czasowe dla każdego konta. Punkt danych zawiera wartości, znormalizowany do podstawowej waluty i okresu. Te dane służą do śledzenia dynamiki przepływów pieniężnych w okresie istnienia konta.
* **Usługa powiadomień (Nieużyta w tym przewodniku Szybki Start)**: przechowuje informacje kontaktowe użytkowników i ustawienia powiadomień, takie jak Przypomnij i częstotliwość tworzenia kopii zapasowych. Zaplanowany proces roboczy zbiera wymagane informacje z innych usług i wysyła wiadomości e-mail do subskrybowanych klientów.

## <a name="infrastructure-services"></a>Usługi infrastruktury
W systemach rozproszonych istnieje kilka popularnych wzorców, które ułatwiają działanie usług podstawowych. Chmura sprężynowa platformy Azure oferuje zaawansowane narzędzia, które rozszerzają zachowanie aplikacji z rozruchem sprężynowym w celu wdrożenia tych wzorców: 

* **Usługa konfiguracji (hostowana w chmurze Azure wiosennej)**: Konfiguracja chmury sieci Azure ze sprężyną jest skalowalną w poziomie scentralizowaną usługą konfiguracji dla systemów rozproszonych. Używa repozytorium podłączane, które aktualnie obsługuje magazyn lokalny, Git i Subversion.
* **Odnajdowanie usług (hostowane w chmurze Azure wiosennej)**: umożliwia automatyczne wykrywanie lokalizacji sieciowych dla wystąpień usług, które mogą mieć dynamicznie przypisane adresy z powodu automatycznego skalowania, niepowodzeń i uaktualnień.
* **Usługa uwierzytelniania (do wdrożenia)** Obowiązki autoryzacji są całkowicie wyodrębniane na oddzielny serwer, który przyznaje tokeny OAuth2 dla usług zasobów zaplecza. Serwer uwierzytelniania wykonuje autoryzację użytkownika i bezpieczną komunikację między maszynami w obrębie obwodu.
* **Brama interfejsu API (do wdrożenia)**: trzy podstawowe usługi uwidaczniają zewnętrzny interfejs API dla klienta. W systemach rzeczywistych liczba funkcji może szybko rosnąć z złożonością systemu. Setki usług mogą być wykorzystywane do renderowania jednej złożonej strony sieci Web. Brama interfejsu API jest pojedynczym punktem wejścia do systemu, używanym do obsługi żądań i kierowania ich do odpowiedniej usługi zaplecza lub do wywołania wielu usług zaplecza, agregowania wyników. 

## <a name="sample-usage-of-piggy-metrics"></a>Przykładowe użycie metryk Piggy
Aby uzyskać pełne szczegóły implementacji, zobacz [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics). Próbki odwołują się do kodu źródłowego zgodnie z wymaganiami.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Udostępnianie wystąpienia chmury Azure wiosny](spring-cloud-quickstart-provision-service-instance.md)
