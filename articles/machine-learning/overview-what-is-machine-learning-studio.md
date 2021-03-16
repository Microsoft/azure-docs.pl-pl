---
title: Co to jest usługa Azure Machine Learning Studio?
description: Studio to portal sieci Web dla Azure Machine Learning obszarów roboczych. W programie Studio nie są połączona żadne kod i pierwsze środowisko do nauki o danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
adobe-target: true
ms.openlocfilehash: 48c4b2a73628ab2105e23054d747e28acc105d01
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563196"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co to jest usługa Azure Machine Learning Studio?

Ten artykuł zawiera informacje na temat Azure Machine Learning Studio — portalu sieci Web dla deweloperów analityków danych w programie [Azure Machine Learning](overview-what-is-azure-ml.md). W programie Studio nie są połączona żadne kod i pierwsze środowisko do nauki o danych.

W tym artykule przedstawiono następujące informacje:
>[!div class="checklist"]
> - Jak [tworzyć projekty uczenia maszynowego](#author-machine-learning-projects) w programie Studio.
> - Jak [zarządzać zasobami i zasobami](#manage-assets-and-resources) w programie Studio.
> - Różnice między [Azure Machine Learning Studio i ml Studio (klasyczne)](#ml-studio-classic-vs-azure-machine-learning-studio).

Zalecamy używanie najnowszej przeglądarki zgodnej z systemem operacyjnym. Obsługiwane są następujące przeglądarki:
  * Microsoft Edge (Nowa Microsoft Edge, Najnowsza wersja. Nie w starszej wersji programu Microsoft Edge)
  * Safari (najnowsza wersja, tylko Mac)
  * Chrome (najnowsza wersja)
  * Firefox (najnowsza wersja)

## <a name="author-machine-learning-projects"></a>Tworzenie projektów uczenia maszynowego

Studio oferuje wiele środowisk tworzenia w zależności od projektu typu i poziomu środowiska użytkownika.

+ **Notesy**

  Pisz i uruchamiaj własny kod w zarządzanych [serwerach Jupyter Notebook](how-to-run-jupyter-notebooks.md) , które są bezpośrednio zintegrowane w programie Studio. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Zrzut ekranu: pisanie i uruchamianie kodu w notesie":::

+ **Projektant usługi Azure Machine Learning**

  Użyj projektanta, aby nauczyć i wdrożyć modele uczenia maszynowego bez konieczności pisania kodu. Przeciągnij i upuść zestawy danych i moduły, aby utworzyć potoki ML. Wypróbuj [samouczek projektanta](tutorial-designer-automobile-price-train-score.md).

    ![Przykład projektanta Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Zautomatyzowany interfejs użytkownika uczenia maszynowego**

  Dowiedz się, jak tworzyć [zautomatyzowane eksperymenty ml](tutorial-first-experiment-automated-ml.md) przy użyciu łatwego w użyciu interfejsu. 

  [![Okienko nawigacji Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Etykietowanie danych**

    Używaj [etykiet danych Azure Machine Learning](how-to-create-labeling-projects.md) do wydajnej koordynacji projektów etykietowania danych.

## <a name="manage-assets-and-resources"></a>Zarządzanie elementami zawartości i zasobami

Zarządzaj zasobami uczenia maszynowego bezpośrednio w przeglądarce. Zasoby są współużytkowane w tym samym obszarze roboczym między zestawem SDK i programem Studio w celu zapewnienia bezproblemowego środowiska. Zarządzanie programem przy użyciu programu Studio:

- Modele
- Zestawy danych
- Magazyny danych
- Zasoby obliczeniowe
- Notebooks
- Eksperymenty
- Uruchamianie dzienników
- Pipelines 
- Punkty końcowe potoku

Nawet jeśli jesteś doświadczonym deweloperem, program Studio może uprościć zarządzanie zasobami obszaru roboczego.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (klasyczny) vs Azure Machine Learning Studio

Opublikowano w 2015, **ml Studio (klasyczny)** był naszym pierwszym konstruktorem uczenia maszynowego w ramach przeciągania i upuszczania. 

**Ml Studio (klasyczny)** to Usługa autonomiczna, która oferuje tylko wizualizację. Studio (klasyczny) nie współdziała z Azure Machine Learning.

**Azure Machine Learning** to oddzielna i nowoczesny usługa, która zapewnia kompletną platformę do nauki o danych. Obsługuje zarówno środowisko w kodzie, jak i w małym kodzie.

**Azure Machine Learning Studio** to portal sieci web *w* Azure Machine Learning, który zawiera opcje niskiej ilości kodu i bez kodu dla tworzenia projektów i zarządzania zasobami. 

Zalecamy, aby nowi użytkownicy wybierali **Azure Machine Learning**, a nie ml Studio (klasyczny), aby uzyskać najnowszy zakres narzędzi do nauki o danych. Jeśli jesteś istniejącym użytkownikiem ML Studio (klasycznym), rozważ [przeprowadzenie migracji do Azure Machine Learning](classic/migrate-overview.md).

Poniżej przedstawiono niektóre zalety przełączania do Azure Machine Learning:

- Skalowalne Klastry obliczeniowe do szkoleń o dużej skali.
- Zabezpieczenia i zarządzanie przedsiębiorstwami.
- Współdziałanie z popularnymi narzędziami Open Source.
- Kompleksowe MLOps.

### <a name="feature-comparison"></a>Porównanie funkcji

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Brak elementów interfejsu użytkownika w programie Studio** Za pomocą kontroli dostępu opartej na rolach platformy Azure można ograniczyć akcje, które można wykonywać przy użyciu Azure Machine Learning. Ograniczenia te mogą uniemożliwić Wyświetlanie elementów interfejsu użytkownika w programie Azure Machine Learning Studio. Na przykład, jeśli przypisano rolę, która nie może utworzyć wystąpienia obliczeniowego, opcja tworzenia wystąpienia obliczeniowego nie będzie wyświetlana w Studio. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami](how-to-assign-roles.md).

## <a name="next-steps"></a>Następne kroki

Odwiedź [Studio](https://ml.azure.com)lub zapoznaj się z różnymi opcjami tworzenia, korzystając z następujących samouczków:  

- + [Rozpocznij pracę we własnym środowisku programistycznym](tutorial-1st-experiment-sdk-setup-local.md)
  + [Korzystanie z notesów Jupyter w wystąpieniu obliczeniowym do uczenia & wdrażania modeli](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie automatycznego uczenia maszynowego do uczenia & wdrażania modeli](tutorial-first-experiment-automated-ml.md)  
  + [Korzystanie z projektanta do uczenia & wdrażania modeli](tutorial-designer-automobile-price-train-score.md)
  + [Korzystanie z programu Studio w zabezpieczonej sieci wirtualnej](how-to-enable-studio-virtual-network.md)