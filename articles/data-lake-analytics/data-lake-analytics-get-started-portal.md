---
title: Utwórz Azure Data Lake Analytics kwerendy & — Azure Portal
description: Tworzenie konta usługi Data Lake Analytics i przesyłanie zadania w języku U-SQL przy użyciu witryny Azure Portal.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 6e51c6848476147073626119689407ebc02c036d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220962"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

W tym artykule opisano sposoby tworzenia kont usługi Azure Data Lake Analytics przy użyciu witryny Azure Portal, definiowania zadań w języku [U-SQL](data-lake-analytics-u-sql-get-started.md) oraz przesyłania zadań do usługi Data Lake Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka musisz dysponować **subskrypcją platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Tworzenie konta Data Lake Analytics

Teraz utworzysz Data Lake Analytics i konto Azure Data Lake Storage Gen1 w tym samym czasie.  Ten krok jest prosty i jego wykonanie zajmuje tylko około 60 sekund.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób**  >   **dane + analiza**  >  **Data Lake Analytics**.
3. Wybierz wartości dla następujących elementów:
   * **Nazwa**: nazwa konta usługi Data Lake Analytics (dozwolone są tylko małe litery i cyfry).
   * **Subskrypcja**: wybierz subskrypcję platformy Azure używaną na potrzeby konta usługi Analytics.
   * **Grupa zasobów**. Wybierz istniejącą grupę zasobów platformy Azure lub utwórz nową.
   * **Lokalizacja**. Wybierz centrum danych platformy Azure na potrzeby konta usługi Data Lake Analytics.
   * **Data Lake Storage Gen1**: Postępuj zgodnie z instrukcjami, aby utworzyć nowe konto Data Lake Storage Gen1 lub Wybierz istniejące. 
4. Opcjonalnie wybierz warstwę cenową dla konta usługi Data Lake Analytics.
5. Kliknij pozycję **Utwórz**. 


## <a name="your-first-u-sql-script"></a>Pierwszy skrypt U-SQL

Poniższy tekst to bardzo prosty skrypt U-SQL. Wszystko to definiuje niewielki zestaw danych w skrypcie, a następnie zapisuje ten zestaw danych do domyślnego konta Data Lake Storage Gen1 jako plik o nazwie `/data.csv` .

```usql
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Przesyłanie zadania U-SQL

1. Na koncie usługi Data Lake Analytics wybierz pozycję **Nowe zadanie**.
2. Wklej tekst powyższego skryptu U-SQL. Wprowadź nazwę zadania. 
3. Wybierz przycisk **Prześlij**, aby uruchomić zadanie.   
4. Monitoruj **Stan** zadania i zaczekaj, aż zmieni się na **Powodzenie**.
5. Wybierz kartę **dane** , a następnie wybierz kartę **wyjście** . Wybierz plik wyjściowy o nazwie `data.csv` i Wyświetl dane wyjściowe.

## <a name="see-also"></a>Zobacz też

* Aby rozpocząć tworzenie aplikacji w języku U-SQL, zobacz artykuł [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).
