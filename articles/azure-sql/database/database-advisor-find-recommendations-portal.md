---
title: Stosowanie zaleceń dotyczących wydajności
description: Użyj Azure Portal, aby znaleźć zalecenia dotyczące wydajności, które mogą zoptymalizować wydajność bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 12/19/2018
ms.openlocfilehash: e96db18993dbb18ebf51d0b30e9172901d10df5b
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398276"
---
# <a name="find-and-apply-performance-recommendations"></a>Znajdź i Zastosuj zalecenia dotyczące wydajności
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Możesz użyć Azure Portal, aby znaleźć zalecenia dotyczące wydajności, które mogą zoptymalizować wydajność bazy danych w Azure SQL Database lub rozwiązać problem opisany w obciążeniu. Strona **zalecenia dotyczące wydajności** w Azure Portal umożliwia znalezienie najważniejszych zaleceń w zależności od ich potencjalnego wpływu.

## <a name="viewing-recommendations"></a>Wyświetlanie zaleceń

Aby wyświetlić i zastosować zalecenia dotyczące wydajności, musisz mieć odpowiednie uprawnienia [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md) na platformie Azure. Aby można było wykonać wszystkie **akcje, wymagane**są uprawnienia współautora bazy danych **SQL** do wyświetlania zaleceń i **właściciel** **bazy danych SQL** . Utwórz lub upuść indeksy i Anuluj tworzenie indeksu.

Wykonaj następujące kroki, aby znaleźć zalecenia dotyczące wydajności Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do obszaru **wszystkie usługi**  >  **bazy danych SQL**, a następnie wybierz bazę danych.
3. Przejdź do **zalecenia** dotyczącego wydajności, aby wyświetlić dostępne rekomendacje dla wybranej bazy danych.

Zalecenia dotyczące wydajności są wyświetlane w tabeli podobnej do przedstawionej na poniższej ilustracji:

![Zrzut ekranu przedstawia zalecenia dotyczące wydajności w tabeli z opisem akcji i rekomendacji.](./media/database-advisor-find-recommendations-portal/recommendations.png)

Zalecenia są sortowane według ich potencjalnego wpływu na wydajność na następujące kategorie:

| Wpływ | Opis |
|:--- |:--- |
| Wysoki |Zalecenia o dużym wpływie powinny zapewnić najbardziej znaczący wpływ na wydajność. |
| Średniaa |Zalecenia dotyczące średniego wpływu powinny poprawić wydajność, ale nie w znaczący sposób. |
| Niski |Zalecenia dotyczące niskiego wpływu powinny zapewnić lepszą wydajność niż bez, ale ulepszenia mogą nie być znaczące. |

> [!NOTE]
> Azure SQL Database musi monitorować działania co najmniej dziennie, aby zidentyfikować pewne zalecenia. Azure SQL Database można łatwiej zoptymalizować pod kątem spójnych wzorców zapytań niż w przypadku losowych rozerwania aktywności. Jeśli rekomendacje nie są obecnie dostępne, na stronie **zalecenia dotyczące wydajności** zostanie wyświetlony komunikat z wyjaśnieniem dlaczego.

Można również wyświetlić stan operacji historycznych. Wybierz zalecenie lub stan, aby wyświetlić więcej informacji.

Oto przykład rekomendacji "Utwórz indeks" w Azure Portal.

