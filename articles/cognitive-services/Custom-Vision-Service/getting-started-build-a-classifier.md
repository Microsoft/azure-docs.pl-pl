---
title: 'Szybki Start: Tworzenie klasyfikatora przy użyciu witryny sieci Web Custom Vision'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak używać witryny sieci Web Custom Vision do tworzenia, uczenia i testowania modelu klasyfikacji obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Rozpoznawanie obrazu, aplikacja rozpoznawania obrazu, niestandardowa wizja
ms.openlocfilehash: d644c323cb60e5ef9a89670cd9b828e3e9676299
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131699"
---
# <a name="quickstart-build-a-classifier-with-the-custom-vision-website"></a>Szybki Start: Tworzenie klasyfikatora przy użyciu witryny sieci Web Custom Vision

W tym przewodniku szybki start dowiesz się, jak utworzyć model klasyfikacji obrazów przy użyciu witryny sieci Web Custom Vision. Po skompilowaniu modelu można przetestować go przy użyciu nowych obrazów i ostatecznie zintegrować go z własną aplikacją rozpoznawania obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw obrazów, za pomocą których można szkolić klasyfikatora. Poniżej znajdują się porady dotyczące wybierania obrazów.

## <a name="create-custom-vision-resources"></a>Tworzenie zasobów Custom Vision

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przeglądarce internetowej przejdź do [strony sieci web Custom Vision](https://customvision.ai) i wybierz pozycję __Zaloguj__. Zaloguj się przy użyciu tego samego konta, które zostało użyte do zalogowania się do Azure Portal.

![Obraz strony logowania](./media/browser-home.png)


1. Aby utworzyć swój pierwszy projekt, wybierz pozycję **Nowy projekt**. Zostanie wyświetlone okno dialogowe **Utwórz nowy projekt** .

    ![Okno dialogowe Nowy projekt zawiera pola nazw, opisów i domen.](./media/getting-started-build-a-classifier/new-project.png)

1. Wprowadź nazwę i opis projektu. Następnie wybierz grupę zasobów. Jeśli Twoje konto zalogowane jest skojarzone z kontem platformy Azure, na liście rozwijanej Grupa zasobów zostaną wyświetlone wszystkie grupy zasobów platformy Azure zawierające zasób Custom Vision Service. 

   > [!NOTE]
   > Jeśli grupa zasobów nie jest dostępna, upewnij się, że zalogowano się do [customvision.AI](https://customvision.ai) przy użyciu tego samego konta, które zostało użyte do zalogowania się do [Azure Portal](https://portal.azure.com/). Upewnij się również, że wybrano ten sam katalog w witrynie sieci Web Custom Vision, co katalog w Azure Portal, w którym znajdują się zasoby Custom Vision. W obu lokacjach możesz wybrać katalog z menu rozwijanego konto w prawym górnym rogu ekranu. 

1. Wybierz pozycję __Klasyfikacja__ w obszarze __typy projektów__. Następnie w obszarze __typy klasyfikacji__ wybierz pozycję **wieloetykietowe** lub **wieloklasowe**, w zależności od przypadku użycia. Klasyfikacja wieloetykietowa stosuje dowolną liczbę tagów do obrazu (zero lub więcej), podczas gdy klasyfikacja wieloklasowa sortuje obrazy w pojedynczej kategorii (Każdy przesłany przez Ciebie obraz zostanie posortowany do najbardziej dopasowanego tagu). Jeśli chcesz, możesz później zmienić typ klasyfikacji.

1. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje klasyfikatora dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli. Jeśli chcesz, będziesz mieć możliwość późniejszej zmiany domeny.

    |Domena|Przeznaczenie|
    |---|---|
    |__Ogólny__| Optymalizacja pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną. |
    |__Żywności__|Optymalizacja pod kątem zdjęć naczyń w postaci widocznej w menu restauracji. Jeśli chcesz sklasyfikować fotografie poszczególnych owoców lub warzyw, użyj domeny żywności.|
    |__Punkty orientacyjne__|Optymalizacja pod kątem rozpoznawalnych terenów, zarówno naturalnych, jak i sztucznej. Ta domena działa najlepiej, gdy punkt orientacyjny jest jasno widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest nieco przesunięty przez osoby przed nim.|
    |__Sprzedaż detaliczna__|Optymalizacja pod kątem obrazów znajdujących się w katalogu zakupów lub witrynie internetowej do kupowania. Jeśli potrzebujesz wysokiej dokładności klasyfikowania między Dresses, Pants i koszulami, Użyj tej domeny.|
    |__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez domeny kompaktowe mogą być eksportowane do lokalnego uruchamiania.|

1. Na koniec wybierz pozycję __Utwórz projekt__.

## <a name="choose-training-images"></a>Wybierz obrazy szkoleniowe

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

W tej sekcji zostaną przesłane i ręcznie oznakowane obrazy, aby ułatwić uczenie klasyfikatora. 

1. Aby dodać obrazy, kliknij przycisk __Dodaj obrazy__ , a następnie wybierz __Przeglądaj pliki lokalne__. Wybierz pozycję __Otwórz__ , aby przejść do tagowania. Wybór tagu zostanie zastosowany do całej grupy obrazów wybranych do przekazania, dzięki czemu można łatwiej przekazywać obrazy w oddzielnych grupach zgodnie z odpowiednimi tagami. Możesz również zmienić Tagi dla poszczególnych obrazów po ich przekazaniu.

    ![Kontrolka Dodaj obrazy jest pokazywana w lewym górnym rogu i jako przycisk w dolnej części.](./media/getting-started-build-a-classifier/add-images01.png)


1. Aby utworzyć tag, wprowadź tekst w polu __Moje Tagi__ i naciśnij klawisz ENTER. Jeśli tag już istnieje, pojawi się w menu rozwijanym. W projekcie z wieloma etykietami można dodać więcej niż jeden tag do obrazów, ale w projekcie wieloklasowym można dodać tylko jedną. Aby zakończyć przekazywanie obrazów, użyj przycisku __Przekaż pliki [Number]__ . 

    ![Obraz przedstawiający tag i stronę przekazywania](./media/getting-started-build-a-classifier/add-images03.png)

1. Po przekazaniu obrazów wybierz opcję __gotowe__ .

    ![Pasek postępu pokazuje wszystkie zadania ukończone.](./media/getting-started-build-a-classifier/add-images04.png)

Aby przekazać inny zestaw obrazów, Wróć do początku tej sekcji i powtórz te kroki.

## <a name="train-the-classifier"></a>Szkolenie klasyfikatora

Aby nauczyć klasyfikatora, wybierz przycisk **poszkol** . Klasyfikator korzysta ze wszystkich bieżących obrazów, aby utworzyć model, który identyfikuje cechy wizualne poszczególnych tagów.

![Przycisk uczenia w prawym górnym rogu paska narzędzi nagłówka strony sieci Web](./media/getting-started-build-a-classifier/train01.png)

Proces szkolenia powinien trwać tylko kilka minut. W tym czasie informacje o procesie szkolenia są wyświetlane na karcie **wydajność** .

![Okno przeglądarki z oknem dialogowym szkoleń w sekcji głównej](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Oceń klasyfikator

Po zakończeniu szkolenia jest szacowany i wyświetlany jego wydajność. W Custom Vision Service są używane obrazy przesłane do szkolenia w celu obliczenia precyzji i odwołania przy użyciu procesu o nazwie [k-złóż krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precyzja i odwoływanie to dwa różne pomiary skuteczności klasyfikatora:

- **Precyzja** wskazuje ułamek zidentyfikowanych klasyfikacji, które były poprawne. Na przykład jeśli model zidentyfikował 100 obrazów jako psy, a 99 z nich rzeczywiście miało psy, dokładność będzie 99%.
- **Odwołanie** wskazuje ułamek rzeczywistych klasyfikacji, które zostały prawidłowo zidentyfikowane. Na przykład jeśli w rzeczywistości istniało 100 obrazów z jabłek, a model zidentyfikowano 80 jako jabłka, przywoływanie będzie 80%.

![Wyniki szkolenia przedstawiają ogólną precyzję i odwołanie oraz precyzję i odwołania dla każdego znacznika w klasyfikatorze.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Próg prawdopodobieństwa

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Zarządzanie iteracjami szkoleń

Za każdym razem, gdy nauczysz klasyfikator, tworzysz nową _iterację_ ze swoimi zaktualizowanymi metrykami wydajności. Wszystkie iteracje można wyświetlić w lewym okienku na karcie **wydajność** . Znajdziesz również przycisk **Usuń** , którego można użyć, aby usunąć iterację, jeśli jest ona przestarzała. Po usunięciu iteracji usuwane są wszystkie obrazy, które są w unikatowy sposób skojarzone.

Zobacz [Używanie modelu z interfejsem API przewidywania,](./use-prediction-api.md) aby dowiedzieć się, jak programistycznie uzyskiwać dostęp do przeszkolonych modeli.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia i uczenia modelu klasyfikacji obrazów przy użyciu witryny sieci Web Custom Vision. Następnie uzyskaj więcej informacji na temat procesu iteracyjnego ulepszania modelu.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)

* [Co to jest usługa Custom Vision?](./overview.md)