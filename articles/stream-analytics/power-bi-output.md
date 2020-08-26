---
title: Power BI dane wyjściowe z Azure Stream Analytics
description: W tym artykule opisano sposób wyprowadzania danych z Azure Stream Analytics do Power BI.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0fc4e9a794429e7f1c1609fac287e67dabb3c878
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875949"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI dane wyjściowe z Azure Stream Analytics

[Power BI](https://powerbi.microsoft.com/) można użyć jako danych wyjściowych dla zadania Stream Analytics, aby zapewnić bogate środowisko wizualizacji wyników analizy. Tej funkcji można użyć w przypadku operacyjnych pulpitów nawigacyjnych, generowania raportów i raportowania opartego na metrykach.

Power BI dane wyjściowe z Stream Analytics nie są obecnie dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

## <a name="ouput-configuration"></a>Konfiguracja dane wyjściowe

W poniższej tabeli wymieniono nazwy właściwości i ich opisy w celu skonfigurowania danych wyjściowych Power BI.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Podaj przyjazną nazwę używaną w zapytaniach, aby skierować dane wyjściowe zapytania do tego Power BI danych wyjściowych. |
| Obszar roboczy grupy |Aby włączyć udostępnianie danych innym użytkownikom Power BI, możesz wybrać grupy wewnątrz konta Power BI lub wybrać pozycję **Mój obszar roboczy** , jeśli nie chcesz pisać do grupy. Aktualizacja istniejącej grupy wymaga odnowienia Power BI uwierzytelniania. |
| Nazwa zestawu danych |Podaj nazwę zestawu danych, który ma być używany przez dane wyjściowe Power BI. |
| Nazwa tabeli |Podaj nazwę tabeli w zestawie danych danych wyjściowych Power BI. Obecnie Power BI dane wyjściowe z zadań Stream Analytics mogą mieć tylko jedną tabelę w zestawie danych. |
| Autoryzowanie połączenia | Musisz autoryzować się przy użyciu Power BI, aby skonfigurować ustawienia danych wyjściowych. Po udzieleniu tego danych wyjściowych do pulpitu nawigacyjnego Power BI można odwołać dostęp poprzez zmianę hasła konta użytkownika, usunięcie danych wyjściowych zadania lub usunięcie zadania Stream Analytics. | 

Przewodnik konfigurowania Power BI danych wyjściowych i pulpitu nawigacyjnego znajduje się w samouczku [Azure Stream Analytics i Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Nie twórz jawnie zestawu danych i tabeli na pulpicie nawigacyjnym Power BI. Zestaw danych i tabela są wypełniane automatycznie po rozpoczęciu zadania, a zadanie rozpoczyna pompowanie danych wyjściowych do Power BI. Jeśli zapytanie zadania nie generuje żadnych wyników, zestaw danych i tabela nie zostaną utworzone. Jeśli Power BI już istnieje zestaw danych i tabela o takiej samej nazwie jak nazwa podana w tym zadaniu Stream Analytics, istniejące dane zostaną zastąpione.
>

### <a name="create-a-schema"></a>Utwórz schemat

Azure Stream Analytics tworzy Power BI zestaw danych i schemat tabeli dla użytkownika, jeśli jeszcze nie istnieją. We wszystkich innych przypadkach tabela jest aktualizowana o nowe wartości. Obecnie w zestawie danych może istnieć tylko jedna tabela. 

Power BI używa zasad przechowywania w pierwszej kolejności (FIFO). Dane będą zbierane w tabeli, dopóki nie trafią 200 000 wierszy.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konwertuj typ danych z Stream Analytics na Power BI

Azure Stream Analytics automatycznie aktualizuje model danych w czasie wykonywania, jeśli zmieni się schemat danych wyjściowych. Zmiany nazwy kolumny, zmiany typu kolumny i Dodawanie lub usuwanie kolumn są śledzone.

W tej tabeli opisano konwersje typów danych z [Stream Analytics typów danych](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) do Power BI [Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), jeśli Power BI zestaw danych i tabela nie istnieją.

Z Stream Analytics | Aby Power BI
-----|-----
bigint | Int64
nvarchar (max) | Ciąg
datetime | Datetime (data/godzina)
float | Double
Rejestruj tablicę | Typ ciągu, stała wartość "IRecord" lub "IArray"

### <a name="update-the-schema"></a>Aktualizowanie schematu

Stream Analytics wnioskuje schemat modelu danych na podstawie pierwszego zestawu zdarzeń w danych wyjściowych. Później, w razie potrzeby, schemat modelu danych zostanie zaktualizowany w celu uwzględnienia zdarzeń przychodzących, które mogą nie pasować do oryginalnego schematu.

Należy unikać `SELECT *` zapytania, aby zapobiec dynamicznej aktualizacji schematu w wierszach. Oprócz potencjalnego wpływu na wydajność może to spowodować niepewność czasu trwania dla wyników. Wybierz dokładne pola, które mają być wyświetlane na pulpicie nawigacyjnym Power BI. Ponadto wartości danych powinny być zgodne z wybranym typem danych.

Poprzedni/bieżący | Int64 | Ciąg | Datetime (data/godzina) | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String (ciąg) | String (ciąg) | Double
Double | Double | String (ciąg) | String (ciąg) | Double
String (ciąg) | String (ciąg) | String (ciąg) | String (ciąg) | String (ciąg) 
Datetime (data/godzina) | String (ciąg) | String (ciąg) |  Datetime (data/godzina) | Ciąg

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

W przypadku wyjściowego rozmiaru partii zobacz [Power BI limity interfejsu API REST](https://msdn.microsoft.com/library/dn950053.aspx).

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-vs-code.md)
