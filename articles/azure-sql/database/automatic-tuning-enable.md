---
title: Włączanie automatycznego dostrajania
description: Automatyczne dostrajanie można włączyć w bazie danych przy użyciu Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 12/03/2019
ms.openlocfilehash: 35fc4b18ee5a98270f715a969354df2be5000150
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444086"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Włączanie dostrajania automatycznego w Azure Portal do monitorowania zapytań i zwiększania wydajności obciążeń
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL Database automatycznie zarządza usługami danych, które stale monitorują zapytania i identyfikują akcję, którą można wykonać w celu zwiększenia wydajności obciążeń. Możesz przejrzeć zalecenia i ręcznie zastosować je lub zezwolić Azure SQL Database automatycznie stosować akcje naprawcze — jest to nazywane **trybem dostrajania automatycznego**.

Dostrajanie automatyczne można włączyć na serwerze lub na poziomie bazy danych za pomocą:

- [Azure Portal](automatic-tuning-enable.md#azure-portal)
- Wywołania [interfejsu API REST](automatic-tuning-enable.md#rest-api)
- Polecenia [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)

> [!NOTE]
> W przypadku wystąpienia zarządzanego Azure SQL obsługiwane opcje FORCE_LAST_GOOD_PLAN można skonfigurować tylko przy użyciu [języka T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Opcje konfiguracji opartej na Azure Portal i automatyczne dostosowywanie indeksu opisane w tym artykule nie dotyczą wystąpienia zarządzanego Azure SQL.

> [!NOTE]
> Konfigurowanie opcji dostrajania automatycznego za pomocą szablonu ARM (Azure Resource Manager) nie jest w tej chwili obsługiwane.

## <a name="enable-automatic-tuning-on-server"></a>Włącz automatyczne dostrajanie na serwerze

Na poziomie serwera można wybrać opcję Dziedzicz konfigurację dostrajania automatycznego z "ustawień domyślnych platformy Azure" lub nie odziedziczyć konfiguracji. Ustawienia domyślne platformy Azure to FORCE_LAST_GOOD_PLAN są włączone, CREATE_INDEX jest wyłączone, a DROP_INDEX jest wyłączone.

> [!IMPORTANT]
> Od marca 2020 nowe ustawienia domyślne platformy Azure na potrzeby dostrajania automatycznego są następujące:
>
> - FORCE_LAST_GOOD_PLAN = włączone, CREATE_INDEX = wyłączone i DROP_INDEX = wyłączone.
> - Istniejące serwery bez skonfigurowanych preferencji automatycznego dostrajania są automatycznie konfigurowane do DZIEDZICZenia ustawień domyślnych platformy Azure. Dotyczy to wszystkich klientów, którzy obecnie mają ustawienia serwera na potrzeby dostrajania automatycznego w niezdefiniowanym stanie.
> - Nowo utworzone serwery zostaną automatycznie skonfigurowane w taki sposób, aby DZIEDZICZYŁY ustawienia domyślne platformy Azure (w przeciwieństwie do wcześniejszego stanu konfiguracji dostrajania automatycznego podczas tworzenia nowego serwera).

### <a name="azure-portal"></a>Azure Portal

Aby włączyć dostrajanie automatyczne na [serwerze](logical-servers.md) w Azure SQL Database, przejdź do serwera w Azure Portal a następnie wybierz opcję **dostrajanie automatyczne** w menu.

![Zrzut ekranu przedstawia dostrajanie automatyczne w Azure Portal, w którym można zastosować opcje dla serwera.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że opcja **DROP_INDEX** w tym momencie nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów i nie powinna być włączona w takich przypadkach. Usuwanie nieużywanych indeksów nie jest obsługiwane dla warstw usług premium i Krytyczne dla działania firmy.

Wybierz opcje dostrajania automatycznego, które chcesz włączyć, a następnie wybierz pozycję **Zastosuj**.

Opcje dostrajania automatycznego na serwerze są stosowane do wszystkich baz danych na tym serwerze. Domyślnie wszystkie bazy danych dziedziczą konfigurację z serwera nadrzędnego, ale można je zastąpić i określić dla każdej bazy danych osobno.

### <a name="rest-api"></a>Interfejs API REST

Aby dowiedzieć się więcej o korzystaniu z interfejsu API REST w celu włączenia dostrajania automatycznego na **serwerze**, zobacz [Aktualizacja dostrajania serwera i pobieranie metod http](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Włączanie dostrajania automatycznego dla pojedynczej bazy danych

Azure SQL Database pozwala indywidualnie określić konfigurację dostrajania automatycznego dla każdej bazy danych. Na poziomie bazy danych można wybrać dziedziczenie automatycznej konfiguracji dostrajania z serwera nadrzędnego, "ustawień domyślnych platformy Azure" lub nie do dziedziczenia konfiguracji. Ustawienia domyślne platformy Azure mają wartość FORCE_LAST_GOOD_PLAN jest włączona, CREATE_INDEX jest wyłączone, a DROP_INDEX jest wyłączone.

> [!TIP]
> Ogólnym zaleceniem jest zarządzanie automatyczną konfiguracją na **poziomie serwera** , dzięki czemu te same ustawienia konfiguracji mogą być stosowane automatycznie w każdej bazie danych. Skonfiguruj automatyczne dostrajanie dla pojedynczej bazy danych tylko wtedy, gdy baza danych ma inne ustawienia niż inne dziedziczą ustawienia z tego samego serwera.

### <a name="azure-portal"></a>Azure Portal

Aby włączyć dostrajanie automatyczne dla **pojedynczej bazy danych**, przejdź do bazy danych w Azure Portal i wybierz opcję **dostrajanie automatyczne**.

Indywidualne ustawienia dostrajania automatycznego można skonfigurować osobno dla każdej bazy danych. Możesz ręcznie skonfigurować indywidualną opcję dostrajania automatycznego lub określić, że opcja dziedziczy ustawienia z serwera.

![Zrzut ekranu przedstawia automatyczne dostrajanie w Azure Portal, w którym można zastosować opcje dla pojedynczej bazy danych.](./media/automatic-tuning-enable/database.png)

Należy pamiętać, że opcja DROP_INDEX nie jest w tej chwili zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksów i nie powinna być włączona w takich przypadkach.

Po wybraniu odpowiedniej konfiguracji kliknij przycisk **Zastosuj**.

### <a name="rest-api"></a>Interfejs API REST

Aby dowiedzieć się więcej o korzystaniu z interfejsu API REST w celu włączenia dostrajania automatycznego dla pojedynczej bazy danych, zobacz [Azure SQL Database automatyczne dostrajanie aktualizacji i pobieranie metod http](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Aby włączyć dostrajanie automatyczne dla pojedynczej bazy danych za pośrednictwem języka T-SQL, nawiąż połączenie z bazą danych i wykonaj następujące zapytanie:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Ustawienie automatycznego dostrajania na automatyczne spowoduje zastosowanie ustawień domyślnych platformy Azure. Ustawienie tej opcji na wartość INHERIT powoduje, że konfiguracja dostrajania automatycznego będzie dziedziczona z serwera nadrzędnego. W przypadku wybrania opcji niestandardowe należy ręcznie skonfigurować dostrajanie automatyczne.

Aby skonfigurować poszczególne opcje dostrajania automatycznego za pośrednictwem języka T-SQL, Połącz się z bazą danych i wykonaj zapytanie takie jak:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Ustawienie opcji dostrajania indywidualnego na włączone spowoduje zastąpienie wszystkich ustawień dziedziczonych przez bazę danych i włączenie opcji dostrajania. Ustawienie go na OFF spowoduje również zastąpienie wszystkich ustawień dziedziczonych przez bazę danych i wyłączenie opcji dostrajania. Opcja dostrajania automatycznego, dla której określono wartość domyślną, będzie dziedziczyć konfigurację dostrajania automatycznego z ustawień na poziomie serwera.  

> [!IMPORTANT]
> W przypadku [aktywnej replikacji geograficznej](auto-failover-group-overview.md), dostrajanie automatyczne należy skonfigurować tylko w podstawowej bazie danych. Automatycznie stosowane akcje dostrajania, takie jak na przykład tworzenie lub usuwanie indeksu, zostaną automatycznie zreplikowane do pomocniczego elementu tylko do odczytu. Próba włączenia dostrajania automatycznego przy użyciu języka T-SQL na pomocniczym serwerze z uprawnieniami tylko do odczytu spowoduje wystąpienie błędu z nieobsługiwaną konfiguracją dla elementu pomocniczego tylko do odczytu.
>

Aby dowiedzieć się więcej na temat opcji obejmować języka T-SQL w celu skonfigurowania dostrajania automatycznego, zobacz [ALTER DATABASE SET Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="disabled-by-the-system"></a>Wyłączone przez system

Dostrajanie automatyczne sprawdza wszystkie akcje wykonywane w bazie danych, a w niektórych przypadkach może określić, że dostrajanie automatyczne nie może prawidłowo współpracować z bazą danych. W takiej sytuacji opcja dostrajania zostanie wyłączona przez system. W większości przypadków dzieje się tak, ponieważ magazyn zapytań nie jest włączony lub jest w stanie tylko do odczytu w określonej bazie danych.

## <a name="permissions"></a>Uprawnienia

Ponieważ dostrajanie automatyczne to funkcja platformy Azure, aby jej używać, należy użyć wbudowanych ról platformy Azure. Tylko uwierzytelnianie SQL nie wystarcza do korzystania z funkcji z Azure Portal.

Aby można było korzystać z dostrajania automatycznego, minimalnym wymaganym uprawnieniem do udzielenia użytkownikowi jest wbudowana rola [współautor SQL Database](../../role-based-access-control/built-in-roles.md#sql-db-contributor) platformy Azure. Możesz również rozważyć użycie wyższych ról uprawnień, takich jak współautor SQL Server, współautor wystąpienia zarządzanego SQL, współautor i właściciel.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfiguruj automatyczne dostrajanie powiadomień e-mail

Zobacz przewodnik po [powiadomieniach o automatycznym dostrajania poczty e-mail](automatic-tuning-email-notifications-configure.md) .

## <a name="next-steps"></a>Następne kroki

- Przeczytaj artykuł dotyczący [dostrajania automatycznego](automatic-tuning-overview.md) , aby dowiedzieć się więcej na temat dostrajania automatycznego i jak może on ułatwić zwiększenie wydajności.
- Zobacz [zalecenia dotyczące wydajności](database-advisor-implement-performance-recommendations.md) , aby zapoznać się z omówieniem zaleceń dotyczących wydajności Azure SQL Database.
- Zobacz [szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md) , aby dowiedzieć się więcej o wyświetlaniu wydajności najważniejszych zapytań.
