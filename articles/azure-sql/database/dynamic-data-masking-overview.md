---
title: Dynamiczne maskowanie danych
description: Dynamiczne maskowanie danych ogranicza narażenie na dane poufne przez zamaskowanie ich dla nieuprzywilejowanych użytkowników w celu Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/04/2020
tags: azure-synpase
ms.openlocfilehash: 0689cea221142ec9c9bdbb18ab82fab00a3e2fe5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398616"
---
# <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, wystąpienie zarządzane usługi Azure SQL i usługa Azure Synapse Analytics obsługują Dynamiczne maskowanie danych. Dynamiczne maskowanie danych ogranicza narażenie na dane poufne przez zamaskowanie ich dla użytkowników bez uprawnień. 

Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając klientom wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

Na przykład przedstawiciel usługi w centrum telefonicznym może identyfikować wywoływania według kilku cyfr numerów kart kredytowych, ale te elementy danych nie powinny być w pełni ujawnione dla przedstawiciela usługi. Można zdefiniować regułę maskowania, która będzie maskować wszystkie oprócz ostatnich czterech cyfr dowolnego numeru karty kredytowej w zestawie wyników dowolnego zapytania. Innym przykładem może być zdefiniowanie odpowiedniej maski danych w celu ochrony danych osobowych, dzięki czemu deweloper może wysyłać zapytania dotyczące środowisk produkcyjnych w celu rozwiązywania problemów bez naruszania przepisów dotyczących zgodności.

## <a name="dynamic-data-masking-basics"></a>Dynamiczne maskowanie danych — podstawy

Aby skonfigurować zasady dynamicznego maskowania danych w Azure Portal, wybierz blok **Dynamiczne maskowanie danych** w obszarze **zabezpieczenia** w okienku Konfiguracja SQL Database. Nie można ustawić tej funkcji przy użyciu portalu dla platformy Azure Synapse (Użyj programu PowerShell lub interfejsu API REST) ani wystąpienia zarządzanego SQL. Aby uzyskać więcej informacji, zobacz [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking).

### <a name="dynamic-data-masking-permissions"></a>Uprawnienia Dynamiczne maskowanie danych

Dynamiczne maskowanie danych można skonfigurować za pomocą ról Administrator Azure SQL Database, administrator serwera lub [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Zasady dynamicznego maskowania danych

* **Użytkownicy SQL wykluczeni z maskowania** — zestaw użytkowników SQL lub tożsamości usługi Azure AD, które uzyskują niemaskowane dane w wynikach zapytania SQL. Użytkownicy z uprawnieniami administratora są zawsze wykluczeni z maskowania i mogą zobaczyć oryginalne dane bez żadnej maski.
* **Reguły maskowania** — zestaw reguł definiujących wskazane pola, które mają być maskowane, oraz używaną funkcję maskowania. Wyznaczonymi polami można zdefiniować przy użyciu nazwy schematu bazy danych, nazwy tabeli i nazwy kolumny.
* **Funkcje maskowania** — zestaw metod kontrolujących narażenie danych w różnych scenariuszach.

| Funkcja maskowania | Maskowanie logiki |
| --- | --- |
| **Wartooć** |**Pełne maskowanie według typów danych określonych pól**<br/><br/>• Użyj XXXX lub mniejszej wartości XS, jeśli rozmiar pola jest krótszy niż 4 znaki dla typów danych ciągu (nchar, ntext, nvarchar).<br/>• Użyj wartości zerowej dla liczbowych typów danych (bigint, bit, decimal, int, Money, numeric, smallint, smallmoney, tinyint, float, Real).<br/>• Użyj 01-01-1900 dla typów danych Data/godzina (Date, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• Dla wariantu SQL, używana jest wartość domyślna bieżącego typu.<br/>• Dla pliku XML \<masked/> jest używany dokument.<br/>• Użyj pustej wartości dla specjalnych typów danych (tabela znaczników czasu, hierarchyid, GUID, Binary, Image, typy przestrzenne varbinary). |
| **Karta kredytowa** |**Metoda maskowania, która ujawnia cztery ostatnie cyfry wydzielonych pól** i dodaje stały ciąg jako prefiks w postaci karty kredytowej.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Poczta e-mail** |**Metoda maskowania, która uwidacznia pierwszą literę i zastępuje domenę xxx.com** przy użyciu stałego prefiksu ciągu w postaci adresu e-mail.<br/><br/>aXX@XXXX.com |
| **Liczba losowa** |**Metoda maskowania, która generuje liczbę losową** zgodnie z wybranymi granicami i rzeczywistymi typami danych. Jeśli wyznaczono granice są równe, funkcja maskowania jest liczbą stałą.<br/><br/>![Zrzut ekranu, który pokazuje metodę maskowania w celu wygenerowania liczby losowej.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Tekst niestandardowy** |**Metoda maskowania, która uwidacznia pierwsze i ostatnie znaki** i dodaje niestandardowy ciąg uzupełniania w środku. Jeśli oryginalny ciąg jest krótszy niż uwidoczniony prefiks i sufiks, zostanie użyty tylko ciąg uzupełniający. <br/>prefiks [dopełnienie] sufiks<br/><br/>![Okienko nawigacji](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Zalecane pola do maskowania

Aparat zaleceń DDM, który flaguje niektóre pola z bazy danych jako potencjalnie poufne pola, które mogą być dobrymi kandydatami do maskowania. W bloku Dynamiczne maskowanie danych w portalu zostaną wyświetlone zalecane kolumny dla bazy danych. Wystarczy kliknąć pozycję **Dodaj maskę** dla jednej lub większej liczby kolumn, a następnie **zapisać** , aby zastosować maskę dla tych pól.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Skonfiguruj Dynamiczne maskowanie danych dla bazy danych przy użyciu poleceń cmdlet programu PowerShell

### <a name="data-masking-policies"></a>Zasady maskowania danych

- [Get-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Reguły maskowania danych

- [Get-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Konfigurowanie dynamicznego maskowania danych dla bazy danych przy użyciu interfejsu API REST

Za pomocą interfejsu API REST można programowo zarządzać zasadami i regułami maskowania danych. Opublikowany interfejs API REST obsługuje następujące operacje:

### <a name="data-masking-policies"></a>Zasady maskowania danych

- [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/createorupdate): tworzy lub aktualizuje zasady maskowania danych bazy danych.
- [Get](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/get): Pobiera zasady maskowania danych bazy danych. 

### <a name="data-masking-rules"></a>Reguły maskowania danych

- [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/datamaskingrules/createorupdate): tworzy lub aktualizuje regułę maskowania danych bazy danych.
- [Lista według bazy danych](https://docs.microsoft.com/rest/api/sql/datamaskingrules/listbydatabase): Pobiera listę reguł maskowania danych bazy danych.
