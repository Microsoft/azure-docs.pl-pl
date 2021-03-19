---
title: Rozwiązywanie problemów z aplikacjami funkcji języka Python w Azure Functions
description: Dowiedz się, jak rozwiązywać problemy z funkcjami języka Python.
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 9b9f5d389eda5d74e7e78cfcfa9a46fba7276cbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846041"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Rozwiązywanie problemów z błędami języka Python w usłudze Azure Functions

Poniżej znajduje się lista przewodników rozwiązywania problemów dotyczących typowych problemów w funkcjach języka Python:

* [ModuleNotFoundError i ImportError](#troubleshoot-modulenotfounderror)
* [Nie można zaimportować "cygrpc"](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>Rozwiązywanie problemów z ModuleNotFoundError

Ta sekcja ułatwia rozwiązywanie problemów związanych z modułami w aplikacji funkcji języka Python. Te błędy zwykle powodują następujący komunikat o błędzie Azure Functions:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Ten problem występuje, gdy aplikacja funkcji języka Python nie może załadować modułu języka Python. Główną przyczyną tego błędu jest jeden z następujących problemów:

- [Nie można znaleźć pakietu](#the-package-cant-be-found)
- [Pakiet nie jest rozpoznawany przy użyciu prawidłowego kółka systemu Linux](#the-package-isnt-resolved-with-proper-linux-wheel)
- [Pakiet jest niezgodny z wersją interpretera języka Python](#the-package-is-incompatible-with-the-python-interpreter-version)
- [Pakiet jest w konflikcie z innymi pakietami](#the-package-conflicts-with-other-packages)
- [Pakiet obsługuje tylko platformy Windows lub macOS](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>Wyświetlanie plików projektu

Aby zidentyfikować rzeczywistą przyczynę problemu, należy uzyskać pliki projektu w języku Python, które są uruchamiane w aplikacji funkcji. Jeśli nie masz plików projektu na komputerze lokalnym, możesz je uzyskać w jeden z następujących sposobów:

- Jeśli aplikacja funkcji ma `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji i jej wartość jest adresem URL, Pobierz plik przez skopiowanie i wklejenie adresu URL do przeglądarki.
- Jeśli aplikacja funkcji ma `WEBSITE_RUN_FROM_PACKAGE` i jest ustawiona na `1` , przejdź do `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` pliku i Pobierz go z najnowszego `href` adresu URL.
- Jeśli aplikacja funkcji nie ma wymienionego powyżej ustawienia aplikacji, przejdź do `https://<app-name>.scm.azurewebsites.net/api/settings` adresu URL i Znajdź go `SCM_RUN_FROM_PACKAGE` . Pobierz plik przez skopiowanie i wklejenie adresu URL do przeglądarki.
- Jeśli żadna z tych elementów nie działa, przejdź do lokalizacji `https://<app-name>.scm.azurewebsites.net/DebugConsole` i odkryj jej zawartość `/home/site/wwwroot` .

Pozostała część tego artykułu pomaga rozwiązać potencjalne przyczyny tego błędu przez sprawdzenie zawartości aplikacji funkcji, zidentyfikowanie głównej przyczyny i rozwiązanie określonego problemu.

### <a name="diagnose-modulenotfounderror"></a>Diagnozuj ModuleNotFoundError

Ta sekcja zawiera szczegóły potencjalnych głównych przyczyn błędów związanych z modułami. Po określeniu, który jest prawdopodobnie główną przyczyną, można przejść do powiązanego rozwiązania.

#### <a name="the-package-cant-be-found"></a>Nie można znaleźć pakietu

Przejdź do `.python_packages/lib/python3.6/site-packages/<package-name>` lub `.python_packages/lib/site-packages/<package-name>` . Jeśli ścieżka pliku nie istnieje, ta brakująca ścieżka prawdopodobnie jest główną przyczyną.

W przypadku korzystania z narzędzi innych firm lub nieaktualnych podczas wdrażania może to spowodować wystąpienie tego problemu.

Zobacz [Włączanie kompilacji zdalnej](#enable-remote-build) lub [Kompiluj zależności natywne](#build-native-dependencies) w celu ograniczenia.

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>Pakiet nie jest rozpoznawany przy użyciu prawidłowego kółka systemu Linux

Przejdź do `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` lub `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Użyj swojego ulubionego edytora tekstu, aby otworzyć plik **kółka** i zapoznaj się z sekcją **tag:** . Jeśli wartość tagu nie zawiera systemu **Linux**, może to być problem.

Funkcje języka Python są uruchamiane tylko w systemie Linux na platformie Azure: środowisko uruchomieniowe w wersji 2. x działa na rozciągnięciu Debian i w środowisku uruchomieniowym v3. x na Debian Buster. Artefakt powinien zawierać poprawne pliki binarne systemu Linux. Użycie `--build local` flagi w podstawowych narzędziach, narzędziach innych firm lub nieaktualnych danych może spowodować użycie starszych plików binarnych.

Zobacz [Włączanie kompilacji zdalnej](#enable-remote-build) lub [Kompiluj zależności natywne](#build-native-dependencies) w celu ograniczenia.

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>Pakiet jest niezgodny z wersją interpretera języka Python

Przejdź do `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` lub `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Za pomocą edytora tekstów Otwórz plik metadanych i zaznacz sekcję **klasyfikatory:** . Jeśli sekcja nie zawiera `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` , lub `Python :: 3.8` , oznacza to, że wersja pakietu jest za stara lub najprawdopodobniej jest już do konserwacji pakiet.

Możesz sprawdzić wersję języka Python aplikacji funkcji z [Azure Portal](https://portal.azure.com). Przejdź do aplikacji funkcji, wybierz pozycję **Eksplorator zasobów**, a następnie wybierz pozycję **Przejdź**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Otwórz Eksplorator zasobów aplikacji funkcji w portalu":::

Po załadowaniu Eksploratora Wyszukaj pozycję **LinuxFxVersion**, która zawiera wersję języka Python.

Zobacz [Aktualizowanie pakietu do najnowszej wersji](#update-your-package-to-the-latest-version) lub [Zastąp pakiet równoważnymi](#replace-the-package-with-equivalents) ograniczeniami.

#### <a name="the-package-conflicts-with-other-packages"></a>Pakiet jest w konflikcie z innymi pakietami

Jeśli sprawdzono, że pakiet został prawidłowo rozpoznany z odpowiednimi kołami systemu Linux, może wystąpić konflikt z innymi pakietami. W niektórych pakietach dokumenty PyPi mogą wyjaśnić niezgodne moduły. Na przykład w programie [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) istnieje instrukcja w następujący sposób:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Dokumentację dotyczącą wersji pakietu można znaleźć w temacie `https://pypi.org/project/<package-name>/<package-version>` .

Zobacz [Aktualizowanie pakietu do najnowszej wersji](#update-your-package-to-the-latest-version) lub [Zastąp pakiet równoważnymi](#replace-the-package-with-equivalents) ograniczeniami.

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>Pakiet obsługuje tylko platformy Windows lub macOS

Otwórz `requirements.txt` Edytor tekstów i sprawdź pakiet w temacie `https://pypi.org/project/<package-name>` . Niektóre pakiety są uruchamiane tylko na platformach Windows lub macOS. Na przykład pywin32 działa tylko w systemie Windows.

Ten `Module Not Found` błąd może nie wystąpić, gdy używasz systemu Windows lub macOS do lokalnego tworzenia oprogramowania. Nie można jednak zaimportować pakietu na Azure Functions, który używa systemu Linux w czasie wykonywania. Jest to prawdopodobnie spowodowane użyciem `pip freeze` do eksportowania środowiska wirtualnego do requirements.txt z komputera z systemem Windows lub macOS podczas inicjowania projektu.

Zobacz [Zastąp pakiet równoważnymi](#replace-the-package-with-equivalents) lub [Handcraft requirements.txt](#handcraft-requirementstxt) na potrzeby ograniczenia.

### <a name="mitigate-modulenotfounderror"></a>Ograniczanie ModuleNotFoundError

Poniżej podano potencjalne ograniczenia dotyczące problemów związanych z modułem. Skorzystaj z [diagnostyki powyżej](#diagnose-modulenotfounderror) , aby określić, które z tych środków zaradczych należy wypróbować.

#### <a name="enable-remote-build"></a>Włącz kompilację zdalną

Upewnij się, że kompilacja zdalna jest włączona. W ten sposób zależy od metody wdrażania.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Upewnij się, że zainstalowano najnowszą wersję [rozszerzenia Azure Functions Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . Sprawdź, czy `.vscode/settings.json` istnieje i zawiera ustawienie `"azureFunctions.scmDoBuildDuringDeployment": true` . Jeśli nie, Utwórz ten plik z `azureFunctions.scmDoBuildDuringDeployment` włączonym ustawieniem i Wdróż ponownie projekt.

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Upewnij się, że zainstalowano najnowszą wersję [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) . Przejdź do lokalnego folderu projektu funkcji i Użyj `func azure functionapp publish <app-name>` do wdrożenia.

## <a name="manual-publishing"></a>[Publikowanie ręczne](#tab/manual)

Jeśli publikujesz ręcznie pakiet w `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` punkcie końcowym, upewnij się, że oba **SCM_DO_BUILD_DURING_DEPLOYMENT** i **ENABLE_ORYX_BUILD** są ustawione na **wartość true**. Aby dowiedzieć się więcej, zobacz [jak korzystać z ustawień aplikacji](functions-how-to-use-azure-function-app-settings.md#settings).

---

#### <a name="build-native-dependencies"></a>Kompiluj zależności natywne

Upewnij się, że zainstalowano najnowszą wersję obu programów **Docker** i [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) . Przejdź do lokalnego folderu projektu funkcji i Użyj `func azure functionapp publish <app-name> --build-native-deps` do wdrożenia.

#### <a name="update-your-package-to-the-latest-version"></a>Aktualizowanie pakietu do najnowszej wersji

Przejrzyj najnowszą wersję pakietu w `https://pypi.org/project/<package-name>` temacie i sprawdź **klasyfikatory:** . Pakiet powinien być `OS Independent` lub zgodny z `POSIX` lub `POSIX :: Linux` w **systemie operacyjnym**. Ponadto język programowania powinien zawierać `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` , lub `Python :: 3.8` .

Jeśli są poprawne, można zaktualizować pakiet do najnowszej wersji, zmieniając wiersz `<package-name>~=<latest-version>` w requirements.txt.

#### <a name="handcraft-requirementstxt"></a>Handcraft requirements.txt

Niektórzy Deweloperzy używają `pip freeze > requirements.txt` do generowania listy pakietów języka Python dla ich środowisk tworzenia. Chociaż ta wygoda powinna działać w większości przypadków, mogą występować problemy z wieloplatformowymi scenariuszami wdrażania, takimi jak tworzenie funkcji lokalnie w systemie Windows lub macOS, ale publikowanie w aplikacji funkcji, która działa w systemie Linux. W tym scenariuszu program `pip freeze` może wprowadzać nieoczekiwane zależności specyficzne dla systemu operacyjnego lub zależności dla lokalnego środowiska deweloperskiego. Te zależności mogą spowodować przerwanie działania aplikacji funkcji w języku Python w systemie Linux.

Najlepszym rozwiązaniem jest sprawdzenie instrukcji import z każdego pliku. PR w kodzie źródłowym projektu i tylko zaewidencjonowanie tych modułów w requirements.txt pliku. Gwarantuje to, że rozpoznawanie pakietów może być obsługiwane prawidłowo w różnych systemach operacyjnych.

#### <a name="replace-the-package-with-equivalents"></a>Zamień pakiet na odpowiedniki

Najpierw zapoznaj się z najnowszą wersją pakietu w temacie `https://pypi.org/project/<package-name>` . Zazwyczaj ten pakiet ma własną stronę usługi GitHub, przejdź do sekcji **problemy** w witrynie GitHub i Wyszukaj, czy problem został rozwiązany. W takim przypadku należy zaktualizować pakiet do najnowszej wersji.

Czasami pakiet mógł zostać zintegrowany do [standardowej biblioteki języka Python](https://docs.python.org/3/library/) (na przykład pathlib). Jeśli tak, ponieważ udostępniamy pewną dystrybucję w języku Python w Azure Functions (Python 3,6, Python 3,7 i Python 3,8), należy usunąć pakiet z requirements.txt.

Jeśli jednak wystąpi problem, którego nie Naprawiono, i jesteś terminem ostatecznym. Zachęcam do wykonywania niektórych badań i znajdowania podobnego pakietu dla projektu. Zazwyczaj społeczność języka Python zapewni szeroką gamę podobnych bibliotek, których można użyć.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>Rozwiązywanie problemów nie może importować "cygrpc"

Ta sekcja ułatwia rozwiązywanie problemów związanych z błędami "cygrpc" w aplikacji funkcji języka Python. Te błędy zwykle powodują następujący komunikat o błędzie Azure Functions:

> `Cannot import name 'cygrpc' from 'grpc._cython'`

Ten błąd występuje, gdy nie można uruchomić aplikacji funkcji języka Python z prawidłowym interpreterem języka Python. Główną przyczyną tego błędu jest jeden z następujących problemów:

- [Interpreter języka Python niezgodny z architekturą systemu operacyjnego](#the-python-interpreter-mismatches-os-architecture)
- [Interpreter języka Python nie jest obsługiwany przez Azure Functions proces roboczy języka Python](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>Diagnozuj błąd odwołania "cygrpc"

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Interpreter języka Python niezgodny z architekturą systemu operacyjnego

Jest to najprawdopodobniej spowodowane przez 32-bitowy interpreter języka Python na 64-bitowym systemie operacyjnym.

Jeśli korzystasz z systemu operacyjnego x64, upewnij się, że interpreter języka Python 3,6, 3,7 lub 3,8 jest również w wersji 64-bitowej.

Można sprawdzić bitową interpretera języka Python za pomocą następujących poleceń:

W systemie Windows w programie PowerShell: `py -c 'import platform; print(platform.architecture()[0])'`

W przypadku powłoki podobnej do systemu UNIX: `python3 -c 'import platform; print(platform.architecture()[0])'`

Jeśli występuje niezgodność między bitową interpretera języka Python a architekturą systemu operacyjnego, Pobierz odpowiedni interpreter języka Python z platformy [Python Software Foundation](https://python.org/downloads/release).

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Interpreter języka Python nie jest obsługiwany przez Azure Functions proces roboczy języka Python

Proces roboczy Azure Functions Python obsługuje tylko Języki Python 3,6, 3,7 i 3,8.
Sprawdź, czy interpreter języka Python jest zgodny z naszą oczekiwaną wersją `py --version` w systemie Windows lub `python3 --version` w systemach opartych na systemie UNIX. Upewnij się, że zwracany wynik to Python 3.6. x, Python 3.7. x lub Python 3.8. x.

Jeśli Twoja wersja interpretera języka Python nie spełnia oczekiwań, Pobierz interpreter Python 3,6, 3,7 lub 3,8 z platformy [Python Software Foundation](https://python.org/downloads/release).

## <a name="next-steps"></a>Następne kroki

Jeśli nie możesz rozwiązać problemu, zgłoś to zespołowi ds. funkcji:

> [!div class="nextstepaction"]
> [Zgłoś nierozwiązany problem](https://github.com/Azure/azure-functions-python-worker/issues)
