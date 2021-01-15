---
title: 'Samouczek: Analiza tonacji z Cognitive Services'
description: Samouczek dotyczący sposobu wykorzystania Cognitive Services do analizy tonacji w Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 6a4833cf0d73939e01fd3e3e7263c6cba3c0a28a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222194"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Samouczek: Analiza tonacji z Cognitive Services (wersja zapoznawcza)

W tym samouczku dowiesz się, jak łatwo wzbogacać dane w usłudze Azure Synapse za pomocą [Cognitive Services](../../cognitive-services/index.yml). Będziemy używać funkcji [Analiza tekstu](../../cognitive-services/text-analytics/index.yml) , aby przeprowadzić analizę tonacji. Użytkownik w usłudze Azure Synapse może po prostu wybrać tabelę zawierającą kolumnę tekstową do wzbogacania z mową. Mową mogą być pozytywne, ujemne, mieszane lub neutralne i prawdopodobieństwo zostanie zwrócone.

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> - Kroki umożliwiające pobranie zestawu danych tabeli Spark zawierającego kolumnę tekstową na potrzeby analizy tonacji.
> - Użyj środowiska kreatora w usłudze Azure Synapse, aby wzbogacić dane przy użyciu Cognitive Services analiza tekstu.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. Musisz być **współautorem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym pracujesz.
- Pula platformy Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Aby można było korzystać z tego samouczka, należy również wykonać kroki opisane w tym samouczku. [Skonfiguruj Cognitive Services w usłudze Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Tworzenie tabeli platformy Spark

W tym samouczku będzie potrzebna tabela platformy Spark.

1. Pobierz następujący plik CSV zawierający zestaw danych na potrzeby analizy tekstu: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Przekaż plik do konta magazynu usługi Azure Synapse ADLSGen2.
![Przekazywanie danych](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Utwórz tabelę Spark na podstawie pliku CSV po kliknięciu prawym przyciskiem myszy pliku i wybraniu polecenia **Nowy Notes — > utworzyć tabelę platformy Spark**.
![Utwórz tabelę platformy Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nadaj tabeli nazwę w komórce kod i uruchom Notes w puli Spark. Pamiętaj, aby ustawić "Header = true".
![Uruchom Notes](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Uruchom Kreatora Cognitive Services

1. Kliknij prawym przyciskiem myszy tabelę Spark utworzoną w poprzednim kroku. Wybierz pozycję "Machine Learning > wzbogacania z istniejącym modelem", aby otworzyć kreatora.
![Uruchom Kreatora oceniania](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Zostanie wyświetlony panel konfiguracja i zostanie wyświetlony monit o wybranie modelu Cognitive Services. Wybierz pozycję analiza tekstu — analiza tonacji.

![Uruchom Kreatora oceniania — krok 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Podaj szczegóły uwierzytelniania

Aby można było uwierzytelnić się w Cognitive Services, należy odwołać się do klucza tajnego w celu użycia w Key Vault. Poniższe dane wejściowe są zależne od [wstępnie wymaganych kroków](tutorial-configure-cognitive-services-synapse.md) , które należy wykonać przed wykonaniem tego kroku.

- **Subskrypcja platformy Azure**: wybierz subskrypcję platformy Azure, do której należy Twój Magazyn kluczy.
- **Konto Cognitive Services**: jest to zasób analiza tekstu, z którym chcesz nawiązać połączenie.
- **Azure Key Vault połączona usługa**: w ramach wstępnie wymaganych kroków została utworzona połączona usługa dla zasobu analiza tekstu. Wybierz ją tutaj.
- **Nazwa wpisu tajnego**: jest to nazwa wpisu tajnego w magazynie kluczy zawierającym klucz do uwierzytelnienia w zasobie Cognitive Services.

![Podaj szczegóły Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Konfigurowanie analiza tonacji

Następnie należy skonfigurować analizę tonacji. Wybierz następujące szczegóły:
- **Język**: Wybierz język tekstu, dla którego chcesz przeprowadzić analizę tonacji. Wybierz pozycję **EN**.
- **Kolumna tekstowa**: jest to kolumna tekstowa w zestawie danych, którą chcesz analizować, aby określić tonacji. Wybierz pozycję **komentarz** kolumny tabeli.

Po zakończeniu kliknij pozycję **Otwórz Notes**. Spowoduje to wygenerowanie notesu z kodem PySpark, który wykonuje analizę tonacji przy użyciu usługi Azure Cognitive Services.

![Konfigurowanie analiza tonacji](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Otwórz Notes i uruchom

Właśnie otwarty Notes korzysta z [biblioteki mmlspark](https://github.com/Azure/mmlspark) w celu nawiązania połączenia z usługami poznawczej.

Podane Azure Key Vault szczegóły umożliwiają bezpieczne odwoływanie się do wpisów tajnych z tego środowiska bez ujawniania ich.

Teraz możesz **uruchamiać wszystkie** komórki, aby wzbogacić dane za pomocą mową. Mową będzie zwracany jako dodatni/ujemny/neutralny/mieszany, a także prawdopodobieństwa na tonacji. Dowiedz się więcej o [analizie Cognitive Services tonacji](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Uruchom analiza tonacji](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Następne kroki
- [Samouczek: wykrywanie anomalii w usłudze Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: Ocena modelu uczenia maszynowego w dedykowanych pulach SQL Synapse platformy Azure](tutorial-sql-pool-model-scoring-wizard.md)
- [Możliwości Machine Learning w usłudze Azure Synapse Analytics](what-is-machine-learning.md)