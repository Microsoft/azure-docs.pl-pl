---
title: Przewodnik programowania U-SQL UDO dla Azure Data Lake
description: Dowiedz się więcej o Azure Data Lake Analytics programowaniu UDO U-SQL, aby umożliwić tworzenie dobrego skryptu USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512653"
---
# <a name="u-sql-user-defined-objects-overview"></a>Omówienie obiektów zdefiniowanych przez użytkownika w języku U-SQL


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: obiekty zdefiniowane przez użytkownika: UDO
Język U-SQL umożliwia Definiowanie niestandardowych obiektów programistycznych, które są nazywane obiektami zdefiniowanymi przez użytkownika lub UDO.

Poniżej znajduje się lista UDO w języku U-SQL:

* Ekstraktory zdefiniowane przez użytkownika
    * Wyodrębnij wiersz według wiersza
    * Służy do implementowania wyodrębniania danych z niestandardowych plików strukturalnych

* Wyskakujące definicje zdefiniowane przez użytkownika
    * Wiersz danych wyjściowych według wiersza
    * Używane do wyprowadzania niestandardowych typów danych lub niestandardowych formatów plików

* Procesory zdefiniowane przez użytkownika
    * Weź jeden wiersz i Wygeneruj jeden wiersz
    * Służy do zmniejszania liczby kolumn lub tworzenia nowych kolumn z wartościami, które są wyprowadzane z istniejącego zestawu kolumn

* Appliers zdefiniowany przez użytkownika
    * Weź jeden wiersz i Wygeneruj 0 do n wierszy
    * Stosowane z ZASTOSOWANIEm ZEWNĘTRZNYm/KRZYŻowym

* Połączenia zdefiniowane przez użytkownika
    * Łączy zestawy wierszy--zdefiniowane przez użytkownika sprzężeń

* Ograniczenia zdefiniowane przez użytkownika
    * Zrób n wierszy i Wygeneruj jeden wiersz
    * Służy do zmniejszania liczby wierszy

UDO jest zazwyczaj wywoływana jawnie w skrypcie U-SQL w ramach następujących instrukcji U-SQL:

* EXTRACT
* ROZDZIELCZOŚCI
* PODSTAWOWYCH
* ŻĄDANY
* ZMNIEJSZENIE

> [!NOTE]  
> UDO są ograniczone do zużywania 0,5 GB pamięci.  To ograniczenie pamięci nie ma zastosowania do lokalnych wykonań.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)