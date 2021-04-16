---
title: Scenariusze magazynu zapytań — Azure Database for PostgreSQL — Flex Server
description: W tym artykule opisano niektóre scenariusze dotyczące magazynu zapytań w Azure Database for PostgreSQL — Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559151"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Scenariusze użycia dla magazynu zapytań — serwer elastyczny

**Dotyczy:** Azure Database for PostgreSQL — pojedynczy serwer w wersji 11, 12

Magazynu zapytań można używać w wielu różnych scenariuszach, w których kluczowe znaczenie ma śledzenie i utrzymywanie przewidywalnej wydajności obciążeń. Rozważmy następujące przykłady: 
- Identyfikowanie i dostrajanie najdroższych zapytań 
- Testowanie A/B: 
- Utrzymywanie stabilnej wydajności podczas uaktualniania 
- Identyfikowanie i ulepszanie obciążeń ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identyfikowanie i dostrajanie kosztownych zapytań 

### <a name="identify-longest-running-queries"></a>Identyfikowanie najdłużej działających zapytań 
Używaj widoków magazynu zapytań azure_sys bazie danych serwera, aby szybko identyfikować najdłużej działające zapytania. Te zapytania zwykle zużywają najwięcej zasobów. Optymalizacja najdłużej działających zapytań może poprawić wydajność przez wolne zasoby używane przez inne zapytania uruchomione w systemie. 

### <a name="target-queries-with-performance-deltas"></a>Zapytania docelowe z różnicami wydajności 
Magazyn zapytań fragmentuje dane wydajności w oknach czasu, dzięki czemu można śledzić wydajność zapytania w czasie. Dzięki temu można dokładnie określić, które zapytania mają wpływ na zwiększenie całkowitego czasu spędzonego. W związku z tym można wykonać ukierunkowane rozwiązywanie problemów z obciążeniem.

### <a name="tuning-expensive-queries"></a>Dostrajanie kosztownych zapytań 
W przypadku zidentyfikowania zapytania o nieoptymalnej wydajności akcja, która zostanie podjąć, zależy od charakteru problemu: 
- Upewnij się, że statystyki są aktualne dla bazowych tabel używanych przez zapytanie.
- Rozważ ponowne napisanie kosztownych zapytań. Na przykład skorzystaj z parametryzacji zapytań i zmniejsz użycie dynamicznego kodu SQL. Implementowanie optymalnej logiki podczas odczytywania danych, takich jak stosowanie filtrowania danych po stronie bazy danych, a nie po stronie aplikacji. 


## <a name="ab-testing"></a>Testowanie A/B: 
Użyj magazynu zapytań, aby porównać wydajność obciążeń przed i po zmianie aplikacji, która ma być wprowadzana lub przed i po migracji. Przykładowe scenariusze użycia magazynu zapytań do oceny wpływu zmian na wydajność obciążenia: 
- Migracja między wersjami postgreSQL. 
- Wprowadza nową wersję aplikacji. 
- Dodawanie dodatkowych zasobów do serwera. 
- Tworzenie brakujących indeksów w tabelach, do których odwołują się kosztowne zapytania. 
- Migracja z pojedynczego serwera do flexa Server. 
 
W każdym z tych scenariuszy zastosuj następujący przepływ pracy: 
1. Uruchom obciążenie przy użyciu magazynu zapytań przed zaplanowaną zmianą, aby wygenerować punkt odniesienia wydajności. 
2. Stosowanie zmian aplikacji w kontrolowanym momencie w czasie. 
3. Kontynuuj uruchamianie obciążenia wystarczająco długo, aby wygenerować obraz wydajności systemu po zmianie. 
4. Porównaj wyniki z przed zmianą i po tej zmianie. 
5. Zdecyduj, czy zachować zmianę, czy wycofać. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identyfikowanie i ulepszanie obciążeń ad hoc 
Niektóre obciążenia nie mają dominujących zapytań, które można dostosować, aby zwiększyć ogólną wydajność aplikacji. Te obciążenia zazwyczaj charakteryzują się stosunkowo dużą liczbą unikatowych zapytań, z których każde zużywa część zasobów systemowych. Każde unikatowe zapytanie jest wykonywane rzadko, dlatego osobno ich zużycie w czasie wykonywania nie jest krytyczne. Z drugiej strony, biorąc pod uwagę, że aplikacja cały czas generuje nowe zapytania, znaczna część zasobów systemowych jest wydawana na kompilację zapytań, co nie jest optymalne. Zwykle taka sytuacja występuje, gdy aplikacja generuje zapytania (zamiast używania procedur składowanych lub zapytań sparametryzowane) lub jeśli opiera się na platformach mapowania obiektowo-relacyjnych, które domyślnie generują zapytania. 
 
Jeśli masz kontrolę nad kodem aplikacji, możesz rozważyć ponowne zapisanie warstwy dostępu do danych w celu użycia procedur składowanych lub zapytań sparametryzowanej. Jednak tę sytuację można również poprawić bez zmian aplikacji, wymuś parametryzację zapytania dla całej bazy danych (wszystkich zapytań) lub dla poszczególnych szablonów zapytań z tym samym skrótem zapytania. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących korzystania z magazynu zapytań](concepts-query-store-best-practices.md)
