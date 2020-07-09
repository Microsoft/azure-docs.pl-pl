---
title: Używanie zadań w Azure Stream Analytics Edge z usługą Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o korzystaniu z Stream Analytics zadań w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: SQL Edge, Stream Analytics,
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e323c56f22c3a7d32bda5afe03a3462d3263f0d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254076"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Korzystanie z Azure Stream Analytics zadań przy użyciu programu SQL Edge

Usługa Azure SQL Edge (wersja zapoznawcza) to zoptymalizowany aparat relacyjnej bazy danych umożliwiający wdrażanie IoT i Edge. Jest on oparty na najnowszych wersjach aparatu bazy danych Microsoft SQL Server, który zapewnia wiodące w branży funkcje, zabezpieczenia i przetwarzanie zapytań. Dzięki wiodącym w branży funkcjom zarządzania relacyjnymi bazami danych SQL Server usługa Azure SQL Edge zapewnia wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i złożonych zdarzeń.

Usługa Azure SQL Edge zawiera natywną implementację środowiska uruchomieniowego usługi Stream Analytics. Ta implementacja pozwala utworzyć zadanie Azure Stream Analytics Edge i wdrożyć to zadanie jako zadanie przesyłania strumieniowego programu SQL Edge. Zadania Azure Stream Analytics można wdrażać w usłudze SQL Edge przy użyciu parametru ASAJobInfo dostępnego za pośrednictwem `module twin's desired properties` opcji modułu programu SQL Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Pole | Opis |
|------|-------------|
| SqlPackage | Identyfikator URI magazynu obiektów blob platformy Azure dla pliku *. zip, który zawiera pakiet SQL Database DAC.
| ASAJobInfo | Identyfikator URI magazynu obiektów blob platformy Azure dla zadania ASA Edge.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Tworzenie zadania Azure Stream Analytics Edge

1. Przejdź do witryny Azure Portal.

2. Utwórz nowe zadanie **Azure Stream Analytics na urządzeniach IoT Edge** . Wybierz środowisko hostingu, które jest przeznaczone dla **krawędzi**.

3. Zdefiniuj dane wejściowe i wyjściowe dla zadania Azure Stream Analytics. Wszystkie dane wyjściowe SQL, które należy skonfigurować w tym miejscu, są powiązane z pojedynczą tabelą w bazie danych. Jeśli zachodzi potrzeba przesyłania strumieniowego danych do wielu tabel, należy utworzyć wiele danych wyjściowych SQL Database. Można skonfigurować dane wyjściowe SQL, aby wskazywały na różne bazy danych.

    **Dane wejściowe**. Wybierz EdgeHub jako dane wejściowe dla zadania brzegowego i podaj informacje o zasobie.

    **Dane wyjściowe**. Wybierz SQL Database jako dane wyjściowe. Wybierz opcję **podaj SQL Database ustawienia ręcznie**. Podaj szczegóły konfiguracji bazy danych i tabeli.

    |Pole      | Opis |
    |---------------|-------------|
    |Alias danych wyjściowych | Nazwa aliasu danych wyjściowych.|
    |baza danych | Nazwa bazy danych. Musi być prawidłową nazwą bazy danych, która istnieje w wystąpieniu programu SQL Edge.|
    |Nazwa serwera | Nazwa (lub adres IP) i numer portu dla wystąpienia programu SQL Server. W przypadku wdrożenia programu SQL Edge można użyć **protokołu TCP:., 1433** jako nazwy serwera.|
    |Nazwa użytkownika | Konto logowania SQL, które ma dostęp do czytnika danych i składnika zapisywania danych do bazy danych określonej wcześniej.|
    |Hasło | Hasło do konta logowania SQL, które zostało określone wcześniej.|
    |Tabela | Nazwa tabeli, która będzie wyprowadzana dla zadania przesyłania strumieniowego.|
    |Dziedzicz partycjonowanie| Włącza dziedziczenie schematu partycjonowania poprzedniego kroku zapytania lub danych wejściowych. Gdy ta opcja jest włączona, można oczekiwać, że lepsza przepływność jest wyświetlana podczas zapisywania w tabeli opartej na dyskach i ma w pełni równoległą topologię dla danego zadania.|
    |Rozmiar wsadu| Maksymalna liczba rekordów wysyłanych z każdą zbiorczą transakcją wstawiania.|

    Oto Przykładowa konfiguracja danych wejściowych/wyjściowych:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat karty danych wyjściowych SQL dla Azure Stream Analytics, zobacz [Azure Stream Analytics danych wyjściowych do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Zdefiniuj zapytanie dotyczące zadania ASA dla zadania Edge. To zapytanie powinno używać zdefiniowanych aliasów Input/Output jako nazw wejściowych i wyjściowych w zapytaniu. Aby uzyskać więcej informacji, zobacz [Stream Analytics Dokumentacja języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Ustaw ustawienia konta magazynu dla zadania brzegowego. Konto magazynu jest używane jako miejsce docelowe publikowania dla zadania brzegowego.

6. W obszarze **Konfiguracja**wybierz pozycję **Publikuj**, a następnie wybierz przycisk **Publikuj** . Zapisz identyfikator URI sygnatury dostępu współdzielonego do użycia z modułem programu SQL Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Wdróż zadanie Azure Stream Analytics Edge w usłudze SQL Edge

Aby wdrożyć zadanie przesyłania strumieniowego do modułu SQL Edge, zaktualizuj konfigurację modułu programu SQL Edge, aby uwzględnić identyfikator URI sygnatury dostępu współdzielonego dla zadania przesyłania strumieniowego z wcześniejszego kroku. Aby zaktualizować moduł programu SQL Edge:

1. W Azure Portal przejdź do wdrożenia IoT Hub.

2. W lewym okienku wybierz pozycję **IoT Edge**.

3. Na stronie **IoT Edge** Znajdź i wybierz IoT Edge, w którym wdrożono moduł programu SQL Edge.

4. Na stronie urządzenie **IoT Edge** wybierz pozycję **Ustaw moduł**.

5. Na stronie **Ustawianie modułów** wybierz pozycję **Konfiguruj** względem modułu SQL Edge.

6. W okienku **IoT Edge modułów niestandardowych** wybierz opcję **Ustaw odpowiednie właściwości sznurka modułu**. Zaktualizuj odpowiednie właściwości, aby uwzględnić identyfikator URI dla `ASAJobInfo` opcji, jak pokazano w poniższym przykładzie.

    > [!NOTE]
    > Identyfikator URI sygnatury dostępu współdzielonego w poniższym kodzie JSON jest tylko przykładem. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Wybierz pozycję **Zapisz**.

8. Na stronie **Ustawianie modułów** wybierz pozycję **dalej**.

9. Na stronie **Ustawianie modułów** wybierz pozycję **dalej** , a następnie **Prześlij**.

10. Po aktualizacji modułu plik zadania usługi Stream Analytics zostanie pobrany, rozpakowany i wdrożony względem wystąpienia programu SQL Edge.

## <a name="next-steps"></a>Następne kroki

- [Wdróż program SQL Edge za Azure Portal](deploy-portal.md).

- [Przesyłanie strumieniowe danych](stream-data.md)

- [Uczenie maszynowe i AI z ONNX w usłudze SQL Edge (wersja zapoznawcza)](onnx-overview.md)
