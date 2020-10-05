---
title: Klonowanie notesu Jupyter z usługi GitHub przy użyciu wersji zapoznawczej Azure Notebooks
description: Szybko Sklonuj Notes Jupyter z repozytorium GitHub i uruchom go na koncie Azure Notebooks.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424481"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Szybki Start: klonowanie notesu w programie Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ten przewodnik Szybki Start umożliwia skopiowanie notesu Jupyter przechowywanego w usłudze GitHub na konto Azure Notebooks. 

Repozytoria GitHub zapewniają magazyn i kontrolę wersji dla notesów Jupyter. Współpracownicy utrzymują lokalne kopie repozytoriów i uruchamiają notesy z tych kopii. Klonowanie notesu Jupyter z usługi GitHub do konta Azure Notebooks tworzy niezależną kopię notesu. Zmiany są przechowywane tylko na koncie Azure Notebooks i nie mają wpływu na oryginalne repozytorium GitHub. 

Ponieważ klon Azure Notebooks znajduje się w chmurze, możesz udostępnić go współpracownikom, którzy nie muszą tworzyć żadnych kopii lokalnych ani instalować Jupyter na swoich komputerach. Można również sklonować Notes po prostu jako punkt wyjścia dla projektu własnych lub do uzyskiwania plików danych. 

## <a name="prerequisites"></a>Wymagania wstępne
Brak.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów Cognitive Services platformy Azure

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz przycisk strzałki w górę (skrót klawiaturowy: U; przycisk pojawia się jako **Przekaż repozytorium GitHub** , gdy okno przeglądarki jest dostatecznie szerokie):

    ![Prześlij polecenie GitHub repozytorium na stronie Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. W wyświetlonym **repozytorium GitHub** wpisz lub ustaw następujące szczegóły, a następnie wybierz pozycję **Importuj**:

   - **Repozytorium GitHub**: Microsoft/poznawcze-Services-notesy (Ta nazwa służy do klonowania notesów Jupyter dla platformy Cognitive Services Azure w systemie [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) ).
   - **Klonuj cyklicznie**: (wyczyszczone)
   - **Nazwa projektu**: klon Cognitive Services
   - **Identyfikator projektu**: poznawcze — usługi — klonowanie
   - **Publiczny**: (wyczyszczone)

     ![Przekazanie okna podręcznego repozytorium GitHub w celu zebrania informacji o repozytorium](media/quickstarts/upload-github-repo-popup.png)

1. Poczekaj, aż zakończy się proces; klonowanie repozytorium może potrwać kilka minut.

1. Po zakończeniu klonowania Azure Notebooks przenosi do nowego projektu, w którym można zobaczyć kopie wszystkich plików.

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="Widok ukończonego klonu." lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>Udostępnianie notesu

1. Aby udostępnić kopię sklonowanego projektu, użyj kontrolki **Udostępnij** lub Uzyskaj link, uzyskaj kod HTML lub promocji, który zawiera link, lub Utwórz wiadomość e-mail z linkiem:

    ![Project Share — polecenie](media/quickstarts/share-project-command.png)

1. Ponieważ opcja **publiczna** została wyczyszczona podczas klonowania projektu, klon jest prywatny. Aby utworzyć kopię publiczną, wybierz pozycję **Ustawienia projektu**, ustaw opcję **projekt publiczny** w menu podręcznym, a następnie wybierz pozycję **Zapisz**.

1. Wybierz Notes w projekcie, aby go uruchomić. Każdy Notes w repozytorium Cognitive Services platformy Azure, na przykład, jest własnym przewodnikiem Szybki Start. Poniższy obraz przedstawia wynik użycia notesu BingImageSearchAPI, po dodaniu klucza subskrypcji interfejsu API Cognitive Services i zmianie okresu wyszukiwania "Puppies" na "Bunnies":

    ![Uruchamianie notesu Jupyter sklonowanego z usługi GitHub](media/quickstarts/clone-notebook-result.png)

1. Po zakończeniu pracy z notesem wybierz pozycję **plik**  >  **Zamknij i zatrzymywanie** , aby zamknąć Notes i jego okno przeglądarki.

1. Aby udostępnić poszczególne notesy w projekcie, kliknij prawym przyciskiem myszy Notes i wybierz polecenie **Kopiuj link** (skrót klawiaturowy: t):

    ![Polecenie menu kontekstowego umożliwiające skopiowanie linku do pojedynczego notesu](media/quickstarts/copy-link-to-individual-notebook.png)

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Akcja domyślna, **Uruchom** (skrót klawiaturowy: r), wyświetla tylko zawartość pliku i nie zezwala na edytowanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
