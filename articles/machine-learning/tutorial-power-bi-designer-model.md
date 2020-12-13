---
title: 'Samouczek: przeciąganie i upuszczanie w celu utworzenia modelu predykcyjnego (część 1 z 2)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak kompilować i wdrażać model predykcyjny uczenia maszynowego za pomocą projektanta, dzięki czemu można go używać do przewidywania wyników w programie Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370232"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Samouczek: Power BI integrację — przeciągnij i upuść, aby utworzyć model predykcyjny (część 1 z 2)

W pierwszej części tego samouczka nauczysz się i wdrożyć model uczenia maszynowego przy użyciu narzędzia Azure Machine Learning Designer — interfejs użytkownika typu "przeciągnij i upuść" z niską ilością kodu. W części 2 zostanie następnie użyty model do przewidywania wyników w programie Microsoft Power BI.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie wystąpienia obliczeniowego Azure Machine Learning
> * Tworzenie klastra wnioskowania Azure Machine Learning
> * Utwórz zestaw danych
> * Trenowanie modelu regresji
> * Wdrażanie modelu w punkcie końcowym oceniania w czasie rzeczywistym


Istnieją trzy różne sposoby tworzenia i wdrażania modelu, który będzie używany w Power BI.  W tym artykule opisano opcję B: uczenie i wdrażanie modeli przy użyciu narzędzia Projektant.  Ta opcja pokazuje środowisko tworzenia w małym kodzie przy użyciu narzędzia Projektant (interfejs użytkownika typu "przeciągnij i upuść").  

Zamiast tego można użyć:

