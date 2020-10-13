---
title: Co to jest zabezpieczenia na poziomie kolumny w usłudze Azure Synapse?
description: Column-Level zabezpieczenia umożliwiają klientom kontrolowanie dostępu do kolumn tabeli bazy danych na podstawie kontekstu wykonywania użytkownika lub członkostwa w grupie, upraszczając projektowanie i kodowanie zabezpieczeń w aplikacji oraz pozwalające na wdrożenie ograniczeń dostępu do kolumn.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: f8bb40f9c80a0785c81c7aeacf783553bf73aa90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259887"
---
# <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny

Column-Level zabezpieczenia umożliwiają klientom kontrolowanie dostępu do kolumn tabeli na podstawie kontekstu wykonywania użytkownika lub członkostwa w grupie.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Ponieważ to wideo zostało ogłoszone, [zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) stały się dostępne dla usługi Azure Synapse.

Zabezpieczenia na poziomie kolumny upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji, co pozwala ograniczyć dostęp do kolumn w celu ochrony poufnych danych. Na przykład w celu zapewnienia, że określeni użytkownicy będą mieli dostęp tylko do niektórych kolumn tabeli odnoszących się do ich działu. Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie na danych w innej warstwie aplikacji. Baza danych stosuje ograniczenia dostępu przy każdym próbie dostępu do danych z dowolnej warstwy. To ograniczenie sprawia, że zabezpieczenia są bardziej niezawodne i niezawodne przez zredukowanie obszaru ogólnego systemu zabezpieczeń. Ponadto zabezpieczenia na poziomie kolumny eliminują również konieczność wprowadzenia widoków do filtrowania kolumn w celu nakładania ograniczeń dostępu dla użytkowników.

Zabezpieczenia na poziomie kolumny można zaimplementować przy użyciu instrukcji [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) języka T-SQL. W przypadku tego mechanizmu obsługiwane są zarówno uwierzytelnianie SQL, jak i Azure Active Directory (Azure AD).

![Diagram przedstawia tabelę schematyczną z pierwszą kolumną, w której znajduje się ZAMKNIĘTA kłódka i jej komórki kolorem pomarańczowym, podczas gdy inne kolumny są białymi komórkami.](./media/column-level-security/cls.png)

## <a name="syntax"></a>Składnia

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak ograniczyć `TestUser` dostęp do `SSN` kolumny `Membership` tabeli:

Utwórz `Membership` tabelę z kolumną SSN służącą do przechowywania numerów PESEL:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Zezwalaj `TestUser` na dostęp do wszystkich kolumn z wyjątkiem kolumny SSN, która ma poufne dane:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Zapytania wykonane jako zakończą `TestUser` się niepowodzeniem, jeśli zawierają kolumnę SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Przypadki użycia

Przykłady sposobu używania zabezpieczeń na poziomie kolumny:

- Firma Financial Services zezwala tylko menedżerom kont na dostęp do numerów PESEL (SSN) klienta, numerów telefonów i innych informacji umożliwiających identyfikację użytkownika.
- Dostawca opieki zdrowotnej umożliwia tylko lekarzy i pielęgniarkom dostęp do poufnych rekordów medycznych, jednocześnie uniemożliwiając członkom działu rozliczania wyświetlanie tych danych.
