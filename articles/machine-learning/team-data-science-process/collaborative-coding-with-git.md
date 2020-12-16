---
title: Programowanie zespołowe za pomocą usługi git — zespołowe przetwarzanie danych dla celów naukowych
description: Jak wspólnie opracowywać kod dla projektów analizy danych przy użyciu narzędzia Git i planowania Agile.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588713"
---
# <a name="collaborative-coding-with-git"></a>Wspólne kodowanie przy użyciu narzędzia Git

W tym artykule opisano, jak używać narzędzia Git jako struktury współpracy programistycznej kodu dla projektów analizy danych. W tym artykule opisano sposób łączenia kodu w Azure Repos z elementami roboczymi [programowania Agile](agile-development.md) w Azure Boards, jak przeprowadzać przeglądy kodu oraz jak tworzyć i scalać żądania ściągnięcia dotyczące zmian.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Łączenie elementu pracy z gałęzią Azure Repos 

Usługa Azure DevOps zapewnia wygodny sposób łączenia scenariusza użytkownika Azure Boards lub elementu pracy zadania przy użyciu gałęzi repozytorium Azure Repos git. Możesz połączyć historię użytkownika lub zadanie bezpośrednio z kodem, z którym jest skojarzony. 

Aby połączyć element roboczy z nową gałęzią, wybierz pozycję **Akcje** wielokropka (**...**) obok elementu pracy, a następnie w menu kontekstowym przewiń do i wybierz pozycję **nowe rozgałęzienie**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

W oknie dialogowym **Tworzenie rozgałęzień** Podaj nazwę nowego rozgałęzienia i podstawowy Azure Repos repozytorium i gałąź usługi git. Repozytorium podstawowe musi znajdować się w tym samym projekcie usługi Azure DevOps, co element roboczy. Gałąź podstawowa może być dowolną istniejącą gałęzią. Wybierz pozycję **Utwórz gałąź**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Możesz również utworzyć nową gałąź przy użyciu następującego polecenia git bash w systemie Windows lub Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Jeśli nie określisz \<base branch name> , Nowa gałąź jest oparta na `main` . 

Aby przełączyć się do gałęzi roboczej, uruchom następujące polecenie: 

```bash
git checkout <working branch name>
```

Po przełączeniu do gałęzi roboczej można rozpocząć opracowywanie kodu lub artefaktów dokumentacji, aby zakończyć element roboczy. Uruchamianie `git checkout main` przełączników z powrotem do `main` gałęzi.

Dobrym sposobem jest utworzenie gałęzi git dla każdego elementu pracy scenariusza użytkownika. Następnie dla każdego elementu pracy zadania można utworzyć gałąź na podstawie gałęzi scenariusza użytkownika. Uporządkuj gałęzie w hierarchii, które odnoszą się do Story-Task użytkownika w przypadku wielu osób pracujących w różnych wątkach użytkownika dla tego samego projektu lub na różnych zadaniach dla tego samego scenariusza użytkownika. Można zminimalizować konflikty, ponieważ każdy członek zespołu pracuje nad inną gałęzią lub innym kodem lub innymi artefaktami podczas udostępniania gałęzi. 

Na poniższym diagramie przedstawiono zalecaną strategię rozgałęziania dla przetwarzania TDSP. Może nie być potrzebna tyle gałęzi, jak pokazano w tym miejscu, zwłaszcza gdy tylko jedna lub dwie osoby pracują nad projektem lub tylko jedna osoba pracuje nad wszystkimi zadaniami ze scenariusza użytkownika. Jednak rozdzielenie gałęzi deweloperskiej od gałęzi głównej jest zawsze dobrym sposobem i może pomóc zapobiec przerwaniu gałęzi wydania przez działania deweloperskie. Pełny opis modelu rozgałęzień usługi git można znaleźć w [pomyślnym modelu rozgałęziania usługi git](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Można również połączyć element roboczy z istniejącą gałęzią. Na stronie **szczegółów** elementu pracy wybierz pozycję **Dodaj link**. Następnie wybierz istniejącą gałąź, do której chcesz połączyć element roboczy, a następnie wybierz przycisk **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Pracuj nad gałęzią i zatwierdzaj zmiany 

Po wprowadzeniu zmian w elemencie roboczym, takim jak dodanie pliku skryptu języka R do gałęzi komputera lokalnego `script` , można zatwierdzić zmianę gałęzi lokalnej do nadrzędnej gałęzi roboczej przy użyciu następujących poleceń usługi git bash:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Tworzenie żądania ściągnięcia

Po zakończeniu jednego lub kilku zatwierdzeń i wypchnięciu, gdy wszystko jest gotowe do scalenia bieżącej gałęzi roboczej z gałęzią podstawową, można utworzyć i przesłać *żądanie ściągnięcia* w Azure Repos. 

Na stronie głównej projektu usługi Azure DevOps wskaż pozycję **repozytoria**  >  **żądań ściągnięcia** w lewym okienku nawigacji. Następnie wybierz jeden z przycisków **nowe żądanie ściągnięcia** lub link **Utwórz żądanie ściągnięcia** .

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Na ekranie **nowe żądanie ściągnięcia** , w razie potrzeby, przejdź do repozytorium git i gałęzi, do których chcesz scalić zmiany. Dodaj lub Zmień inne żądane informacje. W obszarze **recenzenci** Dodaj nazwy recenzentów, a następnie wybierz pozycję **Utwórz**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Przeglądanie i scalanie

Po utworzeniu żądania ściągnięcia recenzenci otrzymują powiadomienie e-mail, aby przejrzeć żądanie ściągnięcia. Recenzenci sprawdzają, czy zmiany zadziałały, i sprawdzają zmiany, jeśli jest to możliwe. Recenzenci mogą wprowadzać komentarze, zażądać zmian i zatwierdzić lub odrzucić żądanie ściągnięcia na podstawie ich oceny. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Po zatwierdzeniu zmian przez recenzentów użytkownik lub ktoś inny z uprawnieniami scalania może scalić gałąź roboczą z gałęzią podstawową. Wybierz pozycję **ukończone**, a następnie wybierz pozycję **pełne scalanie** w oknie dialogowym **Ukończ żądanie ściągnięcia** . Po scaleniu można usunąć gałąź roboczą. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Upewnij się, że żądanie zostało oznaczone jako **ukończone**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Po powrocie do **repozytoriów** w lewym okienku nawigacji można zobaczyć, że została przełączona do głównej gałęzi od momentu `script` usunięcia gałęzi.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Można również użyć następujących poleceń git bash do scalenia `script` gałęzi roboczej z gałęzią podstawową i usunięcia gałęzi roboczej po scaleniu:

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Następne kroki

[Wykonywanie zadań analizy danych](execute-data-science-tasks.md) pokazuje, jak używać narzędzi do wykonywania kilku typowych zadań związanych z nauką o danych, takich jak interaktywna Eksploracja danych, analiza danych, raportowanie i tworzenie modelu.

W [przykładowych przewodnikach](walkthroughs.md) przedstawiono wskazówki dotyczące konkretnych scenariuszy, z linkami i opisami miniatur. Połączone scenariusze ilustrują sposób łączenia narzędzi i usług w chmurze i lokalnych z przepływami pracy lub potokami w celu tworzenia inteligentnych aplikacji. 

