---
title: Przegląd Azure Cloud Shell | Microsoft Docs
description: Przegląd Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89468661"
---
# <a name="overview-of-azure-cloud-shell"></a>Omówienie usługi Azure Cloud Shell

Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę.

Dostęp do Cloud Shell można uzyskać na trzy sposoby:

- **Link bezpośredni**: Otwórz przeglądarkę do [https://shell.azure.com](https://shell.azure.com) .

- **Azure Portal**: wybierz ikonę Cloud Shell na [Azure Portal](https://portal.azure.com):

    ![Ikona umożliwiająca uruchomienie Cloud Shell z Azure Portal](media/overview/portal-launch-icon.png)

- **Fragmenty kodu**: w [docs.Microsoft.com]() i [Microsoft Learn](/learn/)wybierz przycisk **Wypróbuj** , który pojawia się z interfejsem wiersza polecenia platformy Azure i fragmentami kodu Azure PowerShell:

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    Przycisk **wypróbuj go** otwiera Cloud Shell bezpośrednio obok dokumentacji przy użyciu bash (dla fragmentów interfejsu wiersza polecenia platformy Azure) lub programu PowerShell (dla fragmentów kodu Azure PowerShell).

    Aby uruchomić polecenie, użyj polecenia **copy** w fragmencie kodu, użyj **klawiszy CTRL** + **SHIFT** + **v** (Windows/Linux) lub **cmd** + **SHIFT** + **V** (macOS), aby wkleić polecenie, a następnie naciśnij klawisz **Enter**.

## <a name="features"></a>Funkcje

### <a name="browser-based-shell-experience"></a>Środowisko powłoki oparte na przeglądarce

Cloud Shell umożliwia dostęp do opartego na przeglądarce środowiska wiersza polecenia skompilowanego z użyciem zadań zarządzania platformy Azure. Wykorzystanie Cloud Shell do pracy w sposób nieużywany przez maszynę lokalną w sposób zapewniany przez chmurę.

### <a name="choice-of-preferred-shell-experience"></a>Wybór preferowanego środowiska powłoki

Użytkownicy mogą wybierać między bash lub PowerShell.

1. Wybierz **Cloud Shell**.

    ![Ikona Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Wybierz pozycję **bash** lub **PowerShell**.

    ![Wybierz opcję bash lub PowerShell](media/overview/overview-choices.png)

    Po pierwszym uruchomieniu można użyć kontrolki rozwijanej Typ powłoki, aby przełączać się między bash i PowerShell:

    ![Formant listy rozwijanej służący do wybierania bash lub programu PowerShell](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Uwierzytelniona i skonfigurowana stacja robocza platformy Azure

Cloud Shell jest zarządzany przez firmę Microsoft, dzięki czemu zawiera popularne narzędzia wiersza polecenia i obsługę języków. Cloud Shell również bezpiecznie uwierzytelniać się automatycznie w celu natychmiastowego dostępu do zasobów za pomocą interfejsu wiersza polecenia platformy Azure Azure PowerShell lub poleceń cmdlet.

Zapoznaj się z pełną [listą narzędzi zainstalowanych w programie Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Zintegrowany edytor Cloud Shell

Cloud Shell oferuje zintegrowany edytor tekstu graficznego w oparciu o Edytor Monako "open source". Po prostu Twórz i edytuj pliki konfiguracji, uruchamiając je `code .` w celu bezproblemowego wdrożenia za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

[Dowiedz się więcej o edytorze Cloud Shell](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Wiele punktów dostępu

Cloud Shell to elastyczne narzędzie, które może być używane z:

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure)
* [Dokumentacja programu Azure PowerShell](/powershell/azure/)
* [Aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code rozszerzenie konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Łączenie magazynu plików Microsoft Azure

Maszyny Cloud Shell są tymczasowe, ale pliki są utrwalane na dwa sposoby: za pomocą obrazu dysku, a przez zainstalowany udział plików o nazwie `clouddrive` . Podczas pierwszego uruchomienia usługa Cloud Shell monituje o utworzenie grupy zasobów, konta magazynu i udziału usługi Azure Files w Twoim imieniu. Jest to krok jednorazowy, który zostanie automatycznie dołączony do wszystkich sesji. Pojedynczy udział plików może być mapowany i będzie używany przez programy bash i PowerShell w Cloud Shell.

Przeczytaj więcej, aby dowiedzieć się, jak zainstalować [nowe lub istniejące konto magazynu](persisting-shell-storage.md) lub poznać [mechanizmy trwałości używane w Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Zapora magazynu Azure nie jest obsługiwana dla kont magazynu usługi Cloud Shell.

## <a name="concepts"></a>Pojęcia

* Cloud Shell działa na tymczasowym hoście dostępnym dla poszczególnych sesji i poszczególnych użytkowników
* Cloud Shell limit czasu po 20 minutach bez aktywności interaktywnej
* Cloud Shell wymaga zainstalowania udziału plików platformy Azure
* Cloud Shell używa tego samego udziału plików platformy Azure dla programu bash i programu PowerShell
* Cloud Shell przypisano jeden komputer na konto użytkownika
* Cloud Shell utrzymuje $HOME przy użyciu obrazu 5 GB przechowywanego w udziale plików
* Uprawnienia są ustawiane jako zwykły użytkownik systemu Linux w bash

Dowiedz się więcej o funkcjach w programie [Bash Cloud Shell](features.md) i [programu PowerShell w programie Cloud Shell](./features.md).

## <a name="pricing"></a>Ceny

Cloud Shell hostingu maszynowego jest bezpłatny z warunkiem wstępnym udziału Azure Files zainstalowanego. Obowiązują standardowe koszty magazynowania.

## <a name="next-steps"></a>Następne kroki

[Bash w Cloud Shell Szybki Start](quickstart.md) <br>
[Program PowerShell w Cloud Shell Szybki Start](quickstart-powershell.md)