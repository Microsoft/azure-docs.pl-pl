---
title: Konfigurowanie zabezpieczeń na potrzeby odzyskiwania po awarii
description: Zapoznaj się z zagadnieniami dotyczącymi zabezpieczeń w zakresie konfigurowania zabezpieczeń i zarządzania nimi po przywróceniu bazy danych lub przejściu do trybu failover na serwerze pomocniczym.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 92a0c7fd3733b5e27c34c6fd0fe157bfb466a0fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444895"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurowanie zabezpieczeń Azure SQL Database i zarządzanie nimi na potrzeby przywracania geograficznego lub przełączenia w tryb failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano wymagania dotyczące uwierzytelniania w celu skonfigurowania i kontrolowania [aktywnej replikacji geograficznej](active-geo-replication-overview.md) oraz [grup autotrybu failover](auto-failover-group-overview.md). Zawiera również kroki wymagane do skonfigurowania dostępu użytkownika do pomocniczej bazy danych. Na koniec opisano również sposób włączania dostępu do odzyskiwanej bazy danych po użyciu funkcji [przywracania geograficznego](recovery-using-backups.md#geo-restore). Aby uzyskać więcej informacji na temat opcji odzyskiwania, zobacz [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="disaster-recovery-with-contained-users"></a>Odzyskiwanie po awarii z zawartymi użytkownikami

W przeciwieństwie do tradycyjnych użytkowników, które muszą być mapowane na nazwy logowania w bazie danych Master, zawarty użytkownik jest całkowicie zarządzany przez samą bazę danych. Ma to dwie korzyści. W scenariuszu odzyskiwania po awarii użytkownicy mogą nadal łączyć się z nową podstawową bazą danych lub bazą danych odzyskaną przy użyciu funkcji przywracania geograficznego bez żadnej dodatkowej konfiguracji, ponieważ baza danych zarządza użytkownikami. Istnieją także potencjalne korzyści z skalowalności i wydajności z tej konfiguracji z perspektywy logowania. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).

Głównym kompromisem jest to, że zarządzanie procesem odzyskiwania po awarii w dużej skali jest trudniejsze. Jeśli masz wiele baz danych korzystających z tego samego logowania, obsługa poświadczeń przy użyciu zawartych użytkowników w wielu bazach danych może Negate korzyści dla zawartych użytkowników. Na przykład zasady rotacji haseł wymagają spójnego wprowadzania zmian w wielu bazach danych zamiast zmiany hasła logowania jednokrotnego w bazie danych Master. Z tego powodu, jeśli masz wiele baz danych, które używają tej samej nazwy użytkownika i hasła, korzystanie z zawartych użytkowników nie jest zalecane.

## <a name="how-to-configure-logins-and-users"></a>Jak skonfigurować logowania i użytkowników

