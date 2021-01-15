---
title: 'Samouczek: wykrywanie anomalii przy użyciu Cognitive Services'
description: Samouczek dotyczący sposobu korzystania z Cognitive Services na potrzeby wykrywania anomalii w Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 5e7b914d459d2452704f93987ce1bf91bfba988c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222211"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Samouczek: wykrywanie anomalii z Cognitive Services (wersja zapoznawcza)

W tym samouczku dowiesz się, jak łatwo wzbogacać dane w usłudze Azure Synapse za pomocą [Cognitive Services](../../cognitive-services/index.yml). W celu wykrycia anomalii będziemy używać [detektora anomalii](../../cognitive-services/anomaly-detector/index.yml) . Użytkownik w usłudze Azure Synapse może po prostu wybrać tabelę do wzbogacania w celu wykrycia anomalii.

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> - Procedura pobierania zestawu danych tabeli Spark zawierającego dane szeregów czasowych.
> - Użyj środowiska kreatora w usłudze Azure Synapse, aby wzbogacić dane za pomocą usługi poznawczej wykrywania anomalii.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. Musisz być **współautorem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym pracujesz.
- Pula platformy Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Aby można było korzystać z tego samouczka, należy również wykonać kroki opisane w tym samouczku. [Skonfiguruj Cognitive Services w usłudze Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Tworzenie tabeli platformy Spark

W tym samouczku będzie potrzebna tabela platformy Spark.

1. Pobierz następujący plik notesu zawierający kod w celu wygenerowania tabeli Spark: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Przekaż plik do obszaru roboczego usługi Azure Synapse.
![Przekaż Notes](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Otwórz plik notesu i wybierz, aby **uruchomić wszystkie** komórki.
![Utwórz tabelę platformy Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Tabela Spark o nazwie **anomaly_detector_testing_data** powinna teraz pojawić się w domyślnej bazie danych Spark.

## <a name="launch-cognitive-services-wizard"></a>Uruchom Kreatora Cognitive Services

1. Kliknij prawym przyciskiem myszy tabelę Spark utworzoną w poprzednim kroku. Wybierz pozycję "Machine Learning > wzbogacania z istniejącym modelem", aby otworzyć kreatora.
![Uruchom Kreatora oceniania](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Zostanie wyświetlony panel konfiguracja i zostanie wyświetlony monit o wybranie modelu Cognitive Services. Wybierz pozycję detektor anomalii.

![Uruchom Kreatora oceniania — krok 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Podaj szczegóły uwierzytelniania

Aby można było uwierzytelnić się w Cognitive Services, należy odwołać się do klucza tajnego w celu użycia w Key Vault. Poniższe dane wejściowe są zależne od [wstępnie wymaganych kroków](tutorial-configure-cognitive-services-synapse.md) , które należy wykonać przed wykonaniem tego kroku.

- **Subskrypcja platformy Azure**: wybierz subskrypcję platformy Azure, do której należy Twój Magazyn kluczy.
- **Konto Cognitive Services**: jest to zasób analiza tekstu, z którym chcesz nawiązać połączenie.
- **Azure Key Vault połączona usługa**: w ramach wstępnie wymaganych kroków została utworzona połączona usługa dla zasobu analiza tekstu. Wybierz ją tutaj.
- **Nazwa wpisu tajnego**: jest to nazwa wpisu tajnego w magazynie kluczy zawierającym klucz do uwierzytelnienia w zasobie Cognitive Services.

![Podaj szczegóły Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Konfigurowanie wykrywania anomalii

Następnie należy skonfigurować wykrywanie anomalii. Podaj następujące szczegóły:

- Stopień szczegółowości: częstotliwość próbkowania danych. Na przykład jeśli dane mają wartość dla każdej minuty, stopień szczegółowości będzie wynosił co minutę. Wybierz **co miesiąc** 

- Sygnatura czasowa: kolumna reprezentująca godzinę serii. Wybierz **sygnaturę czasową** kolumny

- Wartość szeregów czasowych: kolumna reprezentująca wartość serii w czasie określonym przez kolumnę sygnatur czasowych. Wybierz **wartość** kolumny

- Grupowanie: kolumna grupująca serię. Oznacza to, że wszystkie wiersze, które mają taką samą wartość w tej kolumnie, powinny tworzyć jedną serię czasową. Wybierz **grupę** kolumn

Gdy skończysz, wybierz pozycję **Otwórz Notes**. Spowoduje to wygenerowanie notesu z kodem PySpark, który wykonuje wykrywanie anomalii przy użyciu usługi Azure Cognitive Services.

![Konfigurowanie wykrywania anomalii](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Otwórz Notes i uruchom

Właśnie otwarty Notes korzysta z [biblioteki mmlspark](https://github.com/Azure/mmlspark) w celu nawiązania połączenia z usługami poznawczej.

Podane Azure Key Vault szczegóły umożliwiają bezpieczne odwoływanie się do wpisów tajnych z tego środowiska bez ujawniania ich.

Teraz można **uruchomić wszystkie** komórki, aby przeprowadzić wykrywanie anomalii. Dowiedz się więcej na temat [wykrywania anomalii Cognitive Services](../../cognitive-services/anomaly-detector/index.yml).

![Uruchamianie wykrywania anomalii](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Analiza tonacji przy użyciu usługi Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: Ocena modelu uczenia maszynowego w dedykowanych pulach SQL Synapse platformy Azure](tutorial-sql-pool-model-scoring-wizard.md)
- [Możliwości Machine Learning w usłudze Azure Synapse Analytics](what-is-machine-learning.md)