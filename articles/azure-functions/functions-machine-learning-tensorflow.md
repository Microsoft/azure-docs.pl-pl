---
title: Korzystanie z języka Python i TensorFlow na potrzeby uczenia maszynowego na platformie Azure
description: Użyj języka Python, TensorFlow i Azure Functions z modelem uczenia maszynowego, aby sklasyfikować obraz na podstawie jego zawartości.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994608"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Samouczek: stosowanie modeli uczenia maszynowego w Azure Functions przy użyciu języka Python i TensorFlow

W tym artykule dowiesz się, jak używać języka Python, TensorFlow i Azure Functions z modelem uczenia maszynowego do klasyfikowania obrazu na podstawie jego zawartości. Ponieważ wykonujesz całą pracę lokalnie i nie utworzysz zasobów platformy Azure w chmurze, nie masz kosztu na ukończenie tego samouczka.

> [!div class="checklist"]
> * Zainicjuj lokalne środowisko do tworzenia Azure Functions w języku Python.
> * Zaimportuj niestandardowy model uczenia maszynowego TensorFlow do aplikacji funkcji.
> * Tworzenie bezserwerowego interfejsu API HTTP do klasyfikowania obrazu jako zawierającego pies lub kot.
> * Używanie interfejsu API z poziomu aplikacji internetowej.

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [3.7.4 języka Python](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 i Python 3.6. x są weryfikowane przy użyciu Azure Functions; Środowisko Python 3,8 i jego nowsze wersje nie są jeszcze obsługiwane.)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Edytor kodu, taki jak program [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

1. W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy Azure Functions Core Tools jest w wersji 2.7.1846 lub nowszej.
1. Uruchom system `python --version` (Linux/MacOS) lub `py --version` (Windows), aby sprawdzić raporty wersji języka Python 3.7. x.

## <a name="clone-the-tutorial-repository"></a>Klonowanie repozytorium samouczka

1. W terminalu lub oknie polecenia Sklonuj następujące repozytorium za pomocą narzędzia Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Przejdź do folderu i przejrzyj jego zawartość.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *rozpoczęcie* pracy z Twoim samouczkiem jest folderem roboczym.
    - *End* to końcowy wynik i pełna implementacja dla odwołania.
    - *zasoby* zawierają model uczenia maszynowego i biblioteki pomocnika.
    - *fronton* to witryna sieci Web, która wywołuje aplikację funkcji.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego języka Python

Przejdź do folderu *Start* i uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv` . Upewnij się, że używasz języka Python 3,7, który jest obsługiwany przez Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli środowisko Python nie zainstalowało pakietu venv na dystrybucji systemu Linux, uruchom następujące polecenie:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Wszystkie kolejne polecenia są uruchamiane w tym aktywowanym środowisku wirtualnym. (Aby wyjść z środowiska wirtualnego, uruchom polecenie `deactivate` ).


## <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję, o nazwie `classify` , która udostępnia punkt końcowy HTTP. Dodaj bardziej szczegółowy kod w dalszej części.

1. W folderze *Start* Użyj Azure Functions Core Tools, aby zainicjować aplikację funkcji języka Python:

    ```
    func init --worker-runtime python
    ```

    Po zainicjowaniu folder *początkowy* zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.js](functions-run-local.md#local-settings-file) i [host.jswłączone](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

    > [!TIP]
    > Ponieważ projekt funkcji jest powiązany z określonym środowiskiem uruchomieniowym, wszystkie funkcje w projekcie muszą być zapisywane w tym samym języku.

1. Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie `--name` argument jest unikatową nazwą funkcji, a `--template` argument określa wyzwalacz funkcji. `func new` Utwórz podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    To polecenie tworzy folder pasujący do nazwy funkcji, *klasyfikowanie*. W tym folderze są dwa pliki: *\_ \_ init \_ \_ . PR*, który zawiera kod funkcji, i *function.json*, który opisuje wyzwalacz funkcji i powiązania wejściowe i wyjściowe. Aby uzyskać szczegółowe informacje na temat zawartości tych plików, zobacz sekcję [Sprawdź zawartość pliku](./create-first-function-cli-python.md#optional-examine-the-file-contents) w przewodniku szybki start dla języka Python.


## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom funkcję, uruchamiając lokalny host środowiska uruchomieniowego Azure Functions w folderze *Start* :

    ```
    func start
    ```
    
1. Gdy `classify` punkt końcowy zostanie wyświetlony w danych wyjściowych, przejdź do adresu URL, ```http://localhost:7071/api/classify?name=Azure``` . Komunikat "Hello Azure!" powinien pojawić się w danych wyjściowych.

1. Użyj **klawisza Ctrl** - **C** , aby zatrzymać hosta.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importowanie modelu TensorFlow i Dodawanie kodu pomocnika

Aby zmodyfikować `classify` funkcję w celu klasyfikowania obrazu na podstawie jego zawartości, należy użyć wstępnie skompilowanego modelu TensorFlow, który został przeszkolony i wyeksportowany z usługi Azure Custom Vision Service. Model, który znajduje się w folderze *resources* pobranej wcześniej próbki, klasyfikuje obraz na podstawie tego, czy zawiera on pies czy Cat. Następnie można dodać kod pomocnika i zależności do projektu.

Aby utworzyć własny model przy użyciu warstwy Bezpłatna Custom Vision Service, postępuj zgodnie z instrukcjami w [repozytorium przykładowego projektu](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

> [!TIP]
> Jeśli chcesz hostować model TensorFlow niezależnie od aplikacji funkcji, zamiast tego możesz zainstalować udział plików zawierający model w aplikacji funkcji systemu Linux. Aby dowiedzieć się więcej, zobacz [Instalowanie udziału plików w aplikacji funkcji języka Python przy użyciu interfejsu wiersza polecenia platformy Azure](./scripts/functions-cli-mount-files-storage-linux.md).

1. W folderze *Start* Uruchom następujące polecenie, aby skopiować pliki modelu do folderu *klasyfikowanie* . Pamiętaj, aby uwzględnić je `\*` w poleceniu. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Sprawdź, czy folder *klasyfikowanie* zawiera pliki o nazwach *model. PB* i *labels.txt*. Jeśli nie, sprawdź, czy uruchomiono polecenie w folderze *Start* .

1. W folderze *Start* Uruchom następujące polecenie, aby skopiować plik z kodem pomocnika do folderu *klasyfikowanie* :

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Sprawdź, czy folder *klasyfikowanie* zawiera teraz plik o nazwie *Predict.py*.

1. Otwórz *menu Start/requirements.txt* w edytorze tekstów i Dodaj następujące zależności wymagane przez kod pomocnika:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Zapisz *requirements.txt*.

1. Zainstaluj zależności, uruchamiając następujące polecenie w folderze *Start* . Instalacja może potrwać kilka minut, w tym czasie można kontynuować modyfikowanie funkcji w następnej sekcji.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    W systemie Windows może wystąpić błąd "nie można zainstalować pakietów ze względu na EnvironmentError: [errno 2] Brak pliku lub katalogu:" po którym występuje długa nazwa ścieżki do pliku, takiego jak *sharded_mutable_dense_hashtable. CPython-37. PYC*. Zazwyczaj ten błąd występuje, ponieważ głębokość ścieżki folderu jest zbyt długa. W takim przypadku należy ustawić klucz rejestru, `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` Aby `1` włączyć długie ścieżki. Alternatywnie Sprawdź, gdzie jest zainstalowany interpreter języka Python. Jeśli ta lokalizacja ma długą ścieżkę, spróbuj zainstalować ją ponownie w folderze o krótszej ścieżce.

> [!TIP]
> Podczas wywoływania metody *Predict.py* w celu utworzenia pierwszego przewidywania funkcja o nazwie `_initialize` ładuje model TensorFlow z dysku i buforuje go w zmiennych globalnych. Ta pamięć podręczna przyspiesza kolejne przewidywania. Aby uzyskać więcej informacji na temat używania zmiennych globalnych, zapoznaj się z [przewodnikiem dla deweloperów Azure Functions Python](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Aktualizowanie funkcji do uruchamiania prognoz

1. Otwórz *klasyfikację/ \_ \_ init \_ \_ . PR* w edytorze tekstów i Dodaj następujące wiersze po istniejących `import` instrukcjach, aby zaimportować standardową bibliotekę JSON i pomocnicy *predykcyjny* :

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Zastąp całą zawartość `main` funkcji następującym kodem:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Ta funkcja odbiera adres URL obrazu w parametrze ciągu zapytania o nazwie `img` . Następnie wywołuje `predict_image_from_url` z biblioteki pomocnika, aby pobrać i sklasyfikować obraz przy użyciu modelu TensorFlow. Funkcja zwraca odpowiedź HTTP z wynikami. 

    > [!IMPORTANT]
    > Ponieważ ten punkt końcowy HTTP jest wywoływany przez stronę sieci Web hostowaną w innej domenie, odpowiedź zawiera `Access-Control-Allow-Origin` nagłówek, który spełnia wymagania funkcji udostępniania zasobów między źródłami (CORS) przeglądarki.
    >
    > W aplikacji produkcyjnej przejdź `*` do konkretnego źródła strony sieci Web, aby zwiększyć bezpieczeństwo.

1. Zapisz zmiany, a następnie założono, że te zależności zakończyły instalację, uruchom ponownie hosta funkcji lokalnej przy użyciu programu `func start` . Upewnij się, że host został uruchomiony w folderze *Start* z aktywowanym środowiskiem wirtualnym. W przeciwnym razie zostanie uruchomiony host, ale podczas wywoływania funkcji pojawią się błędy.

    ```
    func start
    ```

1. W przeglądarce Otwórz następujący adres URL, aby wywołać funkcję z adresem URL obrazu Cat i potwierdzić, że zwrócony kod JSON klasyfikuje obraz jako Cat.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Pozostaw uruchomiony host, ponieważ jest on używany w następnym kroku. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Uruchamianie frontonu lokalnej aplikacji sieci Web w celu przetestowania funkcji

Aby przetestować wywoływanie punktu końcowego funkcji z innej aplikacji sieci Web, istnieje prosta aplikacja w folderze *frontonu* repozytorium.

1. Otwórz nowy terminal lub wiersz polecenia i aktywuj środowisko wirtualne (zgodnie z wcześniejszym opisem w sekcji [Tworzenie i aktywowanie środowiska wirtualnego języka Python](#create-and-activate-a-python-virtual-environment)).

1. Przejdź do folderu *frontonu* repozytorium.

1. Uruchom serwer HTTP przy użyciu języka Python:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. W przeglądarce przejdź do `localhost:8000` , a następnie wprowadź jeden z następujących adresów URL zdjęć do pola tekstowego lub użyj adresu URL dowolnego dostępnego publicznie obrazu.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Wybierz pozycję **Prześlij** , aby wywołać punkt końcowy funkcji do klasyfikowania obrazu.

    ![Zrzut ekranu ukończonego projektu](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Jeśli przeglądarka zgłosi błąd podczas przesyłania adresu URL obrazu, sprawdź Terminal, w którym jest uruchomiona aplikacja funkcji. Jeśli zobaczysz błąd, na przykład "nie znaleziono modułu" PIL "", być może uruchomiono aplikację funkcji w folderze *startowym* bez wcześniejszego aktywowania środowiska wirtualnego utworzonego wcześniej. Jeśli nadal występują błędy, uruchom `pip install -r requirements.txt` ponownie z aktywowanym środowiskiem wirtualnym i Wyszukaj błędy.

> [!NOTE]
> Model zawsze klasyfikuje zawartość obrazu jako kot lub pies, bez względu na to, czy obraz zawiera jedną z domyślnych wartości Dog. Obrazy Tigers i Panthers, na przykład zwykle klasyfikowanie jako kot, ale obrazy słonie, marchew lub plany są klasyfikowane jako Dog.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ponieważ w całości z tego samouczka działa lokalnie na komputerze, nie ma żadnych zasobów ani usług platformy Azure do oczyszczenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób kompilowania i dostosowywania punktu końcowego interfejsu API protokołu HTTP przy użyciu Azure Functions do klasyfikowania obrazów przy użyciu modelu TensorFlow. Wiesz również, jak wywołać interfejs API z poziomu aplikacji sieci Web. Można użyć technik z tego samouczka, aby utworzyć interfejsy API dowolnej złożoności, a wszystko to uruchomione w modelu obliczeniowym bez serwera udostępnianym przez Azure Functions.

> [!div class="nextstepaction"]
> [Wdróż funkcję do Azure Functions przy użyciu przewodnika interfejsu wiersza polecenia platformy Azure](./functions-run-local.md#publish)

Zobacz też:

- [Wdróż funkcję na platformie Azure przy użyciu Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Przewodnik dewelopera w języku Python Azure Functions](./functions-reference-python.md)
- [Instalowanie udziału plików w aplikacji funkcji języka Python przy użyciu interfejsu wiersza polecenia platformy Azure](./scripts/functions-cli-mount-files-storage-linux.md)