![tworzenie indeksu](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Stosowanie zaleceń

Azure SQL Database zapewnia pełną kontrolę nad sposobem włączania zaleceń przy użyciu dowolnej z następujących trzech opcji:

* Stosuj pojedyncze rekomendacje pojedynczo.
* Włącz automatyczne dostosowywanie, aby automatycznie stosować zalecenia.
* Aby ręcznie zaimplementować zalecenie, uruchom zalecany skrypt T-SQL w bazie danych.

Wybierz dowolne zalecenie, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Wyświetl skrypt** , aby przejrzeć dokładne szczegóły dotyczące sposobu tworzenia zalecenia.

Baza danych pozostaje w trybie online, gdy zalecenie jest stosowane — przy użyciu zalecenia dotyczącego wydajności lub dostrajania automatycznego nigdy nie przejdzie bazy danych w tryb offline.

### <a name="apply-an-individual-recommendation"></a>Zastosuj indywidualne zalecenie

Możesz przejrzeć i zaakceptować zalecenia po jednej naraz.

1. Na stronie **zalecenia** wybierz zalecenie.
2. Na stronie **szczegółów** kliknij przycisk **Zastosuj** .

   ![Zastosuj zalecenie](./media/database-advisor-find-recommendations-portal/apply.png)

Wybrane zalecenia są stosowane do bazy danych programu.

### <a name="removing-recommendations-from-the-list"></a>Usuwanie rekomendacji z listy

Jeśli lista zaleceń zawiera elementy, które chcesz usunąć z listy, możesz odrzucić zalecenie:

1. Wybierz zalecenie z listy **zaleceń** , aby otworzyć Szczegóły.
2. Na stronie **szczegółów** kliknij pozycję **Odrzuć** .

W razie potrzeby można dodać odrzucone elementy z listy **rekomendacje** :

1. Na stronie **zalecenia** kliknij przycisk **Wyświetl odrzucony**.
2. Wybierz z listy odrzucony element, aby wyświetlić jego szczegóły.
3. Opcjonalnie kliknij przycisk **Cofnij Odrzuć** , aby dodać indeks z powrotem do głównej listy **zaleceń**.

> [!NOTE]
> Należy pamiętać, że jeśli SQL Database [dostrajania automatycznego](automatic-tuning-overview.md) jest włączona, a jeśli użytkownik ręcznie odrzuci zalecenie z listy, takie zalecenie nigdy nie będzie automatycznie stosowane. Odrzucanie zalecenia jest wygodnym sposobem, aby użytkownicy mogli korzystać z funkcji automatycznego dostrajania w przypadkach, gdy wymagają zastosowania określonego zalecenia.
> Możesz przywrócić to zachowanie, dodając odrzucone zalecenia z powrotem do listy rekomendacji, wybierając opcję Cofnij Odrzuć.

### <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania

Bazę danych można skonfigurować do automatycznego implementowania zaleceń. Gdy udostępnisz zalecenia, są one automatycznie stosowane. Podobnie jak w przypadku wszystkich zaleceń zarządzanych przez usługę, jeśli wpływ na wydajność jest negatywny, zalecenie zostanie cofnięte.

1. Na stronie **zalecenia** kliknij przycisk **Automatyzuj**:

   ![Ustawienia usługi Advisor](./media/database-advisor-find-recommendations-portal/settings.png)
2. Wybierz akcje do zautomatyzowania:

   ![Zrzut ekranu pokazujący, gdzie należy wybrać akcje do zautomatyzowania.](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> Należy pamiętać, że opcja **DROP_INDEX** nie jest obecnie zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów.

Po wybraniu odpowiedniej konfiguracji kliknij przycisk Zastosuj.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ręcznie stosuj zalecenia przy użyciu języka T-SQL

Wybierz dowolne zalecenie, a następnie kliknij przycisk **Wyświetl skrypt**. Uruchom ten skrypt w bazie danych, aby ręcznie zastosować zalecenie.

*Indeksy, które są wykonywane ręcznie, nie są monitorowane i sprawdzane pod kątem wydajności przez usługę* , dlatego zaleca się monitorowanie tych indeksów po utworzeniu, aby upewnić się, że zapewniają one zyski z wydajności i dostosowywać je w razie potrzeby. Aby uzyskać szczegółowe informacje na temat tworzenia indeksów, zobacz [create index (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql). Ponadto ręcznie zastosowane rekomendacje pozostaną aktywne i wyświetlone na liście zaleceń dla 24-48 godzin. przed automatycznym wycofaniem ich przez system. Jeśli chcesz usunąć zalecenie wcześniej, możesz je ręcznie odrzucić.

### <a name="canceling-recommendations"></a>Anulowanie zaleceń

Zaleceń, które znajdują się w stanie **oczekiwania**, **sprawdzania poprawności**lub **sukcesu** , można anulować. Nie można anulować zaleceń dotyczących stanu **wykonywania** .

1. Wybierz zalecenie w obszarze **historia dostrajania** , aby otworzyć stronę **szczegóły rekomendacji** .
2. Kliknij przycisk **Anuluj** , aby przerwać proces stosowania zalecenia.

## <a name="monitoring-operations"></a>Operacje monitorowania

Zastosowanie zalecenia może nie być chwilowo wykonywane. Portal zawiera szczegółowe informacje dotyczące stanu zalecenia. Poniżej przedstawiono możliwe stany, w których może znajdować się indeks:

| Stan | Opis |
|:--- |:--- |
| Oczekiwanie |Odebrano polecenie zastosowania zalecenia i zaplanowano jego wykonanie. |
| Operacji |Zalecenie jest stosowane. |
| Ponownego |Zalecenie zostało pomyślnie zastosowane i usługa mierzy korzyści. |
| Powodzenie |Zalecenie zostało pomyślnie zastosowane i korzyści zostały zmierzone. |
| Error |Wystąpił błąd podczas procesu stosowania zalecenia. Może to być problem przejściowy lub prawdopodobnie zmiana schematu tabeli, a skrypt nie jest już prawidłowy. |
| Przywracanie |Zalecenie zostało zastosowane, ale zostało uznane za niewykonane i jest automatycznie przywracane. |
| Przywrócono |Zalecenie zostało cofnięte. |

Kliknij rekomendację w procesie z listy, aby wyświetlić więcej informacji:

![Zrzut ekranu przedstawiający listę zaleceń w procesie.](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Cofanie zalecenia

Jeśli użyto zalecenia dotyczącego wydajności, aby zastosować zalecenie (oznacza to, że nie uruchomiono ręcznie skryptu T-SQL), program automatycznie przywróci zmianę, jeśli znajdzie wpływ na wydajność na wartość ujemną. Jeśli z jakiegoś powodu po prostu chcesz przywrócić zalecenie, możesz wykonać następujące czynności:

1. Wybierz pomyślnie zastosowane zalecenie w obszarze **historia dostrajania** .
2. Kliknij przycisk **Przywróć** na stronie **szczegółów rekomendacji** .

![Zalecane indeksy](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorowanie wpływu zaleceń dotyczących indeksów

Po pomyślnym wdrożeniu zaleceń (obecnie, operacji indeksowania i zaleceń Sparametryzuj) można kliknąć pozycję szczegółowe dane **zapytania** na stronie Szczegóły zalecenia, aby otworzyć szczegółowe informacje o [wydajności zapytań](query-performance-insight-use.md) i zobaczyć wpływ najważniejszych zapytań na wydajność.

![Monitorowanie wpływu wydajności](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Podsumowanie

Azure SQL Database zawiera zalecenia dotyczące poprawy wydajności bazy danych. Dostarczając skrypty T-SQL, uzyskasz pomoc w optymalizowaniu bazy danych i ostatecznie ulepszaniu wydajności zapytań.

## <a name="next-steps"></a>Następne kroki

Monitoruj swoje rekomendacje i Kontynuuj ich stosowanie w celu udoskonalenia wydajności. Obciążenia baz danych są dynamiczne i zmieniają się w sposób ciągły. Azure SQL Database kontynuuje monitorowanie i udostępnianie zaleceń, które mogą poprawić wydajność bazy danych.

* Zobacz [Automatyczne dostrajanie](automatic-tuning-overview.md) , aby dowiedzieć się więcej na temat dostrajania automatycznego w Azure SQL Database.
* Zobacz [zalecenia dotyczące wydajności](database-advisor-implement-performance-recommendations.md) , aby zapoznać się z omówieniem zaleceń dotyczących wydajności Azure SQL Database.
* Zobacz [szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md) , aby dowiedzieć się więcej o wyświetlaniu wydajności najważniejszych zapytań.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md)