Jeśli używasz nazw logowania i użytkowników (a nie zawartych użytkowników), musisz wykonać dodatkowe czynności, aby upewnić się, że te same nazwy logowania istnieją w bazie danych Master. W poniższych sekcjach opisano kroki i dodatkowe zagadnienia.

  >[!NOTE]
  > Możliwe jest również korzystanie z nazw logowania Azure Active Directory (AAD) do zarządzania bazami danych. Aby uzyskać więcej informacji, zobacz temat [logowania i użytkownicy usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurowanie dostępu użytkowników do pomocniczej lub odzyskiwanej bazy danych

Aby pomocnicza baza danych mogła być używana jako pomocnicza baza danych tylko do odczytu, a w celu zapewnienia prawidłowego dostępu do nowej podstawowej bazy danych lub bazy danych odzyskanej przy użyciu funkcji przywracania geograficznego, baza danych Master serwera docelowego musi mieć odpowiednią konfigurację zabezpieczeń przed odzyskiwaniem.

Określone uprawnienia dla każdego kroku są opisane w dalszej części tego tematu.

Przygotowanie dostępu użytkowników do replikacji geograficznej należy wykonać w ramach konfigurowania replikacji geograficznej. Przygotowywanie dostępu użytkowników do baz danych przywróconych geograficznie należy przeprowadzić w dowolnym momencie, gdy oryginalny serwer jest w trybie online (np. w ramach przechodzenia do szczegółów odzyskiwania po awarii).

> [!NOTE]
> W przypadku przełączenia w tryb failover lub przywracania geograficznego na serwer, który nie ma prawidłowo skonfigurowanych logowań, dostęp do niego będzie ograniczony do konta administratora serwera.

Konfigurowanie nazw logowania na serwerze docelowym obejmuje trzy kroki opisane poniżej:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Określ nazwy logowania z dostępem do podstawowej bazy danych

Pierwszym krokiem procesu jest określenie, które nazwy logowania muszą być zduplikowane na serwerze docelowym. Jest to realizowane za pomocą pary instrukcji SELECT, jednej w logicznej bazie danych Master na serwerze źródłowym i jednej w podstawowej bazie danych.

Tylko administrator serwera lub członek roli serwera **LoginManager** może określić nazwy logowania na serwerze źródłowym przy użyciu następującej instrukcji SELECT.

```sql
SELECT [name], [sid]
FROM [sys].[sql_logins]
WHERE [type_desc] = 'SQL_Login'
```

Tylko członek roli bazy danych db_owner, użytkownik dbo lub administrator serwera, może określić wszystkie nazwy główne użytkownika bazy danych w podstawowej bazie danych.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Znajdź identyfikator SID dla logowań zidentyfikowanych w kroku 1

Porównując dane wyjściowe zapytań z poprzedniej sekcji i pasujące do identyfikatorów SID, możesz zmapować nazwę logowania serwera na użytkownika bazy danych. Nazwy logowania, które mają użytkownika bazy danych o pasującym identyfikatorze SID, mają dostęp użytkownika do tej bazy danych jako podmiot zabezpieczeń bazy danych.

Poniższe zapytanie może służyć do wyświetlania wszystkich podmiotów zabezpieczeń i ich identyfikatorów SID w bazie danych. Tylko członek roli bazy danych db_owner lub administrator serwera może uruchomić to zapytanie.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

> [!NOTE]
> Użytkownicy **INFORMATION_SCHEMA** i **sys** mają *null* identyfikatorów SID, a identyfikator SID **gościa** to **0x00**. Identyfikator SID **dbo** może zaczynać się od *0x01060000000001648000000000048454*, jeśli twórca bazy danych był administratorem serwera, a nie członkiem programu **DBManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Utwórz identyfikatory logowania na serwerze docelowym

Ostatnim krokiem jest przejście do serwera docelowego lub serwerów, a następnie wygenerowanie identyfikatorów logowania przy użyciu odpowiednich identyfikatorów SID. Podstawowa składnia jest następująca.

```sql
CREATE LOGIN [<login name>]
WITH PASSWORD = <login password>,
SID = <desired login SID>
```

> [!NOTE]
> Jeśli chcesz udzielić użytkownikowi dostępu do pomocniczego, ale nie do podstawowego, możesz to zrobić, zmieniając nazwę logowania użytkownika na serwerze podstawowym przy użyciu następującej składni.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Wartość Wyłącz nie zmienia hasła, więc zawsze można ją włączyć, jeśli jest to konieczne.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat zarządzania dostępem do bazy danych i logowaniem, zobacz [SQL Database Security: zarządzanie dostępem do bazy danych i zabezpieczeniami logowania](logins-create-manage.md).
* Aby uzyskać więcej informacji o użytkownikach zawartej bazy danych, zobacz [Użytkownicy zawartej bazy danych — Tworzenie przenośnej bazy danych](https://msdn.microsoft.com/library/ff929188.aspx).
* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej, zobacz [aktywną replikację geograficzną](active-geo-replication-overview.md).
* Aby dowiedzieć się więcej na temat grup autotrybu failover, zobacz [grupy autopraca awaryjna](auto-failover-group-overview.md).
* Aby uzyskać informacje o korzystaniu z przywracania geograficznego, zobacz [geograficznie przywracanie](recovery-using-backups.md#geo-restore)
