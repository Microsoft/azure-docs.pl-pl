---
title: Zadania dla lidera projektu w procesie nauki danych zespołu
description: Szczegółowy przewodnik dotyczący zadań dla projektu, który prowadzi do zespołu procesów nauki o danych zespołowych
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000984"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Zadania lidera projektu w procesie nauki danych zespołu

W tym artykule opisano zadania wykonywane przez *lidera projektu* w celu skonfigurowania repozytorium dla zespołu projektu w [procesie nauki o danych zespołowych](overview.md) (przetwarzania TDSP). PRZETWARZANIA TDSP to struktura opracowana przez firmę Microsoft, która oferuje strukturalną sekwencję działań do wydajnego wykonywania rozwiązań analizy predykcyjnej opartych na chmurze. PRZETWARZANIA TDSP zaprojektowano tak, aby pomóc w ulepszaniu współpracy i uczenia zespołu. Aby zapoznać się z zarysem ról pracowników i skojarzonych zadań dla zespołu ds. analizy danych w przetwarzania TDSP, zobacz temat [role i zadania zespołowego procesu nauki danych](roles-tasks.md).

Lider projektu zarządza codziennymi działaniami poszczególnych naukowców danych w konkretnym projekcie analizy danych w przetwarzania TDSP. Na poniższym diagramie przedstawiono przepływ pracy dla zadań liderów projektu:

![Przepływ pracy zadania lidera projektu](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

W tym samouczku omówiono krok 1. Tworzenie repozytorium projektu i krok 2: repozytorium projektu w repozytorium ProjectTemplate zespołu. 

Dla kroku 3: Utwórz element roboczy funkcji dla projektu i krok 4. Dodawanie wątków dla faz projektu, zobacz [programowanie Agile w projektach naukowych](agile-development.md).

W przypadku kroku 5: Tworzenie i dostosowywanie zasobów magazynu/analizy i udziału, w razie potrzeby, zobacz [Tworzenie danych zespołu i zasoby analityczne](team-lead-tasks.md#create-team-data-and-analytics-resources).

W kroku 6: Konfigurowanie kontroli zabezpieczeń repozytorium projektów, zobacz [Dodawanie członków zespołu i Konfigurowanie uprawnień](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> W tym artykule Azure Repos można skonfigurować projektu przetwarzania TDSP, ponieważ jest to sposób implementacji przetwarzania TDSP w firmie Microsoft. Jeśli Twój zespół korzysta z innej platformy hostingu kodu, zadania lidera projektu są takie same, ale sposób ich wykonania może się różnić.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [Menedżer grupy](group-manager-tasks.md) i [lider zespołu](team-lead-tasks.md) skonfigurowali następujące zasoby i uprawnienia:

- **Organizacja** usługi Azure DevOps dla Twojej jednostki danych
- **Projekt** zespołowy Twojego zespołu ds. analizy danych
- **Repozytoria** szablonów i narzędzi
- **Uprawnienia** na koncie organizacji umożliwiające tworzenie i edytowanie repozytoriów dla projektu

Aby sklonować repozytoria i zmodyfikować zawartość na komputerze lokalnym lub Data Science Virtual Machine (DSVM) lub skonfigurować usługę Azure File Storage i zainstalować ją w DSVM, należy również wziąć pod uwagę tę listę kontrolną:

- Subskrypcja platformy Azure.
- Na maszynie zainstalowano narzędzie git. Jeśli używasz DSVM, program git jest wstępnie zainstalowany. W przeciwnym razie zapoznaj się z [dodatkiem platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, system Windows lub Linux DSVM utworzony i skonfigurowany na platformie Azure. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z [dokumentacją Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- W przypadku systemu Windows DSVM na komputerze zainstalowano program [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . W pliku *README.MD* przewiń w dół do sekcji **pobieranie i instalacja** , a następnie wybierz **najnowszy Instalator**. Pobierz instalatora *exe* ze strony Instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny skonfigurowany na DSVM i dodany do usługi Azure DevOps. Aby uzyskać więcej informacji i instrukcje, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku platformy i narzędzia](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Tworzenie repozytorium projektu w projekcie zespołowym

Aby utworzyć repozytorium projektu w projekcie **Webteam** zespołu:

1. Przejdź do strony **podsumowania** projektu zespołu przy *użyciu protokołu HTTPS \/ / \<server name> / \<organization name> / \<team name> :*, na przykład **https: \/ /dev.Azure.com/DataScienceUnit/myTeam**, a następnie wybierz pozycję **repozytoria** z lewego obszaru nawigacji. 
   
1. Wybierz nazwę repozytorium w górnej części strony, a następnie wybierz pozycję **nowe repozytorium** z listy rozwijanej.
   
   ![Wybierz nowe repozytorium](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. W oknie dialogowym **Tworzenie nowego repozytorium** upewnij się, że wybrano opcję **git** w obszarze **Typ**. Wprowadź *DSProject1* w polu **Nazwa repozytorium**, a następnie wybierz pozycję **Utwórz**.
   
   ![Utwórz repozytorium](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Upewnij się, że na stronie ustawień projektu jest widoczne nowe repozytorium **DSProject1** . 
   
   ![Repozytorium projektu w ustawieniach projektu](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importowanie szablonu zespołowego do repozytorium projektu

Aby wypełnić repozytorium projektu zawartością Twojego repozytorium szablonów zespołu:

1. Na stronie **Podsumowanie** projektu zespołu wybierz pozycję **repozytoria** w lewym okienku nawigacji. 
   
1. Wybierz nazwę repozytorium w górnej części strony, a następnie wybierz pozycję **DSProject1** z listy rozwijanej.
   
1. Na stronie **DSProject1 jest pusta** wybierz pozycję **Importuj**. 
   
   ![Wybierz pozycję Importuj](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. W oknie dialogowym **Importowanie repozytorium git** wybierz pozycję **git** jako **Typ źródła**, a następnie wprowadź adres URL repozytorium **TeamTemplate** w obszarze **Klonowanie adresu URL**. Adres URL to *https: \/ / \<server name> / \<organization name> / \<team name> /_git/ \<team template repository name>*. Na przykład: **https: \/ /dev.Azure.com/DataScienceUnit/myTeam/_git/TeamTemplate**. 
   
1. Wybierz pozycję **Importuj**. Zawartość repozytorium szablonów zespołu jest importowana do repozytorium projektu. 
   
   ![Importuj repozytorium szablonów zespołu](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Jeśli musisz dostosować zawartość repozytorium projektu w celu spełnienia określonych wymagań projektu, możesz dodać, usunąć lub zmodyfikować pliki i foldery repozytorium. Możesz bezpośrednio korzystać z Azure Repos lub klonować repozytorium na komputerze lokalnym lub w DSVM, wprowadzać zmiany i zatwierdzić i wypchnąć aktualizacje do repozytorium projektu udostępnionego. Postępuj zgodnie z instrukcjami w obszarze [Dostosowywanie zawartości repozytoriów zespołu](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się linki do szczegółowych opisów innych ról i zadań zdefiniowanych przez proces nauka danych zespołu:

- [Zadania Menedżera grupy dla zespołu ds. analizy danych](group-manager-tasks.md)
- [Zadania lidera zespołu dla zespołu ds. analizy danych](team-lead-tasks.md)
- [Indywidualne zadania współautor dla zespołu ds. analizy danych](project-ic-tasks.md)