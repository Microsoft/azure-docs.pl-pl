---
title: 'ML Studio (klasyczny): Migruj do Azure Machine Learning'
description: Migruj z programu Studio (wersja klasyczna) do Azure Machine Learning na potrzeby nowoczesnej platformy do nauki o danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fda34a7ee06d35846bcec571e904297d0421c38f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565111"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrowanie do usługi Azure Machine Learning

W tym artykule dowiesz się, jak migrować zasoby programu Studio (klasyczne) do Azure Machine Learning. W tej chwili aby przeprowadzić migrację zasobów, należy ręcznie skompilować eksperymenty.

Azure Machine Learning zapewnia nowoczesnej platformy do nauki o danych, która łączy nie podpodejścia kodu i kodu. Aby dowiedzieć się więcej o różnicach między Studio (klasyczny) i Azure Machine Learning, zobacz sekcję dotyczącą [oceny Azure Machine Learning](#step-1-assess-azure-machine-learning) .


## <a name="recommended-approach"></a>Zalecane podejście

Aby przeprowadzić migrację do Azure Machine Learning, zalecamy następujące podejście:

> [!div class="checklist"]
> * Krok 1. Ocena Azure Machine Learning
> * Krok 2. Tworzenie planu migracji
> * Krok 3. ponowne kompilowanie eksperymentów i usług sieci Web
> * Krok 4. Integrowanie aplikacji klienckich
> * Krok 5. Czyszczenie zasobów Studio (klasyczne)


## <a name="step-1-assess-azure-machine-learning"></a>Krok 1. Ocena Azure Machine Learning
1. Dowiedz się więcej na temat [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/); korzyści, koszty i architektura.

1. Porównaj możliwości Azure Machine Learning i Studio (klasyczne).

    >[!NOTE]
    > Funkcja **Projektant** w Azure Machine Learning zapewnia podobne środowisko przeciągania i upuszczania do programu Studio (klasyczne). Jednak Azure Machine Learning zapewnia również niezawodne, niezawodne [przepływy pracy w kodzie](../concept-model-management-and-deployment.md) . Ta seria migracji koncentruje się na projektancie, ponieważ jest najbardziej podobna do środowiska Studio (klasycznego).

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Sprawdź, czy w programie Azure Machine Learning Designer są obsługiwane moduły o znaczeniu krytycznym (klasyczne). Aby uzyskać więcej informacji, zobacz poniższą tabelę programu [Studio (klasyczną) i projektanta w module](#studio-classic-and-designer-module-mapping) .

4. [Utwórz obszar roboczy Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>Krok 2. Tworzenie planu migracji

1. Zidentyfikuj **zestawy danych**, **modele** i **usługi sieci Web** programu Studio (klasyczne), które chcesz zmigrować.

1. Określ wpływ migracji na firmę.

1. Utwórz plan migracji.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Krok 3. ponowne kompilowanie eksperymentów i usług sieci Web

1. [Migrowanie zestawów danych do Azure Machine Learning](migrate-register-dataset.md).
1. Użyj projektanta, aby [skompilować eksperymenty](migrate-rebuild-experiment.md).
1. Użyj projektanta, aby ponownie [wdrożyć usługi sieci Web](migrate-rebuild-web-service.md).

    >[!NOTE]
    > Azure Machine Learning obsługuje także przepływy pracy w kodzie — w przypadku [zestawów danych](../how-to-create-register-datasets.md), [szkoleń](../how-to-set-up-training-targets.md)i [wdrożeń](../how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Krok 4. Integrowanie aplikacji klienckich

1. Zmodyfikuj aplikacje klienckie, które wywołują usługi sieci Web programu Studio (klasyczne) do korzystania z nowych [punktów końcowych Azure Machine Learning](migrate-rebuild-integrate-with-client-app.md).

## <a name="step-5-cleanup-studio-classic-assets"></a>Krok 5. Czyszczenie zasobów programu Studio (klasyczne)

1. [Wyczyść zasoby Studio (klasyczne)](export-delete-personal-data-dsr.md) , aby uniknąć dodatkowych opłat. Możesz chcieć zachować zasoby do powrotu do momentu zweryfikowania Azure Machine Learning obciążeń.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (klasyczne) i projektowanie modułu — mapowanie

Zapoznaj się z poniższą tabelą, aby zobaczyć, które moduły mają być używane podczas odbudowywania eksperymentów programu Studio (klasycznych) w projektancie.


> [!IMPORTANT]
> Projektant implementuje moduły za pomocą pakietów języka Python typu open source, a nie pakietów C#, takich jak Studio (klasyczne). Ze względu na tę różnicę dane wyjściowe modułów projektanta mogą się nieco różnić od ich odpowiedników w programie Studio (klasycznych).


|Kategoria|Studio (klasyczny) — moduł|Zastępowanie modułu projektanta|
|--------------|----------------|--------------------------------------|
|Wprowadzanie i wyprowadzanie danych|— Wprowadź dane ręcznie </br> -Eksportowanie danych </br> — Importuj dane </br> -Załaduj model przeszkolony </br> -Unpack spakowane zestawy danych|— Wprowadź dane ręcznie </br> -Eksportowanie danych </br> — Importuj dane|
|Konwersje formatu danych|— Konwertuj do formatu CSV </br> -Konwertuj na zestaw danych </br> -Konwertuj na ARFF </br> -Konwertuj na SVMLight </br> -Konwertuj na TSV|— Konwertuj do formatu CSV </br> -Konwertuj na zestaw danych|
|Przekształcanie danych — manipulowanie|-Dodaj kolumny</br> -Dodaj wiersze </br> -Zastosuj transformację SQL </br> -Czyszczenie brakujących danych </br> -Konwertuj na wartości wskaźnika </br> -Edytuj metadane </br> -Dołącz dane </br> -Usuń zduplikowane wiersze </br> -Wybieranie kolumn w zestawie danych </br> -Wybierz kolumny Przekształć </br> -SMOTE </br> -Group — wartości kategorii|-Dodaj kolumny</br> -Dodaj wiersze </br> -Zastosuj transformację SQL </br> -Czyszczenie brakujących danych </br> -Konwertuj na wartości wskaźnika </br> -Edytuj metadane </br> -Dołącz dane </br> -Usuń zduplikowane wiersze </br> -Wybieranie kolumn w zestawie danych </br> -Wybierz kolumny Przekształć </br> -SMOTE|
|Przekształcanie danych — skalowanie i zmniejszanie |-Wartości klipów </br> -Grupowanie danych w pojemnikach </br> -Normalizowanie danych </br>-Podstawowa analiza składników |-Wartości klipów </br> -Grupowanie danych w pojemnikach </br> -Normalizowanie danych|
|Przekształcanie danych — próbkowanie i dzielenie|-Partition i Sample </br> -Podziel dane|-Partition i Sample </br> -Podziel dane|
|Przekształcanie danych — filtr |-Zastosuj filtr </br> -FIR filtr </br> -IIR filtr </br> — Filtr mediana </br> — Filtr średniej wartości </br> -Filtr progu </br> — Filtr zdefiniowany przez użytkownika||
|Przekształcanie danych — uczenie się z liczbami |-Kompilacja przekształcenia obliczeń </br> -Eksportowanie tabeli Count </br> -Importowanie tabeli Count </br> — Przekształć liczbę scaleń</br>  -Modyfikuj parametry tabeli Count||
|Wybór funkcji |-Filtruj wybór funkcji na podstawie filtru </br> -Fisher liniowy discriminant analizy  </br> — Ważność funkcji permutacji |-Filtruj wybór funkcji na podstawie filtru </br>  — Ważność funkcji permutacji|
| Model — Klasyfikacja| -Wieloklasowy Las decyzyjny </br> -Wieloklasowa decyzja Jungle  </br> -Wieloklasowa regresja logistyczna  </br>— Wieloklasowa sieć neuronowych  </br>-Moduł wieloklasowy „jeden przeciw wszystkim” </br>-Two-Class średnia Perceptron </br>-Two-Class maszynę punktu Bayesa </br>-Two-Class drzewo decyzyjne podwyższenie  </br> -Two-Class Las decyzyjny  </br> -Two-Class decyzji Jungle  </br> -Two-Class Locally-Deep SVM </br> -Two-Class regresja logistyczna  </br> -Two-Class sieć neuronowych </br> -Two-Class obsługiwać maszyny wektorowe  | -Wieloklasowy Las decyzyjny </br>  — Drzewo decyzyjne dla wieloklasowego  </br> -Wieloklasowa regresja logistyczna </br> — Wieloklasowa sieć neuronowych </br> -Moduł wieloklasowy „jeden przeciw wszystkim”  </br> -Two-Class średnia Perceptron  </br> -Two-Class drzewo decyzyjne podwyższenie  </br> -Two-Class Las decyzyjny </br>-Two-Class regresja logistyczna </br> -Two-Class sieć neuronowych </br>-Two-Class obsługiwać maszyny wektorowe  |
| Model — klastrowanie| -K-oznacza klastrowanie| -K-oznacza klastrowanie|
| Regresja modelu| -Bayesowskie regresja liniowa  </br> -Zwiększona regresja drzewa decyzyjnego  </br>— Regresja lasu decyzyjnego  </br> — Szybkie regresja quantile lasów  </br> — Regresja liniowa </br> -Neuronowych regresji sieci </br> -Regresja regresji Poissona| -Zwiększona regresja drzewa decyzyjnego  </br>— Regresja lasu decyzyjnego  </br> — Szybkie regresja quantile lasów </br> — Regresja liniowa  </br> -Neuronowych regresji sieci </br> -Regresja Poissona|
| Model — wykrywanie anomalii| -One-Class SVM  </br> -PCA-Based wykrywania anomalii | -PCA-Based wykrywania anomalii|
| Machine Learning — Oceń  | — Krzyżowy model weryfikacji  </br>-Oceń model  </br>-Oceń zalecaną | — Krzyżowy model weryfikacji  </br>-Oceń model </br> -Oceń zalecaną|
| Machine Learning — uczenie| -Czyszczenie klastrów  </br> -Uczenie modelu wykrywania anomalii </br>-Uczenie modelu klastra  </br> — Polecający szkolenie Matchbox-</br> Trenowanie modelu  </br>-Dostrajanie parametrów modelu| -Uczenie modelu wykrywania anomalii  </br> -Uczenie modelu klastra </br> -Uczenie modelu —</br> -Uczenie modelu PyTorch  </br>Polecający ciąg SVD  </br>-Szkolenia szerokiej i głębokiej </br>-Dostrajanie parametrów modelu|
| Machine Learning — Ocena| -Zastosuj transformację  </br>— Przypisywanie danych do klastrów  </br>-Matchbox polecający oceny </br> -Model punktacji|-Zastosuj transformację  </br> — Przypisywanie danych do klastrów </br> -Model obrazu oceny  </br> -Model punktacji </br>-Punktacja SVD zalecenia </br> -Ocena szerokiej i głębokiego polecania|
| Moduły biblioteki OpenCV| — Importowanie obrazów </br>-Wstępnie przeszkolone klasyfikację obrazu kaskadowego | |
| Moduły języka Python| -Wykonaj skrypt języka Python| -Wykonaj skrypt języka Python  </br> -Utwórz model języka Python |
| Moduły języka R  | -Wykonaj skrypt języka R  </br> -Create R model| -Wykonaj skrypt języka R|
| Funkcje statystyczne | -Zastosuj operacje matematyczne </br>-Obliczanie statystyk podstawowych  </br>-Obliczanie korelacji liniowej  </br>-Oceń funkcję prawdopodobieństwa  </br>-Zamień wartości dyskretne  </br>-Sumuj dane  </br>-Przetestuj hipotezę przy użyciu t-test| -Zastosuj operacje matematyczne  </br>-Sumuj dane|
| Analiza tekstu| -Wykrywanie języków  </br>-Wyodrębnij kluczowe frazy z tekstu  </br>-Wyodrębnij funkcje N-gramowe z tekstu  </br>-Mieszanie funkcji </br>-Ukryty Dirichlet alokacji  </br>-Nazwanego rozpoznawania jednostek </br>— Wstępnie przetworzony tekst  </br>-Score Vowpal Wabbit w wersji 7-10  </br>-Score Vowpal Wabbit model wersji 8 </br>-Uczenie modelu Vowpal Wabbit w wersji 7-10  </br>-Uczenie modelu Vowpal Wabbit w wersji 8 |— Konwertuj słowo na wektor </br> -Wyodrębnij funkcje N-gramowe z tekstu </br>-Mieszanie funkcji  </br>-Ukryty Dirichlet alokacji </br>— Wstępnie przetworzony tekst  </br>-Score Vowpal Wabbit model </br> -Uczenie modelu Wabbit Vowpal|
| Szeregi czasowe| Wykrywanie anomalii szeregów czasowych | |
| Usługa internetowa | -Dane wejściowe </br> -Output | -Dane wejściowe </br>  — Dane wyjściowe|
| Przetwarzanie obrazów| | -Zastosuj transformację obrazu </br> -Konwertuj na katalog obrazu </br> -Przekształcenie obrazu init </br> -Split — katalog obrazu  </br> — Klasyfikacja obrazu DenseNet   </br>— Klasyfikacja obrazu ResNet |

Aby uzyskać więcej informacji na temat używania poszczególnych modułów projektanta, zobacz [Dokumentacja modułu projektanta](../algorithm-module-reference/module-reference.md).

### <a name="what-if-a-designer-module-is-missing"></a>Co zrobić w przypadku braku modułu projektanta?

Projektant Azure Machine Learning zawiera najpopularniejsze moduły z programu Studio (klasyczne). Zawiera również nowe moduły korzystające z najnowszych technik uczenia maszynowego. 

Jeśli migracja została zablokowana z powodu braku modułów w projektancie, skontaktuj się z nami, [tworząc bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Przykładowa migracja

W poniższej migracji eksperymentuje niektóre różnice między Studio (klasyczne) i Azure Machine Learning.

### <a name="datasets"></a>Zestawy danych

W programie Studio (klasyczne) **zestawy danych** zostały zapisane w obszarze roboczym i mogą być używane tylko przez Studio (klasyczne).

![Automobile — cena — klasyczny — zestaw danych](./media/migrate-overview/studio-classic-dataset.png)

W Azure Machine Learning **zestawy danych** są zarejestrowane w obszarze roboczym i mogą być używane we wszystkich Azure Machine Learning. Aby uzyskać więcej informacji na temat korzyści z zestawów danych Azure Machine Learning, zobacz [bezpieczny dostęp do danych](../concept-data.md#reference-data-in-storage-with-datasets).

![Automobile — cena — AML — zestaw danych](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Potok

W programie Studio (klasyczny) **eksperymenty** zawierały logikę przetwarzania dla pracy. Eksperymenty zostały utworzone za pomocą modułów przeciągania i upuszczania.


![samochód — cena — klasyczne — eksperyment](./media/migrate-overview/studio-classic-experiment.png)

W Azure Machine Learning **potoki** zawierają logikę przetwarzania dla pracy. Potoki można tworzyć za pomocą modułów przeciągania i upuszczania lub pisząc kod.

![samochód — cena — AML — potok](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Punkt końcowy usługi sieci Web

W programie Studio (klasyczny) **interfejs API żądania/odpowiedzi** został użyty do prognozowania w czasie rzeczywistym. **Interfejs API wykonywania wsadowego** został użyty do prognozowania partii lub ponownego szkolenia.

![Automobile — cena — klasyczny — usługa WebService](./media/migrate-overview/studio-classic-web-service.png)

W Azure Machine Learning **punkty końcowe czasu rzeczywistego** są używane do prognozowania w czasie rzeczywistym. **Punkty końcowe potoku** są używane na potrzeby prognozowania partii lub ponownego szkolenia.

![Automobile — cena — AML — punkt końcowy](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono wymagania wysokiego poziomu dotyczące migracji do Azure Machine Learning. Aby uzyskać szczegółowe instrukcje, zobacz inne artykuły w serii migracji programu Studio (klasycznej):

1. **Omówienie migracji**.
1. [Migrowanie zestawu danych](migrate-register-dataset.md).
1. [Kompiluj potok szkoleniowy Studio (klasyczny)](migrate-rebuild-experiment.md).
1. [Kompiluj ponownie usługę sieci Web programu Studio (klasyczna)](migrate-rebuild-web-service.md).
1. [Zintegruj usługę sieci web Azure Machine Learning z aplikacjami klienckimi](migrate-rebuild-integrate-with-client-app.md).
1. [Migruj skrypt wykonania skryptu języka R](migrate-execute-r-script.md).






