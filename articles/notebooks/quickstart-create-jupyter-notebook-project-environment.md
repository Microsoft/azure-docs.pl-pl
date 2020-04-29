---
title: Tworzenie projektu Azure Notebooks w wersji zapoznawczej przy użyciu środowiska niestandardowego
description: Utwórz nowy projekt w Azure Notebooks wersji zapoznawczej, który jest skonfigurowany z określonym zestawem zainstalowanych pakietów i skryptów uruchamiania.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78196845"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Szybki Start: Tworzenie projektu za pomocą środowiska niestandardowego w programie Azure Notebooks Preview

Projekt w Azure Notebooks to zbiór plików, takich jak notesy, pliki danych, dokumentacja, obrazy i tak dalej, wraz ze środowiskiem, które można skonfigurować za pomocą określonych poleceń Instalatora. Definiując środowisko przy użyciu projektu, każda osoba, która klonuje projekt do własnego konta Azure Notebooks, zawiera wszystkie informacje potrzebne do odtworzenia niezbędnego środowiska.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Tworzenie projektu

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). przycisk może się pojawić tylko **+** wtedy, gdy okno przeglądarki jest wąskie:

    ![Polecenie nowego projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz**:

    - **Nazwa projektu**: projekt ze środowiskiem niestandardowym
    - **Identyfikator projektu**: projekt — środowisko niestandardowe
    - **Projekt publiczny**: (wyczyszczone)
    - **Utwórz element Readme.MD**: (wyczyszczony)

1. Po kilku chwilach Azure Notebooks przechodzi do nowego projektu. Dodaj Notes do projektu, wybierając listę rozwijaną **+ Nowy** (która może być wyświetlana tylko **+** jako), a następnie wybierając pozycję **Notes**.

1. Nadaj notesowi nazwę, np *. Custom Environment. ipynb*, wybierz opcję **Python 3,6** dla języka i wybierz pozycję **New (nowy**).

## <a name="add-a-custom-setup-step"></a>Dodawanie niestandardowego kroku instalacji

1. Na stronie projekt wybierz pozycję **Ustawienia projektu**.

    ![Polecenie Ustawienia projektu](media/quickstarts/project-settings-command.png)

1. W oknie podręcznym **Ustawienia projektu** wybierz kartę **środowisko** , a następnie w obszarze **kroki konfiguracji środowiska**wybierz pozycję **+ Dodaj**:

    ![Polecenie, aby dodać nowy krok konfiguracji środowiska](media/quickstarts/environment-add-command.png)

1. Polecenie **+ Add** tworzy krok zdefiniowany przez operację i plik docelowy, który jest wybierany z plików w projekcie. Obsługiwane są następujące operacje:

   | Operacja | Opis |
   | --- | --- |
   | Requirements. txt | Projekty języka Python definiują ich zależności w pliku Requirements. txt. Po wybraniu tej opcji należy wybrać odpowiedni plik z listy plików projektu, a także wybrać wersję języka Python w dodatkowych listach rozwijanych. W razie potrzeby wybierz **pozycję Anuluj** , aby powrócić do projektu, Przekaż lub Utwórz plik, a następnie wróć do karty**środowisko** **ustawień** > projektu i Utwórz nowy krok. W tym kroku jest uruchamiane automatyczne uruchamianie notesu w projekcie`pip install -r <file>` |
   | Skrypt powłoki | Użyj, aby wskazać skrypt powłoki bash (zazwyczaj plik z rozszerzeniem *. sh* ) zawierający wszystkie polecenia, które chcesz uruchomić w celu zainicjowania środowiska. |
   | Środowisko. yml | Projekt języka Python, który używa Conda do zarządzania środowiskiem, używa pliku Environments *. yml* do opisywania zależności. W przypadku tej opcji wybierz odpowiedni plik z listy plików projektu. |

   > [!WARNING]
   > Ponieważ jest to usługa w wersji zapoznawczej w ramach programowania, istnieje obecnie znany problem `Environment.yml` , w którym ustawienie nie zostanie zastosowane do projektu zgodnie z oczekiwaniami. Projekty i notesy Jupyter w programie nie ładują teraz określonego pliku środowiska.

1. Aby usunąć dowolny krok instalacji, wybierz **znak X** z prawej strony kroku.

1. Po zakończeniu wszystkich kroków instalacji wybierz pozycję **Zapisz**. (Wybierz pozycję **Anuluj** , aby odrzucić zmiany).

1. Aby przetestować środowisko, Utwórz i uruchom nowy Notes, a następnie utwórz komórkę kodu z instrukcjami, które są zależne od pakietu w środowisku, na przykład przy użyciu instrukcji języka `import` Python. Jeśli instrukcja się powiedzie, wymagany pakiet został pomyślnie zainstalowany w środowisku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie projektami i konfigurowanie ich w Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
