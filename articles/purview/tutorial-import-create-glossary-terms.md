---
title: 'Samouczek: tworzenie i importowanie terminów w słowniku w usłudze Azure Purview (wersja zapoznawcza)'
description: W tym samouczku opisano sposób tworzenia terminów w słowniku, dodawania terminów słownika do zasobu i importowania terminów w słowniku.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: b240806b934cae54810cf9dfe1a6c1f369cede7e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587514"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Samouczek: tworzenie i importowanie terminów w słowniku w usłudze Azure Purview (wersja zapoznawcza)

> [!IMPORTANT]
> Usługa Azure Purview jest obecnie dostępna w wersji zapoznawczej. Dodatkowe warunki użytkowania wersji [zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) programu Microsoft Azure obejmują dodatkowe postanowienia prawne dotyczące funkcji platformy Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze wydane jako ogólnie dostępne.

Słownik jest ważnym narzędziem do konserwacji i organizowania katalogu. Słownik można utworzyć, definiując nowe terminy lub importując listę terminów, a następnie stosując te terminy do zasobów.

Ten samouczek *to część 5* z pięciu części serii samouczków, w której poznasz podstawy zarządzania zarządzaniem danymi w całej majątek danych przy użyciu usługi Azure Purview. Ten samouczek opiera się na pracy wykonanej w części 4. Eksplorowanie zestawów zasobów, szczegółów, schematów i klasyfikacji w usłudze [Azure Purview (wersja zapoznawcza).](tutorial-schemas-and-classifications.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie terminów w słowniku.
> * Dodawanie terminów słownika do zasobu.
> * Importuj terminy w słowniku.

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletny [samouczek: eksplorowanie zestawów zasobów, szczegółów, schematów i klasyfikacji w usłudze Azure Purview (wersja zapoznawcza).](tutorial-schemas-and-classifications.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Tworzenie terminów słownika

Terminy biznesowe lub techniczne można tworzyć w słowniku. Możesz również dodać adnotacje do zasobów, stosując do nich warunki.

Oto podsumowanie niektórych najczęściej występujących pól na stronie **termin słownika:**

* **Stan:** przypisz stan do terminu **(Wersja robocza,** **Zatwierdzone,** **Wygasłe** lub **Alert).**

* **Definicja:** wprowadź definicję terminu.

* **Akronim**: wprowadź skróconą wersję terminu, używając początkowych liter każdego wyrazu.

* **Synonimy:** wybierz inne terminy z taką samą lub podobną definicją.

* **Powiązane terminy:** wybierz inne terminy w słowniku, które są powiązane z tym terminem, ale mają różne definicje. Przykłady to terminy techniczne związane z terminem biznesowym, nazwą kodu lub innymi terminami, które powinny być skojarzone z tym terminem.

Utwórz termin słownika, korzystając z następujących kroków:

1. Przejdź do zasobu usługi Azure Purview w witrynie Azure Portal i wybierz **pozycję Open Purview Studio (Otwórz program Purview Studio).** Zostaniesz automatycznie przekierowyny do strony głównej programu Purview Studio.

1. Wybierz **pozycję Słownik** w okienku po lewej stronie, aby otworzyć stronę **Terminy słownika.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Zrzut ekranu przedstawiający stronę terminów słownika.":::

1. Wybierz **nowy termin System** default Continue  >  **(Kontynuuj).**  >  

1. Na **karcie Przegląd** na **stronie Nowy**  termin wprowadź nazwę nowego terminu: *Finanse.*

1. Wprowadź **definicję**: *Ten termin reprezentuje informacje finansowe dla firmy Contoso Inc.*

1. Z listy **rozwijanej** Stan wybierz pozycję **Zatwierdzone.**

1. Po zakończeniu wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego terminu słownika.":::

## <a name="add-glossary-terms-to-an-asset"></a>Dodawanie terminów słownika do zasobu

1. Użyj kroków poznanych w [części 1 tej serii samouczków,](tutorial-scan-data.md) aby znaleźć zasób. Na przykład możesz **Contoso_CashFlow_{N}.csv.**

1. Na stronie szczegółów zasobu wybierz pozycję **Edytuj**.

1. Na liście **rozwijanej Terminy** słownika na **karcie** Przegląd wybierz pozycję **Finanse,** a następnie wybierz pozycję **Zapisz.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania terminu słownika do zasobu.":::

1. Przejdź do sekcji **Terminy słownika** na **karcie Przegląd** i zwróć uwagę, że termin *Financial* został zastosowany do środka trwałego.

## <a name="import-glossary-terms"></a>Importowanie terminów w słowniku

Aby zaimportować terminy lub zaktualizować istniejące terminy zbiorczo, przekaż wstępnie wypełniony szablon do słownika.

W tej procedurze zaimportujesz terminy w słowniku za pośrednictwem pliku CSV:

1. Zwróć uwagę, że plik o nazwie *StarterKitTerms.csv*, który jest częścią zestawu startowego pobranego w części [1 tej serii samouczków.](tutorial-scan-data.md)

   Ten plik zawiera listę wstępnie wypełnionych terminów, które są istotne dla twojej majątku danych.

 > [!Important]
   > Adres e-mail dla ekspertów i ekspertów w . Plik CSV powinien być podstawowym adresem użytkownika z grupy aAD. Alternatywny adres e-mail, główna nazwa użytkownika i wiadomości e-mail spoza usługi AAD nie są jeszcze obsługiwane. Adresy e-mail należy zastąpić adresem podstawowym usługi AAD w organizacji.

1. Aby rozpocząć importowanie, wybierz pozycję **Słownik,** a następnie wybierz **pozycję Importuj terminy**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Zrzut ekranu przedstawiający sposób importowania terminów w słowniku.":::

1. Na stronie **Import terms (Warunki importu)** wybierz **pozycję System default (Domyślne ustawienie systemowe),** a następnie wybierz pozycję **Continue (Kontynuuj).**

1. Wybierz **pozycję** Przeglądaj , przejdź do lokalizacji, do *której pobranoStarterKitTerms.csv*, a następnie wybierz pozycję **Otwórz.**

1. Wybierz **przycisk OK,** aby rozpocząć importowanie warunków.

   Po zakończeniu importowania nowe terminy w słowniku są wyświetlane na **stronie Terminy w słowniku.**

1. Wyświetl każdy z nowo zaimportowanych terminów, aby zobaczyć, jak są zdefiniowane.

## <a name="create-custom-term-templates"></a>Tworzenie niestandardowych szablonów terminu

W tej sekcji dowiesz się, jak utworzyć szablon niestandardowego terminu z atrybutami niestandardowymi i zaimportować dane przy użyciu pliku CSV szablonu.

Istnieje kilka istniejących szablonów terminów systemowych, które można wyświetlić, wybierając pozycję **Słownik**  >  **Zarządzaj szablonami terminów**  >  **System**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="systemowe szablony terminowe.":::

Aby utworzyć nowy szablon niestandardowego terminu, wykonaj następujące czynności:

1. Wybierz **pozycję Słownik z** menu po lewej stronie.
1. Wybieranie **opcji Zarządzaj szablonami**  >  **okresów Szablon nowego** terminu  >  **niestandardowego**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="wylicz nowy szablon niestandardowego terminu.":::

Na **ekranie Szablon nowego** terminu wykonaj następujące czynności:

1. Wprowadź **nazwę szablonu:** `Sensitivity level` .
1. Wprowadź żądany opis, na przykład `Indicates the level of sensitivity for this data.`
1. Wybierz **pozycję + Nowy atrybut,** aby otworzyć okno dialogowe dodawania atrybutów.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="Start ekranu szablonu nowego terminu.":::

1. Na **ekranie Nowy** atrybut wprowadź następujące parametry:

   |Ustawienie|Sugerowana wartość|
   |---------|-----------|
   |Nazwa atrybutu |jest informacjami poufnymi|
   |Typ pola | Wybór pojedynczy|
   |Oznacz jako wymagane | zaznacz to pole wyboru.|
   |+ Dodaj wybór | Dodaj dwie opcje. "Tak" i "Nie".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="dodaj nowy atrybut.":::

1. Powtórz poprzednie kroki, aby dodać kolejny atrybut o nazwie `can be shared externally` . Po dodaniu obu atrybutów wybierz pozycję **Utwórz.**

## <a name="import-terms-from-a-template"></a>Importowanie terminów z szablonu

Następnie zaimportuj nowy termin przy **użyciu** utworzonego szablonu poziomu czułości. 

1. Na **ekranie Terminy w słowniku** wybierz pozycję **Importuj terminy.**

1. Wybierz **pozycję Poziom czułości** na **ekranie Importowanie** terminów. Wybierz opcję **Kontynuuj**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Wybierz poziom czułości.":::

1. Szablon terminu dla **poziomu czułości zawiera** domyślne atrybuty systemowe, a także nowe dodane atrybuty: i `can be shared externally` `is sensitive information` . Wybierz **pozycję Pobierz przykład. Plik CSV.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Pobierz przykładowy plik CSV.":::

1. Zostanie pobrany plik szablonu w celu edytowania i przekazywania nowego terminu słownika z atrybutami klienta. Otwórz pobrany plik CSV. Dodaj nowe terminy i ich odpowiednie wartości jako nowe wiersze w pliku CSV.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Utwórz termin w pliku CSV.":::

   Wszystkie terminy wymienione w kolumnie **Powiązane terminy** lub **Synonimy,** które jeszcze nie istnieją, zostaną utworzone po przesłaniu pliku. Zostaną one utworzone przy użyciu szablonu **domyślnego** System.

1. Aby przekazać plik, wróć do ekranu **Import terms (Importowanie warunków),** a następnie wybierz **pozycję Browse (Przeglądaj)** obok opcji Select the **complete (Wybierz ukończony). Plik CSV do przekazania.** Wybierz plik w oknie dialogowym, które zostanie otwarte, a następnie wybierz przycisk **OK.**

1. Nowe warunki są teraz wyświetlane na ekranie **Terminów w słowniku.** Aby wyświetlić szczegółowe informacje o nowych terminach, kliknij nazwę terminu na liście.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie terminów w słowniku.
> * Dodawanie terminów słownika do zasobu.
> * Importuj terminy w słowniku.

Aby dowiedzieć się więcej o różnych szczegółowych informacjach o wykazie, należy przejść do następnego artykułu.

> [!div class="nextstepaction"]
> [Opis analizy w usłudze Azure Purview](concept-insights.md)
