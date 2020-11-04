---
title: Zadania dla pojedynczego współautora w procesie nauki danych zespołu
description: Szczegółowy przewodnik dotyczący zadań dla pojedynczego współautora w projekcie zespołowym analizy danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321918"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Zadania dla pojedynczego współautora w procesie nauki danych zespołu

W tym temacie opisano zadania wykonywane przez *pojedynczego współautor* w celu skonfigurowania projektu w [procesie nauki o danych zespołowych](overview.md) (przetwarzania TDSP). Celem jest praca w środowisku zespołu współpracy, które jest zgodne z przetwarzania TDSP. PRZETWARZANIA TDSP zaprojektowano tak, aby pomóc w ulepszaniu współpracy i uczenia zespołu. Aby zapoznać się z zarysem ról pracowników i skojarzonych z nimi zadań, które są obsługiwane przez zespół ds. analizy danych w przetwarzania TDSP, zobacz temat [role i zadania zespołowego procesu nauki danych](roles-tasks.md).

Na poniższym diagramie przedstawiono zadania, które zostały ukończone przez poszczególnych współautorów (analityków danych) w celu skonfigurowania środowiska zespołu. Aby uzyskać instrukcje dotyczące wykonywania projektu analizy danych w ramach przetwarzania TDSP, zobacz [wykonywanie projektów analizy danych](./agile-development.md). 

![Zadania poszczególnych współautorów](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** to repozytorium, którego zespół projektu utrzymuje do udostępniania szablonów i zasobów projektu.
- **TeamUtilities** to repozytorium narzędzi, które zespół utrzymuje w odniesieniu do zespołu. 
- **GroupUtilities** to repozytorium, w którym Twoja grupa udostępnia przydatne narzędzia w całej grupie. 

> [!NOTE] 
> W tym artykule wykorzystano Azure Repos i Data Science Virtual Machine (DSVM) w celu skonfigurowania środowiska przetwarzania TDSP, ponieważ jest to sposób implementacji przetwarzania TDSP w firmie Microsoft. Jeśli Twój zespół korzysta z innych platform hostingowych lub deweloperskich, poszczególne zadania współautora są takie same, ale sposób ich wykonania może być różny.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że następujące zasoby i uprawnienia zostały skonfigurowane przez [Menedżera grupy](group-manager-tasks.md), [lidera zespołu](team-lead-tasks.md)i [Lider projektu](project-lead-tasks.md):

- **Organizacja** usługi Azure DevOps dla Twojej jednostki analizy danych
- **Repozytorium projektu** skonfigurowane przez Twój projekt prowadzi do udostępniania szablonów i zasobów projektu
- Repozytoria **GroupUtilities** i **TeamUtilities** zostały skonfigurowane przez Menedżera grupy i lidera zespołu, jeśli ma zastosowanie
- Usługa Azure **File Storage** skonfigurowana dla zasobów udostępnionych dla zespołu lub projektu, jeśli ma zastosowanie
- **Uprawnienia** do klonowania z i wypychania do repozytorium projektu 

Aby sklonować repozytoria i zmodyfikować zawartość na komputerze lokalnym lub DSVM lub zainstalować usługę Azure File Storage w DSVM, należy wziąć pod uwagę tę listę kontrolną:

- Subskrypcja platformy Azure.
- Na maszynie zainstalowano narzędzie git. Jeśli używasz DSVM, program git jest wstępnie zainstalowany. W przeciwnym razie zapoznaj się z [dodatkiem platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, system Windows lub Linux DSVM utworzony i skonfigurowany na platformie Azure. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z [dokumentacją Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- W przypadku systemu Windows DSVM na komputerze zainstalowano program [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . W pliku *README.MD* przewiń w dół do sekcji **pobieranie i instalacja** , a następnie wybierz **najnowszy Instalator**. Pobierz instalatora *exe* ze strony Instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny skonfigurowany na DSVM i dodany do usługi Azure DevOps. Aby uzyskać więcej informacji i instrukcje, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku platformy i narzędzia](platforms-and-tools.md#appendix). 
- Informacje o usłudze Azure File Storage dla dowolnego magazynu plików platformy Azure, który trzeba zainstalować w DSVM. 

## <a name="clone-repositories"></a>Klonowanie repozytoriów

Aby lokalnie korzystać z repozytoriów i wypchnąć zmiany do współużytkowanego zespołu i repozytoriów projektu, należy najpierw skopiować lub *sklonować* repozytoria na komputerze lokalnym. 

1. W usłudze Azure DevOps przejdź do strony podsumowania projektu zespołu przy *użyciu protokołu HTTPS \/ / \<server name> / \<organization name> / \<team name> :* , na przykład **https: \/ /dev.Azure.com/DataScienceUnit/myTeam**.
   
1. Wybierz pozycję **repozytoria** w lewym okienku nawigacji, a w górnej części strony wybierz repozytorium, które chcesz sklonować.
   
1. Na stronie repozytorium wybierz pozycję **Klonuj** w prawym górnym rogu.
   
1. W oknie dialogowym **klonowanie repozytorium** wybierz opcję **https** dla połączenia HTTP lub **SSH** dla połączenia SSH, a następnie skopiuj adres URL klonowania w **wierszu polecenia** do Schowka.
   
   ![Klonowanie repozytorium](./media/project-ic-tasks/clone.png)
   
1. Na komputerze lokalnym lub DSVM Utwórz następujące katalogi:
   
   - Dla systemu Windows: **C:\GitRepos**
   - Dla systemu Linux: **$Home/gitrepos**
   
1. Przejdź do utworzonego katalogu.
   
1. W narzędziu git bash Uruchom polecenie `git clone <clone URL>` dla każdego repozytorium, które chcesz sklonować. 
   
   Na przykład następujące polecenie klonuje repozytorium **TeamUtilities** do katalogu *Webteam* na komputerze lokalnym. 
   
   **Połączenie HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Połączenie SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Upewnij się, że w lokalnym katalogu projektu można zobaczyć foldery sklonowanych repozytoriów.
   
   ![Trzy lokalne foldery repozytorium](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Instalowanie usługi Azure File Storage na DSVM

Jeśli Twój zespół lub projekt ma udostępnione zasoby w usłudze Azure File Storage, zainstaluj magazyn plików na komputerze lokalnym lub DSVM. Postępuj zgodnie z instrukcjami w obszarze [Instalowanie usługi Azure File Storage na komputerze lokalnym lub DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się linki do szczegółowych opisów innych ról i zadań zdefiniowanych przez proces nauka danych zespołu:

- [Zadania Menedżera grupy dla zespołu ds. analizy danych](group-manager-tasks.md)
- [Zadania lidera zespołu dla zespołu ds. analizy danych](team-lead-tasks.md)
- [Zadania lidera projektu dla zespołu ds. analizy danych](project-lead-tasks.md)