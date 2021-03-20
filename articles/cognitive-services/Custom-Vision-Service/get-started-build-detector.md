---
title: 'Szybki Start: Tworzenie detektora obiektów przy użyciu witryny sieci Web Custom Vision'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak używać witryny sieci Web Custom Vision do tworzenia, uczenia i testowania modelu czujnika obiektów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Rozpoznawanie obrazu, aplikacja rozpoznawania obrazu, niestandardowa wizja
ms.openlocfilehash: 5ecd5fee565a8d31e0ff05f3b234771446242d02
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99221299"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>Szybki Start: Tworzenie detektora obiektów przy użyciu witryny sieci Web Custom Vision

W tym przewodniku szybki start dowiesz się, jak utworzyć model czujnika obiektów przy użyciu witryny sieci Web Custom Vision. Po skompilowaniu modelu można przetestować go przy użyciu nowych obrazów i ostatecznie zintegrować go z własną aplikacją rozpoznawania obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw obrazów, za pomocą których można szkolić model detektora. Możesz użyć zestawu [przykładowych obrazów](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) w serwisie GitHub. Możesz też wybrać własne obrazy, korzystając z poniższych wskazówek.

## <a name="create-custom-vision-resources"></a>Tworzenie zasobów Custom Vision

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przeglądarce internetowej przejdź do [strony sieci web Custom Vision](https://customvision.ai) i wybierz pozycję __Zaloguj__. Zaloguj się przy użyciu tego samego konta, które zostało użyte do zalogowania się do Azure Portal.

![Obraz strony logowania](./media/browser-home.png)


1. Aby utworzyć swój pierwszy projekt, wybierz pozycję **Nowy projekt**. Zostanie wyświetlone okno dialogowe **Utwórz nowy projekt** .

    ![Okno dialogowe Nowy projekt zawiera pola nazw, opisów i domen.](./media/get-started-build-detector/new-project.png)

1. Wprowadź nazwę i opis projektu. Następnie wybierz grupę zasobów. Jeśli Twoje konto zalogowane jest skojarzone z kontem platformy Azure, na liście rozwijanej Grupa zasobów zostaną wyświetlone wszystkie grupy zasobów platformy Azure zawierające zasób Custom Vision Service. 

   > [!NOTE]
   > Jeśli grupa zasobów nie jest dostępna, upewnij się, że zalogowano się do [customvision.AI](https://customvision.ai) przy użyciu tego samego konta, które zostało użyte do zalogowania się do [Azure Portal](https://portal.azure.com/). Upewnij się również, że wybrano ten sam katalog w witrynie sieci Web Custom Vision, co katalog w Azure Portal, w którym znajdują się zasoby Custom Vision. W obu lokacjach możesz wybrać katalog z menu rozwijanego konto w prawym górnym rogu ekranu. 

1. Wybierz pozycję __wykrywanie obiektów__ w obszarze __typy projektów__.

1. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje detektor dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli. Jeśli chcesz, będziesz mieć możliwość późniejszej zmiany domeny.

    |Domena|Przeznaczenie|
    |---|---|
    |__Ogólne__| Optymalizacja pod kątem szerokiego zakresu zadań wykrywania obiektów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną. |
    |__Logo__|Optymalizacja pod kątem znajdowania logo marki w obrazach.|
    |__Produkty na półkach__|Optymalizacja pod kątem wykrywania i klasyfikowania produktów na półkach.|
    |__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń wykrywania obiektów w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez domeny kompaktowe mogą być eksportowane do lokalnego uruchamiania.|

1. Na koniec wybierz pozycję __Utwórz projekt__.

## <a name="choose-training-images"></a>Wybierz obrazy szkoleniowe

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

W tej sekcji zostaną przesłane i ręcznie oznakowane obrazy, aby ułatwić uczenie detektora. 

1. Aby dodać obrazy, wybierz pozycję __Dodaj obrazy__ , a następnie wybierz pozycję __Przeglądaj pliki lokalne__. Wybierz pozycję __Otwórz__ , aby przekazać obrazy.

    ![Kontrolka Dodaj obrazy jest pokazywana w lewym górnym rogu i jako przycisk w dolnej części.](./media/get-started-build-detector/add-images.png)

1. Przekazane obrazy będą widoczne **w sekcji nieoznaczonej w** interfejsie użytkownika. Następnym krokiem jest ręczne oznakowanie obiektów, które mają być rozpoznawane przez detektor. Kliknij pierwszy obraz, aby otworzyć okno dialogowe tagowania. 

    ![Przekazane obrazy, w sekcji bez znaczników](./media/get-started-build-detector/images-untagged.png)

1. Kliknij i przeciągnij prostokąt wokół obiektu w obrazie. Następnie wprowadź nową nazwę tagu z **+** przyciskiem lub wybierz istniejący tag z listy rozwijanej. Ważne jest, aby oznaczyć każde wystąpienie obiektów, które mają być wykrywane, ponieważ detektor używa nieoznakowanego obszaru tła jako negatywnego przykładu w szkoleniu. Gdy skończysz tagowanie, kliknij strzałkę po prawej stronie, aby zapisać tagi i przejść do następnego obrazu.

    ![Tagowanie obiektu prostokątnym zaznaczeniem](./media/get-started-build-detector/image-tagging.png)

Aby przekazać inny zestaw obrazów, Wróć do początku tej sekcji i powtórz te kroki.

## <a name="train-the-detector"></a>Uczenie detektora

Aby nauczyć model detektora, wybierz przycisk **szkolenie** . Detektor używa wszystkich bieżących obrazów i ich tagów, aby utworzyć model, który identyfikuje każdy otagowany obiekt.

![Przycisk uczenia w prawym górnym rogu paska narzędzi nagłówka strony sieci Web](./media/getting-started-build-a-classifier/train01.png)

Proces szkolenia powinien trwać tylko kilka minut. W tym czasie informacje o procesie szkolenia są wyświetlane na karcie **wydajność** .

![Okno przeglądarki z oknem dialogowym szkoleń w sekcji głównej](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Oceń detektor

Po zakończeniu szkolenia model jest obliczany i wyświetlany. Usługa Custom Vision używa obrazów przesłanych do szkolenia w celu obliczenia dokładności, odwołania i średniej precyzji. Precyzja i odwoływanie to dwa różne pomiary skuteczności detektora:

- **Precyzja** wskazuje ułamek zidentyfikowanych klasyfikacji, które były poprawne. Na przykład jeśli model zidentyfikował 100 obrazów jako psy, a 99 z nich rzeczywiście miało psy, dokładność będzie 99%.
- **Odwołanie** wskazuje ułamek rzeczywistych klasyfikacji, które zostały prawidłowo zidentyfikowane. Na przykład jeśli w rzeczywistości istniało 100 obrazów z jabłek, a model zidentyfikowano 80 jako jabłka, przywoływanie będzie 80%.
- Średnia **precyzja** to średnia wartość średniej precyzji (AP). AP to obszar pod krzywą precyzji/odwoływania (precyzja została wykreślona względem odwołania dla każdego wykonanego przewidywania).

![Wyniki szkolenia przedstawiają ogólną precyzję i odwołanie oraz średnią precyzję.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Próg prawdopodobieństwa

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>Próg nakładania

Suwak **nakładania się progu** zawiera informacje o tym, w jaki sposób należy wziąć pod uwagę, że w szkoleniu musi być traktowana prawidłowa Prognoza obiektu. Ustawia minimalny dozwolony nakładanie się między polem ograniczenia obiektu a rzeczywistym polem ograniczenia wprowadzonym przez użytkownika. Jeśli pola ograniczenia nie nakładają się na ten stopień, prognozowanie nie będzie uznawane za poprawne.

## <a name="manage-training-iterations"></a>Zarządzanie iteracjami szkoleń

Za każdym razem, gdy nauczysz detektor, tworzysz nową _iterację_ ze swoimi zaktualizowanymi metrykami wydajności. Wszystkie iteracje można wyświetlić w lewym okienku na karcie **wydajność** . W lewym okienku znajdziesz również przycisk **Usuń** , którego można użyć, aby usunąć iterację, jeśli jest ona przestarzała. Po usunięciu iteracji usuwane są wszystkie obrazy, które są w unikatowy sposób skojarzone.

Zobacz [Używanie modelu z interfejsem API przewidywania,](./use-prediction-api.md) aby dowiedzieć się, jak programistycznie uzyskiwać dostęp do przeszkolonych modeli.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia i uczenia modelu czujnika obiektów przy użyciu witryny sieci Web Custom Vision. Następnie uzyskaj więcej informacji na temat procesu iteracyjnego ulepszania modelu.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)

* [Co to jest usługa Custom Vision?](./overview.md)
