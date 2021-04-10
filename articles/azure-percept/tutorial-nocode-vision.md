---
title: Tworzenie rozwiązania "bez obsługi kodu" w usłudze Azure Percept Studio
description: Dowiedz się, jak utworzyć rozwiązanie "bez obsługi kodu" w usłudze Azure Percept Studio i wdrożyć je na platformie Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023133"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Tworzenie rozwiązania "bez obsługi kodu" w usłudze Azure Percept Studio

Usługa Azure Percept Studio umożliwia tworzenie i wdrażanie niestandardowych rozwiązań do przetwarzania obrazów. nie jest wymagane kodowanie. W tym artykule opisano następujące czynności:

- Tworzenie projektu programu Vision w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Zbieraj obrazy szkoleniowe za pomocą Devkit
- Oznacz obrazy szkoleniowe w [Custom Vision](https://www.customvision.ai/)
- Uczenie niestandardowego wykrywania obiektów lub modelu klasyfikacji
- Wdrażanie modelu w Devkit
- Ulepszanie modelu przez skonfigurowanie ponownego szkolenia

Ten samouczek jest przeznaczony dla deweloperów, którzy mają niewielki komfort i nie mają doświadczenia w pracy z usługą Azure Percept.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- Środowisko instalacyjne platformy Azure Percept DK: nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono Devkit z IoT Hub

## <a name="create-a-vision-prototype"></a>Tworzenie prototypu wizji

1. Uruchom przeglądarkę i przejdź do [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na stronie Przegląd Kliknij kartę **samouczki & pokazów** .  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Ekran omówienia usługi Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. W obszarze **samouczki i pokazy wizji** kliknij pozycję **Utwórz prototyp programu Vision**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Ekran demonstracji i samouczki usługi Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Na **nowej stronie prototypu Custom Vision usługi Azure Percept** wykonaj następujące czynności:

    1. W polu **Nazwa projektu** wprowadź nazwę prototypu wizji.

    1. Wprowadź opis prototypu elementu Vision w polu **Opis projektu** .

    1. Wybierz pozycję **Azure PERCEPT DK** w menu rozwijanym **Typ urządzenia** .

    1. Wybierz zasób z menu rozwijanego **zasób** lub kliknij pozycję **Utwórz nowy zasób**. Jeśli wybierzesz opcję utworzenia nowego zasobu, wykonaj następujące czynności w oknie **Tworzenie** :
        1. Wprowadź nazwę nowego zasobu.
        1. Wybierz swoją subskrypcję platformy Azure.
        1. Wybierz grupę zasobów lub Utwórz nową.
        1. Wybierz preferowany region.
        1. Wybierz warstwę cenową (zalecamy S0).
        1. Kliknij pozycję **Utwórz** w dolnej części okna.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Utwórz okno zasobów.":::

    1. W obszarze **Typ projektu** wybierz, czy projekt programu Vision będzie wykonywał wykrywanie obiektów, czy klasyfikację obrazu. Aby uzyskać więcej informacji na temat typów projektów, kliknij przycisk **Pomóż mi wybrać**.

    1. W obszarze **Optymalizacja** wybierz, czy chcesz zoptymalizować projekt pod kątem dokładności, małych opóźnień sieci lub zrównoważenia obu tych elementów.

    1. Kliknij przycisk **Utwórz**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Utwórz niestandardowy ekran prototypu wizji.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Łączenie urządzenia z projektem i przechwytywanie obrazów

Po utworzeniu rozwiązania do obsługi wizji należy dodać Devkit i odpowiadające mu IoT Hub.

1. Włącz Devkit.

1. W menu rozwijanym **IoT Hub** wybierz Centrum IoT, z którym połączono DEVKIT podczas OOBE.

1. W menu rozwijanym **urządzenia** wybierz swój Devkit.

Następnie należy załadować obrazy lub przechwycić obrazy w celu szkolenia Twojego modelu AI. Zalecamy przekazywanie co najmniej 30 obrazów na typ tagu. Na przykład jeśli chcesz skompilować detektor Dog i Cat, musisz przekazać co najmniej 30 obrazów psów i 30 obrazów z kotów. Aby przechwytywać obrazy przy użyciu modelu programu Visioning Devkit, wykonaj następujące czynności:

1. W oknie **Przechwytywanie obrazu** wybierz pozycję **Wyświetl strumień urządzenia** , aby wyświetlić strumień wideo programu Vision SoM.

1. Sprawdź strumień wideo, aby upewnić się, że aparat modelu SoM programu Vision został prawidłowo wyrównany w celu przełączenia zdjęć szkoleniowych. Wprowadź odpowiednie zmiany.

1. W oknie **Przechwytywanie obrazu** kliknij pozycję **Zrób zdjęcie**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Ekran przechwytywania obrazu.":::

1. Alternatywnie można skonfigurować automatyczne Przechwytywanie obrazu w celu zbierania dużej ilości obrazów jednocześnie przez sprawdzenie pola **przechwytywania automatycznego obrazu** . Wybierz preferowaną szybkość przetwarzania obrazu w obszarze **szybkość przechwytywania** i łączną liczbę obrazów, które mają być zbierane w obszarze **cel**. Kliknij pozycję **Ustaw automatyczne przechwytywanie** , aby rozpocząć proces automatycznego przechwytywania obrazu.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Menu rozwijane automatycznego przechwytywania obrazu.":::

Gdy masz wystarczającą liczbę zdjęć, kliknij przycisk **Dalej: obrazy tagów i szkolenia modeli** w dolnej części ekranu. Wszystkie obrazy zostaną zapisane w [Custom Vision](https://www.customvision.ai/).

> [!NOTE]
> Jeśli wybierzesz opcję przekazywania obrazów szkoleniowych bezpośrednio do Custom Vision, pamiętaj, że rozmiar pliku obrazu nie może przekraczać 6MB.

## <a name="tag-images-and-train-your-model"></a>Dodawanie tagów do obrazów i uczenie modelu

Przed przeszkoleniem modelu Dodaj etykiety do obrazów.

1. Na stronie **Tagi obrazy i szkolenia modelu** kliknij pozycję **otwórz projekt w Custom Vision**.

1. Po lewej stronie **Custom Vision** kliknij pozycję **nieoznakowane** w obszarze **Tagi** , aby wyświetlić obrazy, które zostały zebrane w poprzednim kroku. Wybierz co najmniej jeden nieoznakowany obraz.

1. W oknie **szczegóły obrazu** kliknij obraz, aby rozpocząć tagowanie. Jeśli wybrano opcję wykrywanie obiektów jako typ projektu, należy również narysować [pole ograniczenia](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) wokół określonych obiektów, które mają być Tagi. Dostosuj pole ograniczenia zgodnie z wymaganiami. Wpisz tag obiektu i kliknij, **+** Aby zastosować tag. Jeśli na przykład tworzysz rozwiązanie do obsługi, które powiadomi Cię, gdy półka w sklepie wymaga odtworzenia, Dodaj tag "pusty półka" do obrazów pustych półek i Dodaj tag "pełen półka" do obrazów w pełni rozbudowy półek. Powtórz dla wszystkich obrazów nieoznakowanych.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Ekran tagowania obrazu w Custom Vision.":::

1. Po znakowaniu obrazów kliknij ikonę **X** w prawym górnym rogu okna. Kliknij pozycję **otagowane** w obszarze **Tagi** , aby wyświetlić wszystkie nowo otagowane obrazy.

1. Po oznaczeniu obrazów można przystąpić do uczenia modelu AI. Aby to zrobić, kliknij pozycję **uczenie** w górnej części strony. Musisz mieć co najmniej 15 obrazów na typ tagu, aby szkolić model (zalecamy użycie co najmniej 30). Szkolenie zwykle trwa około 30 minut, ale może trwać dłużej, jeśli zestaw obrazów jest niezwykle duży.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Wybór obrazu szkoleniowego z wyróżnionym przyciskiem uczenia.":::

1. Po zakończeniu szkolenia na ekranie zostanie wyświetlona wydajność modelu. Więcej informacji o ocenie tych wyników można znaleźć w dokumentacji dotyczącej [oceny modelu](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector). Po przeprowadzeniu szkolenia możesz również [przetestować model](../cognitive-services/custom-vision-service/test-your-model.md) na dodatkowych obrazach i ponownie pouczenie w razie potrzeby. Za każdym razem, gdy nauczysz model, zostanie on zapisany jako nowa iteracja. Więcej informacji o sposobach poprawy wydajności modelu można [znaleźć w dokumentacji Custom Vision](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) .

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Wyniki szkolenia modelu.":::

    > [!NOTE]
    > W przypadku wybrania testowania modelu na dodatkowych obrazach w Custom Vision należy pamiętać, że rozmiar pliku obrazu testu nie może przekroczyć 4 MB.

Po spełnieniu wydajności modelu Zamknij Custom Vision, zamykając kartę przeglądarka.

## <a name="deploy-your-ai-model"></a>Wdróż model AI

1. Wróć do karty Azure Percept Studio i kliknij przycisk **Dalej: Oceń i Wdróż** w dolnej części ekranu.

1. W oknie **Oblicz i Wdróż** zostanie wyświetlona wydajność wybranej iteracji modelu. Wybierz iterację, którą chcesz wdrożyć w Devkit, w menu rozwijanym **iteracja modelu** i kliknij pozycję **Wdróż model** w dolnej części ekranu.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Ekran wdrożenia modelu." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Po wdrożeniu modelu Wyświetl strumień wideo na urządzeniu, aby zobaczyć, jak inferencing model w działaniu.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Strumień urządzenia przedstawiający działanie detektora słuchawek w działaniu.":::

Po zamknięciu tego okna możesz wrócić i edytować projekt wizji w dowolnym momencie, klikając pozycję **wizja** w obszarze **projekty AI** na stronie głównej usługi Azure Percept Studio i wybierając nazwę projektu wizji.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Strona projektu wizji." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Ulepszanie modelu przez skonfigurowanie ponownego szkolenia

Po przeprowadzeniu szkolenia modelu i wdrożeniu go na urządzeniu można poprawić wydajność modelu przez skonfigurowanie parametrów ponownego uczenia w celu przechwycenia większej ilości danych szkoleniowych. Ta funkcja służy do ulepszania wydajności nauczonego modelu przez umożliwienie przechwytywania obrazów na podstawie zakresu prawdopodobieństwa. Na przykład można ustawić, aby urządzenie przechwycić tylko obrazy szkoleniowe, gdy prawdopodobieństwo jest niskie. Poniżej znajdują się [dodatkowe wskazówki](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) dotyczące dodawania większej liczby obrazów i równoważenia danych szkoleniowych.

1. Aby skonfigurować ponowne szkolenie, Wróć do **projektu**, a następnie do **podsumowania projektu**
1. Na karcie **Przechwytywanie obrazu** wybierz opcję **Automatyczne przechwytywanie obrazu** i **Skonfiguruj ponowne szkolenie**.
1. Skonfiguruj automatyczne Przechwytywanie obrazu, aby zebrać dużą liczbę obrazów jednocześnie przez sprawdzenie pola **przechwytywania automatycznego obrazu** .
1. Wybierz preferowaną szybkość przetwarzania obrazu w obszarze **szybkość przechwytywania** i łączną liczbę obrazów, które mają być zbierane w obszarze **cel**.
1. W sekcji **Konfigurowanie ponownego uczenia** wybierz iterację, dla której chcesz przechwytywać więcej danych szkoleniowych, a następnie wybierz zakres prawdopodobieństwa. Do projektu zostaną przekazane tylko obrazy, które spełniają wartość współczynnika prawdopodobieństwa.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="Przechwytywanie obrazu.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli utworzono nowy zasób platformy Azure dla tego samouczka i nie chcesz już tworzyć ani korzystać z rozwiązania Vision, wykonaj następujące kroki, aby usunąć zasób:

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com/).
1. Kliknij pozycję **wszystkie zasoby**.
1. Kliknij pole wyboru obok zasobu utworzonego w ramach tego samouczka. Typ zasobu zostanie wyświetlony jako **Cognitive Services**.
1. Kliknij ikonę **Usuń** znajdującą się w górnej części ekranu.

## <a name="video-walkthrough"></a>Przewodnik wideo

Aby zapoznać się z przewodnikiem opisanym powyżej, zobacz następujące wideo:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z artykułami dotyczącymi rozbudowy, aby uzyskać informacje na temat dodatkowych funkcji rozwiązania Vision w usłudze Azure Percept Studio.

<!--
Add links to how-to articles and oobe article.
-->