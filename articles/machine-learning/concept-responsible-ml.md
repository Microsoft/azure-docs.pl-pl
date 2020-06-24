---
title: Machine Learning odpowiedzialny (ML)
titleSuffix: Azure Machine Learning
description: Dowiedz się, co to jest właściwa ML i jak używać jej w Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 17cbf4f7b0ae31b0523a0fbea0c1cae8f83b234b
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84982598"
---
# <a name="responsible-machine-learning-ml"></a>Machine Learning odpowiedzialny (ML)

W tym artykule dowiesz się, co odpowiada dana ML i jak można je wdrożyć w ćwiczeniu z Azure Machine Learning.

W trakcie opracowywania i używania systemów AI zaufanie musi znajdować się na poziomie podstawowym. Relacje zaufania z platformą, procesem i modelami. W firmie Microsoft odpowiedzialność za ML obejmuje następujące wartości i zasady:

- Informacje o modelach uczenia maszynowego
  - Interpretuj i wyjaśnij zachowanie modelu
  - Ocenianie i łagodzenie nieuczciwych modeli
- Ochrona osób i ich danych
  - Zapobiegaj narażeniu danych z różnicową prywatność  
- Sterowanie kompleksowym procesem uczenia maszynowego
  - Udokumentowanie cyklu życia uczenia maszynowego za pomocą arkuszy danych

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Odpowiedzialne filary ML":::

Ponieważ sztuczna inteligencja i autonomiczne systemy integrują się z siecią szkieletową, ważne jest, aby aktywnie podejmować wysiłki w celu przewidzenia i ograniczenia nieoczekiwanych konsekwencji tych technologii.

## <a name="interpret-and-explain-model-behavior"></a>Interpretuj i wyjaśnij zachowanie modelu

Trudne w wyjaśnieniu i czarne systemy mogą być problematyczne, ponieważ utrudniają one zainteresowanym podmiotom, takim jak deweloperzy systemu, systemy nadzoru, użytkownicy i osoby podejmujące decyzje biznesowe, aby zrozumieć, dlaczego systemy podejmują pewne decyzje. Niektóre systemy AI są bardziej wyjaśnione niż inne, a czasami istnieje kompromis między systemem i większą dokładnością, który jest bardziej wyjaśniony.

Aby utworzyć interpretowane systemy AI, użyj [InterpretML](https://github.com/interpretml/interpret), pakietu open source utworzonego przez firmę Microsoft. [InterpretML można używać wewnątrz Azure Machine Learning](how-to-machine-learning-interpretability.md) do [interpretacji i wyjaśnienia modeli uczenia maszynowego](how-to-machine-learning-interpretability-aml.md), w tym [zautomatyzowanych modeli uczenia maszynowego](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Ocenianie i łagodzenie nieuczciwych modeli

Ponieważ systemy AI stają się coraz bardziej związane z codziennym podejmowaniem decyzji, jest to bardzo ważne, aby te systemy działały w sposób atrakcyjny dla wszystkich użytkowników.

Nieuczciwą w systemach AI mogą powodować następujące niezamierzone konsekwencje:

- Zatrzymujące się szanse sprzedaży, zasoby lub informacje od użytkowników indywidualnych.
- Wzmacnianie bias i stereotypów.

Nie można przechwytywać ani reprezentować wielu aspektów godziwych. Istnieją narzędzia i praktyki, które mogą poprawić atrakcyjność projektowania i opracowywania systemów AI.

Dwa kluczowe kroki w celu zmniejszenia nieuczciwości w systemach AI są ocenami i ograniczeniami. Zalecamy [FairLearn](https://github.com/fairlearn/fairlearn), pakiet typu "open source", który umożliwia ocenę i łagodzenie potencjalnej nieuczciwości systemów AI. Aby dowiedzieć się więcej na temat sprawiedliwości i pakietu FairLearn, zobacz [godziwość artykułu w ml](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Zapobiegaj narażeniu danych z różnicową prywatność

Gdy dane są używane do analizy, ważne jest, aby dane pozostały prywatne i poufne w całym użyciu. Różnicowa Ochrona prywatności to zestaw systemów i praktyk, które ułatwiają zapewnienie bezpieczeństwa i prywatnego danych osobom.

W tradycyjnych scenariuszach dane pierwotne są przechowywane w plikach i bazach danych. Gdy użytkownicy analizują dane, zazwyczaj korzystają z danych pierwotnych. Jest to problem, ponieważ może naruszać prywatność poszczególnych użytkowników. Różnicowa Ochrona prywatności próbuje rozwiązać ten problem, dodając "szum" lub losowość do danych, tak aby użytkownicy nie mogli identyfikować poszczególnych punktów danych.

Implementacja różnicowych systemów prywatnych jest trudna. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) to projekt open-source, który zawiera różne składniki służące do tworzenia globalnych, różnicowych systemów prywatnych. Aby dowiedzieć się więcej o zasadach ochrony prywatności i projekcie WhiteNoise, zobacz artykuł [zachowanie prywatności danych przy użyciu różnicowych informacji o ochronie prywatności i WhiteNoise](./concept-differential-privacy.md) .

> [!NOTE]
> Należy pamiętać, że zmieniamy nazwy zestawu narzędzi i wprowadzamy nową nazwę w nadchodzących tygodniach. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Udokumentowanie cyklu życia uczenia maszynowego za pomocą arkuszy danych

Dokumentowanie właściwych informacji w procesie uczenia maszynowego jest kluczem do podejmowania właściwych decyzji na każdym etapie. Arkusze danych to sposób dokumentowania zasobów uczenia maszynowego, które są używane i tworzone w ramach cyklu życia uczenia maszynowego.

Modele są uważane za "czarne pola" i często znajdują się na nich małe informacje. Ponieważ systemy uczenia maszynowego stają się coraz bardziej rozpowszechnione i są używane do podejmowania decyzji, korzystanie z arkuszy danych to krok w kierunku tworzenia bardziej odpowiedzialnych systemów uczenia maszynowego.

Niektóre informacje o modelu, które można chcieć udokumentować w ramach arkusza danych:

- Zamierzone użycie
- Architektura modelu
- Używane dane szkoleniowe
- Użyte dane oceny
- Metryki wydajności modelu szkolenia
- Informacje o rzetelności.

Zapoznaj się z poniższym przykładem, aby dowiedzieć się, jak zaimplementować [Arkusz danych dla modeli przy](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)użyciu zestawu SDK Azure Machine Learning.

## <a name="additional-resources"></a>Zasoby dodatkowe

- Użyj szyfrowania homomorphic, aby [wdrożyć zaszyfrowaną usługę sieci Web inferencing](how-to-homomorphic-encryption-seal.md).
- Dowiedz się więcej o zestawie wskazówek [dotyczących informacji dotyczących](https://www.partnershiponai.org/about-ml/) systemu uczenia maszynowego.