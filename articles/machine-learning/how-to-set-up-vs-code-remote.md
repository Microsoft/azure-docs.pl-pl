---
title: Połącz z wystąpieniem obliczeniowym w Visual Studio Code (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nawiązać połączenie z wystąpieniem obliczeniowym Azure Machine Learning w programie Visual Studio Code, aby uruchamiać interaktywne Jupyter Notebook i zdalne obciążenia programistyczne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 3f3794ed4748eda44e863ff4627dedc0a34691af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98070103"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Nawiązywanie połączenia z wystąpieniem obliczeniowym Azure Machine Learning w Visual Studio Code (wersja zapoznawcza)

W tym artykule dowiesz się, jak nawiązać połączenie z wystąpieniem obliczeniowym Azure Machine Learning przy użyciu Visual Studio Code.

[Wystąpienie obliczeniowe Azure Machine Learning](concept-compute-instance.md) jest w pełni zarządzaną stacją roboczą opartą na chmurze dla analityków danych i udostępniają funkcje zarządzania i gotowości przedsiębiorstwa dla administratorów IT.

Istnieją dwa sposoby nawiązywania połączenia z wystąpieniem obliczeniowym z Visual Studio Code:

* Zdalny serwer Jupyter Notebook. Ta opcja pozwala ustawić wystąpienie obliczeniowe jako serwer zdalny Jupyter Notebook.
* [Visual Studio Code programowanie zdalne](https://code.visualstudio.com/docs/remote/remote-overview). Visual Studio Code programowanie zdalne umożliwia korzystanie z kontenera, maszyny zdalnej lub podsystemu Windows dla systemu Linux (WSL) jako w pełni funkcjonalnego środowiska deweloperskiego.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Skonfiguruj wystąpienie obliczeniowe jako zdalny serwer notesu

Aby skonfigurować wystąpienie obliczeniowe jako serwer zdalny Jupyter Notebook należy spełnić kilka wymagań wstępnych:

* Azure Machine Learning Visual Studio Code rozszerzenia. Aby uzyskać więcej informacji, zobacz [Przewodnik konfigurowania rozszerzenia Visual Studio Code Azure Machine Learning](tutorial-setup-vscode-extension.md).
* Azure Machine Learning obszar roboczy. [Użyj rozszerzenia Azure Machine Learning Visual Studio Code, aby utworzyć nowy obszar roboczy](how-to-manage-resources-vscode.md#create-a-workspace) , jeśli jeszcze go nie masz.

Aby nawiązać połączenie z wystąpieniem obliczeniowym:

1. Otwórz Jupyter Notebook w Visual Studio Code.
1. Po załadowaniu zintegrowanego środowiska notesu wybierz opcję **serwer Jupyter**.

    > [!div class="mx-imgBorder"]
    > ![Lista rozwijana uruchamiania serwera zdalnego Jupyter Notebook Azure Machine Learning](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Alternatywnie można również użyć palety poleceń:

    1. Otwórz paletę poleceń, wybierając pozycję **wyświetl > paleta poleceń** na pasku menu.
    1. Wprowadź tekst w polu tekstowym `Azure ML: Connect to Compute instance Jupyter server` .

1. Wybierz `Azure ML Compute Instances` z listy Opcje serwera Jupyter.
1. Wybierz swoją subskrypcję z listy subskrypcji. Jeśli wcześniej skonfigurowano domyślny obszar roboczy Azure Machine Learning, ten krok zostanie pominięty.
1. Wybierz obszar roboczy.
1. Wybierz wystąpienie obliczeniowe z listy. Jeśli go nie masz, wybierz pozycję **Utwórz nowe wystąpienie usługi Azure środowisko obliczeniowe usługi ml** i postępuj zgodnie z monitami, aby go utworzyć.
1. Aby zmiany zaczęły obowiązywać, należy ponownie załadować Visual Studio Code.
1. Otwórz Jupyter Notebook i uruchom komórkę.

> [!IMPORTANT]
> Aby nawiązać połączenie, **należy** uruchomić komórkę.

W tym momencie można nadal uruchamiać komórki w Jupyter Notebook.

> [!TIP]
> Możesz również współpracować z plikami skryptów języka Python (. PR) zawierającymi komórki kodu podobne do Jupyter. Aby uzyskać więcej informacji, zobacz [dokumentację interaktywną języka Python Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Konfigurowanie zdalnego programowania wystąpienia obliczeniowego

W przypadku w pełni funkcjonalnego środowiska tworzenia zdalnego należy spełnić kilka wymagań wstępnych:

* [Visual Studio Code rozszerzenie zdalnego protokołu SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* Wystąpienie obliczeniowe z obsługą protokołu SSH. Aby uzyskać więcej informacji, [zobacz Przewodnik tworzenia wystąpienia obliczeniowego](how-to-create-manage-compute-instance.md).

> [!NOTE]
> Na platformach Windows należy [zainstalować klienta SSH zgodnego z OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , jeśli jeszcze go nie ma. Instrukcje nie są obsługiwane w systemie Windows, ponieważ polecenie SSH musi znajdować się w ścieżce.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Pobieranie portu IP i protokołu SSH dla wystąpienia obliczeniowego

1. Przejdź do Azure Machine Learning Studio na stronie https://ml.azure.com/ .
2. Wybierz [obszar roboczy](concept-workspace.md).
1. Kliknij kartę **wystąpienia obliczeniowe** .
1. W kolumnie **Identyfikator URI aplikacji** kliknij link **SSH** wystąpienia obliczeniowego, którego chcesz użyć jako zdalnego obliczania. 
1. W oknie dialogowym Zwróć uwagę na adres IP i port SSH. 
1. Zapisz swój klucz prywatny w katalogu ~/.SSH/na komputerze lokalnym; na przykład Otwórz edytor dla nowego pliku i Wklej klucz w: 

   System **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   Klucz prywatny będzie wyglądać następująco:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Zmień uprawnienia do pliku, aby upewnić się, że tylko można odczytać plik.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Dodaj wystąpienie jako hosta

Otwórz plik `~/.ssh/config` (Linux) lub `C:\Users<username>.ssh\config` (Windows) w edytorze i Dodaj nowy wpis podobny do poniższej zawartości:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Poniżej przedstawiono niektóre szczegóły dotyczące pól:

|Pole|Opis|
|----|---------|
|Host|Używaj dowolnych skrótów dla wystąpienia obliczeniowego |
|HostName|Jest to adres IP wystąpienia obliczeniowego |
|Port|Jest to port wyświetlany w oknie dialogowym SSH powyżej |
|Użytkownik|Musi to być `azureuser` |
|IdentityFile|Powinien wskazywać plik, w którym zapisano klucz prywatny |

Teraz powinno być możliwe użycie protokołu SSH w wystąpieniu obliczeniowym przy użyciu składni użytej powyżej `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>Połącz VS Code z wystąpieniem

1. Kliknij ikonę Remote-SSH na pasku działania Visual Studio Code, aby wyświetlić konfiguracje protokołu SSH.

1. Kliknij prawym przyciskiem myszy właśnie utworzoną konfigurację hosta SSH.

1. **W bieżącym oknie wybierz pozycję Połącz z hostem**. 

W tym miejscu Pracujesz w całości na wystąpieniu obliczeniowym. Możesz teraz edytować, debugować, korzystać z usługi git, używać rozszerzeń itp. — podobnie jak w przypadku lokalnego Visual Studio Code.

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu Visual Studio Code zdalnej, można użyć wystąpienia obliczeniowego jako zdalnego obliczenia z Visual Studio Code do [interaktywnego debugowania kodu](how-to-debug-visual-studio-code.md).

[Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.
