---
title: 'Interaktywny debugowanie: VS Code & wystąpienia obliczeniowe ML'
titleSuffix: Azure Machine Learning
description: Skonfiguruj VS Code zdalnie, aby interaktywnie debugować kod przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 19d4b6e7994450eacacab51e9a01cbf58eab803a
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433139"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Interaktywna debugowanie w wystąpieniu obliczeniowym Azure Machine Learning przy użyciu VS Code zdalnego

W tym artykule dowiesz się, jak skonfigurować Visual Studio Code zdalne dla wystąpienia obliczeniowego Azure Machine Learning, dzięki czemu możesz **interaktywnie debugować kod** z vs Code. 

+ [Wystąpienie obliczeniowe Azure Machine Learning](concept-compute-instance.md) jest w pełni zarządzaną stacją roboczą opartą na chmurze dla analityków danych i udostępniają funkcje zarządzania i gotowości przedsiębiorstwa dla administratorów IT. 


+ [Visual Studio Code zdalny](https://code.visualstudio.com/docs/remote/remote-overview) Programowanie umożliwia korzystanie z kontenera, maszyny zdalnej lub podsystemu Windows dla systemu Linux (WSL) jako w pełni funkcjonalnego środowiska deweloperskiego. 

## <a name="prerequisite"></a>Wymaganie wstępne  

Na platformach Windows należy [zainstalować klienta SSH zgodnego z OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , jeśli jeszcze go nie ma. 

> [!Note]
> Instrukcje nie są obsługiwane w systemie Windows, ponieważ polecenie SSH musi znajdować się w ścieżce. 

## <a name="get-ip-and-ssh-port"></a>Pobieranie portu IP i protokołu SSH 

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

   **System Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Klucz prywatny będzie wyglądać następująco:
   ```
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

## <a name="add-instance-as-a-host"></a>Dodaj wystąpienie jako hosta 

Otwórz plik `~/.ssh/config` (Linux) lub `C:\Users<username>.ssh\config` (Windows) w edytorze i Dodaj nowy wpis podobny do tego:

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

## <a name="connect-vs-code-to-the-instance"></a>Połącz VS Code z wystąpieniem 

1. [Zainstaluj Visual Studio Code](https://code.visualstudio.com/).

1. [Zainstaluj rozszerzenie Remote SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Kliknij ikonę Remote-SSH po lewej stronie, aby wyświetlić konfiguracje protokołu SSH.

1. Kliknij prawym przyciskiem myszy właśnie utworzoną konfigurację hosta SSH.

1. **W bieżącym oknie wybierz pozycję Połącz z hostem**. 

W tym miejscu Pracujesz w całości na wystąpieniu obliczeniowym. Możesz teraz edytować, debugować, korzystać z usługi git, używać rozszerzeń itp. — podobnie jak w przypadku lokalnego Visual Studio Code. 

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu Visual Studio Code zdalnej, można użyć wystąpienia obliczeniowego jako zdalnego obliczenia z Visual Studio Code do interaktywnego debugowania kodu. 

[Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.