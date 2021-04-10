---
title: Data Lake Analytics ostatnie zmiany
description: Ten artykuł zawiera ciągłą listę najnowszych zmian wprowadzonych w celu Data Lake Analytics.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: cc3f9623f3b09c14e0e478252768890e4de3acad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025462"
---
# <a name="whats-new-in-data-lake-analytics"></a>Co nowego w usłudze Data Lake Analytics?

Azure Data Lake Analytics jest aktualizowany na podstawie aperiodic dla niektórych składników. Aby zaktualizować najnowszą aktualizację, ten artykuł zawiera informacje na temat:

- Powiadomienie dotyczące wersji zapoznawczej składnika Key Preview
- Ważna informacja o wersji składnika, na przykład: lista dostępnych wersji składnika, bieżąca wersja domyślna i tak dalej.


## <a name="notification-of-key-component-beta-preview"></a>Powiadomienie o składniku Key Preview wersji beta

Wersja beta składnika klucza nie jest dostępna dla wersji zapoznawczej. 

## <a name="u-sql-runtime"></a>Środowisko uruchomieniowe języka U-SQL

Azure Data Lake środowisko uruchomieniowe U-SQL, w tym kompilator, optymalizator i Menedżer zadań, polega na tym, że przetwarza kod U-SQL.

Gdy prześlesz zadanie Azure Data Lake Analytics z dowolnego narzędzia, zadanie będzie używać obecnie dostępnego domyślnego środowiska uruchomieniowego w środowisku produkcyjnym. 

Wersja środowiska uruchomieniowego zostanie zaktualizowana aperiodically. A poprzednie środowisko uruchomieniowe będzie dostępne przez jakiś czas. Gdy nowa wersja beta jest gotowa do wersji zapoznawczej, będzie ona również dostępna w tym miejscu.

> [!CAUTION]
> - Wybranie środowiska uruchomieniowego, które różni się od domyślnego, ma możliwość podziału zadań U-SQL. Zdecydowanie zaleca się, aby nie używać tych innych niż domyślne wersji dla środowiska produkcyjnego, ale tylko do celów testowych.
> - Niedomyślna wersja środowiska uruchomieniowego ma stały cykl życia. Zostanie ona automatycznie wygasła.

Następująca wersja jest bieżącą domyślną wersją środowiska uruchomieniowego.

- **release_20200707_scope_2b8d563_usql**

Aby uzyskać informacje na temat rozwiązywania błędów środowiska uruchomieniowego U-SQL, zobacz [Rozwiązywanie problemów z błędami środowiska uruchomieniowego u-SQL](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics teraz używa **.NET Framework v 4.7.2**. 

Jeśli kod skryptu w Azure Data Lake Analytics U-SQL używa zestawów niestandardowych, a te zestawy niestandardowe korzystają z bibliotek platformy .NET, sprawdź poprawność kodu, aby sprawdzić, czy występują jakiekolwiek przerwy.

Aby zrozumieć, jak rozwiązywać problemy z uaktualnieniem platformy .NET przy użyciu [narzędzia Rozwiązywanie problemów z uaktualnieniem platformy .NET](runtime-troubleshoot.md).

## <a name="release-note"></a>Uwaga dotycząca wersji

Aby uzyskać informacje o najnowszych aktualizacjach, zapoznaj się z informacjami o [wersji Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do Data Lake Analytics przy użyciu [](data-lake-analytics-get-started-portal.md)  |  [](data-lake-analytics-get-started-powershell.md)  |  [interfejsu wiersza polecenia](data-lake-analytics-get-started-cli.md) Azure Portal Azure PowerShell

