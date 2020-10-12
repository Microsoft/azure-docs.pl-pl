---
title: Tworzenie i klonowanie notesów Jupyter — wersja zapoznawcza Azure Notebooks
description: Projekty Azure Notebooks w wersji zapoznawczej zarządzają kolekcją notesów i powiązanych plików, które można utworzyć nowe lub klonować z innego źródła.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: 4a51f9b12ca24d16858b41357627ff26d233357f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86181504"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Tworzenie i klonowanie projektów w podglądzie Azure Notebooks

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks organizuje notesy Jupyter i powiązane pliki do grup logicznych nazywanych *projektami*. Najpierw tworzysz projekt jako kontener, a następnie utworzysz lub Sklonowano jeden lub więcej notesów w folderze obok innych plików projektu. (Ten proces jest zaprezentowany w [samouczku](tutorial-create-run-jupyter-notebook.md)).

Projekt utrzymuje również metadane i inne ustawienia konfiguracji, które mają wpływ na serwer, na którym są uruchamiane notesy, w tym niestandardowe kroki instalacji i instalację pakietu. Aby uzyskać więcej informacji, zobacz [Zarządzanie projektami i Konfigurowanie](configure-manage-azure-notebooks-projects.md)ich.

## <a name="use-the-my-projects-dashboard"></a>Korzystanie z pulpitu nawigacyjnego moje projekty

Pulpit nawigacyjny **Moje projekty** w programie `https://notebooks.azure.com/<userID>/projects` to miejsce, w którym można wyświetlać projekty i zarządzać nimi:

