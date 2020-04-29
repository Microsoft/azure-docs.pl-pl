---
title: Analizowanie zmian klientów
titleSuffix: ML Studio (classic) - Azure
description: Analiza przypadku przy opracowywaniu zintegrowanego modelu do analizowania i oceniania zmian klientów przy użyciu Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204532"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analizowanie zmian klientów przy użyciu Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Omówienie
W tym artykule przedstawiono implementację referencyjną projektu analizy zmian klientów, który jest zbudowany przy użyciu Azure Machine Learning Studio (klasyczny). W tym artykule omówiono powiązane modele ogólne do całościowego rozwiązywania problemów z przemysłowym zmianą klientów. Firma Microsoft mierzy również dokładność modeli, które są kompilowane przy użyciu Machine Learning i ocenia wskazówki dotyczące dalszych wdrożeń.  

### <a name="acknowledgements"></a>Podziękowania
Ten eksperyment został opracowany i przetestowany przez Serge Berger, główny analityk danych firmy Microsoft i Roger Barks, dawniej menedżer produktu dla Microsoft Azure Machine Learning Studio (klasyczny). Zespół ds. dokumentacji platformy Azure potwierdzi swoją wiedzę i Dziękujemy za udostępnienie tego oficjalnego dokumentu.

> [!NOTE]
> Dane używane na potrzeby tego eksperymentu nie są publicznie dostępne. Przykład tworzenia modelu uczenia maszynowego na potrzeby analizy zmian można znaleźć w temacie: [Szablon modelu zmian w sieci sprzedaży](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) w [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problem z zmianą klienta
Przedsiębiorstwa na rynku konsumentów i we wszystkich sektorach przedsiębiorstwa muszą zająć się zmianą. Czasami zmiany są nadmierne i wpływają na decyzje dotyczące zasad. Tradycyjne rozwiązanie polega na przewidywaniu wieloskładnikowych zmian i zaspokajaniu ich potrzeb za pośrednictwem usługi concierge, kampanii marketingowych lub stosowania specjalnych zwolnień. Te podejścia mogą się różnić w zależności od branży. Mogą nawet różnić się od konkretnych klastrów konsumenckich w ramach jednej branży (na przykład telekomunikacji).

Typowym czynnikiem jest to, że firmy muszą zminimalizować te specjalne działania związane z przechowywaniem klientów. Z tego względu naturalna metodologia to Ocena każdego klienta z prawdopodobieństwem zmiany i rozliczenia górnych N. Główni klienci mogą być najbardziej dochodowe. Na przykład w bardziej zaawansowane scenariusze jest stosowana funkcja zysku podczas wyboru kandydatów na potrzeby specjalnego zwolnienia. Te zagadnienia są jednak tylko częścią kompletnej strategii postępowania w przypadku zmian. Firmy muszą także wziąć pod uwagę ryzyko związane z ryzykiem (i odpornością na ryzyko), poziom i koszt interwencji oraz dopuszczalną segmentację klientów.  

## <a name="industry-outlook-and-approaches"></a>Branżowa i podejścia do programu Outlook
Zaawansowana obsługa zmian to znak dojrzałej branży. Klasycznym przykładem jest branża telekomunikacyjna, w której Subskrybenci są znani, aby często przełączać się od jednego dostawcy do drugiego. Takie dobrowolne zmiany są bardzo ważne. Ponadto dostawcy zgromadziły znaczną wiedzę na temat *sterowników*zmian, które są czynnikami umożliwiającymi przełączenie klientów.

Na przykład, słuchawki lub wybór urządzenia to dobrze znany sterownik zmian w firmie telefon komórkowy. W związku z tym popularne zasady mają na celu subsydiowanie ceny słuchawki dla nowych subskrybentów i naliczanie klientom pełnej ceny za uaktualnienie. W przeszłości te zasady prowadziły do przeskoku klientów od jednego dostawcy w celu uzyskania nowego rabatu. Jest to z kolei monituje dostawców o udoskonalenie strategii.

Wysoka lotność w ofertach dla słuchawek to czynnik, który szybko unieważnia modele zmian w oparciu o bieżące modele słuchawek. Ponadto telefony komórkowe nie są tylko urządzeniami telekomunikacyjnymi, ale również są zdaniami (należy wziąć pod uwagę telefon iPhone). Te predykcyjne społecznościowe wykraczają poza zakres zwykłych zestawów danych telekomunikacyjnych.

Wynikiem modelowania jest to, że nie można opracować zasad dźwiękowych po prostu przez wyeliminowanie znanych przyczyn zmian. W rzeczywistości ciągła strategia modelowania, w tym modele klasyczne, które określają zmienne kategorii (na przykład drzewa decyzyjne), jest **obowiązkowa**.

Korzystając z zestawów danych Big Data w swoich klientach, organizacje przeprowadzają analizę danych Big Data (w szczególności wykrywanie zmian na podstawie danych Big Data) jako efektywne podejście do problemu. Więcej informacji na temat podejścia do danych Big Data można znaleźć w sekcji zalecenia dotyczące elementu ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia do modelowania zmian klientów
Typowy proces rozwiązywania problemów, który rozwiązuje zmiany klientów, przedstawiono na ilustracji 1-3:  

1. Model ryzyka pozwala rozważyć, jak akcje wpływają na prawdopodobieństwo i ryzyko.
2. Model interwencyjny pozwala rozważyć, jak poziom interwencji może wpłynąć na prawdopodobieństwo zmian i ilość wartości okresu istnienia klienta (CLV).
3. Ta analiza nadaje się do analizy jakościowej, która jest eskalacją do aktywnej kampanii marketingowej, która jest przeznaczona dla segmentów klientów w celu zapewnienia optymalnej oferty.  

![Diagram przedstawiający sposób, w jaki tolerancja ryzyka Plus modele decyzyjne dają szczegółowe informacje umożliwiające podjęcie odpowiednich działań](./media/azure-ml-customer-churn-scenario/churn-1.png)

Ta metoda wyszukiwania do przodu jest najlepszym sposobem traktowania zmian, ale ma złożoność: należy opracować wiele modeli Archetype i śledzenia między modelami. Interakcje między modelami mogą być hermetyzowane, jak pokazano na poniższym diagramie:  

![Diagram interakcji modelu zmian](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Rysunek 4: ujednolicony Archetype wielomodelowy*  

Interakcje między modelami to klucz, jeśli mamy dostarczyć całościowe podejście do przechowywania klientów. Każdy model musi być niezbędny do obniżenia wydajności w czasie; w związku z tym architektura jest pętlą niejawną (podobną do Archetype ustawioną w standardzie wyszukiwania danych przez WYRAZISTy DM, [***3***]).  

Ogólny cykl tworzenia segmentacji i rozkładu ryzyka jest nadal uogólnioną strukturą, która ma zastosowanie do wielu problemów z działalnością biznesową. Analiza zmian jest po prostu silnym przedstawicielem tej grupy problemów, ponieważ wykazuje wszystkie cechy złożonego problemu biznesowego, który nie pozwala na uproszczone rozwiązanie predykcyjne. Aspekty społeczne nowoczesnego podejścia do zmian nie są szczególnie wyróżnione w podejściu, ale aspekty społeczne są hermetyzowane w Archetype modelowania, ponieważ byłyby w modelu.  

Interesujący dodatek to analiza danych Big Data. Dzisiejsza komunikacja telekomunikacyjna i sprzedaż detaliczna zbierają wyczerpujące dane o klientach, a firma Microsoft może w łatwy sposób zastanowić się, że konieczność połączenia z wieloma modelami stanie się typowym trendem, uwzględniając nowe trendy, takie jak Internet rzeczy i powszechne urządzenia, które umożliwiają firmie korzystanie z inteligentnych rozwiązań na wielu warstwach.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementacja Archetype modelowania w Machine Learning Studio (klasyczny)
Co to jest najlepszy sposób wdrożenia zintegrowanego podejścia modelowania i oceniania? W tej sekcji pokazano, jak to zrobić za pomocą Azure Machine Learning Studio (klasyczny).  

Podejście wielomodelowe to musi podczas projektowania globalnego Archetype na potrzeby zmian. Nawet etap oceny (predykcyjny) musi być wiele modelem.  

Na poniższym diagramie przedstawiono prototyp utworzony przez siebie, który wykorzystuje cztery algorytmy oceniania w Machine Learning Studio (klasyczny) do przewidywania zmian. Powodem korzystania z podejścia wielomodelowego jest nie tylko utworzenie klasyfikatora wieloskładnikowego w celu zwiększenia dokładności, ale również do ochrony przed nadmiernym dopasowaniem i ulepszania funkcji wyboru.  

![Zrzut ekranu przedstawiający złożony obszar roboczy programu Studio (klasyczny) z wieloma połączonymi modułami](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Rysunek 5. prototyp podejścia do modelowania zmian*  

Poniższe sekcje zawierają więcej szczegółów na temat prototypowego modelu oceniania, który został zaimplementowany przy użyciu Machine Learning Studio (klasyczny).  

### <a name="data-selection-and-preparation"></a>Wybór i przygotowanie danych
Dane używane do tworzenia modeli i oceny klientów uzyskano z rozwiązania w pionie programu CRM, z danymi zasłoniętymi w celu ochrony prywatności klientów. Dane zawierają informacje o 8 000 subskrypcjach w Stanach Zjednoczonych i łączyją trzy źródła: dane aprowizacji (metadane subskrypcji), dane działania (użycie systemu) i dane obsługi klienta. Dane nie obejmują żadnych informacji związanych z działalnością klienta; nie obejmują na przykład metadanych lojalności ani kredytów.  

Dla uproszczenia procesy oczyszczania i czyszczenia danych są poza zakresem, ponieważ zakłada się, że Przygotowywanie danych zostało już zrobione w innym miejscu.

Wybór funkcji modelowania jest oparty na wstępnym ocenie zestawu predykcyjnych, zawartej w procesie, który używa modułu lasów losowych. Dla implementacji w Machine Learning Studio (klasyczny) obliczamy średnią, medianę i zakresy dla funkcji reprezentatywnych. Na przykład dodaliśmy zagregowane dane jakościowe, takie jak minimum i maksimum wartości dla aktywności użytkownika.

Przechwycono również informacje o danych czasowych przez ostatnie sześć miesięcy. Dane są analizowane przez jeden rok, a firma Microsoft ustanowiła, że nawet jeśli wystąpiły statystycznie znaczące trendy, wpływ na zmiany jest znacznie zmniejszany po sześciu miesiącach.  

Najważniejszym punktem jest to, że cały proces, w tym ETL, wybór funkcji i modelowanie, został zaimplementowany w Machine Learning Studio (klasyczny), przy użyciu źródeł danych w Microsoft Azure.   

Poniższe diagramy ilustrują używane dane.  

![Zrzut ekranu przedstawiający przykład danych używanych z nieprzetworzonymi wartościami](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Ilustracja 6. fragment źródła danych (zasłonięty)*  

![Zrzut ekranu przedstawiający funkcje statystyczne wyodrębnione ze źródła danych](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Rysunek 7. funkcje wyodrębnione ze źródła danych*
 

> Należy zauważyć, że te dane są prywatne i w związku z tym nie można udostępniać modelu i danych.
> Jednak w przypadku podobnego modelu korzystającego z publicznie dostępnych danych zapoznaj się z tym przykładowym eksperymentem w [Azure AI Gallery](https://gallery.azure.ai/): [odpływ](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Aby dowiedzieć się więcej o tym, jak zaimplementować model analizy zmian przy użyciu Cortana Intelligence Suite, zalecamy także [ten film wideo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) według starszego menedżera programów wee Hyong tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algorytmy używane w prototypie
Użyto następujących czterech algorytmów uczenia maszynowego do utworzenia prototypu (bez dostosowania):  

1. Regresja logistyczna (LR)
2. Drzewo podwyższanych decyzji (BT)
3. Średnia Perceptron (AP)
4. Obsługa maszyny wektorowej (SVM)  

Na poniższym diagramie przedstawiono część powierzchni projektowej eksperymentu, która wskazuje sekwencję, w której zostały utworzone modele:  

![Zrzut ekranu przedstawiający małą sekcję kanwy eksperymentu dotyczącej programu Studio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Ilustracja 8. Tworzenie modeli w Machine Learning Studio (klasyczny)*  

### <a name="scoring-methods"></a>Metody oceniania
Są oceniane cztery modele przy użyciu zestawu danych szkoleniowych z etykietą.  

Przesyłamy również zestaw danych oceniania do porównywalnego modelu skompilowanego przy użyciu wersji klasycznej SAS Enterprise Miner 12. Mierzy dokładność modelu SAS i wszystkie cztery modele Machine Learning Studio (klasyczne).  

## <a name="results"></a>Wyniki
W tej sekcji przedstawimy nasze ustalenia dotyczące dokładności modeli w oparciu o zestaw danych oceniania.  

### <a name="accuracy-and-precision-of-scoring"></a>Dokładność i dokładność oceniania
Ogólnie rzecz biorąc, implementacja w Azure Machine Learning Studio (klasyczna) znajduje się za SYGNATURą dostępu współdzielonego o 10-15% (obszar poniżej krzywej lub AUC).  

Jednak najważniejszym metryką w przypadku zmian jest współczynnik niepowodzeń klasyfikacji: to jest, z górnych zmian, które są przewidywane przez klasyfikator, który faktycznie **nie** został zmieniony, i jeszcze otrzymało specjalne traktowanie? Na poniższym diagramie porównano tę nieokreśloną częstotliwość klasyfikacji dla wszystkich modeli:  

![Obszar pod wykresem krzywej porównujący wydajność 4 algorytmów](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Rysunek 9: obszar prototypu Passau pod krzywą*

### <a name="using-auc-to-compare-results"></a>Porównywanie wyników przy użyciu AUC
Obszar pod krzywą (AUC) jest metryką, która przedstawia globalną miarę *separability* między dystrybucją wyników dla populacji dodatniej i ujemnej. Jest podobna do tradycyjnego grafu charakterystycznego operatora odbiornika (ROC), ale jedna ważna różnica polega na tym, że Metryka AUC nie wymaga wybrania wartości progowej. Zamiast tego podsumowuje wyniki na **wszystkie** możliwe opcje. W przeciwieństwie do tradycyjnego wykresu ROC pokazuje dodatnią stawkę na osi pionowej i fałszywą dodatnią stawkę na osi poziomej, a próg klasyfikacji jest różny.   

AUC jest używany jako miara wartości dla różnych algorytmów (lub różnych systemów), ponieważ umożliwia porównywanie modeli za pomocą ich AUCch. Jest to popularne podejście w branżach, takich jak meteorologiczne i bionauki. W ten sposób AUC reprezentuje popularne narzędzie do oceny wydajności klasyfikatora.  

### <a name="comparing-misclassification-rates"></a>Porównanie wskaźników niesklasyfikowanych
Porównano niesklasyfikowane kursy dotyczące zestawu danych przy użyciu danych programu CRM o około 8 000 subskrypcjach.  

* Częstotliwość niepowodzeń klasyfikacji sygnatury dostępu współdzielonego wynosi 10-15%.
* Częstotliwość niepowodzeń klasyfikacji Machine Learning Studio (klasyczna) była 15-20% dla najbardziej pożądanych zmian 200-300.  

W branży telekomunikacyjnej ważne jest, aby zająć się tylko tym klientom, którzy mają największe ryzyko związane z zmianą, oferując im usługi concierge lub inne szczególne traktowanie. W tym względzie implementacja Machine Learning Studio (klasyczna) osiąga wyniki na wartości nominalnej z modelem SAS.  

O tym samym tokenie dokładność jest ważniejsze od precyzji, ponieważ firma Microsoft interesuje się w prawidłowym klasyfikowaniu potencjalnych zmian.  

Poniższy diagram z witryny Wikipedia przedstawia relację w livelyej i łatwej do zrozumienia ilustracji:  

![Dwa elementy docelowe. Jeden element docelowy pokazuje luźno pogrupowane znaczniki, ale blisko byków oznaczonych jako "niska dokładność: Dobry stopień precyzji. Inna lokalizacja docelowa jest ściśle zgrupowana, ale daleko od buhajów — oczy oznaczonej jako "niska dokładność: niska wartość rzeczywista, dobra precyzja"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Rysunek 10. kompromis między dokładnością i dokładnością*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Dokładne i precyzyjne wyniki dla podwyższanego modelu drzewa decyzyjnego
Poniższy wykres przedstawia nieprzetworzone wyniki oceny przy użyciu prototypu Machine Learning dla modelu drzewa decyzyjnego, który ma być największą dokładnością spośród czterech modeli:  

![Fragment tabeli przedstawiający dokładność, precyzję, odwołanie, F-Score, AUC, średnią utratę dziennika i utratę dzienników szkolenia dla czterech algorytmów](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Rysunek 11: Charakterystyka podwyższania modelu drzewa decyzyjnego*

## <a name="performance-comparison"></a>Porównanie wydajności
Porównano szybkość, z jaką dane były oceniane przy użyciu modeli Machine Learning Studio (klasycznych) i porównywalny model utworzony przy użyciu wersji klasycznej SAS Enterprise Miner 12,1.  

Poniższa tabela zawiera podsumowanie wydajności algorytmów:  

*Tabela 1. Ogólna wydajność (dokładność) algorytmów*

| LR | BT | Interfejs API | SVM |
| --- | --- | --- | --- |
| Średni model |Najlepszy model |Identyfikuj |Średni model |

Modele hostowane w Machine Learning Studio (klasyczne) przeprowadzili SYGNATURę dostępu współdzielonego o 15-25% w celu przyspieszenia wykonywania, ale dokładność była wysoka na wartości nominalnej.  

## <a name="discussion-and-recommendations"></a>Dyskusja i zalecenia
W branży telekomunikacyjnej kilka praktyk okazało się analizować zmiany, w tym:  

* Metryki pochodne dla czterech podstawowych kategorii:
  * **Jednostka (na przykład subskrypcja)**. Zastrzegaj podstawowe informacje o subskrypcji i/lub kliencie, które są przedmiotem zmian.
  * **Działanie**. Uzyskaj wszystkie możliwe informacje o użyciu, które są związane z jednostką, na przykład liczbę logowań.
  * **Obsługa klienta**. Zbieraj informacje z dzienników obsługi klienta, aby wskazać, czy subskrypcja ma problemy lub interakcje z obsługą klienta.
  * **Dane konkurencyjne i biznesowe**. Uzyskaj wszelkie informacje dotyczące klienta (na przykład mogą być niedostępne lub trudne do śledzenia).
* Użyj ważności do wyboru funkcji dysku. Oznacza to, że wzmocniony Model drzewa decyzyjnego zawsze jest podejściem obietnicowym.  

Użycie tych czterech kategorii tworzy złudzenie, *że proste podejście* , oparte na indeksach utworzonych na odpowiednich czynnikach na kategorię, powinno być wystarczające do identyfikowania klientów narażonych na zmiany. Niestety, chociaż to pojęcie wydaje się wiarygodne, jest to fałszywe zrozumienie. Przyczyną jest to, że zmiany są wynikiem czasowym, a czynniki wpływające na postęp są zwykle w Stanach przejściowych. Co potencjalni klienci, którzy powinni wziąć pod uwagę opuszczenie dzisiaj, mogą się różnić w przyszłości. z tego momentu będzie ona inna niż sześć miesięcy. W związku z tym model *probabilistyczne* jest konieczny.  

Ta ważna obserwacja jest często zastosowana w firmie, która zwykle preferuje analizę biznesową ukierunkowaną na analizy biznesowej, głównie ponieważ jest to łatwiejsze sprzedawanie i przyjmowanie prostej automatyzacji.  

Jednak obietnica samoobsługowej analizy przy użyciu Machine Learning Studio (klasyczny) polega na tym, że cztery kategorie informacji, które są klasyfikowane według działu lub działu, stają się cennym źródłem dla uczenia maszynowego na temat zmian.  

Kolejną atrakcyjną możliwością Azure Machine Learning Studio (klasyczną) jest możliwość dodawania niestandardowego modułu do repozytorium wstępnie zdefiniowanych modułów, które są już dostępne. Ta funkcja zasadniczo tworzy możliwość wybierania bibliotek i tworzenia szablonów dla rynków pionowych. Jest to ważny odróżniający Azure Machine Learning Studio (klasyczny) w miejscu na rynku.  

Mamy nadzieję, że w przyszłości przejdziemy do tego tematu, szczególnie w odniesieniu do analizy danych Big Data.
  

## <a name="conclusion"></a>Podsumowanie
W tym dokumencie opisano rozsądne podejście do działania typowego problemu związanego z zmianą klienta przy użyciu struktury ogólnej. Uważamy prototyp dla modeli oceniania i zaimplementowano go przy użyciu Azure Machine Learning Studio (klasyczne). Na koniec oceniamy dokładność i wydajność rozwiązania prototypowego w odniesieniu do porównywalnych algorytmów SAS.  

 

## <a name="references"></a>Dokumentacja
[1] Analiza predykcyjna: poza przewidywaniami, W. McKnight, zarządzanie informacjami, lipiec/sierpień 2011, p. 18 – 20.  

[2] Artykuł witryny Wikipedia: [dokładność i precyzja](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [wyraziste-DM 1,0: Przewodnik wyszukiwania danych krok po kroku](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing danych Big Data: zwiększaj efektywność klientów i zwiększ wartość](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Szablon modelu zmian odpływ](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) w [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Dodatek
![Migawka prezentacji na prototypie zmian](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Ilustracja 12. migawka prezentacji na prototypie zmian*
