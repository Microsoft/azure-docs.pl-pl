---
title: 'Samouczek: przeciąganie i upuszczanie w celu utworzenia modelu predykcyjnego (część 1 z 2)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skompilować i wdrożyć model predykcyjny uczenia maszynowego przy użyciu narzędzia Projektant. Później możesz użyć jej do przewidywania wyników w programie Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814792"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Samouczek: Power BI Integration — przeciąganie i upuszczanie w celu utworzenia modelu predykcyjnego (część 1 z 2)

W części 1 tego samouczka nauczysz się i wdrożyć model uczenia maszynowego przy użyciu programu Azure Machine Learning Designer. Projektant jest interfejsem użytkownika typu "przeciągnij i upuść". W części 2 będziesz używać modelu do przewidywania wyników w programie Microsoft Power BI.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz wystąpienie obliczeniowe Azure Machine Learning.
> * Utwórz Azure Machine Learning klaster wnioskowania.
> * Utwórz zestaw danych.
> * Uczenie modelu regresji.
> * Wdróż model w punkcie końcowym oceniania w czasie rzeczywistym.


Istnieją trzy sposoby tworzenia i wdrażania modelu, który będzie używany w Power BI.  W tym artykule opisano "Opcja B: uczenie i wdrażanie modeli przy użyciu narzędzia Projektant".  Ta opcja jest środowiskoem autorskim o małym kodzie, które korzysta z interfejsu projektanta.  

Zamiast tego można użyć jednej z innych opcji:

