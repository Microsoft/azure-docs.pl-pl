---
title: Ocenianie dużej liczby serwerów fizycznych na potrzeby migracji na platformę Azure za pomocą Azure Migrate | Microsoft Docs
description: Opisuje sposób oceny dużej liczby serwerów fizycznych na potrzeby migracji na platformę Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: 6009975337f8f4caaf43e4b8b715e42c6ce2ffdc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494583"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Ocenianie dużej liczby serwerów fizycznych na potrzeby migracji na platformę Azure

W tym artykule opisano sposób oceny dużej liczby lokalnych serwerów fizycznych na potrzeby migracji na platformę Azure przy użyciu narzędzia do oceny serwera Azure Migrate.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 


W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Zaplanuj ocenę na dużą skalę.
> * Skonfiguruj uprawnienia platformy Azure i przygotuj serwery fizyczne do oceny.
> * Utwórz projekt Azure Migrate i Utwórz ocenę.
> * Zapoznaj się z oceną zgodnie z planem migracji.


> [!NOTE]
> Jeśli chcesz wypróbować koncepcję weryfikacji kilku serwerów przed oceną na dużą skalę, postępuj zgodnie z naszymi [seriami samouczków](./tutorial-discover-physical.md).

## <a name="plan-for-assessment"></a>Planowanie oceny

Planując ocenę dużej liczby serwerów fizycznych, istnieje kilka rzeczy, które należy wziąć pod uwagę:

- **Planowanie Azure Migrate projektów**: informacje na temat wdrażania projektów Azure Migrate. Na przykład jeśli centra danych znajdują się w różnych lokalizacje geograficzneach lub chcesz przechowywać metadane związane z odnajdywaniem, oceną lub migracją w innej lokalizacji geograficznej, może być konieczne przeprowadzenie wielu projektów.
- **Planowanie urządzeń**: Azure Migrate używa lokalnego urządzenia Azure Migrate wdrożonego na komputerze z systemem Windows w celu ciągłego odnajdywania serwerów na potrzeby oceny i migracji. Urządzenie monitoruje zmiany środowiska, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadane i dane dotyczące wydajności na platformie Azure. Należy ustalić liczbę urządzeń do wdrożenia.


## <a name="planning-limits"></a>Limity planowania
 
Użyj limitów podsumowania w tej tabeli w celu zaplanowania.

**Planowanie** | **Limity**
--- | --- 
**Projekty Azure Migrate** | Oceń do 35 000 serwerów w projekcie.
**Urządzenie usługi Azure Migrate** | Urządzenie może wykryć do 1000 serwerów.<br/> Urządzenie może być skojarzone tylko z pojedynczym projektem Azure Migrate.<br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem Azure Migrate. <br/><br/> 
**Grupa** | Można dodać maksymalnie 35 000 serwerów w jednej grupie.
**Ocena Azure Migrate** | W ramach jednej oceny można ocenić do 35 000 serwerów.


## <a name="other-planning-considerations"></a>Inne zagadnienia związane z planowaniem

- Aby rozpocząć odnajdywanie z urządzenia, należy wybrać każdy serwer fizyczny. 

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Przygotuj platformę Azure i serwery fizyczne do oceny serwera. 

1. Sprawdź [wymagania i ograniczenia dotyczące obsługi serwera fizycznego](migrate-support-matrix-physical.md).
2. Skonfiguruj uprawnienia dla Twojego konta platformy Azure, aby móc korzystać z Azure Migrate.
3. Przygotuj serwery fizyczne.

Aby skonfigurować te ustawienia, postępuj zgodnie z instrukcjami podanymi w [tym samouczku](./tutorial-discover-physical.md) .

## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami dotyczącymi planowania wykonaj następujące czynności:

1. Tworzenie projektu w usłudze Azure Migrate.
2. Dodaj narzędzie do oceny serwera Azure Migrate do projektów.

[Dowiedz się więcej](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Utwórz oceny dla serwerów fizycznych.
1. Zapoznaj się z ocenami w temacie przygotowanie do planowania migracji.

[Dowiedz się więcej](tutorial-assess-physical.md) o tworzeniu i przeglądaniu ocen.
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowany do skalowania Azure Migrate oceny dla serwerów fizycznych.
> * Przygotowano platformę Azure i serwery fizyczne do oceny.
> * Utworzono projekt Azure Migrate i uruchomiono oceny.
> * Przegląd ocen w przygotowaniu do migracji.

Teraz [Dowiedz się, jak](concepts-assessment-calculation.md) są obliczane oceny i jak [modyfikować oceny](how-to-modify-assessment.md).