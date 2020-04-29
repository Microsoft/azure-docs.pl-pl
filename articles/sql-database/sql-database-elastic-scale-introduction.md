---
title: Skalowanie w poziomie
description: Deweloperzy oprogramowania jako usługi (SaaS) mogą łatwo tworzyć elastyczne i skalowalne bazy danych w chmurze przy użyciu tych narzędzi
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061644"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)
Możesz łatwo skalować bazy danych Azure SQL Database przy użyciu narzędzi **Elastic Database** . Te narzędzia i funkcje umożliwiają korzystanie z zasobów bazy danych **Azure SQL Database** do tworzenia rozwiązań dla obciążeń transakcyjnych, a zwłaszcza aplikacji oprogramowania jako usługi (SaaS). Elastic Database funkcje składają się z:

* [Elastic Database Biblioteka kliencka](sql-database-elastic-database-client-library.md): Biblioteka kliencka to funkcja, która umożliwia tworzenie i konserwowanie baz danych podzielonej na fragmenty.  Zobacz Rozpoczynanie [pracy z narzędziami Elastic Database](sql-database-elastic-scale-get-started.md).
* [Narzędzie Elastic Database Split-Merge](sql-database-elastic-scale-overview-split-and-merge.md): przenosi dane między bazami danych podzielonej na fragmenty. To narzędzie jest przydatne do przeniesienia danych z wielodostępnej bazy danych do bazy danych z jedną dzierżawą (lub odwrotnie). Zobacz [samouczek narzędzia do dzielenia i scalania Elastic Database](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Zadania Elastic Database](elastic-jobs-overview.md): Używanie zadań do zarządzania dużą liczbą baz danych SQL Azure. Łatwe wykonywanie operacji administracyjnych, takich jak zmiany schematu, zarządzanie poświadczeniami, aktualizacje danych referencyjnych, zbieranie danych o wydajności lub zbieranie informacji telemetrycznych dzierżawy (klienta) za pomocą zadań.
* [Zapytanie Elastic Database](sql-database-elastic-query-overview.md) (wersja zapoznawcza): umożliwia uruchomienie zapytania Transact-SQL obejmującego wiele baz danych. Dzięki temu można nawiązać połączenie z narzędziami do raportowania, takimi jak Excel, Power BI, Tableau itp.
* [Transakcje elastyczne](sql-database-elastic-transactions-overview.md): Ta funkcja umożliwia uruchamianie transakcji obejmujących kilka baz danych w Azure SQL Database. Transakcje Elastic Database są dostępne dla aplikacji .NET korzystających z programu ADO .NET i integrują się z znanym środowiskiem programistycznym korzystającym z [klas System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

Poniższa ilustracja przedstawia architekturę zawierającą **funkcje Elastic Database** w odniesieniu do kolekcji baz danych.

Na tej ilustracji kolory bazy danych reprezentują schematy. Bazy danych o tym samym kolorze współdzielą ten sam schemat.

1. Zestaw **baz danych usługi Azure SQL** jest hostowany na platformie Azure przy użyciu architektury fragmentowania.
2. **Biblioteka klienta Elastic Database** służy do zarządzania zestawem fragmentu.
3. Podzestaw baz danych jest umieszczany w **puli elastycznej**. (Zobacz [co to jest pula?](sql-database-elastic-pool.md)).
4. **Zadanie Elastic Database** uruchamiane jest zaplanowane lub ad hoc skryptów T-SQL dla wszystkich baz danych.
5. **Narzędzie Split-Merge** służy do przenoszenia danych z jednego fragmentuu do drugiego.
6. **Zapytanie Elastic Database** umożliwia pisanie zapytania obejmującego wszystkie bazy danych w zestawie fragmentu.
7. **Transakcje elastyczne** umożliwiają uruchamianie transakcji obejmujących kilka baz danych. 

![Narzędzia elastycznych baz danych][1]

## <a name="why-use-the-tools"></a>Dlaczego warto korzystać z narzędzi?
Osiągnięcie elastyczności i skalowania aplikacji w chmurze jest proste w przypadku maszyn wirtualnych i magazynu obiektów BLOB — po prostu Dodaj lub Odejmij jednostki lub Zwiększ moc. Jednak było to wyzwanie dla przetwarzania danych stanowych w relacyjnych bazach danych. Wyzwania w następujących scenariuszach:

* Zwiększanie i zmniejszanie pojemności relacyjnej bazy danych w ramach obciążenia.
* Zarządzanie hotspotami, które mogą mieć wpływ na określony podzbiór danych — takich jak zajęty klient końcowy (dzierżawa).

Tradycyjnie scenariusze takie jak te zostały rozbudowane przez inwestycję w serwery baz danych o większej skali, aby obsługiwały aplikację. Jednak ta opcja jest ograniczona w chmurze, w której wszystkie operacje przetwarzania są wykonywane na wstępnie zdefiniowanym sprzęcie. Zamiast tego dystrybuowanie danych i przetwarzanie w wielu identycznie spójnych bazach danych (wzorzec skalowalny w poziomie znany jako "fragmentowania") stanowi alternatywę dla tradycyjnego skalowania w górę zarówno w zakresie kosztów, jak i elastyczności.

## <a name="horizontal-and-vertical-scaling"></a>Skalowanie w poziomie i w pionie
Na poniższej ilustracji przedstawiono poziome i pionowe wymiary skalowania, które są podstawowymi sposobami skalowania elastycznych baz danych.

![Skalowanie w poziomie i w pionie][2]

Skalowanie w poziomie polega na dodawaniu lub usuwaniu baz danych w celu dostosowania pojemności lub ogólnej wydajności, zwanej również "skalowaniem na zewnątrz". Fragmentowania, w którym dane są partycjonowane w kolekcji z identycznie uporządkowanymi bazami danych, jest typowym sposobem implementacji skalowania w poziomie.  

Skalowanie w pionie odnosi się do zwiększenia lub zmniejszenia rozmiaru obliczeniowego pojedynczej bazy danych, znanej również jako "skalowanie w górę".

Większość aplikacji baz danych w skali chmury używa kombinacji tych dwóch strategii. Na przykład oprogramowanie jako aplikacja usługi może używać skalowania poziomego w celu aprowizacji nowych klientów końcowych i skalowania w pionie, aby umożliwić każdej bazie danych klienta końcowego zwiększanie lub zmniejszanie zasobów zgodnie z potrzebami obciążeń.

* Skalowanie w poziomie jest zarządzane przy użyciu [biblioteki klienta Elastic Database](sql-database-elastic-database-client-library.md).
* Skalowanie w pionie jest realizowane przy użyciu Azure PowerShell poleceń cmdlet do zmiany warstwy usług lub umieszczania baz danych w puli elastycznej.

## <a name="sharding"></a>Dzielenie na fragmenty
*Fragmentowanie* to technika dystrybucji dużej ilości danych o identycznej strukturze do wielu niezależnych baz danych. Jest to szczególnie popularne w przypadku deweloperów w chmurze tworzących oferty oprogramowania jako usługi (SAAS) dla klientów końcowych lub firm. Ci klienci końcowi są często nazywani "dzierżawcami". Fragmentowania może być wymagana z dowolnej liczby przyczyn:  

* Całkowita ilość danych jest zbyt duża, aby zmieścić je w ograniczeniach pojedynczej bazy danych.
* Przepływność transakcji całkowitego obciążenia przekracza możliwości pojedynczej bazy danych.
* Dzierżawcy mogą wymagać fizycznej izolacji od siebie, więc dla każdej dzierżawy są potrzebne osobne bazy danych.
* Różne sekcje bazy danych mogą być konieczne w różnych lokalizacje geograficzneach w celu zapewnienia zgodności, wydajności lub geopolitycznej przyczyny.

W innych scenariuszach, takich jak pozyskiwanie danych z urządzeń rozproszonych, fragmentowania może służyć do wypełniania zestawu baz danych, które są organizowane okresowo. Na przykład oddzielna baza danych może być przeznaczona dla każdego dnia lub tygodnia. W takim przypadku klucz fragmentowania może być liczbą całkowitą reprezentującą datę (obecną we wszystkich wierszach tabel podzielonej na fragmenty), a zapytania pobierające informacje dla zakresu dat muszą być kierowane przez aplikację do podzestawu baz danych obejmujących dany zakres.

Fragmentowania działa najlepiej, gdy każda transakcja w aplikacji może być ograniczona do pojedynczej wartości klucza fragmentowania. Gwarantuje to, że wszystkie transakcje są lokalne dla określonej bazy danych.

## <a name="multi-tenant-and-single-tenant"></a>Wiele dzierżawców i jedna dzierżawa
Niektóre aplikacje wykorzystują najprostszą metodę tworzenia oddzielnej bazy danych dla każdej dzierżawy. To podejście jest **wzorcem fragmentowania o pojedynczej dzierżawie** , który zapewnia izolację, możliwość tworzenia kopii zapasowej/przywracania i skalowanie zasobów na poziomie szczegółowości dzierżawy. W przypadku pojedynczej dzierżawy fragmentowania każda baza danych jest skojarzona z określoną wartością identyfikatora dzierżawy (lub wartością klucza klienta), ale klucz ten nie zawsze musi znajdować się w samych danych. Jest on odpowiedzialny za kierowanie poszczególnych żądań do odpowiedniej bazy danych, a Biblioteka klienta może uprościć ten proces.

![Pojedynczy dzierżawca a wiele dzierżawców][4]

Inne scenariusze pakują wiele dzierżawców do baz danych, zamiast izolować je do oddzielnych baz danych. Ten wzorzec jest typowym **wielodostępnym wzorcem fragmentowania** — może to być spowodowane faktem, że aplikacja zarządza dużą liczbą małych dzierżawców. W fragmentowania z wieloma dzierżawcami wiersze w tabelach bazy danych są przeznaczone do przeprowadzenia klucza identyfikującego identyfikator dzierżawy lub klucz fragmentowania. Następnie warstwa aplikacji jest odpowiedzialna za kierowanie żądania dzierżawy do odpowiedniej bazy danych i może być obsługiwana przez bibliotekę klienta Elastic Database. Ponadto zabezpieczenia na poziomie wiersza mogą służyć do filtrowania wierszy, do których każdy dzierżawca ma dostęp — Aby uzyskać szczegółowe informacje, zobacz aplikacje wielodostępne [z narzędziami Elastic Database i zabezpieczeniami na poziomie wiersza](sql-database-elastic-tools-multi-tenant-row-level-security.md). Ponowne dystrybuowanie danych między bazami danych może być niezbędne w przypadku wzorca fragmentowania z wieloma dzierżawcami. jest to łatwiejsze w przypadku narzędzia do dzielenia i łączenia Elastic Database. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Przenoszenie danych z wielu do baz danych z jedną dzierżawą
Podczas tworzenia aplikacji SaaS jest ona typowa oferowana klientom potencjalną wersję próbną oprogramowania. W takim przypadku jest opłacalne korzystanie z bazy danych z wieloma dzierżawcami dla danych. Jednak gdy potencjalny klient zostanie klientem, baza danych o pojedynczej dzierżawie jest lepsza, ponieważ zapewnia lepszą wydajność. Jeśli klient utworzył dane w okresie próbnym, użyj [Narzędzia Split-Merge](sql-database-elastic-scale-overview-split-and-merge.md) , aby przenieść dane z wielu dzierżawców do nowej bazy danych z jedną dzierżawą.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać przykładową aplikację, która demonstruje bibliotekę kliencką, zobacz Wprowadzenie [do narzędzi Elastic Database](sql-database-elastic-scale-get-started.md).

Aby skonwertować istniejące bazy danych do korzystania z narzędzi, zobacz [Migrowanie istniejących baz danych w celu skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby wyświetlić informacje dotyczące puli elastycznej, zobacz [zagadnienia dotyczące cen i wydajności puli elastycznej](sql-database-elastic-pool.md)lub Utwórz nową pulę z [elastycznymi pulami](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

