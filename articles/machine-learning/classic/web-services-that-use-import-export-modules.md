---
title: 'ML Studio (klasyczny): importowanie/eksportowanie danych w usługach sieci Web — Azure'
description: Dowiedz się, jak wysyłać i odbierać dane z usługi sieci Web za pomocą modułów Importuj dane i Eksportuj dane.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dc348318401c9362636893d70294496c7012408
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308468"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Wdrażanie usług sieci Web Azure Machine Learning Studio (klasycznych), które używają modułów importowania danych i eksportu danych

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do. ](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Podczas tworzenia eksperymentu predykcyjnego zazwyczaj dodawane są dane wejściowe i wyjściowe usługi sieci Web. Podczas wdrażania eksperymentu klienci mogą wysyłać i odbierać dane z usługi sieci Web za pośrednictwem danych wejściowych i wyjściowych. W przypadku niektórych aplikacji dane użytkownika mogą być dostępne ze strumieniowego źródła danych lub już znajdują się w zewnętrznym źródle danych, takim jak Azure Blob Storage. W takich przypadkach nie potrzebują danych odczytu i zapisu przy użyciu wejściowych i wyjściowych usług sieci Web. Mogą zamiast tego używać usługi wykonywania wsadowego (BES) do odczytywania danych ze źródła danych przy użyciu modułu Importuj dane i zapisywania wyników oceniania w innej lokalizacji danych przy użyciu modułu eksport danych.

Moduły import danych i eksport danych umożliwiają odczytywanie i zapisywanie w różnych lokalizacjach danych, takich jak adres URL sieci Web za pośrednictwem protokołu HTTP, zapytanie programu Hive, baza danych w Azure SQL Database, Azure Table Storage, Azure Blob Storage, dostarczanie strumieniowe danych lub SQL Server Database.

W tym temacie użyto przykładu "Przykładowe 5: pociąg, test, szacuje się, że klasyfikacja binarna: zestaw danych dla dorosłych" i przyjęto założenie, że zestaw danych został już załadowany do tabeli Azure SQL o nazwie censusdata.

## <a name="create-the-training-experiment"></a>Tworzenie eksperymentu szkoleniowego
Po otwarciu "Przykładowe 5: uczenie, test, Oblicz dla klasyfikacji binarnej — zestaw danych dla dorosłych" używa przykładowego zestawu danych statystycznych zysków dla dorosłych spisu treści. A eksperyment na kanwie będzie wyglądać podobnie do poniższej ilustracji:

![Początkowa konfiguracja eksperymentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Aby odczytać dane z tabeli Azure SQL:

1. Usuń moduł DataSet.
2. W polu wyszukiwania składniki wpisz import.
3. Z listy wyników Dodaj moduł *Importuj dane* do kanwy eksperymentu.
4. Połącz dane wyjściowe modułu *Importuj dane* dane wejściowe modułu *czyste brakujące dane* .
5. W okienku właściwości wybierz pozycję **Azure SQL Database** na liście rozwijanej **Źródło danych** .
6. W polach **Nazwa serwera bazy danych** , **Nazwa bazy danych** , **Nazwa użytkownika** i **hasło** wprowadź odpowiednie informacje dotyczące bazy danych.
7. W polu kwerenda bazy danych wprowadź następujące zapytanie.

    ```tsql
     select [age],
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
    ```
8. W dolnej części kanwy eksperymentu kliknij pozycję **Uruchom**.

## <a name="create-the-predictive-experiment"></a>Utwórz eksperyment predykcyjny
Następnie należy skonfigurować eksperyment predykcyjny, z którego wdrażana jest usługa sieci Web.

1. W dolnej części kanwy eksperymentu kliknij pozycję **Skonfiguruj usługę sieci Web** i wybierz opcję **predykcyjna usługa sieci Web [zalecane]**.
2. Usuń moduły *danych wejściowych* i *usług* internetowych usługi sieci Web z eksperymentu predykcyjnego.
3. W polu wyszukiwania składniki wpisz Export.
4. Z listy wyników Dodaj moduł *eksportu danych* do kanwy eksperymentu.
5. Połącz dane wyjściowe modułu *wynik model* dane wejściowe modułu *eksport danych* .
6. W okienku właściwości wybierz pozycję **Azure SQL Database** na liście rozwijanej miejsce docelowe danych.
7. W polu **Nazwa serwera bazy danych** , **Nazwa bazy danych** , **nazwa konta użytkownika serwera** i **hasło konta użytkownika serwera** wprowadź odpowiednie informacje dotyczące bazy danych.
8. Na **liście rozdzielanych przecinkami kolumn, które mają być zapisane** pole wpisz etykiety z wynikami.
9. W **polu Nazwa tabeli danych** wpisz dbo. ScoredLabels. Jeśli tabela nie istnieje, jest tworzona, gdy eksperyment jest uruchamiany lub usługa sieci Web jest wywoływana.
10. Na **liście rozdzielanej przecinkami pól kolumny DataTable** wpisz ScoredLabels.

Podczas pisania aplikacji, która wywołuje końcową usługę sieci Web, może być konieczne określenie innej kwerendy wejściowej lub tabeli docelowej w czasie wykonywania. Aby skonfigurować te dane wejściowe i wyjściowe, użyj funkcji parametrów usługi sieci Web, aby ustawić właściwość *Źródło danych* modułu *Import* danych i Właściwość docelowa eksport danych trybu *dane* .  Aby uzyskać więcej informacji na temat parametrów usługi sieci Web, zobacz [wpis Azure Machine Learning Studio usługi sieci Web](/archive/blogs/machinelearning/azureml-web-service-parameters) w blogu Cortana Intelligence i Machine Learning.

Aby skonfigurować parametry usługi sieci Web dla kwerendy importu i tabeli docelowej:

1. W okienku właściwości modułu *Importuj dane* kliknij ikonę w prawym górnym rogu pola **kwerendy bazy danych** i wybierz pozycję **Ustaw jako parametr usługi sieci Web**.
2. W okienku właściwości modułu *eksport danych* kliknij ikonę w prawym górnym rogu pola **Nazwa tabeli danych** i wybierz pozycję **Ustaw jako parametr usługi sieci Web**.
3. W dolnej części okienka właściwości modułu *eksport danych* w sekcji **Parametry usługi sieci Web** kliknij pozycję kwerenda bazy danych i Zmień nazwę zapytania.
4. Kliknij pozycję **Nazwa tabeli danych** i zmień jej nazwę na **tabelę**.

Gdy wszystko będzie gotowe, eksperyment powinien wyglądać podobnie do poniższej ilustracji:

![Końcowy wygląd eksperymentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teraz można wdrożyć eksperyment jako usługę sieci Web.

## <a name="deploy-the-web-service"></a>Wdrażanie usługi internetowej
Można je wdrożyć w klasycznej lub nowej usłudze sieci Web.

### <a name="deploy-a-classic-web-service"></a>Wdrażanie klasycznej usługi sieci Web
Aby wdrożyć jako klasyczną usługę sieci Web i utworzyć aplikację w celu jej użycia:

1. W dolnej części kanwy eksperymentu kliknij pozycję Uruchom.
2. Po zakończeniu przebiegu kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [klasyczny]**.
3. Na pulpicie nawigacyjnym usługi sieci Web Znajdź klucz interfejsu API. Skopiuj i Zapisz go do późniejszego użycia.
4. W **domyślnej tabeli punktów końcowych** kliknij link **wykonywania wsadowego** , aby otworzyć stronę pomocy interfejsu API.
5. W programie Visual Studio Utwórz aplikację konsolową w języku C#: **Nowy**  >  **projekt**  >  **Visual C#**  >  **Windows Classic**  >  **Aplikacja konsolowa (.NET Framework)**.
6. Na stronie Pomoc interfejsu API Znajdź **przykładową sekcję kod** w dolnej części strony.
7. Skopiuj i wklej przykładowy kod w języku C# do pliku Program.cs, a następnie usuń wszystkie odwołania do magazynu obiektów BLOB.
8. Zaktualizuj wartość zmiennej *apiKey* z kluczem interfejsu API zapisanym wcześniej.
9. Znajdź deklarację żądania i zaktualizuj wartości parametrów usługi sieci Web, które są przesyłane do modułów *Importuj dane* i *Eksportuj dane* . W takim przypadku należy użyć oryginalnego zapytania, ale zdefiniować nową nazwę tabeli.

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. Uruchom aplikację.

Po zakończeniu przebiegu zostanie dodana nowa tabela zawierająca wyniki oceniania.

### <a name="deploy-a-new-web-service"></a>Wdróż nową usługę sieci Web

> [!NOTE]
> Aby wdrożyć nową usługę sieci Web, musisz mieć wystarczające uprawnienia w subskrypcji, w której wdrażana jest usługa sieci Web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci web Azure Machine Learning](manage-new-webservice.md).

Aby wdrożyć program jako nową usługę sieci Web i utworzyć aplikację do użycia:

1. W dolnej części kanwy eksperymentu kliknij pozycję **Uruchom**.
2. Po zakończeniu przebiegu kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [New]**.
3. Na stronie wdrażanie eksperymentu wprowadź nazwę usługi sieci Web i wybierz plan cenowy, a następnie kliknij przycisk **Wdróż**.
4. Na stronie **Szybki Start** **kliknij pozycję** Użyj.
5. W sekcji **przykładowy kod** kliknij pozycję **Batch**.
6. W programie Visual Studio Utwórz aplikację konsolową w języku C#: **Nowy**  >  **projekt**  >  **Visual C#**  >  **Windows Classic**  >  **Aplikacja konsolowa (.NET Framework)**.
7. Skopiuj i wklej przykładowy kod w języku C# do pliku Program.cs.
8. Zaktualizuj wartość zmiennej *apiKey* przy użyciu **klucza podstawowego** znajdującego się w sekcji **podstawowe informacje o zużyciu** .
9. Znajdź deklarację *scoreRequest* i zaktualizuj wartości parametrów usługi sieci Web, które są przesyłane do modułów *Importuj dane* i *Eksportuj dane* . W takim przypadku należy użyć oryginalnego zapytania, ale zdefiniować nową nazwę tabeli.

    ```csharp
    var scoreRequest = new
    {
        Inputs = new Dictionary<string, StringTable>()
        {
        },
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable3" },
        }
    };
    ```
10. Uruchom aplikację.