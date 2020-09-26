---
title: Omówienie dostrajania automatycznego
description: Azure SQL Database i wystąpienie zarządzane usługi Azure SQL analizują zapytania SQL i automatycznie dostosowuje się do obciążenia użytkownika.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 03/30/2020
ms.openlocfilehash: 4c2faa6f015a8c1ce8f360155abdc14367d3057b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330744"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Dostrajanie automatyczne w Azure SQL Database i wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkcja automatycznego dostrajania Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL zapewniają wydajność szczytową i stabilną w ramach ciągłego dostrajania wydajności na podstawie systemu AI i uczenia maszynowego.

Dostrajanie automatyczne to w pełni zarządzana usługa Intelligent Performance, która używa wbudowanej analizy do ciągłego monitorowania zapytań wykonywanych w bazie danych i automatycznie usprawnia ich wydajność. Jest to osiągane dzięki dynamicznej adaptacji bazy danych do zmieniających się obciążeń i stosowania zaleceń dostrajania. Dostrajanie automatyczne nauczy się w poziomie od wszystkich baz danych na platformie Azure za pośrednictwem AI i dynamicznie ulepsza jego akcje dostrajania. Im dłużej działa baza danych z dostrajaniem automatycznym, tym lepiej jest to wykonywane.

Azure SQL Database i automatyczne dostrajanie wystąpienia zarządzanego usługi Azure SQL może być jedną z najważniejszych funkcji, które można włączyć, aby zapewnić stabilne i szczytowe obciążenia baz danych.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co może zrobić automatyczne dostrajanie

- Automatyczne dostrajanie wydajności baz danych
- Zautomatyzowana weryfikacja zysków z wydajności
- Automatyczne wycofywanie i samokorekcja
- Historia dostrajania
- Skrypty języka Transact-SQL (T-SQL) akcji dostrajania dla wdrożeń ręcznych
- Aktywne monitorowanie wydajności obciążeń
- Możliwość skalowania w poziomie na setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="safe-reliable-and-proven"></a>Bezpieczne, niezawodne i sprawdzone

Operacje dostrajania zastosowane do baz danych w Azure SQL Database są całkowicie bezpieczne dla wydajności najbardziej intensywnych obciążeń. System został zaprojektowany z myślą o tym, aby nie zakłócać obciążeń użytkowników. Zalecenia dotyczące dostrajania automatycznego są stosowane tylko w czasie niskiego użycia. System może również tymczasowo wyłączyć operacje dostrajania automatycznego w celu ochrony wydajności obciążeń. W takim przypadku w Azure Portal zostanie wyświetlony komunikat "wyłączone przez system". Dostrajanie automatyczne uwzględnia obciążenia z najwyższym priorytetem zasobu.

Automatyczne mechanizmy dostrajania są wczesne i idealnie nadaje się do kilku milionów baz danych działających na platformie Azure. Zastosowane operacje dostrajania automatycznego są weryfikowane automatycznie w celu zapewnienia pozytywnego zwiększenia wydajności obciążeń. Zalecenia dotyczące wydajności uległa pogorszeniu są dynamicznie wykrywane i natychmiast przywracane. Po zarejestrowaniu historii dostrajania istnieje jasne śledzenie ulepszeń dostrajania wykonanych dla każdej bazy danych w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.

