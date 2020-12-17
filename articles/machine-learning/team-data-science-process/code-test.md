---
title: Testowanie kodu nauki danych za pomocą procesu nauki o danych Azure DevOps Services w zespole
description: Testowanie kodu analizy danych na platformie Azure z zestawem danych prognozowania dochodowych osób dorosłych z zespołowym procesem nauki o danych i Azure DevOps Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: ce37ee51e549a2bd6f2747f28af8c038c39d8f1f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656839"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Testowanie kodu analizy danych na platformie Azure z zespołowym procesem nauki danych i Azure DevOps Services
W tym artykule przedstawiono wstępne wskazówki dotyczące testowania kodu w przepływie pracy analizy danych. Takie testowanie umożliwia analitykom danych systematyczne i wydajne sprawdzanie jakości i oczekiwanego wyniku ich kodu. Korzystamy z projektu zespołowego przetwarzania danych (przetwarzania TDSP) [, który korzysta z zestawu danych dochodów dla dorosłych](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , który został opublikowany wcześniej, aby pokazać, jak można przeprowadzić testowanie kodu. 

## <a name="introduction-on-code-testing"></a>Wprowadzenie do testowania kodu
"Testowanie jednostkowe" to od dawna dba rozwiązanie do tworzenia oprogramowania. Jednak w przypadku nauki danych często nie jest jasne, co oznacza "testy jednostkowe" i jak należy przetestować kod dla różnych etapów cyklu życia analizy danych, na przykład:

* Przygotowywanie danych
* Badanie jakości danych
* Modelowanie
* Wdrożenie modelu 

W tym artykule zastępuje się termin "testowanie jednostek" z "testowaniem kodu". Odnosi się do testowania jako funkcje, które pomagają ocenić, czy kod dla pewnego etapu cyklu nauki danych ma wynik "zgodnie z oczekiwaniami". Osoba, która zapiszą test, definiuje co "zgodnie z oczekiwaniami" w zależności od wyniku funkcji — na przykład sprawdzanie jakości danych lub modelowanie.

Ten artykuł zawiera odwołania do przydatnych zasobów.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps dla struktury testowania
W tym artykule opisano sposób przeprowadzania i automatyzowania testowania przy użyciu usługi Azure DevOps. Możesz zdecydować się na korzystanie z alternatywnych narzędzi. Pokazujemy również, jak skonfigurować automatyczną kompilację przy użyciu usługi Azure DevOps i agentów kompilacji. W przypadku agentów kompilacji korzystamy z usługi Azure Data Science Virtual Machines (DSVMs).

## <a name="flow-of-code-testing"></a>Przepływ testów kodu
Ogólny przepływ pracy testowania kodu w projekcie analizy danych wygląda następująco: 

![Wykres przepływu testów kodu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Szczegółowe procedury

Wykonaj następujące kroki, aby skonfigurować i uruchomić testy kodu oraz zautomatyzowaną kompilację przy użyciu agenta kompilacji i usługi Azure DevOps:

1. Utwórz projekt w aplikacji klasycznej programu Visual Studio:

    ![Ekran "Tworzenie nowego projektu" w programie Visual Studio](./media/code-test/create_project.PNG)

   Po utworzeniu projektu znajdziesz go w Eksplorator rozwiązań w okienku po prawej stronie:
    
    ![Procedura tworzenia projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Eksplorator rozwiązań](./media/code-test/solution_explorer_in_vs.PNG)

1. Podawanie kodu projektu do repozytorium kodu projektu usługi Azure DevOps: 

    ![Repozytorium kodu projektu](./media/code-test/create_repo.PNG)

1. Załóżmy, że wykonano pewne prace przygotowywania danych, takie jak pozyskiwanie danych, Inżynieria funkcji i tworzenie kolumn etykiet. Chcesz upewnić się, że kod generuje oczekiwane wyniki. Oto kod, którego można użyć do sprawdzenia, czy kod przetwarzania danych działa prawidłowo:

    * Sprawdź, czy nazwy kolumn są prawidłowe:
    
      ![Kod pasujących nazw kolumn](./media/code-test/check_column_names.PNG)

    * Sprawdź, czy poziomy odpowiedzi są prawidłowe:

      ![Kod pasujących poziomów](./media/code-test/check_response_levels.PNG)

    * Sprawdź, czy wartość procentowa odpowiedzi jest uzasadniona:

      ![Kod procentu odpowiedzi](./media/code-test/check_response_percentage.PNG)

    * Sprawdź brakującą stawkę każdej kolumny w danych:
    
      ![Kod dla brakującej stawki](./media/code-test/check_missing_rate.PNG)


1. Po zakończeniu pracy z przetwarzaniem danych i pracą inżynieryjną i przeszkolonym dobrym modelem upewnij się, że model, który został przeszkolony, może prawidłowo wytwarzać nowe zestawy danych. Aby sprawdzić poziomy przewidywania i rozkład wartości etykiet, można użyć następujących dwóch testów:

    * Sprawdź poziomy przewidywania:
    
      ![Kod do sprawdzania poziomów przewidywania](./media/code-test/check_prediction_levels.PNG)

    * Sprawdź rozkład wartości przewidywania:

      ![Kod do sprawdzania wartości przewidywania](./media/code-test/check_prediction_values.PNG)

1. Umieść wszystkie funkcje testowe w skrypcie języka Python o nazwie **test_funcs. PR**:

    ![Skrypt języka Python dla funkcji testowych](./media/code-test/create_file_test_func.PNG)


1. Po przygotowaniu kodów testów można skonfigurować środowisko testowania w programie Visual Studio.

   Utwórz plik w języku Python o nazwie **Test1.py**. W tym pliku Utwórz klasę, która zawiera wszystkie testy, które chcesz wykonać. Poniższy przykład pokazuje sześć testów przygotowane:
    
    ![Plik Python z listą testów w klasie](./media/code-test/create_file_test1_class.PNG)

1. Te testy mogą być automatycznie odnajdywane, jeśli po nazwie klasy umieścisz **codetest. przypadku testowego** . Otwórz Eksploratora testów w prawym okienku, a następnie wybierz pozycję **Uruchom wszystkie**. Wszystkie testy zostaną uruchomione sekwencyjnie i będą informować o tym, czy test zakończy się powodzeniem, czy nie.

    ![Uruchamianie testów](./media/code-test/run_tests.PNG)

1. Zaewidencjonuj swój kod w repozytorium projektu przy użyciu poleceń git. Najnowsza z Twoich zadań zostanie wkrótce uwzględniona na platformie Azure DevOps.

    ![Polecenia narzędzia Git do sprawdzania kodu](./media/code-test/git_check_in.PNG)

    ![Najnowsza wersja pracy w usłudze Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Skonfiguruj automatyczne Kompilowanie i testowanie w usłudze Azure DevOps:

    a. W repozytorium projektu wybierz pozycję **kompilacja i wydanie**, a następnie wybierz pozycję **+ Nowy** , aby utworzyć nowy proces kompilacji.

    ![Wybory dotyczące rozpoczynania nowego procesu kompilacji](./media/code-test/create_new_build.PNG)

    b. Postępuj zgodnie z monitami, aby wybrać lokalizację kodu źródłowego, nazwę projektu, repozytorium i informacje o gałęzi.
    
    ![Informacje źródłowe, nazwa, repozytorium i gałąź](./media/code-test/fill_in_build_info.PNG)

    c. Wybierz szablon. Ponieważ nie ma szablonu projektu języka Python, Zacznij od wybrania **pustego procesu**. 

    ![Lista szablonów i przycisk "pusty proces"](./media/code-test/start_empty_process_template.PNG)

    d. Nadaj nazwę kompilacji i wybierz agenta. W tym miejscu możesz wybrać wartość domyślną, jeśli chcesz użyć DSVM do ukończenia procesu kompilacji. Aby uzyskać więcej informacji na temat ustawiania agentów, zobacz [kompilacje i wydania agentów](/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Kompilacje i wybór agentów](./media/code-test/select_agent.PNG)

    e. Wybierz **+** w okienku po lewej stronie, aby dodać zadanie dla tej fazy kompilacji. Ponieważ będziemy **Test1.py** skrypt języka Python, aby zakończyć wszystkie testy, to zadanie używa polecenia programu PowerShell do uruchomienia kodu w języku Python.
    
    ![Okienko "Dodawanie zadań" z wybranym programem PowerShell](./media/code-test/add_task_powershell.PNG)

    f. W obszarze Szczegóły programu PowerShell wprowadź wymagane informacje, takie jak nazwa i wersja programu PowerShell. Wybierz **skrypt wbudowany** jako typ. 
    
    W polu w obszarze **skrypt wbudowany** można wpisać **Python test1.py**. Upewnij się, że zmienna środowiskowa została prawidłowo skonfigurowana dla języka Python. Jeśli potrzebna jest inna wersja lub jądro języka Python, można jawnie określić ścieżkę, jak pokazano na rysunku: 
    
    ![Szczegóły programu PowerShell](./media/code-test/powershell_scripts.PNG)

    przykład Wybierz pozycję **zapisz & kolejkę** , aby ukończyć proces kompilacji potoku.

    ![Przycisk "Zapisz & kolejki"](./media/code-test/save_and_queue_build_definition.PNG)

Teraz za każdym razem, gdy nowe zatwierdzenie zostanie wypchnięte do repozytorium kodu, proces kompilacji rozpocznie się automatycznie. Można zdefiniować dowolną gałąź. Proces uruchamia plik **Test1.py** na maszynie agenta, aby upewnić się, że wszystko zdefiniowane w kodzie działa poprawnie. 

Jeśli alerty są prawidłowo skonfigurowane, użytkownik zostanie powiadomiony w wiadomości e-mail, gdy kompilacja zostanie zakończona. Możesz również sprawdzić stan kompilacji w usłudze Azure DevOps. Jeśli to się nie powiedzie, możesz sprawdzić szczegóły kompilacji i dowiedzieć się, który fragment jest przerwany.

![Powiadomienie e-mail o powodzeniu kompilacji](./media/code-test/email_build_succeed.PNG)

![Powiadomienie usługi Azure DevOps o powodzeniu kompilacji](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [repozytorium przewidywania dochodu UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) dla konkretnych przykładów testów jednostkowych na potrzeby scenariuszy analizy danych.
* Postępuj zgodnie z powyższym zarysem i przykładami w scenariuszu przewidywania dochodu UCI w swoich projektach analizy danych.

## <a name="references"></a>Odwołania
* [Zespołowe przetwarzanie danych dla celów naukowych](./index.yml)
* [Narzędzia testowania programu Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Zasoby testowe usługi Azure DevOps](https://www.visualstudio.com/team-services/)
* [Maszyny wirtualne Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)