* [Opcja A: uczenie i wdrażanie modeli przy użyciu notesów Jupyter](tutorial-power-bi-custom-model.md). W tym środowisku tworzenia kodu używane są notesy Jupyter hostowane w Azure Machine Learning Studio.
* [Opcja C: uczenie i wdrażanie modeli przy użyciu funkcji automatycznego uczenia maszynowego](tutorial-power-bi-automated-model.md). To środowisko tworzenia kodu nie pełni całkowicie automatyzuje przygotowanie danych i szkolenia modeli.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, możesz skorzystać z [bezpłatnej wersji próbnej](https://aka.ms/AMLFree). 
- Obszar roboczy usługi Azure Machine Learning. Jeśli nie masz jeszcze obszaru roboczego, zapoznaj się z tematem [tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi](./how-to-manage-workspace.md#create-a-workspace).
- Wprowadzana wiedza o przepływach pracy uczenia maszynowego.


## <a name="create-compute-to-train-and-score"></a>Tworzenie obliczeń do uczenia i oceny

W tej sekcji utworzysz *wystąpienie obliczeniowe*. Wystąpienia obliczeniowe są używane do uczenia modeli uczenia maszynowego. Można również utworzyć *klaster wnioskowania* , który będzie hostować wdrożony model na potrzeby oceniania w czasie rzeczywistym.

Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com). W menu po lewej stronie wybierz pozycję **obliczenia** , a następnie **nowe**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia wystąpienia obliczeniowego.":::

Na stronie **Tworzenie wystąpienia obliczeniowego** wybierz rozmiar maszyny wirtualnej. Na potrzeby tego samouczka wybierz maszynę wirtualną **Standard_D11_v2** . Następnie wybierz przycisk **Dalej**. 

Na stronie **Ustawienia** Nazwij wystąpienie obliczeniowe. Następnie wybierz przycisk **Utwórz**. 

>[!TIP]
> Można również użyć wystąpienia obliczeniowego do tworzenia i uruchamiania notesów.

**Stan** wystąpienia obliczeniowego to teraz **Tworzenie**. Udostępnienie maszyny zajmuje około 4 minuty. 

Podczas oczekiwania na stronie **Obliczanie** wybierz kartę **klastry wnioskowania** . Następnie wybierz pozycję **Nowy**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia klastra wnioskowania.":::

Na stronie **Tworzenie klastra wnioskowania** wybierz region i rozmiar maszyny wirtualnej. Na potrzeby tego samouczka wybierz maszynę wirtualną **Standard_D11_v2** . Następnie wybierz przycisk **Dalej**. 

Na stronie **Konfigurowanie ustawień** :

1. Podaj prawidłową nazwę obliczeniową.
1. Wybierz pozycję **Dev-Test** jako cel klastra. Ta opcja powoduje utworzenie jednego węzła do hostowania wdrożonego modelu.
1. Wybierz przycisk **Utwórz**.

**Stan** klastra wnioskowania jest teraz **tworzony**. Wdrożenie klastra z jednym węzłem zajmuje około 4 minuty.

## <a name="create-a-dataset"></a>Utwórz zestaw danych

W tym samouczku użyjesz [zestawu danych cukrzycą](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Ten zestaw danych jest dostępny w [usłudze Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/).

Aby utworzyć zestaw danych, w menu po lewej stronie wybierz pozycję **zestawy** danych. Następnie wybierz pozycję **Utwórz zestaw danych**. Zobaczysz następujące opcje:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego zestawu danych.":::

Wybierz pozycję **z otwartych zestawów danych**. Na stronie **Tworzenie zestawu danych na podstawie otwartych zestawów DataSets** :

1. Użyj paska wyszukiwania, aby znaleźć *cukrzycą*.
1. Wybierz **przykład: cukrzycą**.
1. Wybierz pozycję **Dalej**.
1. Nazwij zestaw danych *cukrzycą*.
1. Wybierz przycisk **Utwórz**.

Aby eksplorować dane, wybierz zestaw danych, a następnie wybierz polecenie **Eksploruj**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób eksplorowania zestawu danych.":::

Dane zawierają 10 bazowych zmiennych wejściowych, takich jak wiek, płeć, indeks masy ciała, średnie ciśnienie krwi oraz sześć pomiarów surowicy krwi. Ma także jedną zmienną docelową o nazwie **Y**. Ta zmienna docelowa jest miarą ilościową cukrzycą w jednym roku po linii bazowej.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Tworzenie modelu uczenia maszynowego przy użyciu narzędzia Projektant

Po utworzeniu zestawów obliczeń i danych można użyć projektanta do utworzenia modelu uczenia maszynowego. W Azure Machine Learning Studio wybierz opcję **Projektant** , a następnie **Nowy potok**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego potoku.":::

Zostanie wyświetlona pusta *Kanwa* i menu **ustawień** :

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania elementu docelowego obliczeń.":::

W menu **Ustawienia** wybierz **pozycję Wybierz element docelowy obliczeń**. Wybierz utworzone wcześniej wystąpienie obliczeniowe, a następnie wybierz pozycję **Zapisz**. Zmień **nazwę roboczą** na coś bardziej zapamiętania, na przykład *cukrzycą-model*. Na koniec wprowadź opis.

W obszarze Lista zasobów rozwiń węzeł **zestawy** danych, a następnie Znajdź **cukrzycą** . Przeciągnij ten składnik na kanwę:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Zrzut ekranu przedstawiający sposób przeciągania składnika na kanwę.":::

Następnie przeciągnij następujące składniki na kanwę:

1. **Regresja liniowa** (znajdująca się w **algorytmach Machine Learning**)
1. **Uczenie modelu** (znajdujące się w **szkole modelu**)

Na kanwie Zwróć uwagę na kółka u góry i u dołu składników. Są to porty.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Zrzut ekranu przedstawiający porty dla niepołączonych składników.":::
 
Teraz *Współpracuj* ze sobą składniki. Wybierz port w dolnej części zestawu danych **cukrzycą** . Przeciągnij go na port w prawym górnym rogu składnika **modelu uczenia** . Wybierz port u dołu składnika **regresji liniowej** . Przeciągnij go na port w lewym górnym rogu składnika **modelu uczenia** .

Wybierz kolumnę DataSet, która ma być używana jako zmienna etykieta (docelowa) do przewidywania. Wybierz składnik **model uczenia** , a następnie wybierz pozycję **Edytuj kolumnę**. 

W oknie dialogowym wybierz pozycję **Wprowadź nazwę kolumny**  >  **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania kolumny etykieta.":::

Wybierz pozycję **Zapisz**. *Przepływ pracy* uczenia maszynowego powinien wyglądać następująco:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Zrzut ekranu przedstawiający składniki połączone.":::

Wybierz pozycję **Prześlij**. W obszarze **eksperymenty** wybierz pozycję **Utwórz nowy**. Nazwij eksperyment, a następnie wybierz pozycję **Prześlij**.

>[!NOTE]
> Pierwsze uruchomienie eksperymentu powinno trwać około 5 minut. Kolejne uruchomienia są znacznie szybsze, ponieważ Projektant buforuje składniki, które zostały uruchomione w celu zmniejszenia opóźnień.

Po zakończeniu eksperymentu zobaczysz następujący widok:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Zrzut ekranu przedstawiający ukończony przebieg.":::

Aby sprawdzić dzienniki eksperymentu, wybierz pozycję **Testuj model** , a następnie wybierz pozycję dane **wyjściowe + dzienniki**.

## <a name="deploy-the-model"></a>Wdrażanie modelu

Aby wdrożyć model w górnej części kanwy, wybierz pozycję **Utwórz**  >  **potok wnioskowania w czasie rzeczywistym**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Zrzut ekranu przedstawiający, gdzie należy wybrać potok wnioskowania w czasie rzeczywistym.":::
 
Potok jest zagęszczony tylko do składników niezbędnych do oceny modelu. Podczas oceny danych nie będą znane wartości zmiennej docelowej. Aby można było usunąć **Y** z zestawu danych. 

Aby usunąć **Y**, Dodaj do kanwy składnik **Wybieranie kolumn w zestawie danych** . Obprzewoduj składnik, tak aby zestaw danych cukrzycą był danymi wejściowymi. Wyniki są danymi wyjściowymi do składnika **modelu oceny** :

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania kolumny.":::

Na kanwie wybierz składnik **Wybieranie kolumn w zestawie danych** , a następnie wybierz pozycję **Edytuj kolumny**. 

W oknie dialogowym **Wybieranie kolumn** wybierz pozycję **według nazwy**. Następnie upewnij się, że wszystkie zmienne wejściowe są zaznaczone, ale *nie* wybrano elementu docelowego:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania ustawień kolumny.":::

Wybierz pozycję **Zapisz**. 

Na koniec wybierz składnik **modelu oceny** i upewnij się, że pole wyboru **Dołącz kolumny oceny do danych wyjściowych** jest wyczyszczone. Aby zmniejszyć opóźnienie, prognozy są wysyłane z powrotem bez danych wejściowych.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Zrzut ekranu przedstawiający ustawienia składnika modelu oceny.":::

W górnej części kanwy wybierz pozycję **Prześlij**.

Po pomyślnym uruchomieniu potoku wnioskowania można wdrożyć model w klastrze wnioskowania. Wybierz pozycję **Wdróż**. 

W oknie dialogowym **konfigurowanie punktu końcowego w czasie rzeczywistym** wybierz pozycję **wdróż nowy punkt końcowy** w czasie rzeczywistym. Nazwij punkt końcowy *My-cukrzycą-model*. Wybierz utworzoną wcześniej wnioskowanie, a następnie wybierz pozycję **Wdróż**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Zrzut ekranu przedstawiający ustawienia punktu końcowego w czasie rzeczywistym.":::
## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób uczenia i wdrożenia modelu projektanta. W następnej części dowiesz się, jak używać tego modelu (SCORE) w Power BI.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z modelu w Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
