---
title: Tworzenie rozszerzenia VS Code zasobów i zarządzanie nimi (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć zasoby Azure Machine Learning i zarządzać nimi przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010677"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Zarządzanie zasobami Azure Machine Learning przy użyciu rozszerzenia VS Code (wersja zapoznawcza)

Dowiedz się, jak zarządzać zasobami Azure Machine Learning przy użyciu rozszerzenia VS Code.

![Rozszerzenie VS Code Azure Machine Learning](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz takiego konta, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
- Program Visual Studio Code. Jeśli go nie masz, [Zainstaluj go](https://code.visualstudio.com/docs/setup/setup-overview).
- VS Code Azure Machine Learning rozszerzenia. Postępuj zgodnie z [przewodnikiem instalacji rozszerzenia VS Code Azure Machine Learning](tutorial-setup-vscode-extension.md#install-the-extension) , aby zainstalować rozszerzenie.

W przypadku wszystkich procesów poniżej założono, że jesteś w widoku Azure Machine Learning w Visual Studio Code. Aby uruchomić rozszerzenie, wybierz ikonę **platformy Azure** na pasku działania vs Code.

## <a name="workspaces"></a>Obszary robocze

Aby uzyskać więcej informacji, zobacz [obszary robocze](concept-workspace.md).

### <a name="create-a-workspace"></a>Utwórz obszar roboczy

1. W widoku Azure Machine Learning kliknij prawym przyciskiem myszy węzeł subskrypcji i wybierz pozycję **Utwórz obszar roboczy**.
1. W wierszu polecenia:
    1. Podaj nazwę obszaru roboczego
    1. Wybieranie subskrypcji platformy Azure
    1. Wybierz lub Utwórz nową grupę zasobów, w której ma zostać zainicjowana obsługa obszaru roboczego
    1. Wybierz lokalizację, w której ma zostać zainicjowana przestrzeń robocza.

Alternatywne metody tworzenia obszaru roboczego obejmują:

- Otwórz widok palety poleceń **> palecie poleceń** i wprowadź tekst monitu o treść **Azure ml: Tworzenie obszaru roboczego**.
- Kliknij `+` ikonę w górnej części widoku Azure Machine Learning.
- Utwórz nowy obszar roboczy po wyświetleniu monitu o wybranie obszaru roboczego podczas aprowizacji innych zasobów.

### <a name="remove-a-workspace"></a>Usuwanie obszaru roboczego

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Kliknij prawym przyciskiem myszy obszar roboczy, który chcesz usunąć.
1. Wybierz, czy chcesz usunąć:
    - *Tylko obszar roboczy*: Ta opcja usuwa **tylko** zasób platformy Azure obszaru roboczego. Grupa zasobów, konta magazynu i inne zasoby, do których dołączono obszar roboczy, nadal znajdują się na platformie Azure.
    - *Ze skojarzonymi zasobami*: Ta opcja usuwa obszar roboczy **i** wszystkie skojarzone z nim zasoby.

## <a name="datastores"></a>Magazyny danych

Rozszerzenie VS Code obecnie obsługuje magazyny danych następujących typów:

- Udział plików platformy Azure
- Azure Blob Storage

Podczas tworzenia obszaru roboczego magazyn danych jest tworzony dla każdego z tych typów.

Aby uzyskać więcej informacji, zobacz [magazyny](concept-data.md#datastores)danych.

### <a name="create-a-datastore"></a>Tworzenie magazynu danych

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego, w którym chcesz utworzyć magazyn danych.
1. Kliknij prawym przyciskiem myszy węzeł **magazyny** danych, a następnie wybierz pozycję **zarejestruj magazyn** danych.
1. W wierszu polecenia:
    1. Podaj nazwę magazynu danych.
    1. Wybierz typ magazynu danych.
    1. Wybierz zasób magazynu. Możesz wybrać zasób magazynu skojarzony z obszarem roboczym lub wybrać dowolny prawidłowy zasób magazynu w ramach subskrypcji platformy Azure.
    1. Wybierz kontener, w którym dane są wewnątrz wcześniej wybranego zasobu magazynu.
1. Plik konfiguracji jest wyświetlany w VS Code. Jeśli plik konfiguracji jest zadowalający, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

### <a name="manage-a-datastore"></a>Zarządzanie magazynem danych

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Rozwiń węzeł **magazyny** danych w obszarze roboczym.
1. Wybierz magazyn danych, który chcesz:
    - *Ustaw jako domyślny*. Za każdym razem, gdy uruchamiasz eksperymenty, jest to magazyn danych, który będzie używany.
    - *Sprawdź ustawienia tylko do odczytu*.
    - *Modify*. Zmień typ uwierzytelniania i poświadczenia. Obsługiwane typy uwierzytelniania obejmują klucz konta i token sygnatury dostępu współdzielonego.

## <a name="datasets"></a>Zestawy danych

Rozszerzenie obsługuje obecnie następujące typy zestawów danych:

- *Tabelaryczny*: umożliwia zmaterializowania danych do elementu Dataframe (Pandas lub PySpark).
- *Plik*: plik lub Kolekcja plików. Umożliwia pobieranie lub Instalowanie plików w ramach obliczeń.

Aby uzyskać więcej informacji, zobacz [zestawy danych](concept-data.md#datasets)

### <a name="create-dataset"></a>Utworzenie zestawu danych

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego, w którym chcesz utworzyć magazyn danych.
1. Kliknij prawym przyciskiem myszy węzeł **zestawy** danych i wybierz polecenie **Utwórz zestaw danych**.
1. W wierszu polecenia:
    1. Wybierz typ zestawu danych
    1. Zdefiniuj, czy dane znajdują się na komputerze, czy w sieci Web
    1. Podaj lokalizację danych. Może to być pojedynczy plik lub katalog zawierający pliki danych.
    1. Wybierz magazyn danych, do którego chcesz przekazać dane.
    1. Podaj prefiks, który pomaga identyfikować zestaw danych w magazynie datastore.

### <a name="version-datasets"></a>Zestawy danych wersji

Podczas kompilowania modeli uczenia maszynowego, jako zmiany danych, może być konieczne uzyskanie wersji zestawu danych. W tym celu w rozszerzeniu VS Code:

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Rozwiń węzeł **zestawy danych** .
1. Kliknij prawym przyciskiem myszy zestaw danych, który chcesz wersji, a następnie wybierz pozycję **Utwórz nową wersję**.
1. W wierszu polecenia:
    1. Wybierz typ zestawu danych
    1. Zdefiniuj, czy dane znajdują się na komputerze, czy w sieci Web.
    1. Podaj lokalizację danych. Może to być pojedynczy plik lub katalog zawierający pliki danych.
    1. Wybierz magazyn danych, do którego chcesz przekazać dane.
    1. Podaj prefiks, który pomaga identyfikować zestaw danych w magazynie datastore.

### <a name="view-dataset-properties"></a>Wyświetl właściwości zestawu danych

Ta opcja umożliwia wyświetlenie metadanych skojarzonych z określonym zestawem danych. W tym celu w rozszerzeniu VS Code:

1. Rozwiń węzeł obszaru roboczego.
1. Rozwiń węzeł **zestawy danych** .
1. Kliknij prawym przyciskiem myszy zestaw danych, który chcesz sprawdzić, i wybierz polecenie **Wyświetl właściwości zestawu danych**. Spowoduje to wyświetlenie pliku konfiguracji z właściwościami najnowszej wersji zestawu danych.

> [!NOTE]
> Jeśli masz wiele wersji zestawu danych, możesz wyświetlić tylko właściwości zestawu danych określonej wersji, rozszerzając węzeł zestawu danych i wykonując te same kroki opisane w tej sekcji w zależności od używanej wersji.

### <a name="unregister-datasets"></a>Wyrejestruj zestawy danych

Aby usunąć zestaw danych i jego całą wersję, Wyrejestruj go. W tym celu w rozszerzeniu VS Code:

1. Rozwiń węzeł obszaru roboczego.
1. Rozwiń węzeł **zestawy danych** .
1. Kliknij prawym przyciskiem myszy zestaw danych, który chcesz wyrejestrować, a następnie wybierz polecenie **Wyrejestruj zestaw danych**.

## <a name="environments"></a>Środowiska

Aby uzyskać więcej informacji, zobacz [środowiska](concept-environments.md).

### <a name="create-environment"></a>Utwórz środowisko

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego, w którym chcesz utworzyć magazyn danych.
1. Kliknij prawym przyciskiem myszy węzeł **środowiska** , a następnie wybierz pozycję **Utwórz środowisko**.
1. W wierszu polecenia:
    1. Podaj nazwę środowiska
    1. Zdefiniuj konfigurację środowiska:
        - *Nadzorowane środowiska*: wstępnie skonfigurowane środowiska w Azure Machine Learning. Możesz bardziej dostosować środowisko, modyfikując `dependencies` Właściwość w pliku JSON. Dowiedz się więcej o [środowiskach nadzorowanych](resource-curated-environments.md).
        - *Plik zależności Conda*: w środowiskach AnaConDa można podać plik zawierający definicję środowiska.
        - *Plik wymagań PIP*: w środowiskach PIP można podać plik zawierający definicję środowiska.
        - *Istniejące środowisko Conda*: Ta opcja wyszukuje środowiska Conda na komputerze lokalnym i próbuje skompilować środowisko z wybranego środowiska.
        - *Niestandardowe*: Definiowanie własnych kanałów i zależności
    1. Plik konfiguracji zostanie otwarty w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

### <a name="view-environment-configurations"></a>Wyświetl konfiguracje środowiska

Aby wyświetlić zależności i konfiguracje dla określonego środowiska w rozszerzeniu:

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Rozwiń węzeł **środowiska** .
1. Kliknij prawym przyciskiem myszy środowisko, które chcesz wyświetlić, a następnie wybierz polecenie **Wyświetl środowisko**.

### <a name="edit-environment-configurations"></a>Edytuj konfiguracje środowiska

Aby edytować zależności i konfiguracje dla określonego środowiska w rozszerzeniu:

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **środowiska** wewnątrz obszaru roboczego.
1. Kliknij prawym przyciskiem myszy środowisko, które chcesz wyświetlić, a następnie wybierz polecenie **Edytuj środowisko**.
1. Po wprowadzeniu zmian, jeśli masz pewność, że konfiguracja zostanie osiągnięta, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

## <a name="experiments"></a>Eksperymenty

Aby uzyskać więcej informacji, zobacz [eksperymenty](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Tworzenie eksperymentu

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Kliknij prawym przyciskiem myszy węzeł **eksperymenty** w obszarze roboczym i wybierz pozycję **Utwórz eksperyment**.
1. W wierszu polecenia podaj nazwę eksperymentu.

### <a name="run-experiment"></a>Uruchom eksperyment

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **eksperymenty** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy eksperyment, który chcesz uruchomić.
1. Na pasku działania wybierz ikonę **Uruchom eksperyment** .
1. Wybierz, czy chcesz uruchomić eksperyment lokalnie, czy zdalnie. Zobacz [Przewodnik debugowania](how-to-debug-visual-studio-code.md) , aby uzyskać więcej informacji na temat lokalnego uruchamiania i debugowania eksperymentów.
1. Wybierz subskrypcję.
1. Wybierz Obszar roboczy usługi ML platformy Azure, w ramach którego chcesz uruchomić eksperyment.
1. Wybierz swój eksperyment.
1. Wybierz lub Utwórz obliczenie do uruchomienia eksperymentu.
1. Wybierz lub Utwórz konfigurację uruchamiania dla eksperymentu.

Alternatywnie możesz wybrać przycisk **Uruchom eksperyment** w górnej części rozszerzenia i skonfigurować przebieg eksperymentu w wierszu polecenia.

### <a name="view-experiment"></a>Wyświetl eksperyment

Aby wyświetlić eksperyment w Azure Machine Learning Studio:

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **eksperymenty** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy eksperyment, który chcesz wyświetlić, a następnie wybierz pozycję **Wyświetl eksperyment**. 
1. Zostanie wyświetlony monit z prośbą o otwarcie adresu URL eksperymentu w programie Azure Machine Learning Studio. Wybierz pozycję **Otwórz**.

### <a name="track-run-progress"></a>Śledzenie postępu przebiegu

Po uruchomieniu eksperymentu możesz chcieć zobaczyć jego postęp. Aby śledzić postęp przebiegu w programie Azure Machine Learning Studio z poziomu rozszerzenia:

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **eksperymenty** w obszarze roboczym.
1. Rozwiń węzeł eksperymenty, dla którego chcesz śledzić postęp.
1. Kliknij prawym przyciskiem myszy przebieg i wybierz pozycję **Widok Uruchom w Azure Portal**.
1. Zostanie wyświetlony monit z prośbą o otwarcie adresu URL uruchamiania w programie Azure Machine Learning Studio. Wybierz pozycję **Otwórz**.

### <a name="download-run-logs--outputs"></a>Pobierz dzienniki uruchamiania & dane wyjściowe

Po zakończeniu przebiegu warto pobrać dzienniki i zasoby, takie jak model wygenerowany w ramach przebiegu eksperymentu.

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **eksperymenty** w obszarze roboczym.
1. Rozwiń węzeł eksperymenty, dla którego chcesz śledzić postęp.
1. Kliknij prawym przyciskiem myszy przebieg:
    - Aby pobrać dane wyjściowe, wybierz pozycję **Pobierz dane wyjściowe**.
    - Aby pobrać dzienniki, wybierz pozycję **Pobierz dzienniki**.

### <a name="view-run-metadata"></a>Wyświetl metadane uruchamiania

W rozszerzeniu można sprawdzić metadane, takie jak konfiguracja uruchomienia używana do uruchamiania, a także szczegóły uruchamiania.

## <a name="compute-instances"></a>Wystąpienia obliczeniowe

Aby uzyskać więcej informacji, zobacz [wystąpienia obliczeniowe](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Tworzenie wystąpienia obliczeniowego

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego, w którym chcesz utworzyć wystąpienie obliczeniowe.
1. Kliknij prawym przyciskiem myszy węzeł **wystąpienia obliczeniowe** i wybierz polecenie **Utwórz wystąpienie obliczeniowe**.
1. W wierszu polecenia:
    1. Podaj nazwę wystąpienia obliczeniowego.
    1. Wybierz rozmiar maszyny wirtualnej z listy.
    1. Zdecyduj, czy chcesz włączyć dostęp SSH.
        1. Jeśli włączysz dostęp SSH, musisz również podać publiczny klucz SSH lub plik zawierający klucz. Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący tworzenia i używania kluczy SSH na platformie Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

### <a name="stop-or-restart-compute-instance"></a>Zatrzymywanie lub ponowne uruchamianie wystąpienia obliczeniowego

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **wystąpienia obliczeniowego** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy wystąpienie obliczeniowe, które chcesz zatrzymać lub uruchomić ponownie, a następnie wybierz odpowiednio polecenie **Zatrzymaj wystąpienie obliczeniowe** lub **ponownie uruchom wystąpienie obliczeniowe** .

### <a name="view-compute-instance-configuration"></a>Wyświetl konfigurację wystąpienia obliczeniowego

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **wystąpienia obliczeniowego** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy wystąpienie obliczeniowe, które chcesz sprawdzić, i wybierz polecenie **Wyświetl właściwości wystąpienia obliczeniowego**.

### <a name="delete-compute-instance"></a>Usuwanie wystąpienia obliczeniowego

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **wystąpienia obliczeniowego** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy wystąpienie obliczeniowe, które chcesz usunąć, a następnie wybierz polecenie **Usuń wystąpienie obliczeniowe**.

## <a name="compute-clusters"></a>Klastry obliczeniowe

Rozszerzenie obsługuje następujące typy obliczeń:

- Azure Machine Learning klaster obliczeniowy
- Azure Kubernetes Service

Aby uzyskać więcej informacji, zobacz [cele obliczeń](concept-compute-target.md#train).

### <a name="create-compute"></a>Tworzenie obliczeń

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego, w którym chcesz utworzyć klaster obliczeniowy.
1. Kliknij prawym przyciskiem myszy węzeł **Klastry obliczeniowe** , a następnie wybierz pozycję **Utwórz obliczenia**.
1. W wierszu polecenia:
    1. Wybierz typ obliczeń
    1. Wybierz rozmiar maszyny wirtualnej. Dowiedz się więcej o [rozmiarach maszyn wirtualnych](../virtual-machines/sizes.md).
    1. Podaj nazwę dla obliczenia.

### <a name="view-compute-configuration"></a>Wyświetlanie konfiguracji obliczeniowej

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **Klastry obliczeniowe** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy obliczenia, które chcesz wyświetlić, a następnie wybierz polecenie **Wyświetl właściwości obliczania**.

### <a name="edit-compute-scale-settings"></a>Edytuj ustawienia skali obliczeniowej

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **Klastry obliczeniowe** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy obliczenia, które chcesz edytować, a następnie wybierz pozycję **Edytuj obliczenia**.
1. Plik konfiguracji do obliczenia zostanie otwarty w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

### <a name="delete-compute"></a>Usuń obliczenia

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **Klastry obliczeniowe** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy obliczenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń obliczenia**.

### <a name="create-run-configuration"></a>Utwórz konfigurację uruchamiania

Aby utworzyć konfigurację uruchamiania w rozszerzeniu:

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **Klastry obliczeniowe** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy obiekt docelowy obliczeń, w ramach którego chcesz utworzyć konfigurację uruchamiania, a następnie wybierz pozycję **Utwórz konfigurację uruchamiania**.
1. W wierszu polecenia:
    1. Podaj nazwę obiektu docelowego obliczeń
    1. Wybierz lub Utwórz nowe środowisko.
    1. Wpisz nazwę skryptu, który chcesz uruchomić, lub naciśnij klawisz **Enter** w przeglądarce, aby uzyskać skrypt na komputerze lokalnym.
    1. Obowiązkowe Wybierz, czy chcesz utworzyć odwołanie do danych dla danego przebiegu szkoleniowego. Spowoduje to wyświetlenie monitu o zdefiniowanie zestawu danych w konfiguracji przebiegu.
        1. Wybierz jeden z zarejestrowanego zestawu danych, aby połączyć się z konfiguracją uruchomieniową plik konfiguracji dla tego elementu DataSet zostanie otwarty w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.
    1. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

### <a name="edit-run-configuration"></a>Edytuj konfigurację uruchomieniową

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł klaster obliczeniowy w węźle **Klastry obliczeniowe** obszaru roboczego.
1. Kliknij prawym przyciskiem myszy konfigurację uruchamiania, którą chcesz edytować, a następnie wybierz polecenie **Edytuj konfigurację uruchomieniową**.
1. Plik konfiguracji dla konfiguracji przebiegu zostanie otwarty w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

### <a name="delete-run-configuration"></a>Usuń konfigurację przebiegu

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Rozwiń węzeł klastra obliczeniowego w węźle **Klastry obliczeniowe** .
1. Kliknij prawym przyciskiem myszy konfigurację uruchamiania, którą chcesz edytować, a następnie wybierz polecenie **Usuń konfigurację uruchomieniową**.

## <a name="models"></a>Modele

Aby uzyskać więcej informacji, zobacz [modele](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>Rejestrowanie modelu

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Kliknij prawym przyciskiem myszy węzeł **modele** i wybierz polecenie **zarejestruj model**.
1. W wierszu polecenia:
    1. Podaj nazwę modelu
    1. Wybierz, czy model jest plikiem czy folderem.
    1. Znajdź model na komputerze lokalnym.
    1. Plik konfiguracji dla modelu w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

### <a name="view-model-properties"></a>Wyświetl właściwości modelu

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **modele** wewnątrz obszaru roboczego.
1. Kliknij prawym przyciskiem myszy Model, którego właściwości chcesz wyświetlić, a następnie wybierz polecenie **Wyświetl właściwości modelu**. Plik zostanie otwarty w edytorze zawierającym właściwości modelu.

### <a name="download-model"></a>Pobierz model

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **modele** wewnątrz obszaru roboczego.
1. Kliknij prawym przyciskiem myszy Model, który chcesz pobrać, a następnie wybierz pozycję **Pobierz plik modelu**.

### <a name="delete-a-model"></a>Usuwanie modelu

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **modele** wewnątrz obszaru roboczego.
1. Kliknij prawym przyciskiem myszy Model, który chcesz usunąć, a następnie wybierz pozycję **Usuń model**.

## <a name="endpoints"></a>Punkty końcowe

Rozszerzenie VS Code obsługuje następujące cele wdrożenia:

- Azure Container Instances
- Azure Kubernetes Service

Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci Web](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Tworzenie wdrożeń

> [!NOTE]
> Tworzenie wdrożenia działa obecnie tylko ze środowiskami Conda.

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł obszaru roboczego.
1. Kliknij prawym przyciskiem myszy węzeł **punkty końcowe** i wybierz polecenie **Wdróż usługę**.
1. W wierszu polecenia:
    1. Zdecyduj, czy chcesz użyć już zarejestrowanego modelu, czy pliku modelu lokalnego.
    1. Wybierz model
    1. Wybierz miejsce docelowe wdrożenia, do którego chcesz wdrożyć model.
    1. Podaj nazwę modelu.
    1. Podaj skrypt do uruchomienia podczas oceniania modelu.
    1. Podaj plik zależności Conda.
    1. Plik konfiguracji dla danego wdrożenia zostanie wyświetlony w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.

> [!NOTE]
> Alternatywnie możesz kliknąć prawym przyciskiem myszy zarejestrowany model w węźle *modele* i wybrać polecenie **Wdróż usługę z zarejestrowanego modelu**.

### <a name="delete-deployments"></a>Usuń wdrożenia

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **punkty końcowe** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy wdrożenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń usługę**.
1. Zostanie wyświetlony monit z potwierdzeniem usunięcia usługi. Wybierz przycisk **OK**.

### <a name="manage-deployments"></a>Zarządzanie wdrożeniami

Oprócz tworzenia i usuwania wdrożeń, można wyświetlać i edytować ustawienia skojarzone z danym wdrożeniem.

1. Rozwiń węzeł subskrypcji zawierający obszar roboczy.
1. Rozwiń węzeł **punkty końcowe** w obszarze roboczym.
1. Kliknij prawym przyciskiem myszy wdrożenie, które chcesz zarządzać:
    - Aby edytować ustawienia, wybierz pozycję **Edytuj usługę**.
        - Plik konfiguracji dla danego wdrożenia zostanie wyświetlony w edytorze. Jeśli masz pewność, że używasz konfiguracji, wybierz pozycję **Zapisz i Kontynuuj** lub Otwórz paletę poleceń vs Code (**Wyświetl > paletę poleceń**) i wpisz **Azure ml: Zapisz i Kontynuuj**.
    - Aby wyświetlić ustawienia konfiguracji wdrożenia, wybierz pozycję **Wyświetl właściwości usługi**.

## <a name="next-steps"></a>Następne kroki

[Uczenie modelu klasyfikacji obrazów](tutorial-train-deploy-image-classification-model-vscode.md) przy użyciu rozszerzenia vs Code.