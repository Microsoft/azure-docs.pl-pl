---
title: Importowanie i eksportowanie danych przy użyciu projektów z podglądem Azure Notebooks
description: Dowiedz się, jak przenieść dane do projektu Azure Notebooks w wersji zapoznawczej ze źródeł zewnętrznych oraz jak eksportować dane z projektu.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834118"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Pracuj z plikami danych w projektach Azure Notebooks w wersji zapoznawczej

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Dane to Lifeblood wielu notesów Jupyter, szczególnie dla notesów używanych do analizy danych. Za pomocą Azure Notebooks można łatwo importować z różnych źródeł do projektu, a następnie używać tych danych z notesów. Notesy mogą również generować dane przechowywane w projekcie, które następnie można pobrać do użytku w innym miejscu.

Menu **dane** w działającym notesie udostępnia również polecenia **przekazywania** i **pobierania** , które pracują z plikami w projekcie, a także plikami tymczasowymi bieżącej sesji notesu.

Możesz również użyć kodu w notesie, aby uzyskać bezpośredni dostęp do różnych źródeł danych, w tym plików w projekcie. Możesz również uzyskać dostęp do dowolnych danych za pomocą poleceń w komórce kodu. Ponieważ takie dane są przechowywane w zmiennych w ramach sesji notesu, nie zostaną zapisane w projekcie, chyba że zostanie użyty kod w celu wygenerowania plików projektu.

