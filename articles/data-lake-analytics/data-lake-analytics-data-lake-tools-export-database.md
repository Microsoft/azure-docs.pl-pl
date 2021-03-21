---
title: Eksportowanie bazy danych U-SQL — Azure Data Lake Tools for Visual Studio
description: Dowiedz się, w jaki sposób używać Azure Data Lake Tools for Visual Studio do eksportowania bazy danych U-SQL i automatycznego importowania jej do konta lokalnego.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 0a768990a33f0d60a3222bd3ad323edcc53c9ec0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219092"
---
# <a name="export-a-u-sql-database"></a>Eksportowanie bazy danych U-SQL

W tym artykule dowiesz się, jak za pomocą [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) eksportować bazę danych u-SQL jako jeden skrypt u-SQL i pobrane zasoby. Wyeksportowaną bazę danych można zaimportować do konta lokalnego w tym samym procesie.

Klienci zwykle utrzymują wiele środowisk na potrzeby programowania, testowania i produkcji. Te środowiska są hostowane zarówno na koncie lokalnym, na komputerze lokalnym dewelopera, jak i na koncie Azure Data Lake Analytics na platformie Azure. 

Podczas opracowywania i dostrajania zapytań U-SQL w środowiskach deweloperskich i testowych deweloperzy często muszą ponownie utworzyć swoją służbową w produkcyjnej bazie danych. Kreator eksportu bazy danych ułatwia przyspieszenie tego procesu. Za pomocą Kreatora deweloperzy mogą klonować istniejące środowisko bazy danych i dane przykładowe do innych kont Data Lake Analytics.

## <a name="export-steps"></a>Etapy eksportowania

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1. Eksportowanie bazy danych w Eksplorator serwera

Wszystkie konta Data Lake Analytics, do których masz uprawnienia, są wymienione w Eksplorator serwera. Aby wyeksportować bazę danych:

1. W Eksplorator serwera rozwiń konto, które zawiera bazę danych, która ma zostać wyeksportowana.
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Eksportuj**. 
   
    ![Eksplorator serwera — Eksportowanie bazy danych](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Jeśli opcja menu **Eksportuj** jest niedostępna, należy [zaktualizować narzędzie do najnowszej wersji](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2. Konfigurowanie obiektów, które mają zostać wyeksportowane

Jeśli potrzebna jest tylko niewielka część dużej bazy danych, można skonfigurować podzestaw obiektów, które mają zostać wyeksportowane w Kreatorze eksportu. 

Akcja eksportowania jest wykonywana przez uruchomienie zadania U-SQL. W związku z tym Eksportowanie z konta platformy Azure wiąże się z pewnym kosztem.

![Kreator eksportu bazy danych — Wybieranie opcji Eksportuj obiekty](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3. Sprawdzenie listy obiektów i innych konfiguracji

W tym kroku można zweryfikować wybrane obiekty w polu **listy Eksportuj obiekt** . Jeśli występują błędy, wybierz pozycję **Wstecz** , aby wrócić i poprawnie skonfigurować obiekty, które chcesz wyeksportować.

Można także skonfigurować inne ustawienia dla elementu docelowego eksportu. Opisy konfiguracji są wymienione w poniższej tabeli:

|Konfiguracja|Opis|
|-------------|-----------|
|Nazwa miejsca docelowego|Ta nazwa wskazuje, gdzie mają zostać zapisane wyeksportowane zasoby bazy danych. Przykłady to zestawy, dodatkowe pliki i przykładowe dane. Folder o tej nazwie jest tworzony w folderze głównym danych lokalnych.|
|Katalog projektu|Ta ścieżka definiuje, gdzie ma być zapisany wyeksportowany skrypt U-SQL. Wszystkie definicje obiektów bazy danych są zapisywane w tej lokalizacji.|
|Tylko schemat|W przypadku wybrania tej opcji eksportowane są tylko definicje i zasoby bazy danych (takie jak zestawy i pliki dodatkowe).|
|Schemat i dane|W przypadku wybrania tej opcji eksportowane są definicje baz danych, zasoby i dane. Wszystkie pierwsze N wierszy tabel są eksportowane.|
|Automatycznie Importuj do lokalnej bazy danych|W przypadku wybrania tej opcji wyeksportowana baza danych zostanie automatycznie zaimportowana do lokalnej bazy danych po zakończeniu eksportowania.|

![Kreator eksportu bazy danych — Eksportowanie listy obiektów i innych konfiguracji](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4. sprawdzenie wyników eksportu

Po zakończeniu eksportowania można wyświetlić eksportowane wyniki w oknie Dziennik w kreatorze. Poniższy przykład pokazuje, jak znaleźć wyeksportowany skrypt U-SQL i zasoby bazy danych, w tym zestawy, dodatkowe pliki i dane przykładowe:

![Kreator eksportu bazy danych — Eksportowanie wyników](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importowanie wyeksportowanej bazy danych do konta lokalnego

Najbardziej wygodnym sposobem na zaimportowanie wyeksportowanej bazy danych jest zaznaczenie pola wyboru **Importuj do lokalnej bazy danych automatycznie** w trakcie procesu eksportowania w kroku 3. Jeśli to pole nie zostało zaznaczone, najpierw Znajdź wyeksportowany skrypt U-SQL w dzienniku eksportu. Następnie uruchom skrypt U-SQL lokalnie, aby zaimportować bazę danych do konta lokalnego.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importowanie wyeksportowanej bazy danych do konta Data Lake Analytics

Aby zaimportować bazę danych do innego konta Data Lake Analytics:

1. Przekaż wyeksportowane zasoby, w tym zestawy, dodatkowe pliki i dane przykładowe, do domyślnego konta Azure Data Lake Store konta Data Lake Analytics, które chcesz zaimportować do programu. Wyeksportowany folder zasobów można znaleźć w folderze głównym danych lokalnych. Przekaż cały folder do katalogu głównego domyślnego konta Data Lake Store.
2. Po zakończeniu przekazywania Prześlij wyeksportowany skrypt U-SQL do konta Data Lake Analytics, do którego chcesz zaimportować bazę danych.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie w przypadku wybrania opcji **schemat i dane** w kroku 3 Narzędzie uruchamia zadanie U-SQL w celu wyeksportowania danych przechowywanych w tabelach. Z tego powodu proces eksportowania danych może potrwać wolno i być może nastąpi policzenie kosztów. 

## <a name="next-steps"></a>Następne kroki

* [Informacje o bazach danych U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


