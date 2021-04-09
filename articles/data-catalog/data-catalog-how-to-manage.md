---
title: Zarządzanie zasobami danych w Azure Data Catalog
description: W tym artykule opisano sposób kontrolowania widoczności i własności zasobów danych zarejestrowanych w Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 0182f744e038bc8080b9fb96bfdb1eeda16b86c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674856"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Zarządzanie zasobami danych w Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie
Azure Data Catalog jest przeznaczony do odnajdowania źródeł danych, dzięki czemu można łatwo odnajdywać i zrozumieć źródła danych potrzebne do przeprowadzenia analizy i podejmowania decyzji. Te funkcje odnajdywania zapewniają największy wpływ, gdy ty i inni użytkownicy będą mogli znaleźć i zrozumieć największy zakres dostępnych źródeł danych. Z tymi elementami należy pamiętać, że domyślne zachowanie Data Catalog dotyczy wszystkich zarejestrowanych źródeł danych, które mają być widoczne i wykrywalne przez wszystkich użytkowników wykazu.

Data Catalog nie zapewnia dostępu do samych danych. Dostęp do danych jest kontrolowany przez właściciela źródła danych. Za pomocą Data Catalog można odnajdywać źródła danych i wyświetlać metadane powiązane ze źródłami zarejestrowanymi w katalogu.

Mogą jednak wystąpić sytuacje, w których źródła danych powinny być widoczne tylko dla określonych użytkowników lub do członków określonych grup. W takich scenariuszach użytkownicy mogą przejąć własność zarejestrowanych zasobów danych w wykazie, a następnie kontrolować widoczność posiadanych zasobów.

> [!NOTE]
> Funkcje opisane w tym artykule są dostępne tylko w wersji Standard programu Azure Data Catalog. Wersja bezpłatna nie zapewnia możliwości własności i ogranicza widoczność zasobów danych.
>
>

## <a name="manage-ownership-of-data-assets"></a>Zarządzanie własnością zasobów danych
Domyślnie zasoby danych, które są zarejestrowane w Data Catalog są niewłasnością. Każdy użytkownik z uprawnieniami dostępu do katalogu może odnaleźć te elementy i dodać do nich adnotacje. Użytkownicy mogą przejmować własność nienależących do siebie zasobów danych, a następnie ograniczyć widoczność posiadanych zasobów.

Gdy zasób danych w Data Catalog jest własnością, tylko użytkownicy autoryzowani przez właścicieli mogą odnajdywać zasoby i wyświetlać swoje metadane, a tylko właściciele mogą usunąć element zawartości z wykazu.

> [!NOTE]
> Własność w Data Catalog dotyczy tylko metadanych przechowywanych w katalogu. Własność nie przyznaje żadnych uprawnień do bazowego źródła danych.
>
>

### <a name="take-ownership"></a>Przejęcie na własność
Użytkownicy mogą przejąć własność zasobów danych, wybierając opcję **przejmowanie własności** w portalu Data Catalog. Żadne specjalne uprawnienia nie są wymagane do przejęcia własności nienależącego zasobu danych. Każdy użytkownik może przejąć własność nienależącego zasobu danych.

### <a name="add-owners-and-co-owners"></a>Dodawanie właścicieli i współwłaścicieli
Jeśli zasób danych jest już własnością, inni użytkownicy nie mogą po prostu przejąć na własność. Muszą być dodawane jako współwłaściciele przez istniejącego właściciela. Każdy właściciel może dodawać dodatkowych użytkowników lub grupy zabezpieczeń jako współwłaścicieli.

> [!NOTE]
> Najlepszym rozwiązaniem jest posiadanie co najmniej dwóch osób jako właścicieli dla każdego należącego zasobu danych.
>
>

### <a name="remove-owners"></a>Usuń właścicieli
Podobnie jak każdy właściciel zasobu może dodawać współwłaścicieli, każdy właściciel zasobu może usunąć dowolnego właściciela.

Właściciel zasobu, który usuwa siebie jako właściciel, nie może już zarządzać zasobem. Jeśli właściciel zasobu usunie siebie jako właściciela i nie ma żadnych innych współwłaścicieli, zasób powróci do stanu niebędącego właścicielem.

## <a name="control-visibility"></a>Widoczność formantów
Właściciele zasobów danych mogą kontrolować widoczność zasobów danych, których są właścicielami. Aby ograniczyć widoczność jako domyślną, gdzie wszyscy użytkownicy Data Catalog mogą odnajdywać i wyświetlać zasób danych, właściciel zasobu może przełączać ustawienie widoczności od **wszystkich** do **właścicieli & tych użytkowników** we właściwościach elementu zawartości. Właściciele mogą następnie dodawać określonych użytkowników i grupy zabezpieczeń.

> [!NOTE]
> Zawsze, gdy to możliwe, prawa własności i widoczności zasobów powinny być przypisane do grup zabezpieczeń, a nie do poszczególnych użytkowników.
>
>

## <a name="catalog-administrators"></a>Administratorzy wykazu
Administratorzy Data Catalog są niejawnie współwłaścicielami wszystkich zasobów w katalogu. Właściciele zasobów nie mogą usunąć widoczności administratorów, a administratorzy mogą zarządzać własnością i widocznością wszystkich zasobów danych w wykazie.

## <a name="summary"></a>Podsumowanie
Data Catalog model crowdsourcing na potrzeby odnajdywania metadanych i zasobów danych umożliwia wszystkim użytkownikom wykazu współtworzenie i odnajdowanie. Wersja Standard Edition Data Catalog została zaprojektowana na potrzeby własności i zarządzania w celu ograniczenia widoczności i używania określonych zasobów danych.
