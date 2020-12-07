---
title: Ocenianie dużej liczby maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure za pomocą Azure Migrate | Microsoft Docs
description: Opisuje sposób oceny dużej liczby maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure przy użyciu usługi Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 92c275ee3f8e00e71b80e448c9adb94f0b6d21dc
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753726"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Ocenianie dużej liczby maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure

W tym artykule opisano sposób oceny dużej liczby lokalnych maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure przy użyciu narzędzia do oceny serwera Azure Migrate.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 


W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Zaplanuj ocenę na dużą skalę.
> * Konfigurowanie uprawnień platformy Azure i przygotowanie funkcji Hyper-V do oceny.
> * Utwórz projekt Azure Migrate i Utwórz ocenę.
> * Zapoznaj się z oceną zgodnie z planem migracji.


> [!NOTE]
> Jeśli chcesz wypróbować ocenę koncepcji, aby ocenić kilka maszyn wirtualnych przed oceną na dużą skalę, postępuj zgodnie z naszymi [seriami samouczków](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Planowanie oceny

Planując ocenę dużej liczby maszyn wirtualnych funkcji Hyper-V, istnieje kilka rzeczy, które należy wziąć pod uwagę:

- **Planowanie Azure Migrate projektów**: informacje na temat wdrażania projektów Azure Migrate. Na przykład jeśli centra danych znajdują się w różnych lokalizacje geograficzneach lub chcesz przechowywać metadane związane z odnajdywaniem, oceną lub migracją w innej lokalizacji geograficznej, może być konieczne przeprowadzenie wielu projektów.
- **Planowanie urządzeń**: Azure Migrate używa lokalnego urządzenia Azure Migrate, wdrożonego jako maszyna wirtualna funkcji Hyper-V, w celu ciągłego odnajdywania maszyn wirtualnych na potrzeby oceny i migracji. Urządzenie monitoruje zmiany środowiska, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadane i dane dotyczące wydajności na platformie Azure. Należy ustalić liczbę urządzeń do wdrożenia.


## <a name="planning-limits"></a>Limity planowania
 
Użyj limitów podsumowania w tej tabeli w celu zaplanowania.

**Planowanie** | **Limity**
--- | --- 
**Projekty Azure Migrate** | Oceń do 35 000 maszyn wirtualnych w projekcie.
**Urządzenie usługi Azure Migrate** | Urządzenie może wykryć do 5000 maszyn wirtualnych.<br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.<br/> Urządzenie może być skojarzone tylko z pojedynczym projektem Azure Migrate.<br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem Azure Migrate. <br/><br/> 
**Grupa** | W jednej grupie możesz dodać maksymalnie 35 000 maszyn wirtualnych.
**Ocena Azure Migrate** | W ramach jednej oceny można ocenić do 35 000 maszyn wirtualnych.



## <a name="other-planning-considerations"></a>Inne zagadnienia związane z planowaniem

- Aby rozpocząć odnajdywanie z urządzenia, należy wybrać każdego hosta funkcji Hyper-V. 
- W przypadku korzystania ze środowiska z wieloma dzierżawcami nie można obecnie wykryć tylko maszyn wirtualnych należących do określonej dzierżawy. 

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Przygotuj platformę Azure i funkcję Hyper-V do oceny serwera. 

1. Sprawdź [wymagania i ograniczenia dotyczące obsługi funkcji Hyper-V](migrate-support-matrix-hyper-v.md).
2. Skonfiguruj uprawnienia dla Twojego konta platformy Azure, aby móc korzystać z Azure Migrate
3. Przygotowywanie hostów i maszyn wirtualnych funkcji Hyper-V

Aby skonfigurować te ustawienia, postępuj zgodnie z instrukcjami podanymi w [tym samouczku](./tutorial-discover-hyper-v.md) .

## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami dotyczącymi planowania wykonaj następujące czynności:

1. Utwórz projekty Azure Migrate.
2. Dodaj narzędzie do oceny serwera Azure Migrate do projektów.

[Dowiedz się więcej](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Utwórz oceny dla maszyn wirtualnych funkcji Hyper-V.
1. Zapoznaj się z ocenami w temacie przygotowanie do planowania migracji.

[Dowiedz się więcej](tutorial-assess-hyper-v.md) o tworzeniu i przeglądaniu ocen.
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowane skalowanie Azure Migrate ocen dotyczących maszyn wirtualnych funkcji Hyper-V
> * Przygotowano platformę Azure i funkcję Hyper-V do oceny
> * Utworzono projekt Azure Migrate i uruchomiono oceny
> * Przegląd ocen w przygotowaniu do migracji.

Teraz [Dowiedz się, jak](concepts-assessment-calculation.md) są obliczane oceny i jak [modyfikować oceny](how-to-modify-assessment.md)