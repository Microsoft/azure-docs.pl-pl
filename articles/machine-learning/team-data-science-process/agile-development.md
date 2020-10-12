---
title: Opracowywanie projektów analizy danych — proces nauki danych zespołu
description: Wykonaj projekt analizy danych w systematyczny, kontrolowanym przez siebie wersji i współpracy w obrębie zespołu projektowego, korzystając z procesu nauki o danych zespołowych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440290"
---
# <a name="agile-development-of-data-science-projects"></a>Opracowywanie projektów analizy danych

W tym dokumencie opisano sposób, w jaki deweloperzy mogą wykonywać projekt analizy danych w systematyczny, kontrolowanym przez siebie zakresie i współpracy w zespole projektu przy użyciu [procesu nauki o danych zespołowych](overview.md) (przetwarzania TDSP). PRZETWARZANIA TDSP to struktura opracowana przez firmę Microsoft, która oferuje strukturalną sekwencję działań do wydajnego wykonywania rozwiązań analizy predykcyjnej opartych na chmurze. Aby zapoznać się z ogólnymi rolami i zadaniami, które są obsługiwane przez zespół ds. analizy danych na przetwarzania TDSP, zobacz temat [role i zadania zespołowego procesu nauki danych](roles-tasks.md). 

Ten artykuł zawiera instrukcje dotyczące: 

- Wykonaj *Planowanie przebiegu* dla elementów roboczych związanych z projektem.
- Dodaj *elementy robocze* do przebiegów.
- Utwórz i Użyj *szablonu elementu pracy pochodnego Agile* , który jest wyrównany do etapów cyklu życia przetwarzania TDSP.

Poniższe instrukcje przedstawiają kroki niezbędne do skonfigurowania środowiska zespołu przetwarzania TDSP przy użyciu Azure Boards i Azure Repos na platformie Azure DevOps. Instrukcje korzystają z usługi Azure DevOps, ponieważ jest to implementacja przetwarzania TDSP w firmie Microsoft. Jeśli grupa używa innej platformy hostingu kodu, zadania lidera zespołu zwykle nie zmieniają się, ale sposób na ukończenie zadań jest inny. Na przykład łączenie elementu pracy z gałęzią git może nie być takie samo w przypadku usługi GitHub, ponieważ jest ona Azure Repos.

Na poniższej ilustracji przedstawiono typowe Planowanie przebiegu, kodowanie i przepływ pracy kontroli źródła dla projektu analizy danych:

