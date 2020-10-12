---
title: 'Samouczek: Wdrażanie modeli ML przy użyciu narzędzia Projektant'
titleSuffix: Azure Machine Learning
description: W tym samouczku przedstawiono sposób tworzenia rozwiązania do analizy predykcyjnej w programie Azure Machine Learning Designer. Uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu modułów przeciągania i upuszczania.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: a4923e48c890a50d642d937f014e466e998171cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896635"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą narzędzia Projektant


Można wdrożyć model predykcyjny opracowany w pierwszej części [samouczka](tutorial-designer-automobile-price-train-score.md) , aby dać innym osobom szansę korzystania z niego. W części pierwszej został przeszkolony model. Teraz można generować nowe prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz potok wnioskowania w czasie rzeczywistym.
> * Utwórz klaster inferencing.
> * Wdróż punkt końcowy w czasie rzeczywistym.
> * Przetestuj punkt końcowy w czasie rzeczywistym.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj [jedną z części samouczka](tutorial-designer-automobile-price-train-score.md) , aby dowiedzieć się, jak szkolić i oceny model uczenia maszynowego w projektancie.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

Aby wdrożyć potok, należy najpierw skonwertować potok szkoleniowy do potoku w czasie rzeczywistym. Ten proces powoduje usunięcie modułów szkoleniowych i dodanie danych wejściowych i wyjściowych usługi sieci Web do obsługi żądań.

### <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

1. Nad kanwą potoku wybierz pozycję **Utwórz**  >  **potok wnioskowania w czasie rzeczywistym**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Zrzut ekranu przedstawiający miejsce znalezienia przycisku Utwórz potok":::

    Potok powinien teraz wyglądać następująco: 

   ![Zrzut ekranu przedstawiający oczekiwaną konfigurację potoku po przygotowaniu go do wdrożenia](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Po wybraniu opcji **Utwórz potok wnioskowania**kilka rzeczy zostanie wykonanych:
    
    * Szkolony model jest przechowywany jako moduł **DataSet** w palecie modułów. Można go znaleźć w obszarze **Moje zestawy danych**.
    * Moduły szkoleniowe, takie jak **model uczenia** i **dane podzielone** , są usuwane.
    * Zapisany model przeszkolony zostanie dodany z powrotem do potoku.
    * Dodawane są moduły danych **wejściowych** i **usług** sieci Web. Te moduły pokazują, gdzie dane użytkownika są wprowadzane do potoku i gdzie są zwracane dane.

    > [!NOTE]
    > Domyślnie dane **wejściowe usługi sieci Web** będą oczekiwać tego samego schematu danych co dane szkoleniowe, które są używane do tworzenia potoku predykcyjnego. W tym scenariuszu cena jest uwzględniona w schemacie. Cena nie jest jednak używana jako współczynnik podczas przewidywania.
    >

1. Wybierz pozycję **Prześlij**, a następnie użyj tego samego elementu docelowego obliczeń i eksperymentu, który został użyty w części pierwszej.

    Jeśli jest to pierwsze uruchomienie, ukończenie potoku może potrwać do 20 minut. Domyślne ustawienia obliczeń mają minimalny rozmiar węzła równy 0, co oznacza, że projektant musi przydzielić zasoby po stanie bezczynności. Powtarzające się uruchomienia potoku będą trwać krócej od czasu przydziału zasobów obliczeniowych. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby zwiększyć wydajność.

1. Wybierz pozycję **Deploy** (Wdróż).

## <a name="create-an-inferencing-cluster"></a>Tworzenie klastra inferencing

W wyświetlonym oknie dialogowym możesz wybrać dowolny z istniejących klastrów usługi Azure Kubernetes Service (AKS), aby wdrożyć model. Jeśli nie masz klastra AKS, wykonaj następujące kroki, aby go utworzyć.

1. Wybierz pozycję **obliczenia** w wyświetlonym oknie dialogowym, aby przejść do strony **obliczenia** .

1. Na Wstążce Nawigacja wybierz pozycję **klastry wnioskowania**  >  **+ Nowy**.

    ![Zrzut ekranu przedstawiający sposób uzyskiwania do okienka nowy klaster wnioskowania](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. W okienku klaster wnioskowania Skonfiguruj nową usługę Kubernetes.

1. Wprowadź *AKS-COMPUTE* dla **nazwy obliczeniowej**.
    
1. Wybierz region znajdujący się w pobliżu, który jest dostępny dla **regionu**.

1. Wybierz przycisk **Utwórz**.

    > [!NOTE]
    > Utworzenie nowej usługi AKS trwa około 15 minut. Stan aprowizacji można sprawdzić na stronie **klastry wnioskowania** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

Po zakończeniu aprowizacji usługi AKS Wróć do potoku inferencing w czasie rzeczywistym, aby zakończyć wdrażanie.

1. Wybierz pozycję **Wdróż** powyżej kanwy.

1. Wybierz pozycję **wdróż nowy punkt końcowy**w czasie rzeczywistym. 

1. Wybierz utworzony klaster AKS.

1. Wybierz pozycję **Deploy** (Wdróż).
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Zrzut ekranu przedstawiający miejsce znalezienia przycisku Utwórz potok":::

    Powiadomienie o powodzeniu powyżej kanwy pojawia się po zakończeniu wdrażania. Może to potrwać kilka minut.

## <a name="test-the-real-time-endpoint"></a>Testowanie punktu końcowego w czasie rzeczywistym

Po zakończeniu wdrażania można testować punkt końcowy w czasie rzeczywistym, przechodząc do strony **punkty końcowe** .

1. Na stronie **punkty końcowe** Wybierz wdrożony punkt końcowy.

    ![Zrzut ekranu przedstawiający kartę punkty końcowe czasu rzeczywistego z wyróżnionym ostatnio utworzonym punktem końcowym](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Kliknij przycisk **Testuj**.

1. Możesz ręcznie wprowadzić dane testowe lub użyć autowypełnianych danych przykładowych, a następnie wybrać opcję **Testuj**.

    Portal przesyła żądanie testowe do punktu końcowego i wyświetla wyniki. Mimo że dla danych wejściowych jest generowana wartość ceny, nie jest ona używana do generowania wartości przewidywania.

    ![Zrzut ekranu przedstawiający sposób testowania punktu końcowego w czasie rzeczywistym z etykietą z oceną dla wyróżnionej ceny](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono najważniejsze kroki w temacie Tworzenie, wdrażanie i Używanie modelu uczenia maszynowego w projektancie. Aby dowiedzieć się więcej o sposobach rozwiązywania innych rodzajów problemów za pomocą projektanta, zobacz nasze inne przykładowe potoki.

> [!div class="nextstepaction"]
> [Przykłady projektanta](samples-designer.md)
