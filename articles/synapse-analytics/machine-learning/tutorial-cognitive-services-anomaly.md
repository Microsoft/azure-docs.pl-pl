---
title: 'Samouczek: wykrywanie anomalii przy użyciu Cognitive Services'
description: Dowiedz się, jak używać Cognitive Services do wykrywania anomalii w usłudze Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943510"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Samouczek: wykrywanie anomalii z Cognitive Services (wersja zapoznawcza)

W tym samouczku dowiesz się, jak łatwo wzbogacać dane z usługi Azure Synapse Analytics przy użyciu [usługi azure Cognitive Services](../../cognitive-services/index.yml). Użyjesz [wykrywania anomalii](../../cognitive-services/anomaly-detector/index.yml) , aby znaleźć anomalie. Użytkownik w usłudze Azure Synapse może po prostu wybrać tabelę do wzbogacania w celu wykrycia anomalii.

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> - Kroki umożliwiające pobranie zestawu danych tabeli Spark, który zawiera dane szeregów czasowych.
> - Używanie środowiska kreatora w usłudze Azure Synapse do wzbogacania danych za pomocą wykrywania anomalii w Cognitive Services.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu Azure Data Lake Storage Gen2 skonfigurowanym jako magazyn domyślny. Musisz być *współautorem danych obiektów blob magazynu* w systemie plików Data Lake Storage Gen2, z którym pracujesz.
- Pula platformy Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Zakończenie kroków przed rozpoczęciem konfiguracji w samouczku [konfigurowania Cognitive Services w usłudze Azure Synapse](tutorial-configure-cognitive-services-synapse.md) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Tworzenie tabeli platformy Spark

Dla tego samouczka potrzebna jest tabela platformy Spark.

1. Pobierz następujący plik notesu zawierający kod służący do generowania tabeli Spark: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Przekaż plik do obszaru roboczego usługi Azure Synapse.

   ![Zrzut ekranu pokazujący wybory dotyczące przekazywania notesu.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Otwórz plik notesu i wybierz opcję **Uruchom wszystkie** , aby uruchomić wszystkie komórki.

   ![Zrzut ekranu pokazujący wybory dla tworzenia tabeli Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Tabela Spark o nazwie **anomaly_detector_testing_data** powinna teraz pojawić się w domyślnej bazie danych Spark.

## <a name="open-the-cognitive-services-wizard"></a>Otwieranie kreatora Cognitive Services

1. Kliknij prawym przyciskiem myszy tabelę Spark utworzoną w poprzednim kroku. Wybierz **Machine Learning**  >  **wzbogacanie z istniejącym modelem** , aby otworzyć kreatora.

   ![Zrzut ekranu pokazujący opcje otwierania Kreatora oceniania.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Zostanie wyświetlony panel konfiguracja i zostanie wyświetlony monit o wybranie modelu Cognitive Services. Wybierz pozycję **detektor anomalii**.

   ![Zrzut ekranu, który pokazuje wybór detektora anomalii jako modelu.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Podaj szczegóły uwierzytelniania

Aby uwierzytelnić się w Cognitive Services, należy odwołać się do wpisu tajnego w magazynie kluczy. Poniższe dane wejściowe zależą od [wstępnie wymaganych kroków](tutorial-configure-cognitive-services-synapse.md) , które należy wykonać przed tym punktem.

- **Subskrypcja platformy Azure**: wybierz subskrypcję platformy Azure, do której należy Twój Magazyn kluczy.
- **Konto Cognitive Services**: wprowadź zasób analiza tekstu, z którym chcesz nawiązać połączenie.
- **Azure Key Vault połączona usługa**: w ramach kroków wymagań wstępnych została utworzona połączona usługa dla zasobu analiza tekstu. Wybierz je tutaj.
- **Nazwa wpisu tajnego**: Wprowadź nazwę wpisu tajnego w magazynie kluczy, który zawiera klucz do uwierzytelnienia w zasobie Cognitive Services.

![Zrzut ekranu pokazujący szczegóły uwierzytelniania dla magazynu kluczy.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Konfigurowanie wykrywania anomalii

Podaj następujące informacje, aby skonfigurować detektor anomalii:

- **Stopień szczegółowości**: szybkość próbkowania danych. Wybierz opcję **co miesiąc**. 

- **Kolumna znacznika czasu**: kolumna reprezentująca czas serii. Wybierz **sygnaturę czasową (ciąg)**.

- **Kolumna wartości szeregów czasowych**: kolumna reprezentująca wartość serii w czasie określonym przez kolumnę sygnatur czasowych. Wybierz **wartość (Double)**.

- **Kolumna grupowania**: kolumna grupująca serię. Oznacza to, że wszystkie wiersze, które mają taką samą wartość w tej kolumnie, powinny tworzyć jedną serię czasową. Wybierz pozycję **Grupa (ciąg)**.

Gdy skończysz, wybierz pozycję **Otwórz Notes**. Spowoduje to wygenerowanie notesu z kodem PySpark, który używa platformy Azure Cognitive Services do wykrywania anomalii.

![Zrzut ekranu pokazujący szczegóły konfiguracji wykrywania anomalii.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

Właśnie otwarty Notes korzysta z [biblioteki mmlspark](https://github.com/Azure/mmlspark) w celu nawiązania połączenia z usługą Cognitive Services. Podane Azure Key Vault szczegóły umożliwiają bezpieczne odwoływanie się do wpisów tajnych z tego środowiska bez ujawniania ich.

Teraz można uruchomić wszystkie komórki, aby przeprowadzić wykrywanie anomalii. Wybierz pozycję **Uruchom wszystkie**. [Dowiedz się więcej o detektorze anomalii w Cognitive Services](../../cognitive-services/anomaly-detector/index.yml).

![Zrzut ekranu przedstawiający wykrywanie anomalii.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Analiza tonacji przy użyciu usługi Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: Ocena modelu uczenia maszynowego w dedykowanych pulach SQL Synapse platformy Azure](tutorial-sql-pool-model-scoring-wizard.md)
- [Możliwości uczenia maszynowego w usłudze Azure Synapse Analytics](what-is-machine-learning.md)