[![Pulpit nawigacyjny moje projekty w Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Co można zrobić na pulpicie nawigacyjnym, zależy od tego, czy użytkownik jest zalogowany przy użyciu konta, które jest właścicielem identyfikatora użytkownika:

| Polecenie | Dostępne dla | Opis |
| --- | --- | --- |
| **Wykonane** | Właściciel | Uruchamia serwer projektu i otwiera folder projektu w Jupyter. (Najczęściej możesz przejść do folderu projektu, a następnie uruchomić Notes z tego miejsca). |
| **Pobieranie** | Dowolny użytkownik | Pobiera kopię wybranego projektu jako plik ZIP. |
| **Share** | Dowolny użytkownik | Wyświetla okno podręczne udostępniania, za pomocą którego można uzyskać adres URL wybranego projektu, udostępnić do mediów społecznościowych, wysłać wiadomość e-mail z adresem URL oraz uzyskać kod HTML lub promocji dla programu za pomocą wskaźnika "Uruchom Notes" (zobacz temat [Uzyskiwanie wskaźnika uruchamiania](#obtain-a-launch-badge)) przy użyciu adresu URL. |
| **Usuwanie** | Właściciel | Usuwa wybrany projekt. Tej operacji nie można cofnąć. |
| **Terminal** | Właściciel | Uruchamia serwer Project Server, a następnie otwiera nowe okno przeglądarki z terminalem bash dla tego serwera. |
| **+ Nowy projekt** | Właściciel | Tworzy nowy projekt. Zobacz [Tworzenie nowego projektu](#create-a-new-project). |
| **Przekaż repozytorium GitHub** | Właściciel | Importuje projekt z usługi GitHub. [Importuj projekt z usługi GitHub](#import-a-project-from-github). |
| **Klonowanie** | Dowolny użytkownik | Kopiuje wybrany projekt do własnego konta. Zostanie wyświetlony komunikat z prośbą o zalogowanie się, jeśli nie jest jeszcze. Zobacz [klonowanie projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Uzyskaj wskaźnik uruchamiania

W przypadku użycia polecenia **Udostępnij** i wybrania karty **osadzania** można skopiować kod HTML lub przeznaczenie, które tworzy znaczek "Uruchom Notes":

![Uruchom wskaźnik notesu](https://notebooks.azure.com/launch.png)

Jeśli nie masz projektu Azure Notebooks, możesz utworzyć link, który Sklonuj z usługi GitHub bezpośrednio przy użyciu następujących szablonów, podstawiając odpowiednie nazwy nazwy użytkownika i repozytorium:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przypadku korzystania z polecenia **+ Nowy projekt** Azure Notebooks wyświetla okno podręczne **Utwórz nowy projekt** . W tym oknie podręcznym Wprowadź poniższe informacje, a następnie wybierz pozycję **Utwórz**:

| Pole | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa projektu, który Azure Notebooks używany do wyświetlania. Na przykład "mój projekt notesu". |
| Identyfikator projektu | Niestandardowy identyfikator, który jest częścią adresu URL, który jest używany do udostępniania projektu (formularz to `https://notebooks.azure.com/<user_id>/projects/<project_id>` ). Ten identyfikator może korzystać tylko z liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zarezerwowanym identyfikatorem projektu](#reserved-project-ids). Jeśli nie masz pewności, co należy używać, Wspólna konwencja polega na użyciu małych wersji nazwy projektu, gdzie spacje są włączane do łączników, takich jak "My-Notes-Project" (w razie potrzeby można je dopasować do limitu długości). |
| Public | Jeśli ta wartość jest ustawiona, umożliwia każdej osobie z linkiem dostęp do projektu. Podczas tworzenia projektu prywatnego Usuń zaznaczenie tej opcji. |
| Zainicjuj ten projekt przy użyciu pliku Readme | Jeśli ta wartość jest ustawiona, program tworzy domyślny plik *README.MD* w projekcie. Plik *README.MD* to miejsce, w którym podano dokumentację projektu, w razie potrzeby. |

### <a name="reserved-project-ids"></a>Zarezerwowane identyfikatory projektów

Następujące słowa zastrzeżone nie mogą być używane przez siebie jako identyfikatory projektu. Te słowa zastrzeżone mogą jednak być używane jako część dłuższego identyfikatora projektu.

- informacje
- account
- administracja
- api
- blog
- klasa
- zawartość
- pulpit nawigacyjny
- odkrywanie
- FAQ
- Pomoc
- html
- strona główna
- import
- biblioteka
- zarządzanie
- new
- notebook
- notesy
- pdf
- preview
- wpisaną
- profil
- search
- status
- pomoc techniczna
- test

Jeśli spróbujesz użyć jednego z tych słów jako identyfikatora projektu, okna podręczne **Utwórz nowy projekt** i **Ustawienia projektu** wskazują, "identyfikator biblioteki jest zarezerwowanym identyfikatorem".

Ponieważ identyfikator projektu jest również częścią adresu URL projektu, oprogramowanie usługi AD Blocker może blokować użycie niektórych słów kluczowych, takich jak "anons". W takich przypadkach należy użyć innego wyrazu w IDENTYFIKATORze projektu.

## <a name="import-a-project-from-github"></a>Importowanie projektu z usługi GitHub

Możesz łatwo zaimportować cały publiczny repozytorium GitHub jako projekt obejmujący wszystkie pliki danych i *README.MD* . Użyj polecenia **Przekaż repozytorium GitHub** , podaj następujące informacje w oknie podręcznym, a następnie wybierz pozycję **Importuj**:

| Pole | Opis |
| --- | --- |
| Repozytorium GitHub | Nazwa repozytorium źródłowego w github.com. Na przykład aby sklonować notesy Jupyter dla platformy Azure Cognitive Services w [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) systemie, wprowadź "Microsoft/poznawcze-Services-Notess".  |
| Klonuj cyklicznie | Repozytoria GitHub mogą zawierać wiele repozytoriów podrzędnych. Ustaw tę opcję, jeśli chcesz sklonować repozytorium nadrzędne i wszystkie jego elementy podrzędne. Ponieważ istnieje możliwość, aby repozytorium miało wiele elementów podrzędnych, należy pozostawić tę opcję niezbędną, chyba że wiadomo, że jest ona potrzebna. |
| Project name (Nazwa projektu) | Przyjazna nazwa projektu, który Azure Notebooks używany do wyświetlania. |
| Identyfikator projektu | Niestandardowy identyfikator, który jest częścią adresu URL, który jest używany do udostępniania projektu (formularz to `https://notebooks.azure.com/<user_id>/projects/<project_id>` ). Ten identyfikator może korzystać tylko z liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zarezerwowanym identyfikatorem projektu](#reserved-project-ids). Jeśli nie masz pewności, co należy używać, Wspólna konwencja polega na użyciu małych wersji nazwy projektu, gdzie spacje są włączane do łączników, takich jak "My-Notes-Project" (w razie potrzeby można je dopasować do limitu długości). |
| Public | Jeśli ta wartość jest ustawiona, umożliwia każdej osobie z linkiem dostęp do projektu. Podczas tworzenia projektu prywatnego Usuń zaznaczenie tej opcji. |

Importowanie repozytorium z usługi GitHub również importuje jego historię. Możesz użyć standardowych poleceń git z terminalu, aby zatwierdzić nowe zmiany, ściągnąć zmiany z usługi GitHub i tak dalej.

## <a name="clone-a-project"></a>Klonowanie projektu

Klonowanie powoduje utworzenie kopii istniejącego projektu na własnym koncie, gdzie można uruchomić i zmodyfikować dowolny Notes lub inny plik w projekcie. Klonowanie może również służyć do wykonywania kopii własnych projektów, w których można eksperymentować lub wykonywać inne działania bez zakłócania oryginalnego projektu.

Aby sklonować projekt:

1. Na pulpicie nawigacyjnym **Moje projekty** kliknij prawym przyciskiem myszy żądany projekt i wybierz polecenie **Klonuj** (skrót klawiaturowy: c).

    ![Polecenie klonowania w menu kontekstowym projektu](media/clone-command.png)

1. W oknie podręcznym **klonowanie projektu** wprowadź nazwę i Identyfikator klonu i określ, czy klon jest publiczny. Te ustawienia są takie same jak dla [nowego projektu](#create-a-new-project).

    ![Okno podręczne klonowania projektu](media/clone-project.png)

1. Po wybraniu przycisku **klonuj** Azure Notebooks nawiguje bezpośrednio do kopii.

## <a name="next-steps"></a>Następne kroki

- [Explore sample notebooks (Eksplorowanie przykładowych notesów)](azure-notebooks-samples.md)
- [Instrukcje: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instrukcje: Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Instrukcje: prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
- [Instrukcje: korzystanie z plików danych](work-with-project-data-files.md)
- [Instrukcje: dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Instrukcje: korzystanie z Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
