---
title: plik dołączany
description: plik dołączany
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183664"
---
## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Repozytoria kodu źródłowego zdalnego monitorowania obejmują kod źródłowy i pliki konfiguracji platformy Docker wymagane do uruchomienia obrazów platformy Docker mikrousług.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia, aby przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jeden z następujących zestawów poleceń, aby sklonować repozytorium .NET:

Aby pobrać najnowszą wersję implementacji mikrousług platformy .NET, uruchom polecenie:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Te polecenia umożliwiają pobranie kodu źródłowego dla wszystkich mikrousług oprócz skryptów używanych do lokalnego uruchamiania mikrousług. Chociaż nie jest potrzebny kod źródłowy do uruchamiania mikrousług w Docker, kod źródłowy jest przydatny, jeśli później planujesz zmodyfikować Akcelerator rozwiązania i przetestować zmiany lokalnie.

## <a name="deploy-the-azure-services"></a>Wdrażanie usług platformy Azure

Chociaż w tym artykule przedstawiono sposób lokalnego uruchamiania mikrousług, zależą one od usług platformy Azure działających w chmurze. Użyj poniższego skryptu, aby wdrożyć usługi platformy Azure. W poniższych przykładach skryptu założono, że używasz repozytorium .NET na komputerze z systemem Windows. Jeśli pracujesz w innym środowisku, Dostosuj odpowiednio ścieżki, rozszerzenia plików i separatory ścieżki.

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli nie utworzono jeszcze wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia przejdź do folderu **\services\scripts\local\launch** w sklonowanej kopii repozytorium.

1. Uruchom następujące polecenia, aby zainstalować narzędzie **interfejsu wiersza polecenia i** zalogować się do konta platformy Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Uruchom skrypt **Start. cmd** . Skrypt prosi o następujące informacje:
   * Nazwa rozwiązania.
   * Subskrypcja platformy Azure, która ma być używana.
   * Lokalizacja centrum danych platformy Azure do użycia.

     Skrypt tworzy grupę zasobów na platformie Azure przy użyciu nazwy rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure, które są wykorzystywane przez akcelerator rozwiązań. Tę grupę zasobów można usunąć, gdy nie są już potrzebne odpowiednie zasoby.

     Skrypt dodaje również zestaw zmiennych środowiskowych z prefiksowymi **komputerami** do komputera lokalnego. Te zmienne środowiskowe udostępniają szczegóły zdalnego monitorowania, które mają mieć możliwość odczytu z zasobu Azure Key Vault. Ten Key Vault zasób polega na tym, że monitorowanie zdalne odczyta jego wartości konfiguracyjne z.

     > [!TIP]
     > Po zakończeniu działania skryptu zapisuje także zmienne środowiskowe do pliku o nazwie ** \<your home folder\> \\ . PCs \\ \<solution name\> . env**. Można ich używać do przyszłych wdrożeń akceleratora rozwiązań. Należy pamiętać, że wszystkie zmienne środowiskowe ustawione na komputerze lokalnym zastępują wartości w pliku ** \\ \\ \\ . env skryptów usług** , gdy uruchamiasz **platformę Docker**.

1. Wyjdź z środowiska wiersza polecenia.

### <a name="use-existing-azure-resources"></a>Korzystanie z istniejących zasobów platformy Azure

Jeśli zostały już utworzone wymagane zasoby platformy Azure, Utwórz odpowiednie zmienne środowiskowe na komputerze lokalnym.
Ustaw zmienne środowiskowe dla następujących:
* **PCS_KEYVAULT_NAME** — nazwa zasobu Azure Key Vault
* **PCS_AAD_APPID** — identyfikator aplikacji usługi AAD
* **PCS_AAD_APPSECRET** — wpis tajny aplikacji usługi AAD

Wartości konfiguracyjne zostaną odczytane z tego zasobu Azure Key Vault. Te zmienne środowiskowe mogą być zapisane w pliku ** \<your home folder\> \\ PCs \\ \<solution name\> . env** z wdrożenia. Należy pamiętać, że zmienne środowiskowe ustawione na wartości Zastąp na komputerze lokalnym w pliku ** \\ \\ Local \\ . env skryptów usług** po uruchomieniu **platformy Docker — tworzenie**.

Niektóre konfiguracje wymagające mikrousługi są przechowywane w wystąpieniu **Key Vault** , które zostało utworzone podczas początkowego wdrożenia. Odpowiednie zmienne w magazynie kluczy powinny być modyfikowane w razie konieczności.