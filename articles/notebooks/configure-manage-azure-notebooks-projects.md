---
title: Konfigurowanie i zarządzanie Azure Notebooks wersja zapoznawcza
description: Dowiedz się, jak zarządzać metadanymi projektu, plikami projektu, środowiskiem projektu i instrukcjami konfiguracji za pośrednictwem interfejsu użytkownika Azure Notebooks i bezpośredniego dostępu do terminalu.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: devx-track-python
ms.openlocfilehash: 7674c2151922d26e069b5cd285cb311d7a18fa98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852807"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" /> Zarządzanie projektami i konfigurowanie ich w programie Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Projekt w programie Azure Notebooks Preview jest zasadniczo konfiguracją podstawowej maszyny wirtualnej z systemem Linux, w której działają notesy Jupyter oraz folder plików i metadane opisowe. 

Pulpit nawigacyjny projektu w Azure Notebooks umożliwia zarządzanie plikami i w inny sposób Konfigurowanie charakterystyki projektu:

- Warstwa obliczeniowa, na której działa projekt, który może być warstwą bezpłatną lub maszyną wirtualną platformy Azure.
- Metadane projektu, w tym nazwa, opis, identyfikator, który jest używany podczas udostępniania projektu i czy projekt jest publiczny czy prywatny.
- Notes, dane i inne pliki projektu, którymi można zarządzać jak każdy inny system plików.
- Środowisko projektu, którymi można zarządzać za pomocą skryptów uruchamiania lub bezpośrednio za pomocą terminalu.
- Dzienniki, do których można uzyskać dostęp za pomocą terminalu.

> [!Note]
> Opisane w tym miejscu funkcje zarządzania i konfiguracji są dostępne tylko dla właściciela projektu, który początkowo utworzył projekt. Można jednak sklonować projekt na własne konto, w takim przypadku staje się właścicielem i może skonfigurować projekt zgodnie z potrzebami.

Azure Notebooks uruchamia podstawową maszynę wirtualną przy każdym uruchomieniu notesu lub innego pliku. Serwer automatycznie zapisuje pliki i zamyka się po 60 minutach braku aktywności. Możesz również zatrzymać serwer w dowolnym momencie za pomocą polecenia **Shutdown** (skrót klawiaturowy: h).

## <a name="compute-tier"></a>Warstwa zasobów obliczeniowych