![Jak działa dostrajanie automatyczne](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL Database dostrajanie automatyczne udostępnia swoją podstawową logikę przy użyciu funkcji dostrajania automatycznego SQL Server w aparacie bazy danych. Aby uzyskać dodatkowe informacje techniczne na temat wbudowanego mechanizmu analizy, zobacz [SQL Server dostrajania automatycznego](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

Aby zapoznać się z omówieniem działania dostrajania automatycznego i dla typowych scenariuszy użycia, zobacz osadzony film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania

- [Włączenie dostrajania automatycznego dla Azure SQL Database w Azure Portal](automatic-tuning-enable.md) lub przy użyciu instrukcji [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL.
- Automatyczne dostrajanie dla wystąpienia zarządzanego usługi Azure SQL można włączyć za pomocą instrukcji [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Opcje dostrajania automatycznego

Opcje dostrajania automatycznego dostępne w Azure SQL Database i wystąpieniu zarządzanym Azure SQL:

| Opcja dostrajania automatycznego | Obsługa pojedynczej bazy danych i bazy danych w puli | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **Create index** — identyfikuje indeksy, które mogą zwiększyć wydajność obciążenia, tworzy indeksy i automatycznie sprawdzają, czy wydajność zapytań została ulepszona. | Tak | Nie |
| **Drop index** — identyfikuje nadmiarowe i zduplikowane indeksy codziennie, z wyjątkiem unikatowych indeksów i indeksów, które nie były używane przez długi czas (>90 dni). Należy pamiętać, że ta opcja nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów. Usuwanie nieużywanych indeksów nie jest obsługiwane dla warstw usług premium i Krytyczne dla działania firmy. | Tak | Nie |
| **Wymuś ostatni dobry plan** (automatyczne korekcje planu) — IDENTYFIKUJE zapytania SQL platformy Azure przy użyciu planu wykonywania, który jest wolniejszy niż poprzedni dobry plan, i wykonuje zapytania przy użyciu ostatniego znanego dobrego planu zamiast planu uległa pogorszeniu. | Tak | Tak |

### <a name="automatic-tuning-for-sql-database"></a>Dostrajanie automatyczne dla SQL Database

Dostrajanie automatyczne dla Azure SQL Database używa zaleceń **tworzenia indeksu**, **Drop index**i **Wymuś ostatni dobry plan** usługi Database Advisor, aby zoptymalizować wydajność bazy danych. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące usługi Database Advisor w Azure Portal](database-advisor-find-recommendations-portal.md), w programie [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)i w [interfejsie API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Możesz ręcznie zastosować zalecenia dostosowawcze przy użyciu Azure Portal lub zezwolić na automatyczne dostosowywanie w sposób autonomiczny. Korzyści wynikające z zapewnienia, że system autonomicznie stosują zalecenia dostosowawcze, są automatycznie sprawdzane, czy istnieje pozytywny wzrost wydajności obciążeń i czy nie wykryto znacznej poprawy wydajności. spowoduje to automatyczne przywrócenie zalecenia dotyczącego dostrajania. Należy pamiętać, że w przypadku zapytań, na które mają wpływ zalecenia strojenia, które nie są wykonywane często, faza weryfikacji może trwać do 72 godzin.

W przypadku stosowania zaleceń dotyczących dostrajania przy użyciu języka T-SQL, automatyczne sprawdzanie poprawności wydajności i mechanizmy odwrócenia nie są dostępne. Zalecenia zastosowane w ten sposób pozostaną aktywne i wyświetlone na liście zaleceń dotyczących dostrajania dla 24-48 godzin. przed automatycznym wycofaniem ich przez system. Jeśli chcesz usunąć zalecenie wcześniej, możesz je odrzucić z Azure Portal.

Opcje dostrajania automatycznego mogą być niezależnie włączane lub wyłączane na bazę danych albo mogą być konfigurowane na poziomie serwera i stosowane do każdej bazy danych, która dziedziczy ustawienia z serwera. Serwery mogą dziedziczyć wartości domyślne platformy Azure dla ustawień dostrajania automatycznego. Ustawienia domyślne platformy Azure w tej chwili są ustawione na FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączona.

> [!IMPORTANT]
> Od marca 2020 zmiany ustawień domyślnych platformy Azure na potrzeby automatycznego dostrajania zaczną obowiązywać w następujący sposób:
>
> - Nowe wartości domyślne platformy Azure będą FORCE_LAST_GOOD_PLAN = włączone, CREATE_INDEX = wyłączone i DROP_INDEX = wyłączone.
> - Istniejące serwery bez skonfigurowanych preferencji automatycznego dostrajania zostaną automatycznie skonfigurowane w taki sposób, aby DZIEDZICZYŁY nowe ustawienia domyślne platformy Azure. Dotyczy to wszystkich klientów, którzy obecnie mają ustawienia serwera na potrzeby dostrajania automatycznego w niezdefiniowanym stanie.
> - Nowo utworzone serwery zostaną automatycznie skonfigurowane w taki sposób, aby DZIEDZICZYŁY nowe ustawienia domyślne platformy Azure (w przeciwieństwie do wcześniejszego stanu konfiguracji dostrajania automatycznego podczas tworzenia nowego serwera).

Skonfigurowanie opcji dostrajania automatycznego na serwerze i dziedziczenie ustawień dla baz danych należących do serwera nadrzędnego jest zalecaną metodą konfigurowania dostrajania automatycznego, ponieważ upraszcza to Zarządzanie opcjami dostrajania automatycznego dla dużej liczby baz danych.

Aby dowiedzieć się więcej o tworzeniu powiadomień e-mail dotyczących zaleceń dotyczących dostrajania automatycznego, zobacz [powiadomienia e-mail dotyczące dostrajania automatycznego](automatic-tuning-email-notifications-configure.md).

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Dostrajanie automatyczne dla wystąpienia zarządzanego usługi Azure SQL

Dostrajanie automatyczne dla wystąpienia zarządzanego SQL obsługuje tylko **wymuszanie ostatniego dobrego planu**. Aby uzyskać więcej informacji na temat konfigurowania opcji dostrajania automatycznego przy użyciu języka T-SQL, zobacz [dostrajanie automatyczne wprowadza automatyczne korekcje planu](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) i [automatyczną korektę planu](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat wbudowanej analizy używanej do dostrajania automatycznego, zobacz [sztuczna Optymalizacja dostrajania Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Aby dowiedzieć się, jak dostrajanie automatyczne działa pod okapem, zobacz [Automatyczne indeksowanie milionów baz danych w Microsoft Azure SQL Database](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).
