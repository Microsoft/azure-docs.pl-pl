---
title: 'Samouczek: Analiza tonacji z Cognitive Services'
description: Dowiedz się, jak używać Cognitive Services do analizy tonacji w usłudze Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943705"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Samouczek: Analiza tonacji z Cognitive Services (wersja zapoznawcza)

W tym samouczku dowiesz się, jak łatwo wzbogacać dane z usługi Azure Synapse Analytics przy użyciu [usługi azure Cognitive Services](../../cognitive-services/index.yml). Do przeprowadzenia analizy tonacji będziesz używać funkcji [Analiza tekstu](../../cognitive-services/text-analytics/index.yml) . 

Użytkownik w usłudze Azure Synapse może po prostu wybrać tabelę zawierającą kolumnę tekstową do wzbogacania z mową. Mową mogą być pozytywne, ujemne, mieszane lub neutralne. Prawdopodobieństwo zostanie również zwrócone.

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> - Kroki umożliwiające pobranie zestawu danych tabeli Spark, który zawiera kolumnę tekstową na potrzeby analizy tonacji.
> - Używanie środowiska kreatora w usłudze Azure Synapse do wzbogacania danych za pomocą analiza tekstu w Cognitive Services.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu Azure Data Lake Storage Gen2 skonfigurowanym jako magazyn domyślny. Musisz być *współautorem danych obiektów blob magazynu* w systemie plików Data Lake Storage Gen2, z którym pracujesz.
- Pula platformy Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Kroki przed rozpoczęciem konfiguracji opisane w samouczku [konfigurowanie Cognitive Services w usłudze Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Tworzenie tabeli platformy Spark

Dla tego samouczka potrzebna jest tabela platformy Spark.

1. Pobierz plik [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) , który zawiera zestaw danych do analizy tekstu. 

1. Przekaż plik do konta usługi Azure Synapse Storage w Data Lake Storage Gen2.
  
   ![Zrzut ekranu pokazujący wybory przekazywania danych.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Utwórz tabelę Spark z pliku CSV, klikając plik prawym przyciskiem myszy i wybierając pozycję **Nowy Notes**  >  **Utwórz tabelę platformy Spark**.

   ![Zrzut ekranu pokazujący wybory dla tworzenia tabeli Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nadaj tabeli nazwę w komórce kod i uruchom Notes w puli Spark. Należy pamiętać o ustawieniu `header=True` .

   ![Zrzut ekranu pokazujący uruchomiony Notes.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Otwieranie kreatora Cognitive Services

1. Kliknij prawym przyciskiem myszy tabelę Spark utworzoną w poprzedniej procedurze. Wybierz **Machine Learning**  >  **wzbogacanie z istniejącym modelem** , aby otworzyć kreatora.

   ![Zrzut ekranu pokazujący opcje otwierania Kreatora oceniania.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Zostanie wyświetlony panel konfiguracja i zostanie wyświetlony monit o wybranie modelu Cognitive Services. Wybierz pozycję **Analiza tekstu — analiza tonacji**.

   ![Zrzut ekranu, który pokazuje wybór modelu Cognitive Services.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Podaj szczegóły uwierzytelniania

Aby uwierzytelnić się w Cognitive Services, należy odwołać się do wpisu tajnego magazynu kluczy. Poniższe dane wejściowe zależą od [wstępnie wymaganych kroków](tutorial-configure-cognitive-services-synapse.md) , które należy wykonać przed tym punktem.

- **Subskrypcja platformy Azure**: wybierz subskrypcję platformy Azure, do której należy Twój Magazyn kluczy.
- **Konto Cognitive Services**: wprowadź zasób analiza tekstu, z którym chcesz nawiązać połączenie.
- **Azure Key Vault połączona usługa**: w ramach kroków wymagań wstępnych została utworzona połączona usługa dla zasobu analiza tekstu. Wybierz je tutaj.
- **Nazwa wpisu tajnego**: Wprowadź nazwę wpisu tajnego w magazynie kluczy, który zawiera klucz do uwierzytelnienia w zasobie Cognitive Services.

![Zrzut ekranu pokazujący szczegóły uwierzytelniania dla magazynu kluczy.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Konfigurowanie analizy tonacji

Następnie skonfiguruj analizę tonacji. Wybierz następujące szczegóły:
- **Język**: Wybierz język **angielski** w postaci tekstu, na którym chcesz przeprowadzić analizę tonacji.
- **Kolumna tekstowa**: wybierz opcję **komentarz (ciąg)** jako kolumnę tekstową w zestawie danych, którą chcesz analizować, aby określić tonacji.

Gdy skończysz, wybierz pozycję **Otwórz Notes**. Spowoduje to wygenerowanie notesu z kodem PySpark, który wykonuje analizę tonacji przy użyciu usługi Azure Cognitive Services.

![Zrzut ekranu pokazujący wybory dotyczące konfigurowania analizy tonacji.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

Właśnie otwarty Notes korzysta z [biblioteki mmlspark](https://github.com/Azure/mmlspark) w celu nawiązania połączenia z usługą Cognitive Services. Podane Azure Key Vault szczegóły umożliwiają bezpieczne odwoływanie się do wpisów tajnych z tego środowiska bez ujawniania ich.

Teraz możesz uruchamiać wszystkie komórki, aby wzbogacić dane za pomocą mową. Wybierz pozycję **Uruchom wszystkie**. 

Mową są zwracane jako **pozytywne**, **ujemne**, **neutralne** lub **mieszane**. Uzyskasz również prawdopodobieństwa na tonacji. [Dowiedz się więcej o analizie tonacji w Cognitive Services](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Zrzut ekranu przedstawiający analizę tonacji.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Następne kroki
- [Samouczek: wykrywanie anomalii w usłudze Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: Ocena modelu uczenia maszynowego w dedykowanych pulach SQL Synapse platformy Azure](tutorial-sql-pool-model-scoring-wizard.md)
- [Możliwości uczenia maszynowego w usłudze Azure Synapse Analytics](what-is-machine-learning.md)