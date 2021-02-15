---
title: 'ML Studio (klasyczny): Rozpoczynanie eksperymentów z przykładów — Azure'
description: Dowiedz się, jak używać przykładowych eksperymentów uczenia maszynowego do tworzenia nowych eksperymentów przy użyciu Azure AI Gallery i Azure Machine Learning Studio (klasyczne).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: 40306973dde989862f8dfe9df5c9d873ac18fdd0
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517505"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Tworzenie Azure Machine Learning Studio (klasycznych) eksperymentów z przykładów roboczych w programie Azure AI Gallery

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  




Naucz się, jak zacząć od przykładowych eksperymentów z witryny [Galeria sztucznej inteligencji platformy Azure](https://gallery.azure.ai/), zamiast tworzyć eksperymenty uczenia maszynowego od zera. Używając przykładów, możesz zbudować własne rozwiązanie uczenia maszynowego.

Galeria ma Przykładowe eksperymenty w odniesieniu do zespołu Microsoft Azure Machine Learning Studio (klasyczny), jak również przykłady udostępnione przez społeczność Machine Learning. Można również zadawać pytania i publikować komentarze dotyczące eksperymentów.

Aby poznać sposób korzystania z galerii, obejrzyj 3-minutowy klip wideo [Kopiowanie pracy innych osób w celu przetwarzania danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) z serii [Przetwarzanie danych dla początkujących](data-science-for-beginners-the-5-questions-data-science-answers.md).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Znajdź eksperyment do skopiowania do galerii sztucznej inteligencji platformy Azure
Aby zobaczyć, jakie eksperymenty są dostępne, przejdź do [galerii](https://gallery.azure.ai/) i kliknij pozycję **Experiments** (Eksperymenty) w górnej części strony.

### <a name="find-the-newest-or-most-popular-experiments"></a>Znajdowanie najnowszych lub najpopularniejszych eksperymentów
Na tej stronie możesz zobaczyć eksperymenty z kategorii **Recently added** (Ostatnio dodane) lub przewinąć do kategorii **What's popular** (Popularne) oraz **Popular Microsoft experiments** (Popularne eksperymenty firmy Microsoft).

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Wyszukiwanie eksperymentu spełniającego określone wymagania
Aby przeglądać wszystkie eksperymenty:

1. Kliknij pozycję **Browse all** (Przeglądaj wszystkie) w górnej części strony.
2. Po lewej stronie w obszarze **Refine by** (Uściślij według) w sekcji **Categories** (Kategorie) wybierz pozycję **Experiment** (Eksperyment), aby zobaczyć wszystkie eksperymenty w galerii.
3. Eksperymenty spełniające wymagania możesz znaleźć na kilka różnych sposobów:
   * **Zaznacz filtry po lewej stronie.** Na przykład aby przeglądać eksperymenty, w których jest używany algorytm wykrywania anomalii oparty na analizie PCA: w obszarze **Categories** (Kategorie) kliknij pozycję **Experiment** (Eksperyment). Następnie w pozycji **Algorithms Used** (Używane algorytmy) kliknij opcję **Show all** (Pokaż wszystko), a w oknie dialogowym wybierz opcję **PCA-Based Anomaly Detection** (Wykrywanie anomalii oparte na analizie PCA). Wyświetlenie opcji może wymagać przewinięcia.<br></br>
     ![Wybieranie filtrów](./media/sample-experiments/choose-an-algorithm.png)
   * **Użyj pola wyszukiwania.** Na przykład aby znaleźć eksperymenty zamieszczone przez firmę Microsoft, które są związane z rozpoznawaniem cyfr z użyciem algorytmu SVM dla problemu dwuklasowego, w polu wyszukiwania wprowadź ciąg „digit recognition” (rozpoznawanie cyfr). Następnie wybierz pozycję Filtry **eksperymenty**, **tylko zawartość firmy Microsoft** i **dwuklasową maszynę wektorową**:<br></br>
     ![Użycie pola wyszukiwania](./media/sample-experiments/search-for-experiments.png)
4. Kliknij eksperyment, aby uzyskać więcej informacji.
5. Aby uruchomić i/lub zmodyfikować eksperyment, kliknij pozycję **Open in Studio** (Otwórz w Studio) na stronie eksperymentu. <br></br>

    ![Przykładowy eksperyment](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Tworzenie nowego eksperymentu przy użyciu przykładu jako szablonu
Możesz również utworzyć nowy eksperyment w Machine Learning Studio (klasyczny) przy użyciu przykładu galerii jako szablonu.

1. Zaloguj się przy użyciu poświadczeń konta Microsoft do usługi [Studio](https://studio.azureml.net), a następnie kliknij pozycję **New** (Nowy), aby utworzyć eksperyment.
2. Przejrzyj przykłady i kliknij jeden z nich.

Nowy eksperyment zostanie utworzony w obszarze roboczym Machine Learning Studio (klasycznym) przy użyciu przykładowego eksperymentu jako szablonu.

## <a name="next-steps"></a>Następne kroki
* [Importowanie danych z różnych źródeł](import-data.md)
* [Wdrażanie usługi sieci Web Machine Learning](deploy-a-machine-learning-web-service.md)