---
title: Informacje o aplikacji i wdrażaniu w chmurze Azure wiosennej
description: Rozróżnianie między aplikacjami i wdrażaniem w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: c053a7830e02eb7c460bd030ca3c6a10c00ea78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323641"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Informacje o aplikacji i wdrażaniu w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

**Aplikacje** i **wdrożenia** to dwie kluczowe koncepcje w modelu zasobów chmury wiosennej platformy Azure. W chmurze Azure wiosną *aplikacja* jest abstrakcją jednej aplikacji biznesowej lub jednej mikrousługi.  Jedna wersja kodu lub plik binarny wdrożony jako *aplikacja* jest uruchamiana w ramach *wdrożenia*.  Aplikacje są uruchamiane w *wystąpieniu usługi chmurowej Azure wiosny*lub po prostu *wystąpienie usługi*, jak pokazano Dalej.

 ![Aplikacje i wdrożenia](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

W ramach jednej subskrypcji platformy Azure można mieć wiele wystąpień usługi, ale najłatwiej używać usługi w chmurze Azure wiosennej, gdy wszystkie aplikacje wchodzące w skład aplikacji biznesowej lub mikrousługi znajdują się w jednym wystąpieniu usługi.

Usługa Azure wiosny Cloud w warstwie Standardowa umożliwia jednej aplikacji posiadanie jednego wdrożenia produkcyjnego i jednego wdrożenia przemieszczania, dzięki czemu można łatwo wdrożyć na nim niebieskie/zielone wdrożenie.

## <a name="app"></a>Aplikacja
Następujące funkcje/właściwości są zdefiniowane na poziomie aplikacji.

| Funkcje | Opis |
|:--|:----------------|
| Public</br>Punkt końcowy | Adres URL dostępu do aplikacji |
| Niestandardowy</br>Obszar | Rekord CNAME, który zabezpiecza domenę niestandardową |
| Usługa</br>Wiązanie | Połączenie z innymi usługami platformy Azure |
| Zarządzani</br>Tożsamość | Tożsamość zarządzana przez Azure Active Directory umożliwia aplikacji łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault |
| Stale</br>Magazyn | Ustawienie, które umożliwia utrzymywanie danych poza ponownym uruchomieniem aplikacji |

## <a name="deployment"></a>Wdrożenie

Następujące funkcje/właściwości są zdefiniowane na poziomie wdrożenia i będą wymieniane podczas wymiany wdrożenia produkcyjnego/przejściowego.

| Funkcje | Opis |
|:--|:----------------|
| CPU | Liczba rdzeni wirtualnych na wystąpienie aplikacji |
| Pamięć | GB pamięci na wystąpienie aplikacji|
| Wystąpienie</br>Liczba | Liczba wystąpień aplikacji, ustawiana ręcznie lub automatycznie. |
| Automatyczne skalowanie | Automatyczne skalowanie liczby wystąpień na podstawie wstępnie zdefiniowanych reguł i harmonogramów |
| JVM</br>Opcje | Ustawianie opcji JVM  |
| Środowisko</br>Zmienne | Ustawianie zmiennych środowiskowych |
| Środowisko uruchomieniowe</br>Wersja | Java 8/Java 11|

## <a name="restrictions"></a>Ograniczenia

* **Aplikacja musi mieć jedno wdrożenie produkcyjne**: usunięcie wdrożenia produkcyjnego jest blokowane przez interfejs API. Przed usunięciem należy zastąpić do przemieszczania.
* **Aplikacja może mieć co najwyżej dwa wdrożenia**: utworzenie więcej niż dwóch wdrożeń jest blokowane przez interfejs API. Wdróż nowy plik binarny w istniejącym wdrożeniu produkcyjnym lub przejściowym.
* **Zarządzanie wdrażaniem nie jest dostępne w warstwie Podstawowa**: Użyj warstwy Standardowa na potrzeby możliwości wdrażania Blue-Green.

## <a name="see-also"></a>Zobacz też
* [Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej](spring-cloud-howto-staging-environment.md)
