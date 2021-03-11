---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620182"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](../../how-to-create-immersive-reader.md) , aby rozpocząć konfigurację.  Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* Usługi [git](https://git-scm.com/).
* [Zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) i [PIP](https://docs.python.org/3/installing/index.html). Począwszy od języka Python 3,4, narzędzie PIP jest domyślnie dołączone do instalatorów binarnych języka Python.
* [Kolba](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) i [virtualenvwrapper — win dla systemu Windows](https://pypi.org/project/virtualenvwrapper-win/) lub [virtualenvwrapper dla OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* [Moduł żądania](https://pypi.org/project/requests/2.7.0/).
* IDE, takie jak [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania

Utwórz nowy plik o nazwie **ENV** i wklej do niego następujące nazwy i wartości. Podaj wartości podanych podczas tworzenia zasobu czytnika immersyjny.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Nie Zatwierdź tego pliku w kontroli źródła, ponieważ zawiera on wpisy tajne, które nie powinny być publiczne.

Zabezpiecz punkt końcowy interfejsu API **getimmersivereadertoken** za pomocą uwierzytelniania, na przykład [OAuth](https://oauth.net/2/). Uwierzytelnianie uniemożliwia nieautoryzowanym użytkownikom uzyskanie tokenów do użycia z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego samouczka.

## <a name="create-a-python-web-app-on-windows"></a>Tworzenie aplikacji sieci Web w języku Python w systemie Windows

Utwórz aplikację sieci Web w języku Python przy użyciu `flask` systemu Windows.

Zainstaluj narzędzie [git](https://git-scm.com/).

Po zainstalowaniu usługi git Otwórz wiersz polecenia i Sklonuj repozytorium git zestawu SDK czytnika immersyjny do folderu na komputerze.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Zainstaluj język [Python](https://www.python.org/downloads/).

Zaznacz pole wyboru **Dodaj język Python do ścieżki** .

![Okno dialogowe instalacji systemu Windows w języku Python — krok 1](../../media/pythoninstallone.jpg)

Dodaj **funkcje opcjonalne** , zaznaczając pola wyboru, a następnie wybierz przycisk **dalej**.

![Okno dialogowe instalacji systemu Windows w języku Python — krok 2](../../media/pythoninstalltwo.jpg)

Wybierz opcję **Instalacja niestandardowa** i Ustaw ścieżkę instalacji jako folder główny, na przykład `C:\Python37-32\` . Następnie wybierz pozycję **Zainstaluj**.

![Okno dialogowe instalacji systemu Windows w języku Python — krok 3](../../media/pythoninstallthree.jpg)

Po zakończeniu instalacji języka Python Otwórz wiersz polecenia i użyj go, `cd` Aby przejść do folderu skrypty języka Python.

```cmd
cd C:\Python37-32\Scripts
```

Zainstaluj kolbę.

```cmd
pip install flask
```

Zainstaluj Jinja2. Jest to w pełni funkcjonalny aparat szablonów dla języka Python.

```cmd
pip install jinja2
```

Zainstaluj virtualenv. To narzędzie tworzy izolowane środowiska języka Python.

```cmd
pip install virtualenv
```

Zainstaluj virtualenvwrapper-win. Pomysłem za virtualenvwrapper jest ułatwienie użycia virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Zainstaluj moduł żądania. Żądania to Apache2a licencjonowana Biblioteka HTTP, zapisywana w języku Python.

```cmd
pip install requests
```

Zainstaluj moduł Python-dotenv. Ten moduł odczytuje parę klucz-wartość z pliku ENV i dodaje je do zmiennej środowiskowej.

```cmd
pip install python-dotenv
```

Utwórz środowisko wirtualne.

```cmd
mkvirtualenv advanced-python
```

Użyj, `cd` Aby przejść do folderu głównego projektu przykładowego.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Połącz przykładowy projekt ze środowiskiem. Ta akcja mapuje nowo utworzone środowisko wirtualne do folderu głównego projektu przykładowego.

```cmd
setprojectdir .
```

Aktywuj środowisko wirtualne.

```cmd
activate
```

Projekt powinien być teraz aktywny i zobaczysz coś jak `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` w wierszu polecenia.

Dezaktywuj środowisko.

```cmd
deactivate
```

`(advanced-python)`Prefiks powinien zostać usunięty, ponieważ środowisko zostało zdezaktywowane.

Aby ponownie uaktywnić środowisko, uruchom polecenie `workon advanced-python` z przykładowego folderu głównego projektu.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

Gdy środowisko jest aktywne, uruchom przykładowy projekt, wprowadzając `flask run` z przykładowego folderu głównego projektu.

```cmd
flask run
```

Otwórz przeglądarkę i przejdź do http://localhost:5000 .

## <a name="create-a-python-web-app-on-osx"></a>Tworzenie aplikacji sieci Web w języku Python w witrynie OSX

Utwórz aplikację sieci Web w języku Python za pomocą usługi `flask` w systemie OSX.

Zainstaluj narzędzie [git](https://git-scm.com/).

Po zainstalowaniu usługi git Otwórz pozycję Terminal i Sklonuj repozytorium git zestawu SDK czytnika immersyjny do folderu na komputerze.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Zainstaluj język [Python](https://www.python.org/downloads/).

Folder główny języka Python, na przykład, `Python37-32` powinien znajdować się w folderze Applications.

Po zakończeniu instalacji języka Python Otwórz program Terminal i użyj go, `cd` Aby przejść do folderu skrypty języka Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Zainstaluj program pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Uruchom następujący kod, aby zainstalować narzędzie PIP dla obecnie zalogowanego użytkownika, aby uniknąć problemów z uprawnieniami.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Po wyświetleniu monitu wprowadź hasło.
- Dodaj ścieżkę instalacji PIP do zmiennej PATH.
- Przejdź do dolnej części pliku, a następnie wprowadź ścieżkę, którą chcesz dodać jako ostatni element listy, na przykład `PATH=$PATH:/usr/local/bin` .
- Wybierz **kombinację klawiszy Ctrl + X** , aby zakończyć.
- Wprowadź **Y** , aby zapisać zmodyfikowany bufor.
- Gotowe. Aby go przetestować, w nowym oknie terminalu wprowadź `echo $PATH` .

Zainstaluj kolbę.

```bash
pip install flask --user
```

Zainstaluj Jinja2. Jest to w pełni funkcjonalny aparat szablonów dla języka Python.

```bash
pip install Jinja2 --user
```

Zainstaluj virtualenv. To narzędzie tworzy izolowane środowiska języka Python.

```bash
pip install virtualenv --user
```

Zainstaluj virtualenvwrapper. Pomysłem za virtualenvwrapper jest ułatwienie użycia virtualenv.

```bash
pip install virtualenvwrapper --user
```

Zainstaluj moduł żądania. Żądania to Apache2a licencjonowana Biblioteka HTTP, zapisywana w języku Python.

```bash
pip install requests --user
```

Zainstaluj moduł Python-dotenv. Ten moduł odczytuje parę klucz-wartość z pliku ENV i dodaje je do zmiennej środowiskowej.

```bash
pip install python-dotenv --user
```

Wybierz folder, w którym chcesz zachować swoje środowiska wirtualne, i uruchom następujące polecenie:

```bash
mkdir ~/.virtualenvs
```

Użyj `cd` , aby przejść do folderu przykładowej aplikacji zestawu SDK Python dla czytnika immersyjny.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Utwórz środowisko wirtualne.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Połącz przykładowy projekt ze środowiskiem. Ta akcja mapuje nowo utworzone środowisko wirtualne do folderu głównego projektu przykładowego.

```bash
setprojectdir .
```

Aktywuj środowisko wirtualne.

```bash
activate
```

Projekt powinien być teraz aktywny i zobaczysz coś jak `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` w wierszu polecenia.

Dezaktywuj środowisko.

```bash
deactivate
```

`(advanced-python)`Prefiks powinien zostać usunięty, ponieważ środowisko zostało zdezaktywowane.

Aby ponownie uaktywnić środowisko, uruchom polecenie `workon advanced-python` z przykładowego folderu głównego projektu.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

Gdy środowisko jest aktywne, uruchom przykładowy projekt, wprowadzając `flask run` z przykładowego folderu głównego projektu.

```bash
flask run
```

Otwórz przeglądarkę i przejdź do http://localhost:5000 .

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [zestawem SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../../reference.md).
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
