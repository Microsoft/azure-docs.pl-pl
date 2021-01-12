---
title: Kontrola dostępu oparta na rolach Synapse
description: Artykuł objaśniający kontrolę dostępu opartą na rolach w usłudze Azure Synapse Analytics
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 2d9f3bfe6a273bfb0f3d314d1a4664806bd45ae2
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118816"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Co to jest Synapse kontroli dostępu opartej na rolach (RBAC)?

Synapse RBAC rozszerza możliwości usług [Azure RBAC](../../role-based-access-control/overview.md) dla obszarów roboczych Synapse i ich zawartości. 

Usługa Azure RBAC służy do zarządzania tym, kto może tworzyć, aktualizować lub usuwać obszar roboczy Synapse oraz jego pule SQL, pule Apache Spark i środowiska Integration Runtime.

Synapse RBAC służy do zarządzania tym, kto może:
- Publikuj artefakty kodu i listę lub uzyskaj dostęp do opublikowanych artefaktów kodu, 
- Wykonaj kod na platformie Apaches i w programie Integration Runtime,
- Dostęp do połączonych (danych) usług chronionych przez poświadczenia 
- Monitoruj lub Anuluj wykonywanie zadania, Przejrzyj dane wyjściowe zadania i dzienniki wykonywania.  

>[!Note]
>Chociaż Synapse RBAC służy do zarządzania dostępem do opublikowanych skryptów SQL, zapewnia tylko ograniczoną kontrolę dostępu do pul SQL bez serwera i _nie_ jest używana do kontrolowania dostępu do dedykowanych pul SQL.  Dostęp do pul SQL jest przede wszystkim kontrolowany przy użyciu zabezpieczeń SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>Co mogę zrobić z Synapse RBAC?

Poniżej przedstawiono kilka przykładów tego, co można zrobić za pomocą Synapse RBAC:
  - Zezwalaj użytkownikowi na publikowanie zmian wprowadzonych w Apache Spark notesach i zadaniach do usługi Live.
  - Zezwalaj użytkownikowi na uruchamianie i anulowanie notesów oraz zadań platformy Spark w określonej puli Apache Spark.
  - Zezwalaj użytkownikowi na używanie określonych poświadczeń, aby mogli uruchamiać potoki zabezpieczone przez tożsamość systemu obszaru roboczego i uzyskiwać dostęp do danych w połączonych usługach zabezpieczonych za pomocą poświadczeń. 
  - Umożliwia administratorowi zarządzanie, monitorowanie i anulowanie wykonywania zadań w określonych pulach platformy Spark.    

## <a name="how-synapse-rbac-works"></a>Jak działa Synapse RBAC
Podobnie jak w przypadku usługi Azure RBAC, Synapse RBAC działa przez tworzenie przypisań ról. Przypisanie roli obejmuje trzy elementy: podmiot zabezpieczeń, definicję roli i zakres.  

### <a name="security-principals"></a>Podmioty zabezpieczeń

_Podmiot zabezpieczeń_ to użytkownik, Grupa, nazwa główna usługi lub tożsamość zarządzana.

### <a name="roles"></a>Role
 
_Rola_ to kolekcja uprawnień lub akcji, które mogą być wykonywane na określonych typach zasobów lub typach artefaktów.

Synapse udostępnia wbudowane role definiujące kolekcje akcji, które pasują do potrzeb różnych osób:
- Administratorzy mogą uzyskać pełny dostęp do tworzenia i konfigurowania obszaru roboczego 
- Deweloperzy mogą tworzyć, aktualizować i debugować skrypty SQL, notesy, potoki i Dataflow, ale nie mogą publikować ani wykonywać tego kodu w produkcyjnych zasobach obliczeniowych/danych
- Operatory mogą monitorować i zarządzać stanem systemu, wykonywaniem aplikacji oraz dziennikami przeglądu bez dostępu do kodu lub danych wyjściowych z wykonywania.
- Pracownicy zabezpieczeń mogą zarządzać punktami końcowymi i konfigurować je bez konieczności uzyskiwania dostępu do kodu, zasobów obliczeniowych lub danych.

[Dowiedz się więcej](./synapse-workspace-synapse-rbac-roles.md) na temat wbudowanych ról Synapse. 

### <a name="scopes"></a>Zakresy

_Zakres_ definiuje zasoby lub artefakty, do których odnosi się ten dostęp.  Synapse obsługuje zakresy hierarchiczne.  Uprawnienia przyznane w zakresie wyższego poziomu są dziedziczone przez obiekty na niższym poziomie.  W Synapse RBAC zakres najwyższego poziomu jest obszarem roboczym.  Przypisanie roli z zakresem obszaru roboczego uprawnia do wszystkich odpowiednich obiektów w obszarze roboczym.  

Bieżące obsługiwane zakresy w obszarze roboczym to: Pula Apache Spark, środowisko Integration Runtime, połączona usługa i poświadczenie. 

Dostęp do artefaktów kodu jest przyznawany z zakresem obszaru roboczego.  Udzielanie dostępu do kolekcji artefaktów w obszarze roboczym będzie obsługiwane w nowszej wersji.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Rozpoznawanie przypisań ról w celu określenia uprawnień

Przypisanie roli przyznaje podmiotowi zabezpieczeń uprawnienia zdefiniowane przez rolę w określonym zakresie.

Synapse RBAC to model dodatku, taki jak Azure RBAC. Wiele ról może być przypisanych do jednego podmiotu zabezpieczeń i w różnych zakresach. Podczas obliczania uprawnień podmiotu zabezpieczeń system traktuje wszystkie role przypisane do podmiotu danych i grup, które bezpośrednio lub pośrednio zawierają podmiot zabezpieczeń.  Uwzględnia również zakres każdego przydziału w ustaleniu uprawnień, które mają zastosowanie.  

## <a name="enforcing-assigned-permissions"></a>Wymuszanie przypisanych uprawnień

W programie Synapse Studio określone przyciski lub opcje mogą być wyszarzone lub w przypadku próby wykonania akcji mogą zostać zwrócone błędy uprawnień, jeśli nie masz wymaganych uprawnień. 

Jeśli przycisk lub opcja jest wyłączona, umieszczenie kursora nad przyciskiem lub opcja powoduje wyświetlenie etykietki narzędzia z wymaganym uprawnieniem.  Skontaktuj się z administratorem Synapse, aby przypisać rolę, która przyznaje wymagane uprawnienia. W [tym miejscu](./synapse-workspace-synapse-rbac-roles.md)można zobaczyć role, które zawierają określone akcje.

## <a name="who-can-assign-synapse-rbac-roles"></a>Kto może przypisać role RBAC Synapse?

Tylko administrator Synapse może przypisać role RBAC Synapse.  Administrator Synapse na poziomie obszaru roboczego może udzielić dostępu w dowolnym zakresie.  Administrator Synapse w zakresie niższego poziomu może udzielić dostępu tylko w tym zakresie. 

Po utworzeniu nowego obszaru roboczego twórca automatycznie otrzymuje rolę administratora Synapse w zakresie obszaru roboczego.   

## <a name="where-do-i-manage-synapse-rbac"></a>Gdzie mogę zarządzać Synapse RBAC?

Synapse RBAC jest zarządzany w programie Synapse Studio przy użyciu narzędzi kontroli dostępu w centrum zarządzania. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z wbudowanymi [rolami RBAC Synapse](./synapse-workspace-synapse-rbac-roles.md).

Dowiedz się [, jak przejrzeć przydziały ról RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md) dla obszaru roboczego.

Dowiedz się [, jak przypisać role RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md)