Domyślnie projekty są uruchamiane w **bezpłatnej warstwie obliczeniowej** , co jest ograniczone do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciu. Można pominąć te ograniczenia i zwiększyć moc obliczeniową przy użyciu innej maszyny wirtualnej, która została zainicjowana w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [jak używać analizy danych Virtual Machines](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Edytowanie metadanych projektu

Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**, a następnie wybierz kartę **informacje** , która zawiera metadane projektu, zgodnie z opisem w poniższej tabeli. Metadane projektu można zmienić w dowolnym momencie.

| Ustawienie | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa projektu, który Azure Notebooks używany do wyświetlania. Na przykład "Hello world w języku Python". |
| Identyfikator projektu | Niestandardowy identyfikator, który jest częścią adresu URL, który jest używany do udostępniania projektu. Ten identyfikator może korzystać tylko z liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zarezerwowanym identyfikatorem projektu](create-clone-jupyter-notebooks.md#reserved-project-ids). Jeśli nie masz pewności, co należy używać, Wspólna konwencja polega na użyciu małych wersji nazwy projektu, gdzie spacje są włączane do łączników, takich jak "My-Notes-Project" (w razie potrzeby można je dopasować do limitu długości). |
| Projekt publiczny | Jeśli ta wartość jest ustawiona, umożliwia każdej osobie z linkiem dostęp do projektu. Podczas tworzenia projektu prywatnego Usuń zaznaczenie tej opcji. |
| Ukryj klony | Jeśli ta wartość jest ustawiona, inni użytkownicy nie będą widzieć listy klonów, które zostały wykonane dla tego projektu. Ukrywanie klonów jest przydatne w przypadku projektów, które są współużytkowane z wieloma osobami, które nie są częścią tej samej organizacji, na przykład w przypadku korzystania z notesu do nauczania klasy. |

> [!Important]
>
> Zmiana identyfikatora projektu unieważnia wszystkie linki do projektu, który można wcześniej udostępnić.

## <a name="manage-project-files"></a>Zarządzanie plikami projektu

Pulpit nawigacyjny projektu pokazuje zawartość systemu folderu projektu. Aby zarządzać tymi plikami, można użyć różnych poleceń.

### <a name="create-new-files-and-folders"></a>Tworzenie nowych plików i folderów

**+ Nowe** polecenie (skrót klawiaturowy: n) tworzy nowe pliki lub foldery. Korzystając z polecenia, najpierw wybierz typ elementu do utworzenia:

| Typ elementu | Opis | Zachowanie polecenia |
| --- | --- | --- |
| **Notes** | Notes Jupyter | Wyświetla okno podręczne, w którym można określić nazwę pliku i język notesu. |
| **Folder** | Podfolder | Tworzy pole edycji na liście plików projektu, do którego zostanie wprowadzona nazwa folderu. |
| **Pusty plik** | Plik, w którym można przechowywać dowolną zawartość, taką jak tekst, dane itp. | Tworzy pole edycji na liście plików projektu, w którym należy wprowadzić nazwę pliku. |
| **Znaczniki języka Markdown** | Plik promocji. | Tworzy pole edycji na liście plików projektu, w którym należy wprowadzić nazwę pliku. |

### <a name="upload-files"></a>Przekazywanie plików

Polecenie **upload** oferuje dwie opcje importowania danych z innych lokalizacji: **od adresu URL** i **z komputera**. Aby uzyskać więcej informacji, zobacz artykuł [Pracuj z plikami danych w projektach notesów platformy Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Wybieranie poleceń specyficznych dla pliku

Każdy element na liście plików projektu zawiera polecenia za pomocą menu kontekstowego po kliknięciu prawym przyciskiem myszy:

![Polecenia w menu kontekstowym pliku](media/project-file-commands.png)

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Uruchom | r (lub kliknij) | Uruchamia plik notesu. Inne typy plików są otwierane do wyświetlania.  |
| Kopiuj link | Y | Kopiuje link do pliku do Schowka. |
| Uruchom w laboratorium Jupyter Lab | j | Uruchamia Notes w JupyterLab, który jest większym interfejsem zorientowanym na dewelopera niż Jupyter zwykle. |
| Wersja zapoznawcza | p | Otwiera Podgląd HTML pliku; w przypadku notesów Podgląd jest renderowaniem tylko do odczytu w notesie. Aby uzyskać więcej informacji, zobacz sekcję [wersja zapoznawcza](#preview) . |
| Edytuj plik | mogę | Otwiera plik do edycji. |
| Pobierz | d | Pobiera plik zip, który zawiera plik lub zawartość folderu. |
| Zmień nazwę | a | Żąda nowej nazwy dla pliku lub folderu. |
| Usuwanie | x | Monituje o potwierdzenie, a następnie trwale usuwa plik z projektu. Usunięć nie można cofnąć. |
| Move | m | Przenosi plik do innego folderu w tym samym projekcie. |

#### <a name="preview"></a>Wersja zapoznawcza

Wersja zapoznawcza pliku lub notesu jest widokiem zawartości tylko do odczytu. uruchomione komórki notesu są wyłączone. Wersja zapoznawcza jest pokazywana każdemu użytkownikowi, który ma link do pliku lub notesu, ale nie zalogował się do Azure Notebooks. Po zalogowaniu użytkownik może sklonować Notes na własne konto lub pobrać Notes na komputer lokalny.

Strona Podgląd obsługuje kilka poleceń paska narzędzi ze skrótami klawiaturowymi:

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Udostępnij | s | Wyświetla okno podręczne udostępniania, z którego można uzyskać link, udostępnić je do mediów społecznościowych, uzyskać kod HTML do osadzenia i wysłać wiadomość e-mail. |
| Klonowanie | c  | Sklonuj Notes na swoje konto. |
| Uruchom | r | Uruchamia Notes, jeśli jest to możliwe. |
| Pobierz | d | Pobiera kopię notesu. |

## <a name="configure-the-project-environment"></a>Konfigurowanie środowiska projektu

Istnieją trzy sposoby konfigurowania środowiska podstawowej maszyny wirtualnej, w której uruchomione są notesy:

- Uwzględnij jednorazowy skrypt inicjujący
- Użyj ustawień środowiska projektu, aby określić kroki instalacji
- Uzyskaj dostęp do maszyny wirtualnej za pomocą terminalu.

Wszystkie formy konfiguracji projektu są stosowane za każdym razem, gdy maszyna wirtualna jest uruchomiona, i w ten sposób ma wpływ na wszystkie notesy w ramach projektu.

### <a name="one-time-initialization-script"></a>Skrypt inicjalizacji jednorazowej

Pierwszy raz Azure Notebooks tworzy serwer dla projektu, szuka pliku w projekcie o nazwie *aznbsetup.sh*. Jeśli ten plik jest obecny, Azure Notebooks go uruchamiać. Dane wyjściowe skryptu są przechowywane w folderze projektu jako *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Kroki konfiguracji środowiska

Możesz użyć ustawień środowiska projektu, aby utworzyć poszczególne kroki konfigurowania środowiska.

Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**, a następnie wybierz kartę **środowisko** , w której można dodawać, usuwać i modyfikować kroki instalacji projektu:

![Okno podręczne ustawień projektu z wybraną kartą środowisko](media/project-settings-environment-steps.png)

Aby dodać krok, najpierw wybierz pozycję **+ Dodaj**, a następnie wybierz typ kroku z listy rozwijanej **operacja** :

![Selektor operacji dla nowego kroku konfiguracji środowiska](media/project-settings-environment-details.png)

Informacje, które następnie projekt zależą od wybranego typu operacji:

- **Requirements.txt**: na drugiej liście rozwijanej wybierz plik *requirements.txt* , który znajduje się już w projekcie. Następnie wybierz wersję języka Python z trzeciej listy rozwijanej, która zostanie wyświetlona. Przy użyciu pliku *requirements.txt* Azure Notebooks uruchamiany `pip install -r` z plikiem *requirements.txt* podczas uruchamiania serwera notesu. Nie musisz jawnie instalować pakietów z poziomu notesu.

- **Skrypt powłoki**: na drugiej liście rozwijanej wybierz skrypt powłoki bash w projekcie (zazwyczaj plik z rozszerzeniem *. sh* ) zawierający wszystkie polecenia, które chcesz uruchomić w celu zainicjowania środowiska.

- **Środowisko. yml**: na drugiej liście rozwijanej wybierz plik Environments *. yml* dla projektów języka Python korzystających ze środowiska Conda.

   > [!WARNING]
   > Ponieważ jest to usługa w wersji zapoznawczej w ramach programowania, istnieje obecnie znany problem, w którym ustawienie nie zostanie `Environment.yml` zastosowane do projektu zgodnie z oczekiwaniami. Projekty i notesy Jupyter w programie nie ładują teraz określonego pliku środowiska.

Po zakończeniu dodawania kroków wybierz pozycję **Zapisz**.

### <a name="use-the-terminal"></a>Korzystanie z terminalu

Na pulpicie nawigacyjnym projektu polecenie **terminalu** otwiera terminal systemu Linux, który zapewnia bezpośredni dostęp do serwera. W ramach terminalu można pobierać dane, edytować pliki i zarządzać nimi, sprawdzać procesy, a nawet używać narzędzi takich jak VI i nano.

> [!Note]
> Jeśli masz skrypty uruchamiania w środowisku projektu, otwarcie terminalu może wyświetlić komunikat informujący o tym, że Instalator jest nadal w toku.

W terminalu można wydać wszystkie standardowe polecenia systemu Linux. Można również użyć `ls` w folderze głównym, aby zobaczyć różne środowiska, które istnieją na maszynie wirtualnej, takie jak *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*i *R*, wraz z folderem *projektu* zawierającym projekt:

![Terminal projektu w Azure Notebooks](media/project-terminal.png)

Aby mieć wpływ na określone środowisko, należy najpierw zmienić katalogi w tym folderze środowiska.

W przypadku środowisk Python można znaleźć `pip` i `conda` w folderze *bin* każdego środowiska. Można również używać aliasów wbudowanych dla środowisk:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Zmiany wprowadzone na serwerze dotyczą tylko bieżącej sesji, z wyjątkiem plików i folderów tworzonych w folderze *projektu* . Na przykład edytowanie pliku w folderze projektu jest utrwalane między sesjami, ale pakiety z `pip install` nie są.

> [!Note]
> W przypadku korzystania `python` z programu lub należy `python3` wywołać wersje języka Python zainstalowane w systemie, które nie są używane w notesach. Nie masz uprawnień do operacji takich jak `pip install` albo upewnij się, że używasz aliasów specyficznych dla wersji.

## <a name="access-notebook-logs"></a>Dostęp do dzienników notesu

Jeśli podczas uruchamiania notesu wystąpią problemy, dane wyjściowe z Jupyter są przechowywane w folderze o nazwie *. NB. log*. Dostęp do tych dzienników można uzyskać za pomocą polecenia **terminala** lub pulpitu nawigacyjnego projektu.

Zwykle, gdy uruchamiasz Jupyter lokalnie, być może został on uruchomiony z okna terminalu. W oknie terminalu są wyświetlane dane wyjściowe, takie jak stan jądra.

Aby wyświetlić dzienniki, użyj następującego polecenia w terminalu:

```bash
cat .nb.log
```

Możesz również użyć polecenia z komórki kodu w notesie w języku Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: współdziałanie z plikami danych projektu](work-with-project-data-files.md)
- [Dostęp do danych w chmurze w notesie](access-data-resources-jupyter-notebooks.md)
