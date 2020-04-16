---
title: Szybki start Zbuduj klasyfikator — usługa niestandardowej wizji
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
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404050"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Szybki start: Jak zbudować klasyfikator z niestandardowych wizji

W tym przewodniku Szybki start dowiesz się, jak utworzyć klasyfikator za pośrednictwem witryny internetowej usługi Custom Vision. Po utworzeniu modelu klasyfikatora, można użyć usługi Niestandardowe vision klasyfikacji obrazu.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw obrazów, z którymi można trenować klasyfikatora. Poniżej znajdziesz wskazówki dotyczące wybierania obrazów.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Tworzenie zasobów usługi Custom Vision w witrynie Azure portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przeglądarce internetowej przejdź do [strony internetowej Custom Vision](https://customvision.ai) i wybierz pozycję Zaloguj __się__. Zaloguj się przy użyciu tego samego konta, którego użyto do zalogowania się do witryny Azure portal.

![Obraz strony logowania](./media/browser-home.png)


1. Aby utworzyć pierwszy projekt, wybierz pozycję **Nowy projekt**. Zostanie wyświetlone okno dialogowe **Tworzenie nowego projektu.**

    ![Nowe okno dialogowe projektu ma pola dotyczące nazwy, opisu i domen.](./media/getting-started-build-a-classifier/new-project.png)

1. Wprowadź nazwę i opis projektu. Następnie wybierz grupę zasobów. Jeśli twoje konto zalogowane jest skojarzone z kontem platformy Azure, na rozwijaniu grupy zasobów zostaną wyświetlone wszystkie grupy zasobów platformy Azure, które zawierają zasób usługi niestandardowej wizji. 

   > [!NOTE]
   > Jeśli żadna grupa zasobów nie jest dostępna, upewnij się, że zalogowano się do [customvision.ai](https://customvision.ai) przy użyciu tego samego konta, które było używane do logowania się do [witryny Azure portal](https://portal.azure.com/). Ponadto upewnij się, że wybrano ten sam "Katalog" w portalu usługi Custom Vision jako katalog w witrynie Azure portal, w którym znajdują się zasoby usługi Custom Vision. W obu witrynach możesz wybrać katalog z menu rozwijanego konta w prawym górnym rogu ekranu. 

1. Wybierz __klasyfikację__ w obszarze __Typy projektów__. Następnie w obszarze __Typy klasyfikacji__wybierz **opcję Multilabel** lub **Multiclass**, w zależności od przypadku użycia. Klasyfikacja wielolabelowa ma zastosowanie do dowolnej liczby znaczników do obrazu (zero lub więcej), podczas gdy klasyfikacja wieloklasowa sortuje obrazy w pojedyncze kategorie (każdy przesłany obraz zostanie posortowany według najbardziej prawdopodobnego znacznika). Jeśli chcesz, możesz później zmienić typ klasyfikacji.

1. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje klasyfikator dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli. Jeśli chcesz, możesz później zmienić domenę.

    |Domain|Przeznaczenie|
    |---|---|
    |__Ogólny__| Zoptymalizowany pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę rodzajową. |
    |__Żywności__|Zoptymalizowany pod kątem zdjęć potraw, jak można je zobaczyć w menu restauracji. Jeśli chcesz sklasyfikować zdjęcia poszczególnych owoców lub warzyw, użyj domeny Żywność.|
    |__Zabytki__|Zoptymalizowany pod kątem rozpoznawalnych punktów orientacyjnych, zarówno naturalnych, jak i sztucznych. Ta domena działa najlepiej, gdy punkt orientacyjny jest wyraźnie widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest lekko zasłonięty przez ludzi przed nim.|
    |__Sprzedaż detaliczna__|Zoptymalizowane pod kątem obrazów, które znajdują się w katalogu zakupów lub witrynie zakupów. Jeśli chcesz, aby wysoka precyzja klasyfikacji między sukienki, spodnie i koszule, należy użyć tej domeny.|
    |__Domeny kompaktowe__| Zoptymalizowany pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach mobilnych. Modele generowane przez domeny kompaktowe można eksportować do uruchamiania lokalnie.|

1. Na koniec wybierz pozycję __Utwórz projekt__.

## <a name="choose-training-images"></a>Wybieranie obrazów szkoleniowych

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

W tej sekcji przekażesz i ręcznie oznaczysz obrazy, aby pomóc w szkoleniu klasyfikatora. 

1. Aby dodać obrazy, kliknij przycisk __Dodaj obrazy,__ a następnie wybierz pozycję __Przeglądaj pliki lokalne__. Wybierz __otwórz,__ aby przejść do tagowania. Wybór tagu zostanie zastosowany do całej grupy obrazów wybranych do przesłania, dzięki czemu łatwiej będzie przesyłać obrazy w oddzielnych grupach zgodnie z ich pożądanymi tagami. Możesz też zmienić znaczniki poszczególnych obrazów po ich przesłaniu.

    ![Formant dodaj obrazy jest wyświetlany w lewym górnym rogu i jako przycisk na dole.](./media/getting-started-build-a-classifier/add-images01.png)


1. Aby utworzyć znacznik, wprowadź tekst w polu __Moje znaczniki__ i naciśnij klawisz Enter. Jeśli tag już istnieje, pojawi się w menu rozwijanym. W projekcie wielolabelowym można dodać więcej niż jeden znacznik do obrazów, ale w projekcie wieloklasowym można dodać tylko jeden. Aby zakończyć przesyłanie obrazów, użyj przycisku __Przekaż [numer] plików.__ 

    ![Obraz strony tagu i przesyłania](./media/getting-started-build-a-classifier/add-images03.png)

1. Wybierz __pozycję Gotowe__ po przesłaniu obrazów.

    ![Na pasku postępu są wyświetlane wszystkie wykonane zadania.](./media/getting-started-build-a-classifier/add-images04.png)

Aby przesłać inny zestaw obrazów, wróć do górnej części tej sekcji i powtórz te czynności.

## <a name="train-the-classifier"></a>Szkolenie klasyfikatora

Aby wyszkolić klasyfikatora, wybierz przycisk **Pociąg.** Klasyfikator używa wszystkich bieżących obrazów do utworzenia modelu, który identyfikuje walory wizualne każdego tagu.

![Przycisk pociągu w prawym górnym rogu paska narzędzi nagłówka strony sieci Web](./media/getting-started-build-a-classifier/train01.png)

Proces szkolenia powinien trwać tylko kilka minut. W tym czasie informacje o procesie szkolenia są wyświetlane na karcie **Wydajność.**

![Okno przeglądarki z oknem szkoleniowym w sekcji głównej](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Oceń klasyfikator

Po zakończeniu szkolenia wydajność modelu jest szacowana i wyświetlana. Usługa Custom Vision Service używa obrazów przesłanych do szkolenia do obliczania precyzji i przywołania przy użyciu procesu zwanego [k-fold cross validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precyzja i przywołanie to dwa różne pomiary skuteczności klasyfikatora:

- **Dokładność** wskazuje ułamek zidentyfikowanych klasyfikacji, które były poprawne. Na przykład, jeśli model zidentyfikował 100 obrazów jako psy, a 99 z nich było rzeczywiście psów, to precyzja będzie 99%.
- **Odwołanie** wskazuje ułamek rzeczywistych klasyfikacji, które zostały poprawnie zidentyfikowane. Na przykład, gdyby faktycznie było 100 obrazów jabłek, a model zidentyfikował 80 jako jabłka, wycofanie byłoby 80%.

![Wyniki szkolenia pokazują ogólną precyzję i wycofanie oraz precyzję i odwołanie dla każdego znacznika w klasyfikatorze.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Próg prawdopodobieństwa

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Zarządzanie iteracjami szkoleniowymi

Za każdym razem, gdy szkolisz klasyfikatora, tworzysz nową _iterację_ z własnymi zaktualizowanymi metrykami wydajności. Wszystkie iteracje można wyświetlić w lewym okienku karty **Wydajność.** Znajdziesz tu również przycisk **Usuń,** którego można użyć do usunięcia iteracji, jeśli jest przestarzała. Usunięcie iteracji powoduje usunięcie wszystkich obrazów, które są z nią jednoznacznie skojarzone.

Zobacz [Korzystanie z modelu z interfejsem API przewidywania,](./use-prediction-api.md) aby dowiedzieć się, jak uzyskać dostęp do wyszkolonych modeli programowo.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć i wyszkolić model klasyfikacji obrazów za pomocą witryny sieci Web usługi Custom Vision. Następnie uzyskaj więcej informacji na temat iteracyjnego procesu ulepszania modelu.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)

