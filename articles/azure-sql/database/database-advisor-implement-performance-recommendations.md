---
title: Zalecenia dotyczące wydajności usługi Database Advisor dla Azure SQL Database
description: Azure SQL Database zawiera zalecenia dotyczące baz danych, które mogą zwiększyć wydajność zapytań w programie Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500856"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Zalecenia dotyczące wydajności Database Advisor Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database uczy się i dostosowuje do aplikacji. Azure SQL Database ma wiele klasyfikatorów baz danych, które udostępniają niestandardowe zalecenia, które umożliwiają zmaksymalizowanie wydajności. Te doradcy baz danych stale oceniają i analizują historię użycia oraz zapewnią zalecenia na podstawie wzorców obciążeń, które zwiększają wydajność.

## <a name="performance-overview"></a>Przegląd wydajności

Przegląd wydajności zawiera podsumowanie wydajności bazy danych, a także ułatwia dostrajanie wydajności i rozwiązywanie problemów.

![Przegląd wydajności dla Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- Kafelek **rekomendacje** zawiera podział zaleceń dotyczących dostrajania dla bazy danych (w przypadku większej liczby najważniejszych zaleceń są wyświetlane trzy zalecenia). Kliknięcie tego kafelka spowoduje przejście do **[opcji zalecenia dotyczące wydajności](database-advisor-find-recommendations-portal.md#viewing-recommendations)**.
- Kafelek **działania dostrajania** zawiera podsumowanie bieżących i ukończonych akcji dostrajania dla bazy danych, dzięki czemu można szybko wyświetlić historię działania dostrajania. Kliknięcie tego kafelka spowoduje przejście do widoku historii pełnego dostrajania dla bazy danych.
- Kafelek **autodostrajania** pokazuje **[konfigurację](automatic-tuning-enable.md)** autodostrajania dla bazy danych (opcje dostrajania, które są automatycznie stosowane do bazy danych). Kliknięcie tego kafelka spowoduje otwarcie okna dialogowego konfiguracji automatyzacji.
- Kafelek **zapytania bazy danych** pokazuje podsumowanie wydajności zapytań dla bazy danych (ogólne użycie jednostek DTU i najpopularniejsze zapytania zużywające zasoby). Kliknięcie tego kafelka spowoduje przejście do **[szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md)**.

## <a name="performance-recommendation-options"></a>Opcje rekomendacji dotyczących wydajności

Opcje rekomendacji wydajności dostępne w Azure SQL Database są następujące:

| Zalecenie dotyczące wydajności | Obsługa pojedynczej bazy danych i bazy danych w puli | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **Tworzenie zaleceń dotyczących indeksów** — zalecane jest utworzenie indeksów, które mogą poprawić wydajność obciążeń. | Tak | Nie |
| **Porzuć zalecenia dotyczące indeksu** — zalecane jest usunięcie nadmiarowych i zduplikowanych indeksów codziennie, z wyjątkiem indeksów unikatowych i indeksów, które nie były używane przez długi czas (>90 dni). Należy pamiętać, że ta opcja nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów. Usuwanie nieużywanych indeksów nie jest obsługiwane dla warstw usług premium i Krytyczne dla działania firmy. | Tak | Nie |
| **Zalecenia dotyczące zapytań Sparametryzuj (wersja zapoznawcza)** — zalecane parametryzacja w przypadkach, gdy istnieje co najmniej jedno zapytanie, które jest stale ponownie kompilowane, ale kończy się na tym samym planie wykonywania zapytań. | Tak | Nie |
| **Napraw zalecenia dotyczące problemów ze schematem (wersja zapoznawcza)** — zalecenia dotyczące korekcji schematu są wyświetlane, gdy Azure SQL Database zauważy anomalię w liczbie błędów SQL związanych ze schematami, które są wykonywane w bazie danych. Firma Microsoft jest obecnie przestarzała zalecenia "Napraw problem ze schematem". | Tak | Nie |

![Zalecenia dotyczące wydajności Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

Aby zastosować zalecenia dotyczące wydajności, zobacz [stosowanie zaleceń](database-advisor-find-recommendations-portal.md#applying-recommendations). Aby wyświetlić stan zaleceń, zobacz [monitorowanie operacji](database-advisor-find-recommendations-portal.md#monitoring-operations).

Możesz również znaleźć pełną historię działań dostrajania, które zostały zastosowane w przeszłości.

## <a name="create-index-recommendations"></a>Tworzenie zaleceń dotyczących indeksów

Azure SQL Database stale monitoruje uruchomione zapytania i identyfikuje indeksy, które mogą poprawić wydajność. Po upływie wystarczającej pewności od braku określonego indeksu zostanie utworzony nowy zalecenie **tworzenia indeksu** .

Azure SQL Database kompilacja jest niedostępna, co szacuje wzrost wydajności, indeks przekroczy czas. W zależności od szacowanego zwiększenia wydajności poziom zaleceń jest klasyfikowany jako wysoki, średni lub niski.

Indeksy tworzone przy użyciu rekomendacji są zawsze oflagowane jako indeksy tworzone przez Autostart. Możesz zobaczyć, które indeksy są tworzone w sposób tworzony, przeglądając [Widok sys. Indexs](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Tworzone indeksy nie blokują poleceń ALTER/RENAME.

W przypadku próby porzucenia kolumny, która ma utworzony przez niego indeks, polecenie kończy się powodzeniem. Tworzony przez siebie indeks został również usunięty przy użyciu polecenia. Indeksy regularne blokują polecenie Zmień/Zmień nazwę dla kolumn, które są indeksowane.

Po zastosowaniu zalecenia tworzenia indeksu Azure SQL Database porównuje wydajność zapytań z wydajnością bazową. Jeśli nowy indeks poprawił wydajność, zalecenie jest oflagowane jako pomyślne, a raport wpływ jest dostępny. Jeśli indeks nie poprawi wydajności, jest automatycznie przywracany. Azure SQL Database korzysta z tego procesu, aby upewnić się, że zalecenia zwiększają wydajność bazy danych.

Wszelkie zalecenia dotyczące **tworzenia indeksu** mają zasady wycofywania, które nie zezwalają na stosowanie zalecenia, jeśli użycie zasobów bazy danych lub puli jest wysokie. Zasady wycofywania uwzględniają użycie procesora CPU, danych we/wy, operacji we/wy, dziennika i dostępnego magazynu.

Jeśli procesor CPU, we/wy danych lub dziennik operacji we/wy są większe niż 80% w ciągu ostatnich 30 minut, zalecenie tworzenia indeksu zostanie odroczone. Jeśli dostępny magazyn będzie niższy niż 10% po utworzeniu indeksu, zalecenie przechodzi do stanu błędu. Jeśli po kilku dniach funkcja automatycznego dostrajania nadal uważa, że indeks będzie korzystny, proces zostanie uruchomiony ponownie.

Ten proces jest powtarzany do momentu, gdy jest wystarczająco dużo dostępnego miejsca w magazynie, aby można było utworzyć indeks lub dopóki indeks nie będzie już widoczny.

## <a name="drop-index-recommendations"></a>Porzuć zalecenia dotyczące indeksów

Oprócz wykrywania brakujących indeksów Azure SQL Database stale analizuje wydajność istniejących indeksów. Jeśli indeks nie jest używany, Azure SQL Database zaleca porzucenie go. Usuwanie indeksu jest zalecane w dwóch przypadkach:

- Indeks jest duplikatem innego indeksu (ta sama kolumna indeksowana i uwzględniona, schemat partycji i filtry).
- Indeks nie był używany przez dłuższy czas (93 dni).

Zalecenia dotyczące usuwania indeksów również przechodzą przez weryfikację po wdrożeniu. W przypadku zwiększenia wydajności jest dostępny raport o wpływie. W przypadku obniżenia wydajności zaleca się przywrócenie zalecenia.

## <a name="parameterize-queries-recommendations-preview"></a>Zalecenia dotyczące zapytań Sparametryzuj (wersja zapoznawcza)

Zalecenia dotyczące *zapytań Sparametryzuj* pojawiają się, gdy istnieje co najmniej jedno zapytanie, które jest stale ponownie kompilowane, ale kończy się na tym samym planie wykonywania zapytań. Ten warunek umożliwia utworzenie możliwości zastosowania wymuszonych parametryzacja. Wymuszone parametryzacja, z kolei, umożliwia zalogowanie się i ponowne użycie planów zapytania w przyszłości, co zwiększa wydajność i zmniejsza wykorzystanie zasobów.

Najpierw należy skompilować każde zapytanie w celu wygenerowania planu wykonania. Każdy wygenerowany plan zostanie dodany do pamięci podręcznej planu. Kolejne wykonania tego samego zapytania mogą ponownie wykorzystać ten plan z pamięci podręcznej, co eliminuje konieczność tworzenia dodatkowej kompilacji.

Zapytania z wartościami niesparametryzowanymi mogą zmniejszać wydajność, ponieważ plan wykonania jest ponownie kompilowany przy każdej zmianie wartości niesparametryzowanych. Te same zapytania z różnymi wartościami parametrów w wielu przypadkach generują te same plany wykonania. Jednak plany te i tak są dodawane oddzielnie do pamięci podręcznej planów.

Proces ponownego kompilowania planów wykonania powoduje zużycie zasobów bazy danych, wydłużenie czasu wykonywania zapytań i przepełnienie pamięci podręcznej planów. To z kolei prowadzi do wykluczenia planów z pamięci podręcznej. Takie zachowanie można zmienić, ustawiając opcję wymuszone parametryzacja w bazie danych.

Aby ułatwić oszacowanie wpływu tego zalecenia, należy uzyskać porównanie między rzeczywistym użyciem procesora CPU a przewidywanym użyciem procesora CPU (jak w przypadku zastosowania zalecenia). To zalecenie może pomóc w uzyskaniu oszczędności procesora. Może również pomóc w zmniejszeniu czasu trwania zapytania i nakładu pracy w pamięci podręcznej planu, co oznacza, że więcej planów może pozostać w pamięci podręcznej i być ponownie używane. To zalecenie można szybko zastosować, wybierając polecenie **Zastosuj** .

Po zastosowaniu tego zalecenia włącza wymuszone parametryzacja w ciągu kilku minut w bazie danych. Uruchamia proces monitorowania, który trwa około 24 godzin. Po upływie tego czasu zobaczysz raport z walidacji. Ten raport przedstawia użycie procesora przez bazę danych przez 24 godziny przed zastosowaniem zalecenia i po nim. Azure SQL Database Advisor ma mechanizm bezpieczeństwa, który automatycznie przywraca zastosowane zalecenie w przypadku wykrycia regresji wydajności.

## <a name="fix-schema-issues-recommendations-preview"></a>Rozwiąż zalecenia dotyczące problemów ze schematem (wersja zapoznawcza)

> [!IMPORTANT]
> Firma Microsoft jest obecnie przestarzała zalecenia "Napraw problem ze schematem". Zalecamy używanie [Intelligent Insights](intelligent-insights-overview.md) do monitorowania problemów z wydajnością bazy danych, w tym problemów ze schematami, które zostały wcześniej omówione w zaleceniach dotyczących problemu ze schematem.

**Rozwiązanie problemów ze schematami** pojawia się, gdy Azure SQL Database zauważy anomalię w liczbie błędów SQL związanych ze schematami, które są wykonywane w bazie danych. To zalecenie zazwyczaj pojawia się, gdy baza danych napotyka wiele błędów związanych ze schematem (Nieprawidłowa nazwa kolumny, Nieprawidłowa nazwa obiektu itd.) w ciągu godziny.

"Problemy ze schematem" są klasą błędów składniowych. Są one wykonywane, gdy definicja zapytania SQL i definicja schematu bazy danych nie są wyrównane. Na przykład jedna z kolumn oczekiwanych przez zapytanie może nie być w tabeli docelowej lub na odwrót.

Zalecenie "Rozwiąż problem ze schematem" pojawia się, gdy Azure SQL Database zauważy anomalię w liczbie błędów SQL związanych ze schematami, które są wykonywane w bazie danych. W poniższej tabeli przedstawiono błędy związane z problemami ze schematem:

| Kod błędu SQL | Komunikat |
| --- | --- |
| 201 |Procedura lub funkcja "*" oczekuje parametru "*", który nie został podany. |
| 207 |Nieprawidłowa nazwa kolumny "*". |
| 208 |Nieprawidłowa nazwa obiektu "*". |
| 213 |Nazwa kolumny lub liczba podanych wartości nie pasują do definicji tabeli. |
| 2812 |Nie można znaleźć procedury składowanej "*". |
| 8144 |Określono za dużo argumentów procedury lub funkcji *. |

## <a name="custom-applications"></a>Aplikacje niestandardowe

Deweloperzy mogą rozważyć tworzenie niestandardowych aplikacji przy użyciu zaleceń dotyczących wydajności Azure SQL Database. Do wszystkich zaleceń wymienionych w portalu dla bazy danych można uzyskać dostęp za pomocą interfejsu API [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) .

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat automatycznego dostrajania indeksów bazy danych i planów wykonywania zapytań, zobacz [Azure SQL Database dostrajania automatycznego](automatic-tuning-overview.md).
- Aby uzyskać więcej informacji o automatycznym monitorowaniu wydajności bazy danych za pomocą zautomatyzowanej diagnostyki i głównej przyczyny problemów z wydajnością, zobacz [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
- Zobacz [szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md) , aby dowiedzieć się więcej o wydajności najważniejszych zapytań.