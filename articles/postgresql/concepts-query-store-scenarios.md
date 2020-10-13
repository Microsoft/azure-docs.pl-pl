---
title: Scenariusze magazynu zapytań — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano niektóre scenariusze dla magazynu zapytań w Azure Database for PostgreSQL-pojedynczym serwerze.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ad646e39e9b6a87611dc2f40330a77fe70c92c27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710553"
---
# <a name="usage-scenarios-for-query-store"></a>Scenariusze użycia dla magazynu zapytań

**Dotyczy:** Azure Database for PostgreSQL — jeden serwer w wersji 9,6, 10, 11

Magazynu zapytań można używać w wielu różnych scenariuszach, w których śledzenie i obsługa przewidywalna wydajność obciążeń ma kluczowe znaczenie. Rozważmy następujące przykłady: 
- Identyfikowanie i dostrajanie największych kosztownych zapytań 
- Testowanie A/B: 
- Zachowywanie stabilności wydajności podczas uaktualniania 
- Identyfikowanie i ulepszanie obciążeń ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Zidentyfikuj i Dostosuj kosztowne zapytania 

### <a name="identify-longest-running-queries"></a>Zidentyfikuj najdłuższe uruchomione zapytania 
Użyj widoku [szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w Azure Portal, aby szybko identyfikować najdłuższe uruchomione zapytania. Te zapytania zazwyczaj mają na celu zużywanie znacznej ilości zasobów. Optymalizacja najdłuższych uruchomionych pytań może zwiększyć wydajność dzięki zwolnieniu zasobów do użycia przez inne zapytania działające w systemie. 

### <a name="target-queries-with-performance-deltas"></a>Kierowanie zapytań przy użyciu różnic wydajności 
Magazyn zapytań wycinkiuje dane wydajności w oknach czasu, dzięki czemu można śledzić wydajność zapytania w czasie. Ułatwia to zidentyfikowanie, które zapytania przyczyniają się do wzrostu całkowitego czasu trwania. W związku z tym można rozwiązać problemy związane z obciążeniem.

### <a name="tuning-expensive-queries"></a>Dostrajanie kosztownych zapytań 
Po zidentyfikowaniu zapytania z nieoptymalną wydajnością, podejmowana akcja zależy od natury problemu: 
- Użyj [zaleceń dotyczących wydajności](concepts-performance-recommendations.md) , aby określić, czy istnieją sugerowane indeksy. Jeśli tak, Utwórz indeks, a następnie użyj magazynu zapytań do oceny wydajności zapytań po utworzeniu indeksu. 
- Upewnij się, że statystyki są aktualne dla źródłowych tabel używanych przez zapytanie.
- Rozważ ponowne zapisanie kosztownych zapytań. Na przykład Skorzystaj z zalet zapytania parametryzacja i zmniejsz użycie dynamicznego języka SQL. Zaimplementuj optymalną logikę podczas odczytywania danych, takich jak stosowanie filtrowania danych po stronie bazy danych, a nie po stronie aplikacji. 


## <a name="ab-testing"></a>Testowanie A/B: 
Użyj magazynu zapytań, aby porównać wydajność obciążeń przed zmianą i po zmianie aplikacji, którą planujesz wprowadzić. Przykłady scenariuszy dotyczących używania magazynu zapytań do oceny wpływu zmiany środowiska lub aplikacji na wydajność obciążeń: 
- Wdrażanie nowej wersji aplikacji. 
- Dodawanie dodatkowych zasobów do serwera programu. 
- Tworzenie brakujących indeksów w tabelach, do których odwołuje się kosztowne zapytania. 
 
W każdym z tych scenariuszy zastosuj następujący przepływ pracy: 
1. Uruchom obciążenie z magazynem zapytań przed zaplanowaną zmianą, aby wygenerować linię bazową wydajności. 
2. Zastosuj zmiany aplikacji w kontrolowanej chwili w czasie. 
3. Kontynuuj uruchamianie obciążeń wystarczająco długo, aby generować obraz wydajności systemu po zmianie. 
4. Porównaj wyniki przed zmianą i po niej. 
5. Zdecyduj, czy zachować zmianę czy wycofać. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identyfikowanie i ulepszanie obciążeń ad hoc 
Niektóre obciążenia nie mają zapytań dominujących, które można dostosować w celu zwiększenia ogólnej wydajności aplikacji. Te obciążenia zwykle charakteryzują się stosunkowo dużą liczbą unikatowych zapytań, a każdy z nich zużywa część zasobów systemowych. Każde unikatowe zapytanie jest wykonywane rzadko, więc poszczególne użycie środowiska uruchomieniowego nie jest krytyczne. Z drugiej strony, jeśli aplikacja generuje nowe zapytania przez cały czas, znaczna część zasobów systemowych jest poświęcana na kompilację zapytań, która nie jest optymalna. Zwykle sytuacja taka występuje, gdy aplikacja generuje zapytania (zamiast używać procedur składowanych lub zapytań parametrycznych) lub jeśli opiera się na strukturach mapowania obiektów relacyjnych, które generują zapytania domyślnie. 
 
Jeśli masz kontrolę nad kodem aplikacji, możesz rozważyć przetworzenie warstwy dostępu do danych, aby użyć procedur składowanych lub zapytań parametrycznych. Tę sytuację można jednak ulepszyć bez zmian w aplikacjach, wymuszając parametryzacja zapytania dla całej bazy danych (wszystkie zapytania) lub dla poszczególnych szablonów zapytań z tym samym skrótem zapytania. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących korzystania z magazynu zapytań](concepts-query-store-best-practices.md)