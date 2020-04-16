---
title: Szybki start Zbuduj detektor obiektów - Usługa Niestandardowej Wizji
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć model klasyfikacji obrazu za pomocą witryny sieci Web usługi Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ba121400368f37c4a562a9c34e209c59d15b173c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404107"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Szybki start: jak zbudować detektor obiektów z wizją niestandardową

W tym przewodniku Szybki start dowiesz się, jak zbudować detektor obiektów za pośrednictwem witryny internetowej Custom Vision. Po utworzeniu modelu detektora można użyć usługi Custom Vision do wykrywania obiektów.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw obrazów, za pomocą których można trenować model detektora. Można użyć zestawu [przykładowych obrazów](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) w usłudze GitHub. Możesz też wybrać własne obrazy, korzystając z poniższych wskazówek.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Tworzenie zasobów usługi Custom Vision w witrynie Azure portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przeglądarce internetowej przejdź do [strony internetowej Custom Vision](https://customvision.ai) i wybierz pozycję Zaloguj __się__. Zaloguj się przy użyciu tego samego konta, którego użyto do zalogowania się do witryny Azure portal.

![Obraz strony logowania](./media/browser-home.png)


1. Aby utworzyć pierwszy projekt, wybierz pozycję **Nowy projekt**. Zostanie wyświetlone okno dialogowe **Tworzenie nowego projektu.**

    ![Nowe okno dialogowe projektu ma pola dotyczące nazwy, opisu i domen.](./media/get-started-build-detector/new-project.png)

1. Wprowadź nazwę i opis projektu. Następnie wybierz grupę zasobów. Jeśli twoje konto zalogowane jest skojarzone z kontem platformy Azure, na rozwijaniu grupy zasobów zostaną wyświetlone wszystkie grupy zasobów platformy Azure, które zawierają zasób usługi niestandardowej wizji. 

   > [!NOTE]
   > Jeśli żadna grupa zasobów nie jest dostępna, upewnij się, że zalogowano się do [customvision.ai](https://customvision.ai) przy użyciu tego samego konta, które było używane do logowania się do [witryny Azure portal](https://portal.azure.com/). Ponadto upewnij się, że wybrano ten sam "Katalog" w portalu usługi Custom Vision jako katalog w witrynie Azure portal, w którym znajdują się zasoby usługi Custom Vision. W obu witrynach możesz wybrać katalog z menu rozwijanego konta w prawym górnym rogu ekranu. 

1. Wybierz __opcję Wykrywanie obiektów__ w obszarze __Typy projektów__.

1. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje detektor dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli. Jeśli chcesz, możesz później zmienić domenę.

    |Domain|Przeznaczenie|
    |---|---|
    |__Ogólne__| Zoptymalizowany pod kątem szerokiego zakresu zadań wykrywania obiektów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę rodzajową. |
    |__Logo__|Zoptymalizowany pod kątem znajdowania logo marki na obrazach.|
    |__Domeny kompaktowe__| Zoptymalizowany pod kątem ograniczeń wykrywania obiektów w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez domeny kompaktowe można eksportować do uruchamiania lokalnie.|

1. Na koniec wybierz pozycję __Utwórz projekt__.

## <a name="choose-training-images"></a>Wybieranie obrazów szkoleniowych

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

W tej sekcji można przesłać i ręcznie oznaczyć obrazy, aby pomóc w szkoleniu detektora. 

1. Aby dodać obrazy, kliknij przycisk __Dodaj obrazy,__ a następnie wybierz pozycję __Przeglądaj pliki lokalne__. Wybierz __otwórz,__ aby przesłać obrazy.

    ![Formant dodaj obrazy jest wyświetlany w lewym górnym rogu i jako przycisk na dole.](./media/get-started-build-detector/add-images.png)

1. Przesłane obrazy będą widoczne w sekcji **Bez znaczników** interfejsu użytkownika. Następnym krokiem jest ręczne oznaczanie obiektów, które mają być rozpoznawane przez detektor. Kliknij pierwszy obraz, aby otworzyć okno dialogowe tagowania. 

    ![Przesłane obrazy w sekcji Bez znaczników](./media/get-started-build-detector/images-untagged.png)

1. Kliknij i przeciągnij prostokąt wokół obiektu na obrazie. Następnie wprowadź nową nazwę tagu za pomocą przycisku **+** lub wybierz istniejący tag z listy rozwijanej. Bardzo ważne jest, aby oznaczyć każde wystąpienie obiektów, które chcesz wykryć, ponieważ detektor używa nieoznakowanego obszaru tła jako negatywnego przykładu w szkoleniu. Po zakończeniu tagowania kliknij strzałkę po prawej stronie, aby zapisać tagi i przejść do następnego obrazu.

    ![Oznaczanie obiektu prostokątnym zaznaczeniem](./media/get-started-build-detector/image-tagging.png)

Aby przesłać inny zestaw obrazów, wróć do górnej części tej sekcji i powtórz te czynności.

## <a name="train-the-detector"></a>Trenuj detektor

Aby wyszkolić model detektora, wybierz przycisk **Pociąg.** Detektor używa wszystkich bieżących obrazów i ich tagów do utworzenia modelu, który identyfikuje każdy oznaczony obiekt.

![Przycisk pociągu w prawym górnym rogu paska narzędzi nagłówka strony sieci Web](./media/getting-started-build-a-classifier/train01.png)

Proces szkolenia powinien trwać tylko kilka minut. W tym czasie informacje o procesie szkolenia są wyświetlane na karcie **Wydajność.**

![Okno przeglądarki z oknem szkoleniowym w sekcji głównej](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Oceń detektor

Po zakończeniu szkolenia wydajność modelu jest obliczana i wyświetlana. Usługa Custom Vision używa obrazów przesłanych do szkolenia do obliczania precyzji, przywołania i średniej precyzji. Precyzja i przywołanie to dwa różne pomiary skuteczności detektora:

- **Dokładność** wskazuje ułamek zidentyfikowanych klasyfikacji, które były poprawne. Na przykład, jeśli model zidentyfikował 100 obrazów jako psy, a 99 z nich było rzeczywiście psów, to precyzja będzie 99%.
- **Odwołanie** wskazuje ułamek rzeczywistych klasyfikacji, które zostały poprawnie zidentyfikowane. Na przykład, gdyby faktycznie było 100 obrazów jabłek, a model zidentyfikował 80 jako jabłka, wycofanie byłoby 80%.

![Wyniki szkolenia pokazują ogólną precyzję i wycofanie oraz średnią precyzję.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Próg prawdopodobieństwa

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Zarządzanie iteracjami szkoleniowymi

Za każdym razem, gdy szkolisz detektor, tworzysz nową _iterację_ z własnymi zaktualizowanymi wskaźnikami wydajności. Wszystkie iteracje można wyświetlić w lewym okienku karty **Wydajność.** W lewym okienku znajdziesz również przycisk **Usuń,** którego można użyć do usunięcia iteracji, jeśli jest przestarzała. Usunięcie iteracji powoduje usunięcie wszystkich obrazów, które są z nią jednoznacznie skojarzone.

Zobacz [Korzystanie z modelu z interfejsem API przewidywania,](./use-prediction-api.md) aby dowiedzieć się, jak uzyskać dostęp do wyszkolonych modeli programowo.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak tworzyć i szkolić model detektora obiektów za pomocą witryny internetowej Usługi Custom Vision. Następnie uzyskaj więcej informacji na temat iteracyjnego procesu ulepszania modelu.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)

