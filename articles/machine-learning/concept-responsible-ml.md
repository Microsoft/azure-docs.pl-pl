---
title: Co to jest odpowiedzialne Uczenie maszynowe (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, co to jest uczenie maszynowe i jak z nich korzystać z Azure Machine Learning, aby zrozumieć modele, chronić dane i kontrolować cykl życia modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 02/25/2021
ms.custom: responsible-ml
ms.openlocfilehash: f849968c16d2c3d8940a71da0e0cda536882d9c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101692066"
---
# <a name="what-is-responsible-machine-learning-preview"></a>Jakie są osoby odpowiedzialne za uczenie maszynowe? (wersja zapoznawcza)

Ten artykuł zawiera informacje o tym, co jest odpowiedzialne za uczenie maszynowe (ML) i sposobach ich używania do Azure Machine Learning.

## <a name="responsible-machine-learning-principles"></a>Odpowiedzialne zasady uczenia maszynowego

W trakcie opracowywania i używania systemów AI zaufanie musi znajdować się na poziomie podstawowym. Relacje zaufania z platformą, procesem i modelami. W firmie Microsoft odpowiedzialność za uczenie maszynowe obejmuje następujące wartości i zasady:

- Informacje o modelach uczenia maszynowego
  - Interpretuj i wyjaśnij zachowanie modelu
  - Ocenianie i łagodzenie nieuczciwych modeli
- Ochrona osób i ich danych
  - Zapobiegaj narażeniu danych z różnicową prywatność
  - Współpraca z zaszyfrowanymi danymi przy użyciu szyfrowania homomorphic
- Sterowanie kompleksowym procesem uczenia maszynowego
  - Udokumentowanie cyklu życia uczenia maszynowego za pomocą arkuszy danych

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Odpowiedzialne filary ML — interpretowanie, różnicowa prywatność, szyfrowanie homomorphic, śledzenie inspekcji — Azure Machine Learning":::

Ponieważ sztuczna inteligencja i autonomiczne systemy integrują się z siecią szkieletową, ważne jest, aby aktywnie podejmować wysiłki w celu przewidzenia i ograniczenia nieoczekiwanych konsekwencji tych technologii.

## <a name="interpret-and-explain-model-behavior"></a>Interpretuj i wyjaśnij zachowanie modelu

Trudne do wyjaśnienia i nieprzezroczyste systemy mogą być problematyczne, ponieważ utrudniają one uczestnikom, takim jak deweloperzy systemu, organy nadzorujące, użytkownicy i osoby podejmujące decyzje biznesowe, aby zrozumieć, dlaczego systemy podejmują pewne decyzje. Niektóre systemy AI są bardziej wyjaśnione niż inne, a czasami istnieje kompromis między systemem i większą dokładnością, który jest bardziej wyjaśniony.

Aby utworzyć interpretowane systemy AI, użyj [InterpretML](https://github.com/interpretml/interpret), pakietu open source utworzonego przez firmę Microsoft. Pakiet InterpretML obsługuje szeroką gamę technik interpretacji, takich jak wyjaśnienia SHapley dodatków (kształt), objaśnienie śladowe i ważność funkcji permutacji (PFI).  [InterpretML można używać wewnątrz Azure Machine Learning](how-to-machine-learning-interpretability.md) do [interpretacji i wyjaśnienia modeli uczenia maszynowego](how-to-machine-learning-interpretability-aml.md), w tym [zautomatyzowanych modeli uczenia maszynowego](how-to-machine-learning-interpretability-automl.md).

## <a name="mitigate-fairness-in-machine-learning-models"></a>Eliminowanie godziwości modeli uczenia maszynowego

Ponieważ systemy AI stają się coraz bardziej związane z codziennym podejmowaniem decyzji, jest to bardzo ważne, aby te systemy działały w sposób atrakcyjny dla wszystkich użytkowników.

Nieuczciwą w systemach AI mogą powodować następujące niezamierzone konsekwencje:

- Zatrzymujące się szanse sprzedaży, zasoby lub informacje od użytkowników indywidualnych.
- Wzmacnianie bias i stereotypów.

Nie można przechwytywać ani reprezentować wielu aspektów godziwych. Istnieją narzędzia i praktyki, które mogą poprawić atrakcyjność projektowania i opracowywania systemów AI.

Dwa kluczowe kroki w celu zmniejszenia nieuczciwości w systemach AI są ocenami i ograniczeniami. Zalecamy [FairLearn](https://github.com/fairlearn/fairlearn), pakiet typu "open source", który umożliwia ocenę i łagodzenie potencjalnej nieuczciwości systemów AI. Aby dowiedzieć się więcej na temat sprawiedliwości i pakietu FairLearn, zobacz [godziwość artykułu w ml](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Zapobiegaj narażeniu danych z różnicową prywatność

Gdy dane są używane do analizy, ważne jest, aby dane pozostały prywatne i poufne w całym użyciu. Różnicowa Ochrona prywatności to zestaw systemów i praktyk, które ułatwiają zapewnienie bezpieczeństwa i prywatnego danych osobom.

W tradycyjnych scenariuszach dane pierwotne są przechowywane w plikach i bazach danych. Gdy użytkownicy analizują dane, zazwyczaj korzystają z danych pierwotnych. Jest to problem, ponieważ może naruszać prywatność poszczególnych użytkowników. Różnicowa Ochrona prywatności próbuje rozwiązać ten problem, dodając "szum" lub losowość do danych, tak aby użytkownicy nie mogli identyfikować poszczególnych punktów danych.

Implementacja różnicowych systemów prywatnych jest trudna. [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core) to projekt open-source, który zawiera różne składniki służące do tworzenia globalnych, różnicowych systemów prywatnych. Aby dowiedzieć się więcej o zasadach ochrony prywatności i projekcie SmartNoise, zobacz artykuł [zachowanie prywatności danych przy użyciu różnicowych informacji o ochronie prywatności i SmartNoise](./concept-differential-privacy.md) .

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Pracuj z zaszyfrowanymi danymi przy użyciu szyfrowania homomorphic

W tradycyjnych rozwiązaniach magazynu i obliczeń w chmurze chmura musi mieć niezaszyfrowaną dostęp do danych klienta w celu ich obliczenia. Ten dostęp udostępnia dane operatorom chmury. Prywatność danych opiera się na zasadach kontroli dostępu wdrożonych przez chmurę i zaufanych przez klienta.

Szyfrowanie Homomorphic pozwala na przeprowadzanie obliczeń na zaszyfrowanych danych bez konieczności uzyskiwania dostępu do klucza tajnego (odszyfrowywania). Wyniki obliczeń są szyfrowane i mogą być ujawnione tylko przez właściciela klucza tajnego. Przy użyciu szyfrowania homomorphic operatory w chmurze nigdy nie będą miały niezaszyfrowanego dostępu do danych przechowywanych i przetwarzanych przez te osoby. Obliczenia są wykonywane bezpośrednio na zaszyfrowanych danych. Prywatność danych opiera się na najnowocześniejszej kryptografii, a właściciel danych kontroluje wszystkie wersje informacji. Aby uzyskać więcej informacji na temat szyfrowania homomorphic firmy Microsoft, zobacz [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

Aby rozpocząć korzystanie z szyfrowania homomorphic w Azure Machine Learning, użyj [zaszyfrowanego](https://pypi.org/project/encrypted-inference/) powiązania języka Python dla [programu Microsoft Seal](https://github.com/microsoft/SEAL). Microsoft SEAL to biblioteka szyfrowania homomorphic typu open source, która umożliwia dodawanie i mnożenie w przypadku szyfrowanych liczb całkowitych lub liczb rzeczywistych. Aby dowiedzieć się więcej o programie Microsoft SEAL, zapoznaj się z tematem [centrum architektury platformy Azure](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) lub [stronę projektu Microsoft Research](https://www.microsoft.com/research/project/microsoft-seal/).

Zapoznaj się z poniższym przykładem, aby dowiedzieć się, [jak wdrożyć zaszyfrowaną usługę sieci Web inferencing w Azure Machine Learning](how-to-homomorphic-encryption-seal.md).

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Udokumentowanie cyklu życia uczenia maszynowego za pomocą arkuszy danych

Dokumentowanie właściwych informacji w procesie uczenia maszynowego jest kluczem do podejmowania właściwych decyzji na każdym etapie. Arkusze danych to sposób dokumentowania zasobów uczenia maszynowego, które są używane i tworzone w ramach cyklu życia uczenia maszynowego.

Modele są uważane za "pola nieprzezroczyste" i często są niewielkimi informacjami na ich temat. Ponieważ systemy uczenia maszynowego stają się coraz bardziej rozpowszechnione i są używane do podejmowania decyzji, korzystanie z arkuszy danych to krok w kierunku tworzenia bardziej odpowiedzialnych systemów uczenia maszynowego.

Niektóre informacje o modelu, które można chcieć udokumentować w ramach arkusza danych:

- Zamierzone użycie
- Architektura modelu
- Używane dane szkoleniowe
- Użyte dane oceny
- Metryki wydajności modelu szkolenia
- Informacje o rzetelności.

Zapoznaj się z poniższym przykładem, aby dowiedzieć się, jak zaimplementować [Arkusz danych dla modeli przy](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)użyciu zestawu SDK Azure Machine Learning.

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby uzyskać więcej informacji, zobacz [odpowiedzialny zestaw narzędzi do innowacji](/azure/architecture/guide/responsible-innovation/) , aby poznać najlepsze rozwiązania.
- Dowiedz się więcej o zestawie wskazówek [dotyczących informacji dotyczących](https://www.partnershiponai.org/about-ml/) systemu uczenia maszynowego.