![Zespołowe przetwarzanie danych dla celów naukowych](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Typy elementów roboczych

W środowisku planowania przetwarzania TDSP przebiegu są cztery często używane typy *elementów roboczych* : *funkcje*, *historie użytkowników*, *zadania*i *Błędy*. Zaległość wszystkich elementów roboczych jest na poziomie projektu, a nie na poziomie repozytorium git. 

Poniżej przedstawiono definicje typów elementów roboczych:

- **Funkcja**: funkcja odpowiada zakontraktowaniu projektu. Różne zaangażowanie z klientem to różne funkcje i najlepiej wziąć pod uwagę różne etapy projektu jako różne funkcje. W przypadku wybrania schematu, takiego jak *\<ClientName>-\<EngagementName>* nazwa funkcji, można łatwo rozpoznać kontekst projektu i zaangażowania z samych nazw.
  
- **Scenariusz**użycia: historie użytkownika są elementami roboczymi, które są konieczne do zakończenia funkcji. Przykładami scenariuszy użycia są:
  - Pobieranie danych 
  - Eksplorowanie danych 
  - Generuj funkcje
  - Tworzenie modeli
  - Operacjonalizowanie modeli 
  - Ponowne szkolenie modeli
  
- **Zadanie**: zadania są możliwy do przypisania elementy robocze, które należy wykonać w celu ukończenia określonego scenariusza użytkownika. Na przykład zadania w scenariuszu użytkownika *pobieranie danych* mogą być następujące:
  - Pobierz poświadczenia SQL Server
  - Przekazywanie danych do usługi Azure Synapse Analytics
  
- **Usterka**: usterki są problemy w istniejącym kodzie lub dokumentach, które muszą zostać naprawione, aby można było ukończyć zadanie. Jeśli usterki są spowodowane brakującymi elementami roboczymi, mogą one być eskalacją do scenariuszy użytkownika lub zadań. 

Analityki danych mogą być bardziej wygodne przy użyciu szablonu Agile, który zastąpi funkcje, historie użytkownika i zadania z etapami cyklu życia przetwarzania TDSP i podetapami. Aby utworzyć szablon pochodny Agile, który jest zgodny z etapami cyklu życia przetwarzania TDSP, zobacz [Korzystanie z szablonu pracy Agile przetwarzania TDSP](#set-up-agile-dsp-6).

> [!NOTE]
> PRZETWARZANIA TDSP pożyczy się pojęciami dotyczącymi funkcji, historii użytkowników, zadań i błędów w ramach zarządzania kodem oprogramowania (SCM). Koncepcje przetwarzania TDSP mogą się nieco różnić od ich tradycyjnych definicji SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planowanie przebiegów

Wielu analityków danych jest zaangażowanych w wiele projektów, co może potrwać miesiąc i kontynuować w różnych tępy. Planowanie przebiegu jest przydatne w przypadku priorytetyzacji projektów oraz planowania i alokacji zasobów. W Azure Boards można łatwo tworzyć i śledzić elementy robocze oraz zarządzać nimi, a także przeprowadzać Planowanie przebiegu, aby upewnić się, że projekty są przesuwane w oczekiwany sposób.

Aby uzyskać więcej informacji na temat planowania przebiegu, zobacz [przebiegi Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Aby uzyskać więcej informacji o planowaniu przebiegu w Azure Boards, zobacz [przypisywanie elementów zaległości do przebiegu](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Dodawanie funkcji do zaległości 

Po utworzeniu projektu i repozytorium kodu projektu można dodać funkcję do zaległości, aby reprezentować prace dla projektu.

1. Na stronie projektu wybierz pozycję **płyty**  >  **zaległości** na lewym pasku nawigacyjnym. 
   
1. Na karcie **zaległości** , jeśli typ elementu pracy na górnym pasku to **historie**, lista rozwijana i wybierz pozycję **funkcje**. Następnie wybierz pozycję **nowy element roboczy.**
   
   ![Wybierz nowy element roboczy](./media/agile-development/2-sprint-team-overview.png)
   
1. Wprowadź tytuł dla funkcji, zazwyczaj nazwę projektu, a następnie wybierz pozycję **Dodaj do góry**. 
   
   ![Wprowadź tytuł i wybierz pozycję Dodaj do góry](./media/agile-development/3-sprint-team-add-work.png)
   
1. Z listy **zaległości** wybierz i Otwórz nową funkcję. Wypełnij pola Opis, przypisz członka zespołu i ustaw parametry planowania. 
   
   Możesz również połączyć funkcję z repozytorium kodu Azure Repos projektu, wybierając pozycję **Dodaj link** w sekcji **programowanie** . 
   
   Po edytowaniu funkcji wybierz pozycję **zapisz & Zamknij**.
   
   ![Edytuj funkcję i wybierz pozycję Zapisz & Zamknij](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Dodawanie scenariusza użytkownika do funkcji 

W ramach tej funkcji można dodać historie użytkowników, aby opisać najważniejsze kroki niezbędne do ukończenia projektu. 

Aby dodać nową historię użytkownika do funkcji:

1. Na karcie **zaległości** wybierz pozycję **+** z lewej strony funkcji. 
   
   ![Dodaj nową historię użytkownika w ramach funkcji](./media/agile-development/4-sprint-add-story.png)
   
1. Nadaj użytkownikowi tytułowi i edytuj szczegóły, takie jak przypisanie, stan, opis, komentarze, planowanie i priorytet. 
   
   Możesz również połączyć scenariusz użytkownika z gałęzią Azure Repos repozytorium kodu projektu, wybierając pozycję **Dodaj link** w sekcji **programowanie** . Wybierz repozytorium i gałąź, do której chcesz połączyć element roboczy, a następnie wybierz przycisk **OK**.
   
   ![Dodaj łącze](./media/agile-development/5-sprint-edit-story.png)
   
1. Po zakończeniu edycji scenariusza użytkownika wybierz pozycję **zapisz & Zamknij**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Dodawanie zadania do scenariusza użytkownika 

Zadania są szczegółowymi krokami, które są niezbędne do wykonania każdego scenariusza użytkownika. Po zakończeniu wszystkich zadań ze scenariusza użytkownika należy wykonać również ukończenie scenariusza użytkownika. 

Aby dodać zadanie do scenariusza użytkownika, zaznacz **+** obok elementu scenariusz użytkownika i wybierz **zadanie**. Wypełnij tytuł i inne informacje w zadaniu.

![Dodawanie zadania do scenariusza użytkownika](./media/agile-development/7-sprint-add-task.png)

Po utworzeniu funkcji, historii użytkownika i zadaniach można je wyświetlić w widokach **zaległości** lub **tablice** w celu śledzenia ich stanu.

![Widok zaległości](./media/agile-development/8-sprint-backlog-view.png)

![Widok tablic](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Korzystanie z szablonu pracy przetwarzania TDSP Agile

Analityki danych mogą być bardziej wygodne przy użyciu szablonu Agile, który zastąpi funkcje, historie użytkownika i zadania z etapami cyklu życia przetwarzania TDSP i podetapami. W Azure Boards można utworzyć szablon pochodny Agile, który używa etapów cyklu życia przetwarzania TDSP do tworzenia i śledzenia elementów roboczych. Poniższe kroki przedstawiają Konfigurowanie szablonu procesu Agile specyficznego dla analizy danych i tworzenie elementów roboczych analizy danych opartych na szablonie.

### <a name="set-up-an-agile-data-science-process-template"></a>Konfigurowanie szablonu procesu analizy danych Agile

1. Na stronie głównej organizacji usługi Azure DevOps wybierz pozycję **Ustawienia organizacji** w obszarze nawigacji po lewej stronie. 
   
1. W lewym panelu nawigacyjnym **Ustawienia organizacji** w obszarze **płyty**wybierz pozycję **proces**. 
   
1. W okienku **wszystkie procesy** wybierz pozycję **...** obok metody **Agile**, a następnie wybierz pozycję **Utwórz Dziedziczony proces**.
   
   ![Utwórz Dziedziczony proces z Agile](./media/agile-development/10-settings.png) 
   
1. W oknie dialogowym **Tworzenie dziedziczonego procesu z Agile** wprowadź nazwę *AgileDataScienceProcess*i wybierz pozycję **Utwórz proces**.
   
   ![Utwórz proces AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. We **wszystkich procesach**wybierz nowy **AgileDataScienceProcess**. 
   
1. Na karcie **typy elementów roboczych** Wyłącz **epiku**, **funkcję**, **historię użytkownika**i **zadanie** , wybierając pozycję **...** obok każdego elementu, a następnie wybierz pozycję **Wyłącz**. 
   
   ![Wyłącz typy elementów roboczych](./media/agile-development/12-disable.png)
   
1. We **wszystkich procesach**wybierz kartę **poziomy zaległości** . W obszarze **zaległości portfolio**wybierz pozycję **...** obok pozycji **epiku (wyłączone)**, a następnie wybierz pozycję **Edytuj/Zmień nazwę**. 
   
1. W oknie dialogowym **Edytowanie poziomu zaległości** :
   1. W obszarze **Nazwa**Zastąp **epiku** *projektami przetwarzania TDSP*. 
   1. W obszarze **typy elementów roboczych na tym poziomie zaległości**wybierz pozycję **nowy typ elementu pracy**, wprowadź *projekt przetwarzania TDSP*i wybierz pozycję **Dodaj**. 
   1. W obszarze **domyślny typ elementu pracy**rozwiń listę rozwijaną i wybierz pozycję **projekt przetwarzania TDSP**. 
   1. Wybierz pozycję **Zapisz**.
   
   ![Ustaw poziom zaległości portfolio](./media/agile-development/13-rename.png)  
   
1. Wykonaj te same kroki, aby zmienić nazwy **funkcji** na *etapy przetwarzania TDSP*i dodać następujące nowe typy elementów roboczych:
   
   - *Zrozumienie biznesowe*
   - *Pozyskiwanie danych*
   - *Modelowanie*
   - *Wdrożenie*
   
1. W **obszarze zaległości wymagania**Zmień nazwy **wątków** na *podetapy przetwarzania TDSP*, Dodaj nowy typ elementu pracy *przetwarzania TDSP podetapu*i ustaw domyślny typ elementu pracy na **podetap przetwarzania TDSP**.
   
1. W obszarze **zaległości iteracji**Dodaj nowy typ elementu pracy *przetwarzania TDSP zadania*i ustaw go jako domyślny typ elementu pracy. 
   
Po wykonaniu tych kroków poziomy zaległości powinny wyglądać następująco:
   
 ![Poziomy zaległości szablonu przetwarzania TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Tworzenie elementów roboczych procesu nauki o danych Agile

Za pomocą szablonu procesu analizy danych można tworzyć projekty przetwarzania TDSP i śledzić elementy robocze, które odpowiadają etapom cyklu życia przetwarzania TDSP.

1. Na stronie głównej organizacji usługi Azure DevOps wybierz pozycję **Nowy projekt**. 
   
1. W oknie dialogowym **Utwórz nowy projekt** Nadaj projektowi nazwę, a następnie wybierz pozycję **Zaawansowane**. 
   
1. W obszarze **proces elementu pracy**rozwiń listę rozwijaną i wybierz pozycję **AgileDataScienceProcess**, a następnie wybierz pozycję **Utwórz**.
   
   ![Tworzenie projektu przetwarzania TDSP](./media/agile-development/15-newproject.png)
   
1. W nowo utworzonym projekcie wybierz pozycję **płyty**  >  **zaległości** na lewym pasku nawigacyjnym.
   
1. Aby wyświetlić przetwarzania TDSP projekty, wybierz ikonę **Konfiguruj ustawienia zespołu** . Na ekranie **Ustawienia** zaznacz pole wyboru **projekty przetwarzania TDSP** , a następnie wybierz pozycję **Zapisz i Zamknij**.
   
   ![Zaznacz pole wyboru projekty przetwarzania TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Aby utworzyć projekt przetwarzania TDSP specyficzny dla analizy danych, wybierz pozycję **projekty przetwarzania TDSP** na górnym pasku, a następnie wybierz pozycję **nowy element roboczy**. 
   
1. W oknie podręcznym Nadaj nazwę elementowi roboczemu projektu przetwarzania TDSP, a następnie wybierz pozycję **Dodaj do góry**.
   
   ![Utwórz element roboczy projektu analizy danych](./media/agile-development/17-dsworkitems0.png)
   
1. Aby dodać element roboczy w ramach projektu przetwarzania TDSP, zaznacz **+** obok projektu, a następnie wybierz typ elementu pracy do utworzenia. 
   
   ![Wybierz typ elementu pracy analizy danych](./media/agile-development/17-dsworkitems1.png)
   
1. Wypełnij szczegóły w nowym elemencie roboczym, a następnie wybierz pozycję **zapisz & Zamknij**.
   
1. Kontynuuj Wybieranie **+** symboli obok elementów roboczych, aby dodać nowe etapy przetwarzania TDSP, podetapów i zadań. 
   
Oto przykład sposobu wyświetlania elementów roboczych projektu analizy danych w widoku **zaległości** :

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Następne kroki

Programowanie [do współpracy przy użyciu narzędzia Git](collaborative-coding-with-git.md) zawiera opis sposobu wykonywania współpracy w zakresie tworzenia kodu dla projektów analizy danych przy użyciu narzędzia Git jako struktury tworzenia kodu współdzielonego oraz sposobu łączenia tych działań kodowania z pracą zaplanowaną z procesem Agile.

W [przykładowych przewodnikach](walkthroughs.md) przedstawiono wskazówki dotyczące konkretnych scenariuszy, z linkami i opisami miniatur. Połączone scenariusze ilustrują sposób łączenia narzędzi i usług w chmurze i lokalnych z przepływami pracy lub potokami w celu tworzenia inteligentnych aplikacji.
  
Dodatkowe zasoby dotyczące procesów Agile:

- [Proces Agile](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Typy elementów roboczych procesu Agile i przepływ pracy](/azure/devops/boards/work-items/guidance/agile-process-workflow)

