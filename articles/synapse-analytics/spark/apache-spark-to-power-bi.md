---
title: Notesy programu Azure Synapse Studio
description: Ten samouczek zawiera omówienie sposobu tworzenia pulpitu nawigacyjnego Power BI przy użyciu Apache Spark i puli SQL bezserwerowej.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 791cab369dcbf9cab8d1256377cfee4a433c21b9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450898"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Samouczek: Tworzenie raportu Power BI przy użyciu Apache Spark i usługi Azure Synapse Analytics

Organizacje często muszą przetwarzać duże ilości danych przed przekazaniem kluczowych stronom biznesowym. W tym samouczku dowiesz się, jak wykorzystać zintegrowane środowiska w usłudze Azure Synapse Analytics, aby przetwarzać dane przy użyciu Apache Spark i później udostępniać je użytkownikom końcowym za pośrednictwem Power BI i SQL Server.

## <a name="before-you-begin"></a>Przed rozpoczęciem
- [Obszar roboczy usługi Azure Synapse Analytics](../quickstart-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. 
- Power BI obszar roboczy i Power BI Desktop do wizualizacji danych. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie obszaru roboczego Power BI](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces) i [Instalowanie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Połączona usługa do łączenia obszarów roboczych usługi Azure Synapse Analytics i Power BI. Aby uzyskać szczegółowe informacje, zobacz [łączenie z obszarem roboczym Power BI](../quickstart-power-bi.md)
- Bezserwerowa Pula Apache Spark w obszarze roboczym analizy Synapse. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli Apache Spark bezserwerowej](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Pobieranie i przygotowywanie danych
W tym przykładzie użyjesz Apache Spark, aby przeprowadzić analizę danych TIP podróży od nowa Jorku. Dane są dostępne za pomocą [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Ten podzestaw zestawu danych zawiera informacje dotyczące żółtej podróży z taksówką, w tym informacje o każdej podróży, godzinie rozpoczęcia i zakończenia oraz lokalizacji, kosztu i innych interesujących atrybutów.

1. Uruchom następujące wiersze, aby utworzyć ramkę danych Spark, wklejając kod w nowej komórce. Spowoduje to pobranie danych za pośrednictwem interfejsu API Open DataSets. Ściąganie wszystkich tych danych spowoduje wygenerowanie około 1 500 000 000 wierszy. Poniższy przykład kodu używa start_date i end_date do zastosowania filtru zwracającego pojedynczy miesiąc danych.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Przy użyciu Apache Spark SQL zostanie utworzona baza danych o nazwie NycTlcTutorial. Ta baza danych będzie używana do przechowywania wyników przetwarzania danych.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Następnie użyjemy operacji platformy Spark Dataframe do przetwarzania danych. W poniższym kodzie wykonujemy następujące przekształcenia:
   1. Usuwanie kolumn, które nie są zbędne.
   2. Usuwanie elementów wartości odstających/nieprawidłowych za pomocą filtrowania.
   3. Tworzenie nowych funkcji, takich jak ```tripTimeSecs``` i ```tipped``` na potrzeby dodatkowej analizy.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Na koniec będziemy zapisywać nasze ramki danych przy użyciu metody Apache Spark ```saveAsTable``` . Umożliwi to późniejsze wykonywanie zapytań i nawiązywanie połączenia z tą samą tabelą przy użyciu pul SQL bezserwerowych.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Wykonywanie zapytań dotyczących danych przy użyciu pul SQL bezserwerowych
Usługa Azure Synapse Analytics umożliwia korzystanie z różnych aparatów obliczeniowych obszarów roboczych w celu udostępniania baz danych i tabel między jej pulami Apache Spark i bezserwerową pulą SQL. Jest to obsługiwane za pomocą funkcji [zarządzania metadanymi](../metadata/overview.md) Synapse. W związku z tym bazy danych utworzone przez platformę Spark i ich tabele Parquet są widoczne w puli SQL bezserwerowej.

Aby wykonać zapytanie dotyczące Apache Spark tabeli przy użyciu puli SQL bezserwerowej:
   1. Po zapisaniu tabeli Apache Spark przejdź do karty **dane** .
   
   2. W obszarze **obszary robocze** Znajdź właśnie utworzoną tabelę Apache Spark i wybierz pozycję **Nowy skrypt SQL** , a następnie **Wybierz pozycję pierwsze 100 wierszy**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="Zapytanie SQL." border="true":::

   3. Możesz kontynuować udoskonalanie zapytania lub nawet wizualizowanie wyników przy użyciu opcji wykresów SQL.

## <a name="connect-to-power-bi"></a>Łączenie z usługą Power BI
Następnie będziemy łączyć pulę SQL bezserwerową z naszym obszarem roboczym Power BI. Po nawiązaniu połączenia z obszarem roboczym będziesz mieć możliwość tworzenia raportów Power BI zarówno bezpośrednio z usługi Azure Synapse Analytics, jak i z poziomu pulpitu Power BI.

>[!Note]
> Przed rozpoczęciem należy skonfigurować połączoną usługę w [obszarze roboczym Power BI](../quickstart-power-bi.md) i pobrać [pulpit Power BI](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces).  

Aby połączyć pulę SQL bezserwerową z naszym obszarem roboczym Power BI:

1.  Przejdź do karty **Power BI zestawy** danych i wybierz opcję **+ Nowy zestaw danych**. W wierszu polecenia Pobierz plik. pbids z bazy danych programu SQL Analytics, która ma być używana jako źródło danych. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI zestawy danych." border="true":::

2.  Otwórz plik z Power BI Desktop, aby utworzyć zestaw danych. Po otwarciu pliku Połącz się z bazą danych programu SQL Server przy użyciu opcji **konto Microsoft** i **Import** . 
   

## <a name="create-a-power-bi-report"></a>Tworzenie raportu usługi Power BI
1. Z poziomu pulpitu Power BI można teraz dodać wykres **przyczynników wpływających na klucz** do raportu.
   
   1. Przeciągnij średnią kolumnę *tipAmount* w celu **przeanalizowania** osi.
   
   2. Przeciągnij kolumny **weekdayString**, Average **TripDistance** i Average **tripTimeSecs** do osi **Wyjaśnij według** . 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. Na karcie Narzędzia główne Power BI pulpicie wybierz pozycję **Publikuj** i **Zapisz** zmiany. Wprowadź nazwę pliku i Zapisz ten raport w *obszarze roboczym NycTaxiTutorial*.
   
3. Ponadto można tworzyć wizualizacje Power BI z poziomu obszaru roboczego analizy usługi Azure Synapse. W tym celu przejdź do karty **programowanie** w obszarze roboczym usługi Azure Synapse i otwórz kartę Power BI. W tym miejscu możesz wybrać swój raport i kontynuować tworzenie dodatkowych wizualizacji. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Obszar roboczy analizy usługi Azure Synapse." border="true":::

Aby uzyskać więcej informacji na temat sposobu tworzenia zestawu danych za pomocą serwera SQL bezserwerowego i łączenia się z Power BI, można odwiedzić ten samouczek na temat [nawiązywania połączenia z programem Power BI Desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat możliwości wizualizacji danych w usłudze Azure Synapse Analytics można uzyskać, odwiedzając następujące dokumenty i samouczki:
   - [Wizualizowanie danych za pomocą pul Apache Spark bezserwerowych](../spark/apache-spark-data-visualization-tutorial.md)
   - [Omówienie wizualizacji danych z pulami Apache Spark](../spark/apache-spark-data-visualization.md)
