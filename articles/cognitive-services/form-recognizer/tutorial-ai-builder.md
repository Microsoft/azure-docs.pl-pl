---
title: 'Samouczek: Tworzenie aplikacji przetwarzania formularzy przy użyciu konstruktora AI — aparat rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym samouczku użyjesz programu AI Builder do tworzenia i uczenia aplikacji do przetwarzania formularzy.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467123"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Samouczek: Tworzenie aplikacji do przetwarzania formularzy przy użyciu programu AI Builder

[Konstruktor AI](/ai-builder/overview) jest możliwością platformy umożliwiającej automatyzację procesów i prognozowanie wyników w celu poprawy wydajności działania firmy. Za pomocą przetwarzania formularzy programu AI można tworzyć modele AI, które identyfikują i wyodrębniają pary klucz-wartość oraz dane tabeli z dokumentów formularzy.

> [!NOTE]
> Ten projekt jest również dostępny jako [moduł Microsoft Learn](/learn/modules/get-started-with-form-processing/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz model AI przetwarzający formularz
> * Szkolenie modelu
> * Publikowanie modelu do użycia w usłudze Azure PowerShell lub programie PowerShell

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw składający się z co najmniej pięciu form tego samego typu do użycia na potrzeby szkoleń/testowania danych. Zobacz [Tworzenie zestawu danych szkoleniowych](./build-training-data-set.md) dla wskazówek i opcji tworzenia zestawu danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2128080).
* [Licencjonowanie](https://go.microsoft.com/fwlink/?linkid=2085130)aplikacji lub pakietu do automatyzacji Licencja musi zawierać [Common Data Service](https://powerplatform.microsoft.com/common-data-service/).
* [Dodatek lub wersja próbna](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)programu AI Builder.


## <a name="create-a-form-processing-project"></a>Tworzenie projektu przetwarzania formularza

1. Przejdź do pozycji [aplikacje zaawansowane](https://make.powerapps.com/) lub [Automatyzacja](https://flow.microsoft.com/signin)i zaloguj się przy użyciu konta organizacji.
1. W okienku po lewej stronie wybierz pozycję kompilacja programu **AI Builder**  >  .
1. Wybierz kartę **Przetwarzanie formularza** .
1. Wpisz nazwę modelu.
1. Wybierz przycisk **Utwórz**.

## <a name="upload-and-analyze-documents"></a>Przekazywanie i analizowanie dokumentów

Na stronie **Dodawanie dokumentów** należy dostarczyć przykładowe dokumenty, aby szkolić model dla typu formularza, z którego mają zostać wyodrębnione informacje. Po przekazaniu dokumentów Konstruktor AI analizuje je, aby sprawdzić, czy są one wystarczające do uczenia modelu.

> [!NOTE]
> Narzędzie AI Builder nie obsługuje obecnie następujących typów danych wejściowych przetwarzania formularzy:
>
> - Tabele złożone (tabele zagnieżdżone, scalone nagłówki lub komórki itd.)
> - Pola wyboru lub przyciski radiowe
> - Dokumenty PDF dłuższe niż 50 stron
> - Pliki PDF z możliwością wypełniania
>
> Aby uzyskać więcej informacji na temat wymagań dotyczących dokumentów wejściowych, zobacz [wymagania wejściowe](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Przekazywanie dokumentów

1. Wybierz pozycję **Dodaj dokumenty**, wybierz co najmniej pięć dokumentów, a następnie wybierz pozycję **Przekaż**.
1. Po zakończeniu przekazywania wybierz pozycję **Zamknij**.
1. Następnie wybierz pozycję **Analizuj**.

> [!NOTE] 
> Po przekazaniu tych dokumentów nadal można usunąć niektóre dokumenty lub przekazać dodatkowe.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który podświetla przycisk Dodaj dokumenty.](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analizowanie dokumentów

Podczas kroku analizy Konstruktor AI sprawdza dokumenty, które zostały przekazane i wykrywa pola i tabele. Czas potrzebny na wykonanie tej operacji zależy od liczby dostarczonych dokumentów, ale w większości przypadków może to potrwać kilka minut.

Po zakończeniu analizy wybierz miniaturę, aby otworzyć środowisko wyboru pola.

> [!IMPORTANT]
> Jeśli analiza nie powiodła się, prawdopodobnie z powodu niepowodzenia wykrywania strukturalnego tekstu w dokumentach przez konstruktora AI. Sprawdź, czy aktualizowane dokumenty są zgodne z [wymaganiami wejściowymi](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Wybieranie pól formularza

Na stronie Wybór pola wybierz odpowiednie pola:

1. Aby zaznaczyć pole, kliknij prostokąt wskazujący wykrytym polu w dokumencie lub zaznacz wiele pól, klikając i przeciągając je. Możesz również wybrać pola z okienka po prawej stronie.
1. Kliknij nazwę wybranego pola, jeśli chcesz zmienić jego nazwę, aby dostosować je do swoich potrzeb lub znormalizować wyodrębnione etykiety.

    Po kliknięciu wykrytego pola zostaną wyświetlone następujące informacje:

    - **Nazwa pola**: Nazwa etykiety dla wykrytych pól.
    - **Wartość pola**: wartość wygenerowanego pola.

> [!div class="mx-imgBorder"]
> ![Znakowanie strony](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Niewykryte pola etykiety

Jeśli pole do etykiet nie zostało wykryte automatycznie przez model, możesz narysować prostokąt wokół jego wartości i wpisać nazwę etykiety w wyświetlonym oknie dialogowym.

## <a name="train-your-model"></a>Szkolenie modelu

1. Wybierz pozycję **Dalej**, aby zaznaczyć wybrane pola formularza. Jeśli wszystko wygląda dobrze, wybierz pozycję **Trenuj**, aby wytrenować model.

    > [!div class="mx-imgBorder"]
    > ![Strona szkoleń](./media/tutorial-ai-builder/summary-train-page.png)
1. Po zakończeniu trenowania wybierz pozycję **Przejdź do strony Szczegóły** na ekranie **Ukończono trenowanie**.
## <a name="quick-test-your-model"></a>Szybkie testowanie modelu

Strona Szczegóły umożliwia przetestowanie modelu przed jego opublikowaniem lub użyciem:

1. Na stronie Szczegóły wybierz pozycję **Szybki test**.
2. Możesz przeciągnąć i upuścić dokument lub wybrać opcję **Przekaż z urządzenia** , aby przekazać plik testowy. Szybki test powinien trwać tylko kilka sekund, zanim wyświetli wyniki.
3. Możesz wybrać opcję **Rozpocznij od nowa** , aby uruchomić inny test lub **zamknąć** , jeśli skończysz.

### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Jeśli otrzymujesz złe wyniki lub wyniki o niskim poziomie pewności dla niektórych pól, wypróbuj następujące wskazówki:

- Przeszkol przy użyciu formularzy o różnych wartościach w każdym polu.
- Przeszkol przy użyciu większego zestawu dokumentów szkoleniowych. Im więcej dokumentów jest oznakowanych, tym większym konstruktorem AI będzie dowiedzieć się, jak lepiej rozpoznać pola.
- Pliki PDF można zoptymalizować, wybierając tylko niektóre strony, z których ma zostać nadane szkolenie. Użyj opcji **Drukuj**  >  **do pliku PDF** , aby wybrać pewne strony w dokumencie.

## <a name="publish-your-model"></a>Publikowanie modelu

Jeśli masz zadowolony z modelu, wybierz pozycję **Publikuj**  , aby opublikować. Po zakończeniu publikowania model zostanie oznaczony jako **Opublikowany** i będzie gotowy do użycia.

> [!div class="mx-imgBorder"]
> ![Strona publikowania modelu](./media/tutorial-ai-builder/model-page.png)

Po opublikowaniu modelu przetwarzania formularza można go używać w [aplikacji kanwy aplikacji](/ai-builder/form-processor-component-in-powerapps) lub w programie do [automatyzacji](/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z dokumentacją programu AI Builder, aby korzystać z modelu przetwarzania formularzy.

* [Korzystanie z składnika form-procesor w aplikacjach zaawansowanych](/ai-builder/form-processor-component-in-powerapps)
* [Korzystanie z modelu przetwarzania formularzy w programie do automatyzacji](/ai-builder/form-processing-model-in-flow)