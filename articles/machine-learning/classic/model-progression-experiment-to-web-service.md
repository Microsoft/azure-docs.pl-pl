---
title: 'ML Studio (klasyczny): jak model jest usługą sieci Web — Azure'
description: Przegląd Mechanics, jak model Azure Machine Learning Studio (klasyczny) postępuje z eksperymentu deweloperskiego z usługą sieci Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518627"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Jak model Machine Learning Studio (klasyczny) postępuje z eksperymentu z usługą sieci Web

**dotyczy:** ![ Jest to znacznik wyboru, co oznacza, że ten artykuł ma zastosowanie do Machine Learning Studio (klasyczne). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ to jest X, co oznacza, że ten artykuł ma zastosowanie do Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (klasyczny) zapewnia interaktywną kanwę, która umożliwia tworzenie, uruchamianie, testowanie i Iterowanie ***eksperymentu*** reprezentującego model analizy predykcyjnej. Dostępne są szeroką gamę modułów, które mogą:

* Wprowadzanie danych do eksperymentu
* Manipulowanie danymi
* Uczenie modelu przy użyciu algorytmów uczenia maszynowego
* Ocenianie modelu
* Ocena wyników
* Końcowe wartości wyjściowe

Po zakończeniu eksperymentu możesz wdrożyć go jako ***klasyczną Azure Machine Learning sieci Web** _ lub _ *_Nowa Azure Machine Learning usługa sieci Web_**, aby użytkownicy mogli wysyłać nowe dane i odbierać wyniki.

W tym artykule przedstawiono omówienie Mechanics, jak model Machine Learning postępuje od eksperymentu programistycznego do działającej usługi sieci Web.

> [!NOTE]
> Istnieją inne sposoby tworzenia i wdrażania modeli uczenia maszynowego, ale ten artykuł koncentruje się na sposobach używania Machine Learning Studio (klasycznego). Na przykład aby zapoznać się z opisem sposobu tworzenia klasycznej usługi sieci Web predykcyjnej przy użyciu języka R, zobacz Kompilacja na blogu [& wdróż Web Apps predykcyjny przy użyciu RStudio i Azure Machine Learning Studio](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml).
>
>

Mimo że Azure Machine Learning Studio (klasyczny) jest zaprojektowana w celu ułatwienia projektowania i wdrażania *modelu analizy predykcyjnej*, można użyć programu Studio (klasycznego) do opracowania eksperymentu, który nie zawiera modelu analizy predykcyjnej. Na przykład eksperyment może po prostu wprowadzać dane, manipulować nim, a następnie wyprowadzać wyniki. Podobnie jak w przypadku eksperymentu analizy predykcyjnej, możesz wdrożyć ten eksperyment niepredykcyjny jako usługę sieci Web, ale jest to prostsze proces, ponieważ eksperyment nie jest szkoleniowy ani nie ocenia modelu uczenia maszynowego. Chociaż nie jest to typowy sposób użycia programu Studio (klasyczny), zostanie on uwzględniony w dyskusji, dzięki czemu możemy przekazać nam pełny opis sposobu działania programu Studio (klasycznego).

## <a name="developing-and-deploying-a-predictive-web-service"></a>Opracowywanie i wdrażanie usługi sieci Web predykcyjnej
Poniżej przedstawiono etapy, które zwykle są używane podczas opracowywania i wdrażania przy użyciu Machine Learning Studio (klasyczne):

![Przepływ wdrożenia](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Rysunek 1 — etapy typowego modelu analizy predykcyjnej*

### <a name="the-training-experiment"></a>Eksperyment szkoleniowy
***Eksperyment szkoleniowy*** to początkowa faza tworzenia usługi sieci Web w Machine Learning Studio (klasyczny). Celem eksperymentu szkoleniowego jest stworzenie miejsca do opracowania, testowania, iteracji i ostatecznie uczenia modelu uczenia maszynowego. Możesz nawet przeszkolić wiele modeli jednocześnie, gdy szukasz najlepszego rozwiązania, ale po zakończeniu eksperymentowania wybierzesz jeden szkolony model i usuniesz resztę z eksperymentu. Aby zapoznać się z przykładem opracowywania eksperymentu analizy predykcyjnej, zobacz [opracowywanie rozwiązania do analizy predykcyjnej w celu oceny ryzyka kredytowego w Azure Machine Learning Studio (klasyczny)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Eksperyment predykcyjny
Gdy w doświadczeniu szkoleniowym masz model szkolony, kliknij pozycję **Skonfiguruj usługę sieci Web** i wybierz opcję **predykcyjna usługa internetowa** w Machine Learning Studio (klasyczny), aby zainicjować proces konwertowania eksperymentu szkoleniowego na **_eksperyment predykcyjny_**. Celem eksperymentu predykcyjnego jest użycie przeszkolonego modelu do oceny nowych danych, a celem ostatecznie staje się to usługa sieci Web platformy Azure.

Ta konwersja jest wykonywana przez następujące kroki:

* Przekonwertuj zestaw modułów używanych do szkolenia w jeden moduł i Zapisz go jako przeszkolony model
* Eliminowanie wszelkich obcych modułów niezwiązanych z ocenianiem
* Dodawanie portów wejściowych i wyjściowych, które będą używane przez usługę sieci Web

Może istnieć więcej zmian, które należy wykonać, aby przygotować eksperyment predykcyjny gotowy do wdrożenia jako usługa sieci Web. Jeśli na przykład usługa sieci Web ma wyprowadzić tylko podzestaw wyników, można dodać moduł filtrowania przed portem wyjściowym.

W tym procesie konwersji eksperyment szkoleniowy nie jest odrzucany. Po zakończeniu procesu masz dwie karty w programie Studio (klasyczne): jeden dla eksperymentu szkoleniowego i jeden dla eksperymentu predykcyjnego. W ten sposób możesz wprowadzić zmiany do eksperymentu szkoleniowego przed wdrożeniem usługi sieci Web i ponownie skompilować eksperyment predykcyjny. Możesz też zapisać kopię eksperymentu szkoleniowego, aby rozpocząć inną linię eksperymentowania.

> [!NOTE]
> Gdy klikniesz pozycję **Usługa sieci Web predykcyjna** , rozpocznie się automatyczny proces konwersji eksperymentu szkoleniowego na eksperyment predykcyjny, a to działa dobrze w większości przypadków. Jeśli eksperyment szkoleniowy jest skomplikowany (na przykład masz wiele ścieżek do szkolenia, które są połączone ze sobą), możesz chcieć wykonać tę konwersję ręcznie. Aby uzyskać więcej informacji, zobacz [jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>Usługa sieci Web
Po upewnieniu się, że Twój eksperyment predykcyjny jest gotowy, możesz wdrożyć usługę jako klasyczną usługę sieci Web lub nową usługę sieci Web opartą na Azure Resource Manager. Aby operacjonalizować model przez wdrożenie go jako *klasycznej usługi sieci web Machine Learning*, kliknij przycisk **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [klasyczny]**. Aby wdrożyć program jako *nową usługę sieci web Machine Learning*, kliknij przycisk **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [New]**. Użytkownicy mogą teraz wysyłać dane do modelu przy użyciu interfejsu API REST usługi sieci Web i odbierać wyniki. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Nietypowy przypadek: Tworzenie niepredykcyjnej usługi sieci Web
Jeśli eksperyment nie nauczy modelu analizy predykcyjnej, nie trzeba tworzyć eksperymentu szkoleniowego i eksperymentu oceniania — istnieje tylko jeden eksperyment i można go wdrożyć jako usługę sieci Web. Machine Learning Studio (klasyczny) wykrywa, czy eksperyment zawiera model predykcyjny przez analizowanie użytych modułów.

Po ukończeniu eksperymentu i spełnieniu z nim:

1. Kliknij pozycję **Konfiguruj usługę sieci Web** i wybierz pozycję Przeprowadź ponowne **szkolenie usługa sieci Web** — węzły wejściowe i wyjściowe są dodawane automatycznie
2. Kliknij przycisk **Uruchom**
3. Kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [klasyczny]** lub **Wdróż usługę sieci Web [New]** w zależności od środowiska, w którym chcesz wdrożyć.

Usługa sieci Web jest teraz wdrożona i możesz uzyskać do niej dostęp i zarządzać nią podobnie jak predykcyjna usługa sieci Web.

## <a name="updating-your-web-service"></a>Aktualizowanie usługi sieci Web
Teraz, po wdrożeniu eksperymentu jako usługi sieci Web, co należy zaktualizować?

To zależy od tego, co należy zaktualizować:

**Chcesz zmienić dane wejściowe lub wyjściowe albo chcesz zmodyfikować sposób manipulowania danymi przez usługę sieci Web**

Jeśli nie zmieniasz modelu, ale tylko zmienisz sposób obsługi danych przez usługę sieci Web, możesz edytować eksperyment predykcyjny, a następnie kliknąć pozycję **Wdróż usługę sieci** Web i wybrać pozycję **Wdróż usługę sieci Web [klasyczny]** lub ponownie **Wdróż usługę sieci Web [New]** . Usługa sieci Web zostanie zatrzymana, zostanie wdrożony zaktualizowany eksperyment predykcyjny i usługa sieci Web zostanie uruchomiona ponownie.

Oto przykład: Załóżmy, że eksperyment predykcyjny zwraca cały wiersz danych wejściowych z przewidywanym wynikiem. Możesz zdecydować, że usługa sieci Web ma jedynie zwrócić wynik. Dzięki temu można dodać moduł **kolumny projektu** w eksperymentie predykcyjnym bezpośrednio przed portem wyjściowym, aby wykluczyć kolumny inne niż wynik. Po kliknięciu przycisku **Wdróż usługę sieci Web** i wybraniu opcji **Wdróż usługę sieci Web [klasyczny]** lub ponownie **Wdróż usługę sieci Web [New]** usługa sieci Web zostanie zaktualizowana.

**Chcesz ponownie przeprowadzić uczenie modelu z nowymi danymi**

Jeśli chcesz zachować model uczenia maszynowego, ale chcesz go ponownie przeszkolić przy użyciu nowych danych, będziesz mieć dwie możliwości:

1. **Przeszkol model w trakcie działania usługi sieci Web** — Jeśli chcesz ponownie przeprowadzić uczenie modelu w trakcie działania usługi sieci Web predykcyjnej, możesz to zrobić, wprowadzając kilka modyfikacji eksperymentu szkoleniowego, aby przeprowadzić eksperyment szkoleniowy ***_, a następnie wdrożyć go jako _* usługę _sieci Web_**. Aby uzyskać instrukcje, jak to zrobić, zobacz temat ponowne [uczenie Machine Learning modeli](./retrain-machine-learning-model.md).
2. Wróć **do oryginalnego eksperymentu szkoleniowego i Użyj różnych danych szkoleniowych do opracowania modelu** — eksperyment predykcyjny jest połączony z usługą sieci Web, ale eksperyment szkoleniowy nie jest bezpośrednio połączony w ten sposób. Jeśli zmodyfikujesz oryginalny eksperyment szkoleniowy i klikniesz pozycję **Konfiguruj usługę sieci Web**, zostanie utworzony *Nowy*     eksperyment predykcyjny, który po wdrożeniu spowoduje utworzenie *nowej* usługi sieci Web. Nie tylko aktualizuje oryginalną usługę sieci Web.

   Jeśli musisz zmodyfikować eksperyment szkoleniowy, otwórz go, a następnie kliknij pozycję **Zapisz jako** , aby utworzyć kopię. Spowoduje to pozostawienie oryginalnego eksperymentu szkoleniowego, eksperymentu predykcyjnego i usługi sieci Web. Teraz można utworzyć nową usługę sieci Web ze zmianami. Po wdrożeniu nowej usługi sieci Web możesz zdecydować, czy zatrzymać poprzednią usługę sieci Web, czy będzie ona działać obok nowej.

**Chcesz nauczyć inny model**

Jeśli chcesz wprowadzić zmiany w oryginalnym eksperymentie predykcyjnym, na przykład wybranie innego algorytmu uczenia maszynowego, podjęcie innej metody szkoleniowej itp., należy wykonać poniższą procedurę w celu przeprowadzenia ponownego szkolenia modelu: Otwórz eksperyment szkoleniowy, kliknij pozycję **Zapisz jako** , aby utworzyć kopię, a następnie uruchom nową ścieżkę tworzenia modelu, tworząc eksperyment predykcyjny i wdrażając usługę sieci Web. Spowoduje to utworzenie nowej usługi sieci Web, która nie jest powiązana z oryginalną. możesz zdecydować, która z nich ma być uruchomiona.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat procesu opracowywania i eksperymentowania, zobacz następujące artykuły:

* Konwertowanie eksperymentu — [jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)](deploy-a-machine-learning-web-service.md)
* Wdrażanie usługi sieci Web — [wdrażanie usługi sieci web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* ponowne uczenie modelu — ponownie [poszkol Machine Learning modele](./retrain-machine-learning-model.md)

Przykłady całego procesu znajdują się w temacie:

* [Samouczek uczenia maszynowego: Tworzenie pierwszego eksperymentu w Azure Machine Learning Studio (klasyczny)](create-experiment.md)
* [Przewodnik: opracowywanie rozwiązania do analizy predykcyjnej w celu oceny ryzyka kredytowego w Azure Machine Learning](tutorial-part1-credit-risk.md)