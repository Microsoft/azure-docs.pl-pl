---
title: Włączanie automatycznego dostrajania
description: Automatyczne dostrajanie można włączyć na Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299332"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Włączanie dostrajania automatycznego w celu monitorowania zapytań i zwiększania wydajności obciążeń

Azure SQL Database jest automatycznie zarządzaną usługą danych, która stale monitoruje zapytania i identyfikuje akcję, którą można wykonać w celu zwiększenia wydajności obciążeń. Możesz przejrzeć zalecenia i ręcznie zastosować je lub zezwolić Azure SQL Database automatycznie stosować akcje naprawcze — jest to nazywane **trybem dostrajania automatycznego**.

Dostrajanie automatyczne można włączyć na serwerze lub poziomie bazy danych za pomocą [Azure Portal](sql-database-automatic-tuning-enable.md#azure-portal), wywołań [interfejsu API REST](sql-database-automatic-tuning-enable.md#rest-api) i poleceń [języka T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) .

> [!NOTE]
> Dla wystąpienia zarządzanego, obsługiwana opcja FORCE_LAST_GOOD_PLAN można skonfigurować tylko przy użyciu [języka T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Opcje konfiguracji na podstawie portalu i automatycznego dostrajania indeksu opisane w tym artykule nie dotyczą wystąpienia zarządzanego.

> [!NOTE]
> Konfigurowanie opcji dostrajania automatycznego przy użyciu szablonu ARM (Azure Resource Manager) nie jest w tej chwili obsługiwane.

## <a name="enable-automatic-tuning-on-server"></a>Włącz automatyczne dostrajanie na serwerze

Na poziomie serwera można wybrać opcję Dziedzicz konfigurację dostrajania automatycznego z "ustawień domyślnych platformy Azure" lub nie odziedziczyć konfiguracji. Ustawienia domyślne platformy Azure to FORCE_LAST_GOOD_PLAN są włączone, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączone.

> [!IMPORTANT]
> Od marca 2020 zmiany ustawień domyślnych platformy Azure na potrzeby automatycznego dostrajania zaczną obowiązywać w następujący sposób:
>
> - Nowe wartości domyślne platformy Azure będą FORCE_LAST_GOOD_PLAN = włączone, CREATE_INDEX = wyłączone i DROP_INDEX = wyłączone.
> - Istniejące serwery bez skonfigurowanych preferencji automatycznego dostrajania zostaną automatycznie skonfigurowane w taki sposób, aby DZIEDZICZYŁY nowe ustawienia domyślne platformy Azure. Dotyczy to wszystkich klientów, którzy obecnie mają ustawienia serwera na potrzeby dostrajania automatycznego w niezdefiniowanym stanie.
> - Nowo utworzone serwery zostaną automatycznie skonfigurowane w taki sposób, aby DZIEDZICZYŁY nowe ustawienia domyślne platformy Azure (w przeciwieństwie do wcześniejszego stanu konfiguracji dostrajania automatycznego podczas tworzenia nowego serwera).

### <a name="azure-portal"></a>Azure Portal

Aby włączyć dostrajanie automatyczne na Azure SQL Database **serwerze**logicznym, przejdź do serwera w Azure Portal a następnie wybierz opcję **dostrajanie automatyczne** w menu.

![Serwer](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że opcja **DROP_INDEX** nie jest w tej chwili zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów i nie powinna być włączona w takich przypadkach. Usuwanie nieużywanych indeksów nie jest obsługiwane dla warstw usług premium i Krytyczne dla działania firmy.
>

Wybierz opcje dostrajania automatycznego, które chcesz włączyć, a następnie wybierz pozycję **Zastosuj**.

Opcje dostrajania automatycznego na serwerze są stosowane do wszystkich baz danych na tym serwerze. Domyślnie wszystkie bazy danych dziedziczą konfigurację z serwera nadrzędnego, ale można je zastąpić i określić dla każdej bazy danych osobno.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się więcej o korzystaniu z interfejsu API REST w celu włączenia dostrajania automatycznego na serwerze, zobacz [SQL Server aktualizowania dostrajania automatycznego i pobieranie metod http](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Włączanie dostrajania automatycznego dla pojedynczej bazy danych

Azure SQL Database pozwala indywidualnie określić konfigurację dostrajania automatycznego dla każdej bazy danych. Na poziomie bazy danych można wybrać dziedziczenie automatycznej konfiguracji dostrajania z serwera nadrzędnego, "ustawień domyślnych platformy Azure" lub nie do dziedziczenia konfiguracji. Ustawienia domyślne platformy Azure mają wartość FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest włączona, a DROP_INDEX jest wyłączone.

> [!TIP]
> Ogólnym zaleceniem jest zarządzanie automatyczną konfiguracją na **poziomie serwera** , dzięki czemu te same ustawienia konfiguracji mogą być stosowane automatycznie w każdej bazie danych. Skonfiguruj automatyczne dostrajanie dla pojedynczej bazy danych tylko wtedy, gdy baza danych ma inne ustawienia niż inne dziedziczą ustawienia z tego samego serwera.
>

### <a name="azure-portal"></a>Azure Portal

Aby włączyć dostrajanie automatyczne dla **pojedynczej bazy danych**, przejdź do bazy danych w Azure Portal i wybierz opcję **dostrajanie automatyczne**.

Indywidualne ustawienia dostrajania automatycznego można skonfigurować osobno dla każdej bazy danych. Możesz ręcznie skonfigurować indywidualną opcję dostrajania automatycznego lub określić, że opcja dziedziczy ustawienia z serwera.

![baza danych](./media/sql-database-automatic-tuning-enable/database.png)

Należy pamiętać, że opcja DROP_INDEX nie jest w tej chwili zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów i nie powinna być włączona w takich przypadkach.

Po wybraniu odpowiedniej konfiguracji kliknij przycisk **Zastosuj**.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się więcej o korzystaniu z interfejsu API REST w celu włączenia dostrajania automatycznego dla pojedynczej bazy danych, zobacz [SQL Database aktualizowania dostrajania automatycznego i pobieranie metod http](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Aby włączyć dostrajanie automatyczne dla pojedynczej bazy danych za pośrednictwem języka T-SQL, nawiąż połączenie z bazą danych i wykonaj następujące zapytanie:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Ustawienie automatycznego dostrajania na automatyczne spowoduje zastosowanie ustawień domyślnych platformy Azure. Ustawienie tej opcji na wartość INHERIT powoduje, że konfiguracja dostrajania automatycznego będzie dziedziczona z serwera nadrzędnego. W przypadku wybrania opcji niestandardowe należy ręcznie skonfigurować dostrajanie automatyczne.

Aby skonfigurować poszczególne opcje dostrajania automatycznego za pośrednictwem języka T-SQL, Połącz się z bazą danych i wykonaj zapytanie takie jak:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Ustawienie opcji dostrajania pojedynczej na włączone spowoduje zastąpienie wszystkich ustawień dziedziczonych przez bazę danych i włączenie opcji dostrajania. Ustawienie go na OFF spowoduje również zastąpienie wszystkich ustawień dziedziczonych przez bazę danych i wyłączenie opcji dostrajania. Opcja dostrajania automatycznego, dla której określono wartość domyślną, będzie dziedziczyć konfigurację dostrajania automatycznego z ustawień na poziomie serwera.  

> [!IMPORTANT]
> W przypadku [aktywnej replikacji geograficznej](sql-database-auto-failover-group.md), dostrajanie automatyczne należy skonfigurować tylko w podstawowej bazie danych. Automatycznie stosowane akcje dostrajania, takie jak na przykład tworzenie lub usuwanie indeksu, zostaną automatycznie zreplikowane do pomocniczego elementu tylko do odczytu. Próba włączenia dostrajania automatycznego przy użyciu języka T-SQL na pomocniczym serwerze z uprawnieniami tylko do odczytu spowoduje wystąpienie błędu z nieobsługiwaną konfiguracją dla elementu pomocniczego tylko do odczytu.
>

Aby skonfigurować funkcję automatycznego dostrajania, zobacz sekcję [ALTER DATABASE SET Options (Transact-SQL) dla programu SQL Database Server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Wyłączone przez system

Dostrajanie automatyczne sprawdza wszystkie akcje wykonywane w bazie danych, a w niektórych przypadkach może określić, że dostrajanie automatyczne nie może prawidłowo współpracować z bazą danych. W takiej sytuacji opcja dostrajania zostanie wyłączona przez system. W większości przypadków dzieje się tak, ponieważ magazyn zapytań nie jest włączony lub jest w stanie tylko do odczytu w określonej bazie danych.

## <a name="permissions"></a>Uprawnienia

Ponieważ Dostosowywanie automatyczne to funkcja platformy Azure, aby móc jej używać, należy użyć wbudowanych ról RBAC platformy Azure. Tylko uwierzytelnianie SQL nie jest wystarczające do korzystania z funkcji z Azure Portal.

Aby można było korzystać z dostrajania automatycznego, minimalnym wymaganym uprawnieniem do udzielenia użytkownikowi jest wbudowana rola [współautor bazy danych SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) platformy Azure. Możesz również rozważyć użycie wyższych ról przywilejów, takich SQL Server jak współautor, współautor i właściciel.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfiguruj automatyczne dostrajanie powiadomień e-mail

Zobacz przewodnik po [powiadomieniach o automatycznym dostrajania poczty e-mail](sql-database-automatic-tuning-email-notifications.md) .

## <a name="next-steps"></a>Następne kroki

* Przeczytaj artykuł dotyczący [dostrajania automatycznego](sql-database-automatic-tuning.md) , aby dowiedzieć się więcej na temat dostrajania automatycznego i jak może on ułatwić zwiększenie wydajności.
* Zobacz [zalecenia dotyczące wydajności](sql-database-advisor.md) , aby zapoznać się z omówieniem zaleceń dotyczących wydajności Azure SQL Database.
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) , aby dowiedzieć się więcej o wyświetlaniu wydajności najważniejszych zapytań.