Praca z kodem w danych jest Najlepsza w działającym notesie: w tym celu zapoznaj się z artykułem [pobieranie do danych w Azure Notebooks przykładowym notesie](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Pozostała część tego artykułu zawiera szczegółowe informacje o operacjach plików na poziomie projektu.

## <a name="import-data"></a>Importowanie danych

Pliki można przenieść do projektu z pulpitu nawigacyjnego projektu lub w działającym notesie przy użyciu menu **dane** lub polecenia, takiego jak `curl` .

### <a name="import-files-from-the-project-dashboard"></a>Importowanie plików z pulpitu nawigacyjnego projektu

1. W projekcie przejdź do folderu, w którym chcesz zaimportować pliki.

1. Wybierz polecenie **Przekaż** , a następnie **z adresu URL** lub **z komputera** i projektu niezbędne szczegóły dotyczące danych, które chcesz zaimportować:

   - Adres **URL**: wprowadź adres źródłowy w polu **adres URL pliku** i nazwę pliku do przypisania do notesu w projekcie w polu **Nazwa pliku** . Następnie wybierz pozycję **+ Dodaj plik** , aby dodać adres URL do listy przekazywania. Powtórz ten proces dla dowolnych dodatkowych adresów URL, a następnie wybierz pozycję **gotowe**.

     ![Okno podręczne przekazywania z adresu URL](media/quickstarts/upload-from-url-popup.png)

   - **Z komputera**: Przeciągnij i upuść pliki do okna podręcznego lub wybierz pozycję **Wybierz pliki**, a następnie wyszukaj i wybierz pliki danych do zaimportowania. Możesz porzucić lub wybrać dowolną liczbę plików, dowolnego typu i formatu, ponieważ jest ona do kodu w notesie, aby otworzyć plik i przeanalizować jego dane.

     ![Okno podręczne przekazywania z komputera](media/quickstarts/upload-from-computer-popup.png)

1. Po zaimportowaniu pliki są wyświetlane na pulpicie nawigacyjnym projektu i można uzyskać do nich dostęp w kodzie notesu przy użyciu względnych nazw ścieżek do folderu zawierającego.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importowanie plików z menu plik w notesie

1. W działającym notesie wybierz polecenie **File**  >  **Przekaż** plik:

    ![Polecenie menu przekazywania plików w notesie](media/file-menu-upload.png)

1. W otwartym oknie dialogowym przejdź do i wybierz pliki, które chcesz przekazać. Można wybrać dowolną liczbę plików dowolnego typu. Po zakończeniu wybierz pozycję **Otwórz** .

1. W wyświetlonym oknie podręcznym **Stan przekazywania** wybierz **folder docelowy** z listy rozwijanej:

    - Folder sesji ( *~/* ): przekazuje pliki do bieżącej sesji notesu, ale nie tworzy plików w projekcie. Folder sesji jest równorzędny do folderu projektu, ale nie jest utrwalany po zakończeniu sesji. Aby uzyskać dostęp do plików sesji w kodzie, należy prefiksować nazwy plików ze ścieżką względną *. /*.

        Korzystanie z folderu sesji jest przydatne w przypadku eksperymentowania i pozwala uniknąć powstawania projektu przy użyciu plików, które mogą lub nie muszą być długoterminowe. Można również przekazać pliki do folderu sesji, które mają identyczne nazwy, do plików w projekcie bez powodowania konfliktów i bez konieczności zmiany nazwy plików. Załóżmy na przykład, że masz już jedną wersję *data.csv* w projekcie, ale chcesz eksperymentować z inną wersją *data.csv*. Przekazując plik do folderu sesji, można uruchomić Notes przy użyciu danych w przekazanym pliku (odwołując się do niego w kodzie za pomocą *.. /data.csv*), a nie dane w pliku projektu.

    - Folder projektu (*/Project*): przekazuje pliki do projektu, w którym można uzyskać do nich dostęp przy użyciu względnych nazw ścieżek w kodzie. Przekazywanie pliku do tego folderu jest takie samo jak przekazywanie pliku na pulpicie nawigacyjnym projektu. Plik jest zapisywany w projekcie i jest dostępny w późniejszych sesjach.

        Przekazywanie kończy się niepowodzeniem, jeśli próbujesz przekazać plik o tej samej nazwie, która już istnieje w projekcie. Aby zastąpić plik, Przekaż nowy plik z pulpitu nawigacyjnego projektu, który zapewnia opcję zastąpienia.

1. Wybierz pozycję **Rozpocznij przekazywanie** , aby ukończyć proces.

### <a name="create-or-import-files-using-commands"></a>Tworzenie lub importowanie plików przy użyciu poleceń

Możesz użyć poleceń w obrębie terminalu lub w komórce kodu w języku Python do tworzenia plików w folderach projektu i sesji. Na przykład polecenia takie jak `curl` i `wget` pobierają pliki bezpośrednio z Internetu.

Aby pobrać pliki w terminalu, wybierz polecenie **Terminal** na pulpicie nawigacyjnym projektu, a następnie wprowadź odpowiednie polecenia:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

W przypadku korzystania z komórki kodu języka Python w notesie należy wykonać prefiks poleceń z poleceniem `!` .

Folder projektu jest folderem domyślnym, więc określenie docelowej nazwy pliku, np. *oil_price.csv* tworzy plik w projekcie. Aby utworzyć plik sesji, poprzedź nazwę prefiksem *... /* AS w *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Tworzenie plików w kodzie

W przypadku korzystania z kodu, który tworzy plik, taki jak `write_csv` Funkcja Pandas, nazwy ścieżek są zawsze względne w stosunku do folderu projektu. Za pomocą *.. /* tworzy plik sesji, który zostaje odrzucony, gdy Notes zostanie zatrzymany i zamknięty.

## <a name="export-files"></a>Eksportuj pliki

Dane można eksportować z pulpitu nawigacyjnego projektu lub z poziomu notesu.

## <a name="export-files-from-the-project-dashboard"></a>Eksportowanie plików z pulpitu nawigacyjnego projektu

Na pulpicie nawigacyjnym projektu kliknij prawym przyciskiem myszy plik i wybierz polecenie **Pobierz**:

![Polecenie pobierania w menu kontekstowym elementu projektu](media/download-command.png)

Możesz również wybrać plik i użyć polecenia **Pobierz** (skrót klawiaturowy: d) na pulpicie nawigacyjnym:

![Pobierz polecenie paska narzędzi na pulpicie nawigacyjnym projektu](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Eksportowanie plików z menu dane w notesie

1. Wybierz **File**  >  polecenie menu**Pobierz** plik:

    ![Polecenie menu pobierania danych w notesie](media/file-menu-download.png)

1. Zostanie wyświetlone okno podręczne pokazujące foldery w sesji; folder *projektu* zawiera pliki projektu:

    ![Pobieranie danych — menu podręczne poleceń, w których wybierasz pliki i foldery](media/file-menu-download-popup.png)

1. Zaznacz pola z lewej strony plików i folderów, które chcesz pobrać, a następnie wybierz pozycję **Pobierz wybrane**.

1. Notes przygotowuje jeden plik *zip* zawierający wybrane pliki, które następnie zapisuje się w zwykły sposób z poziomu przeglądarki. Notes tworzy plik *zip* nawet po pobraniu pojedynczego pliku.

## <a name="next-steps"></a>Następne kroki

- [Dostęp do danych w chmurze w notesie](access-data-resources-jupyter-notebooks.md)