* [Opcja A: uczenie i wdrażanie modeli przy użyciu notesów](tutorial-power-bi-custom-model.md) — środowisko tworzenia po raz pierwszy przy użyciu notesów Jupyter hostowanych w programie Azure Machine Learning Studio.
* [Opcja C: uczenie i wdrażanie modeli przy użyciu zautomatyzowanej](tutorial-power-bi-automated-model.md) sieci — środowisko tworzenia bez kodu, które w pełni automatyzuje przygotowanie danych i szkolenia modeli.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure ([dostępna jest bezpłatna wersja próbna](https://aka.ms/AMLFree)). 
- Obszar roboczy usługi Azure Machine Learning. Jeśli jeszcze nie masz obszaru roboczego, postępuj zgodnie z instrukcjami [tworzenia obszar roboczy usługi Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Wprowadzana wiedza o przepływach pracy uczenia maszynowego.


## <a name="create-compute-for-training-and-scoring"></a>Tworzenie obliczeń na potrzeby szkoleń i oceniania

W tej sekcji utworzysz *wystąpienie obliczeniowe* służące do uczenia modeli uczenia maszynowego. Ponadto należy utworzyć *klaster wnioskowania* , który będzie używany do hostowania wdrożonego modelu na potrzeby oceniania w czasie rzeczywistym.

Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com) i wybierz pozycję **obliczenia** z menu po lewej stronie, a następnie **Nowy**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia wystąpienia obliczeniowego":::

Na powstającym ekranie **Tworzenie wystąpienia obliczeniowego** wybierz rozmiar maszyny wirtualnej (w tym samouczku wybierz pozycję a), a następnie przycisk `Standard_D11_v2` **dalej**. Na stronie Ustawienia podaj prawidłową nazwę wystąpienia obliczeniowego, a następnie wybierz pozycję **Utwórz**. 

>[!TIP]
> Wystąpienia obliczeniowego można także użyć do tworzenia i wykonywania notesów.

Teraz można zobaczyć, że **stan** wystąpienia obliczeniowego to **Tworzenie** — zajmie to około 4 minut na zainicjowanie obsługi maszyny. Gdy oczekujesz, wybierz kartę **klaster wnioskowania** na stronie obliczenia, a następnie pozycję **nowe**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia klastra wnioskowania":::

Na stronie **Tworzenie klastra wnioskowania** wybierz region, po którym następuje rozmiar maszyny wirtualnej (w tym samouczku wybierz pozycję a `Standard_D11_v2` ), a następnie wybierz pozycję **dalej**. Na stronie **Konfigurowanie ustawień** :

1. Podaj prawidłową nazwę obliczeniową
1. Wybierz opcję **Dev-Test** jako cel klastra (tworzy pojedynczy węzeł do hostowania wdrożonego modelu)
1. Wybierz pozycję **Utwórz**

Teraz możesz zobaczyć, że **stan** klastra wnioskowania to **Tworzenie** — zajmie to około 4 minut na wdrożenie klastra z jednym węzłem.

## <a name="create-a-dataset"></a>Utwórz zestaw danych

W tym samouczku użyjesz [zestawu danych cukrzycą](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), który jest dostępny na [platformie Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/).

Aby utworzyć zestaw danych, w menu po lewej stronie wybierz opcję **zestawy** danych, a następnie **Utwórz zestaw danych** — zostaną wyświetlone następujące opcje:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego zestawu danych":::

Wybierz pozycję **z otwartych zestawów** danych, a następnie w oknie **Tworzenie DataSet z otwartej grupy zestawów** :

1. Wyszukaj *cukrzycą* przy użyciu paska wyszukiwania
1. Wybierz **przykład: cukrzycą**
1. Wybierz pozycję **Dalej**
1. Podaj nazwę zestawu danych — *cukrzycą*
1. Wybierz pozycję **Utwórz**

Możesz eksplorować dane, wybierając zestaw danych, a następnie **Eksploruj**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób eksplorowania zestawu danych":::

Dane zawierają 10 zmiennych wejściowych linii bazowej (takich jak wiek, płeć, indeks masy ciała, średnie ciśnienie krwi oraz sześć pomiarów surowicy krwi) i jedna zmienna docelowa o nazwie **Y** (miara ilościowa cukrzycą z postępem roku po linii bazowej).

## <a name="create-a-machine-learning-model-using-designer"></a>Tworzenie modelu Machine Learning przy użyciu narzędzia Projektant

Po utworzeniu zestawów obliczeń i danych można przejść do tworzenia modelu uczenia maszynowego za pomocą projektanta. W Azure Machine Learning Studio wybierz opcję **Projektant** , a po niej **Nowy potok**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego potoku":::

Zobaczysz pustą *kanwę* , w której można także wyświetlić **menu Ustawienia**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania elementu docelowego obliczeń":::

W **menu Ustawienia** **Wybierz pozycję cel obliczeń** , a następnie wybierz utworzone wcześniej wystąpienie obliczeniowe, a następnie pozycję **Zapisz**. Zmień **nazwę swojej wersji roboczej** na bardziej zapamiętaną (na przykład *cukrzycą-model*) i wprowadź opis.

Następnie w obszarze wymienione zasoby rozwiń węzeł **zestawy** danych i odszukaj **cukrzycą** DataSet-przeciągnij i upuść ten moduł na kanwie:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Zrzut ekranu przedstawiający sposób przeciągnięcia składnika na":::

Następnie przeciągnij i upuść następujące składniki na kanwie:

1. Regresja liniowa (znajdująca się w **algorytmach Machine Learning**)
1. Uczenie modelu (znajdujące się w **szkole modelu**)

Kanwa powinna wyglądać jak (Zauważ, że górne i dolne składniki mają małe koła nazywane portami wyróżnionymi poniżej):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Zrzut ekranu przedstawiający sposób niepołączonych składników":::
 
Następnie należy *połączyć te składniki ze sobą* . Wybierz port u dołu zestawu danych **cukrzycą** i przeciągnij go na port z prawej strony w górnej części składnika **modelu uczenia** . Wybierz port u dołu składnika **regresji liniowej** i przeciągnij na port po lewej stronie na górze portu **modelu uczenia** .

Wybierz kolumnę w zestawie danych, która ma być używana jako zmienna etykieta (docelowa) do przewidywania. Wybierz składnik **modelu uczenia** , a następnie pozycję **Edytuj kolumnę**. W oknie dialogowym — zaznacz pole wyboru **Wprowadź nazwę kolumny** , a następnie **Y** na liście rozwijanej:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Zrzut ekranu — wybór kolumny etykieta":::

Wybierz pozycję **Zapisz**. *Przepływ pracy* uczenia maszynowego powinien wyglądać następująco:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Zrzut ekranu przedstawiający sposób działania połączonych składników":::

Wybierz pozycję **Prześlij** , a następnie **Utwórz nowy** w obszarze eksperyment. Podaj nazwę eksperymentu, po którym następuje **przesłanie**.

>[!NOTE]
> Ukończenie eksperymentu podczas pierwszego uruchomienia powinno zająć około 5 minut. Kolejne uruchomienia są znacznie szybszymi pamięciami podręcznymi projektanta, które już uruchamiają składniki, aby zmniejszyć opóźnienie.

Po zakończeniu eksperymentu zobaczysz:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Zrzut ekranu przedstawiający ukończony przebieg":::

Dzienniki eksperymentu można sprawdzić, wybierając **kolejno pozycje uczenie model** , a następnie **wyjściowe + dzienniki**.

## <a name="deploy-the-model"></a>Wdrażanie modelu

Aby wdrożyć model, wybierz pozycję **Utwórz potok wnioskowania** (znajdujący się u góry kanwy), a następnie **potok wnioskowania** o czasie rzeczywistym:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Zrzut ekranu przedstawiający potok wnioskowania w czasie rzeczywistym":::
 
Potok jest zagęszczony tylko do składników niezbędnych do przeprowadzenia oceny modelu. Podczas oceny danych nie będą znane wartości zmiennej docelowej, dlatego można usunąć **Y** z zestawu danych. Aby usunąć, Dodaj do kanwy a **Wybierz kolumny w** składniku DataSet. Aby obsłużyć ten składnik, cukrzycą zestaw danych wejściowych, a wyniki są danymi wyjściowymi składnika **modelu oceny** :

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Zrzut ekranu przedstawiający usuwanie kolumny":::

Wybierz pozycję **Wybierz kolumny w zestawie danych** na kanwie, a następnie pozycję **Edytuj kolumny**. W oknie dialogowym Wybieranie kolumn wybierz pozycję **według nazwy** , a następnie upewnij się, że wszystkie zmienne wejściowe są wybrane, ale **nie** do elementu docelowego:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Zrzut ekranu przedstawiający usuwanie ustawień kolumny":::

Wybierz pozycję **Zapisz**. Na koniec wybierz składnik **modelu oceny** i upewnij się, że pole wyboru **Dołącz kolumny oceny do danych wyjściowych** nie jest zaznaczone (tylko prognozy są wysyłane z powrotem, a nie dane wejściowe *i* Przewidywania, skracając czas opóźnienia):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Zrzut ekranu przedstawiający ustawienia składnika modelu oceny":::

Wybierz pozycję **Prześlij** w górnej części kanwy.

Po pomyślnym uruchomieniu potoku wnioskowania można wdrożyć model w klastrze wnioskowania. Wybierz pozycję **Wdróż**, co spowoduje wyświetlenie okna dialogowego **konfigurowanie punktu końcowego w czasie rzeczywistym** . Wybierz pozycję **wdróż nowy punkt końcowy** w czasie rzeczywistym, nazwij punkt końcowy **My-cukrzycą-model**, wybierz wcześniej utworzone wnioskowanie, a następnie wybierz pozycję **Wdróż**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Zrzut ekranu przedstawiający ustawienia punktu końcowego czasu rzeczywistego":::
## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób uczenia i wdrożenia modelu projektanta. W następnej części dowiesz się, jak używać tego modelu (wyniku) z Power BI.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z modelu w Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
