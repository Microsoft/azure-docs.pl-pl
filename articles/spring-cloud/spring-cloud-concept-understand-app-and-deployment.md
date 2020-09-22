---
title: Informacje o aplikacji i wdrażaniu w chmurze Azure wiosennej
description: Rozróżnianie między aplikacjami i wdrażaniem w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9e909db0041979eb7bc4fc30bd9551382e83c488
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892506"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Informacje o aplikacji i wdrażaniu w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

**Aplikacje** i **wdrożenia** to dwie kluczowe koncepcje w modelu zasobów chmury wiosennej platformy Azure. W chmurze Azure wiosną *aplikacja* jest abstrakcją jednej aplikacji biznesowej lub jednej mikrousługi.  Jedna wersja kodu lub plik binarny wdrożony jako *aplikacja* jest uruchamiana w ramach *wdrożenia*.  Aplikacje są uruchamiane w *wystąpieniu usługi chmurowej Azure wiosny*lub po prostu *wystąpienie usługi*, jak pokazano Dalej.

 ![Aplikacje i wdrożenia](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

W ramach jednej subskrypcji platformy Azure można mieć wiele wystąpień usługi, ale najłatwiej używać usługi w chmurze Azure wiosennej, gdy wszystkie aplikacje wchodzące w skład aplikacji biznesowej lub mikrousługi znajdują się w jednym wystąpieniu usługi.

Usługa Azure wiosny Cloud w warstwie Standardowa umożliwia jednej aplikacji posiadanie jednego wdrożenia produkcyjnego i jednego wdrożenia przemieszczania, dzięki czemu można łatwo wdrożyć na nim niebieskie/zielone wdrożenie.

## <a name="app"></a>Aplikacja
Następujące funkcje/właściwości są zdefiniowane na poziomie aplikacji.

| Wyliczenie | Definicja |
|:--|:----------------|
| Public</br>Punkt końcowy | Adres URL dostępu do aplikacji |
| Niestandardowy</br>Domena | Rekord CNAME, który zabezpiecza domenę niestandardową |
| Usługa</br>Wiązanie | Właściwości konfiguracji powiązania ustawione w function.jsw pliku i atrybut *ServiceBusTrigger* |
| Zarządzani</br>Tożsamość | Tożsamość zarządzana przez Azure Active Directory umożliwia aplikacji łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault |
| Stale</br>Magazyn | Ustawienie, które umożliwia utrzymywanie danych poza ponownym uruchomieniem aplikacji |

## <a name="deployment"></a>Wdrożenie

Następujące funkcje/właściwości są zdefiniowane na poziomie wdrożenia i będą wymieniane podczas wymiany wdrożenia produkcyjnego/przejściowego.

| Wyliczenie | Definicja |
|:--|:----------------|
| Procesor CPU | Liczba rdzeni wirtualnych na wystąpienie aplikacji |
| Pamięć | Ustawienie przydzielenia pamięci do skalowania w górę lub w poziomie do wdrożenia |
| Wystąpienie</br>Liczba | Liczba wystąpień aplikacji, ustawiana ręcznie lub automatycznie. |
| Automatyczne skalowanie | Automatyczne skalowanie liczby wystąpień na podstawie wstępnie zdefiniowanych reguł i harmonogramów |
| JVM</br>Opcje | ustawienie: JAVA_OPTS |
| Środowisko</br>Zmienne | Ustawienia, które mają zastosowanie do całego środowiska chmury Azure wiosny |
| Środowisko uruchomieniowe</br>Wersja | Java 8/Java 11|

## <a name="restrictions"></a>Ograniczenia

* **Aplikacja musi mieć jedno wdrożenie produkcyjne**: usunięcie wdrożenia produkcyjnego jest blokowane przez interfejs API. Przed usunięciem należy zastąpić do przemieszczania.
* **Aplikacja może mieć co najwyżej dwa wdrożenia**: utworzenie więcej niż dwóch wdrożeń jest blokowane przez interfejs API. Wdróż nowy plik binarny w istniejącym wdrożeniu produkcyjnym lub przejściowym.
* **Zarządzanie wdrażaniem nie jest dostępne w warstwie Podstawowa**: Użyj warstwy Standardowa dla funkcji wdrażania Blue-Green.

## <a name="see-also"></a>Zobacz też
* [Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej](spring-cloud-howto-staging-environment.md)
