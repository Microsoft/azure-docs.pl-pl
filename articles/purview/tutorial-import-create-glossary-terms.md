---
title: 'Samouczek: Tworzenie i importowanie terminów słownika w usłudze Azure kontrolą (wersja zapoznawcza)'
description: W tym samouczku opisano sposób tworzenia terminów słownika, dodawania terminów słownika do elementu zawartości i importowania terminów słownika.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 4dc8dd84c22d094c87c82f5a920015f886780df2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399812"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Samouczek: Tworzenie i importowanie terminów słownika w usłudze Azure kontrolą (wersja zapoznawcza)

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

Słownik jest ważnym narzędziem do konserwacji i organizowania wykazu. Możesz skompilować słownik, definiując nowe warunki lub importując listę warunków, a następnie stosując te warunki do zasobów.

Ten samouczek jest *częścią 5 serii samouczków z pięcioma częścią* , w której przedstawiono podstawowe informacje dotyczące zarządzania nadzorem danych w ramach obszaru danych przy użyciu usługi Azure kontrolą. Ten samouczek kompiluje pracę wykonaną w [części 4: Eksplorowanie zestawów zasobów, szczegółów, schematów i klasyfikacji w usłudze Azure kontrolą (wersja zapoznawcza)](tutorial-schemas-and-classifications.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz warunki słownika.
> * Dodaj warunki słownika do elementu zawartości.
> * Importuj warunki słownika.

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletny [Samouczek: Eksplorowanie zestawów zasobów, szczegółów, schematów i klasyfikacji w usłudze Azure kontrolą (wersja zapoznawcza)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Tworzenie terminów słownika

W słowniku można tworzyć warunki biznesowe lub techniczne. Możesz również dodawać adnotacje do elementów zawartości, stosując do nich warunki.

Oto podsumowanie niektórych najbardziej popularnych pól na stronie **termin słownika** :

* **Stan**: przypisanie stanu do terminu (**wersja robocza**, **zatwierdzone**, **wygasłe** lub **alert**).

* **Definicja**: wprowadź definicję terminu.

* **Akronim**: wprowadź skróconą wersję terminu przy użyciu początkowych liter każdego wyrazu.

* **Synonimy**: Wybierz inne terminy z tą samą lub podobną definicją.

* **Powiązane warunki**: Wybierz inne terminy w słowniku, które są powiązane z tym, ale mają różne definicje. Przykłady to warunki techniczne, które są związane z terminem biznesowym, nazwą kodu lub innymi postanowieniami, które powinny być powiązane z terminem.

Utwórz termin słownika, wykonując następujące czynności:

1. Przejdź do zasobu usługi Azure kontrolą w Azure Portal i wybierz pozycję **Otwórz kontrolą Studio**. Nastąpi automatyczne przekierowanie do strony głównej programu kontrolą Studio.

1. Wybierz **słownik** z okienka po lewej stronie, aby otworzyć stronę **warunki słownika** .

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Zrzut ekranu przedstawiający stronę warunki słownika.":::

1. Wybierz pozycję **nowy termin**  >  **domyślne**  >  **Kontynuuj**.

1. Na karcie **Przegląd** na stronie **nowy termin** wprowadź **nazwę** nowego terminu: *finanse*.

1. Wprowadź **definicję**: *ten termin reprezentuje informacje finansowe firmy Contoso Inc.*

1. Z listy rozwijanej **stan** wybierz pozycję **zatwierdzone**.

1. Po zakończeniu wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego terminu słownika.":::

## <a name="add-glossary-terms-to-an-asset"></a>Dodawanie terminów słownika do elementu zawartości

1. Skorzystaj z kroków opisanych w [części 1 tej serii samouczków](tutorial-scan-data.md) , aby znaleźć element zawartości. Na przykład **Contoso_CashFlow_ {N}. csv**.

1. Na stronie Szczegóły zasobu wybierz pozycję **Edytuj**.

1. Na liście rozwijanej **terminy słownika** na karcie **Przegląd** wybierz pozycję **finanse**, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania słownika do elementu zawartości.":::

1. Przejdź do sekcji **warunki słownika** na karcie **Przegląd** i zwróć uwagę na to, że warunki *finansowe* zostały zastosowane do elementu zawartości.

## <a name="import-glossary-terms"></a>Importuj terminy słownika

Aby zaimportować warunki lub zaktualizować istniejące terminy zbiorczo, Przekaż wstępnie wypełniony szablon do słownika.

Ta procedura polega na zaimportowaniu terminów słownika za pośrednictwem pliku CSV:

1. Zwróć uwagę na miejsce przechowywania pliku o nazwie *StarterKitTerms.csv*, który jest częścią zestawu startowego pobranego w [części 1 tej serii samouczków](tutorial-scan-data.md).

   Ten plik zawiera listę wstępnie wypełnionych terminów odnoszących się do danej ilości danych.

1. Aby rozpocząć importowanie, wybierz pozycję **słownik**, a następnie wybierz pozycję **Importuj warunki**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Zrzut ekranu przedstawiający sposób importowania terminów słownika.":::

1. Na stronie **Importuj warunki** wybierz opcję **system domyślny**, a następnie wybierz pozycję **Kontynuuj**.

1. Wybierz pozycję **Przeglądaj**, przejdź do lokalizacji, w której pobrano *StarterKitTerms.csv*, a następnie wybierz pozycję **Otwórz**.

1. Wybierz **przycisk OK** , aby rozpocząć importowanie warunków.

   Po zakończeniu importowania nowe terminy słownika są wyświetlane na stronie **warunki słownika** .

1. Przejrzyj wszystkie nowo zaimportowane terminy, aby zobaczyć, jak zostały zdefiniowane.

## <a name="create-custom-term-templates"></a>Tworzenie niestandardowych szablonów terminów

W tej sekcji dowiesz się, jak utworzyć niestandardowy szablon terminu z atrybutami niestandardowymi i zaimportować dane przy użyciu pliku CSV szablonu.

Istnieje kilka istniejących szablonów terminów systemu, które można wyświetlić, wybierając pozycję **słownik**  >  **Zarządzanie terminami**  >  **system**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="Szablony terminów systemowych.":::

Aby utworzyć nowy szablon warunku niestandardowego, wykonaj następujące czynności:

1. Wybierz **słownik** z menu po lewej stronie.
1. Wybierz pozycję **Zarządzaj**  >    >  **szablonami terminów niestandardowy nowy termin**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="screate nowy szablon warunków niestandardowych.":::

Na ekranie **nowy termin szablonu** wykonaj następujące czynności:

1. Wprowadź **nazwę szablonu**: `Sensitivity level` .
1. Wprowadź żądany opis, taki jak `Indicates the level of sensitivity for this data.`
1. Wybierz pozycję **+ Nowy atrybut** , aby otworzyć okno dialogowe umożliwiające dodanie atrybutów.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="Początek ekranu szablonu nowego terminu.":::

1. Na ekranie **Nowy atrybut** wprowadź następujące parametry:

   |Ustawienie|Sugerowana wartość|
   |---------|-----------|
   |Nazwa atrybutu|informacje poufne|
   |Typ pola|Przyjmij|Pojedynczy wybór|
   |Oznacz jako wymagane|zaznacz to pole wyboru.|
   |+ Dodaj wybór| Dodaj dwie opcje. "Yes" i "No".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Dodaj nowy atrybut.":::

1. Powtórz poprzednie kroki, aby dodać inny atrybut o nazwie `can be shared externally` . Po dodaniu obu atrybutów, na koniec wybierz pozycję **Utwórz**.

## <a name="import-terms-from-a-template"></a>Importuj warunki z szablonu

Następnie należy zaimportować nowy termin przy użyciu szablonu **poziomu czułości** , który został utworzony. 

1. Na ekranie **warunki słownika** wybierz pozycję **Importuj warunki**.

1. Wybierz pozycję **poziom czułości** na ekranie **Importuj warunki** . Wybierz opcję **Kontynuuj**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Wybierz pozycję poziom czułości.":::

1. Szablon terminu dla **poziomu czułości** zawiera domyślne atrybuty systemowe, a także nowe dodane atrybuty: `can be shared externally` i `is sensitive information` . Wybierz pozycję **Pobierz przykład. Plik CSV** .

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Pobierz przykładowy plik CSV.":::

1. Plik szablonu jest pobierany, aby można było edytować i przekazywać nowy termin słownika przy użyciu atrybutów klienta. Otwórz pobrany plik CSV. Dodaj nowe warunki i ich odpowiednie wartości jako nowe wiersze w pliku CSV.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Utwórz termin w pliku CSV.":::

   Wszystkie terminy wymienione w kolumnie **powiązane warunki** lub **synonimy** , które jeszcze nie istnieją, zostaną utworzone podczas przekazywania pliku. Zostaną one utworzone przy użyciu **domyślnego szablonu systemowego** .

1. Aby przekazać plik, Wróć do ekranu **Importuj warunki** i wybierz pozycję **Przeglądaj** obok **opcji Zakończ. Plik CSV do przekazania** . W otwartym oknie dialogowym Wybierz plik, a następnie wybierz przycisk **OK**.

1. Nowe warunki są teraz wyświetlane na ekranie **warunki słownika** . Aby wyświetlić szczegółowe informacje na temat nowych warunków, kliknij nazwę terminu na liście.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz warunki słownika.
> * Dodaj warunki słownika do elementu zawartości.
> * Importuj warunki słownika.

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat różnych szczegółowych informacji o wykazie.

> [!div class="nextstepaction"]
> [Informacje o usłudze Azure kontrolą](concept-insights.md)
