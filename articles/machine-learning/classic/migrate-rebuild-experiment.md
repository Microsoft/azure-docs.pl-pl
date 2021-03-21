---
title: 'ML Studio (klasyczny): Migruj do eksperymentu Azure Machine Learning-Build'
description: Przebuduj Studio (klasyczne) eksperymenty w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565167"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Ponowne kompilowanie eksperymentu programu Studio (klasycznego) w Azure Machine Learning

W tym artykule dowiesz się, jak ponownie skompilować eksperyment programu Studio (klasyczny) w Azure Machine Learning. Aby uzyskać więcej informacji na temat migrowania z programu Studio (klasyczny), zobacz [artykuł Omówienie migracji](migrate-overview.md).

W programie Studio (klasyczne) **eksperymenty** są podobne do **potoków** w Azure Machine Learning. Jednak w przypadku potoków Azure Machine Learning są zbudowane na tym samym zapleczu, który ma uprawnienia do zestawu SDK. Oznacza to, że dostępne są dwie opcje rozwoju usługi Machine Learning: Projektant "przeciągnij i upuść" albo zestaw SDK dla kodu.

Aby uzyskać więcej informacji o tworzeniu potoków z zestawem SDK, zobacz [co to są potoki Azure Machine Learning](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Obszar roboczy usługi Azure Machine Learning. [Utwórz obszar roboczy Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Eksperyment z Studio (klasyczny) do migracji.
- [Przekaż zestaw danych](migrate-register-dataset.md) do Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Ponowne kompilowanie potoku

Po [przeprowadzeniu migracji zestawu danych do Azure Machine Learning](migrate-register-dataset.md)możesz ponownie utworzyć eksperyment.

W Azure Machine Learning Wizualizacja jest nazywana **projektem potoku**. W tej sekcji utworzysz ponownie klasyczny eksperyment jako wersję roboczą potoku.

1. Przejdź do Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com))
1. W okienku nawigacji po lewej **stronie wybierz opcję** > **łatwe w obsłudze prekompilowanych modułów** ![ zrzut ekranu przedstawiający sposób tworzenia nowej wersji roboczej potoku.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Ręcznie Skompiluj eksperyment z modułami projektanta.
    
    Zapoznaj się z [tabelą mapowania modułów](migrate-overview.md#studio-classic-and-designer-module-mapping) , aby znaleźć moduły zamienne. Wiele popularnych modułów programu Studio (klasycznych) ma identyczne wersje w projektancie.

    > [!Important]
    > Jeśli eksperyment korzysta z modułu wykonywania skryptu języka R, musisz wykonać dodatkowe kroki, aby przeprowadzić migrację eksperymentu. Aby uzyskać więcej informacji, zobacz [Migrowanie modułów skryptów języka R](migrate-execute-r-script.md).

1. Dostosuj parametry.
    
    Zaznacz każdy moduł i Dostosuj parametry w panelu Ustawienia modułu po prawej stronie. Użyj parametrów, aby ponownie utworzyć funkcje eksperymentu programu Studio (klasycznego). Aby uzyskać więcej informacji na temat każdego modułu, zobacz [odwołanie do modułu](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Prześlij wyniki przebiegu i sprawdzenia

Po ponownym utworzeniu eksperymentu programu Studio (klasycznego) czas na przesłanie **uruchomienia potoku**.

Uruchomienie potoku jest wykonywane w **miejscu docelowym obliczeń** dołączonym do obszaru roboczego. Można ustawić domyślny cel obliczeń dla całego potoku lub określić cele obliczeniowe dla poszczególnych modułów.

Po przesłaniu przebiegu z wersji roboczej potoku zostanie on **przeuruchamiany do uruchomienia potoku**. Każde uruchomienie potoku jest rejestrowane i rejestrowane Azure Machine Learning.

Aby ustawić domyślny obiekt docelowy obliczeń dla całego potoku:
1. Wybierz ikonę **koła zębatego** ![ w projektancie ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) obok nazwy potoku.
1. Wybierz pozycję **Wybierz element docelowy obliczeń**.
1. Wybierz istniejące obliczenie lub Utwórz nowe obliczenie, postępując zgodnie z instrukcjami wyświetlanymi na ekranie.

Teraz, gdy obiekt docelowy obliczeń jest ustawiony, można przesłać uruchomienie potoku:

1. W górnej części kanwy wybierz pozycję **Prześlij**.
1. Wybierz pozycję **Utwórz nowy** , aby utworzyć nowy eksperyment.
    
    Eksperymenty organizują podobne uruchomienia potoków. W przypadku uruchomienia potoku wiele razy można wybrać ten sam eksperyment dla kolejnych uruchomień. Jest to przydatne w przypadku rejestrowania i śledzenia.
1. Wprowadź nazwę eksperymentu. Następnie wybierz pozycję **Prześlij**.

    Pierwsze uruchomienie może potrwać do 20 minut. Ponieważ domyślne ustawienia obliczeń mają minimalny rozmiar węzła równy 0, projektant musi przydzielić zasoby po stanie bezczynności. Kolejne uruchomienia zajmują mniej czasu, ponieważ węzły są już przydzieleni. Aby przyspieszyć czas działania, można utworzyć zasoby obliczeniowe o minimalnym rozmiarze węzła równym 1 lub większym.

Po zakończeniu przebiegu można sprawdzić wyniki każdego modułu:

1. Kliknij prawym przyciskiem myszy moduł, którego dane wyjściowe chcesz zobaczyć.
1. Wybierz opcję **Wizualizuj**, **Wyświetlaj dane wyjściowe** lub **Wyświetl dziennik**.

    - **Wizualizowanie**: Podgląd zestawu danych wyników.
    - **Wyświetl dane wyjściowe**: Otwórz link do lokalizacji magazynu danych wyjściowych. Użyj tego polecenia, aby eksplorować lub pobrać dane wyjściowe. 
    - **Wyświetl dziennik**: Wyświetl dzienniki sterowników i systemu. Użyj **70_driver_log** , aby wyświetlić informacje związane z skryptem przesłanym przez użytkownika, np. błędy i wyjątki.

> [!IMPORTANT]
> Moduły projektanta używają pakietów języka Python Open Source w porównaniu z pakietami C# w programie Studio (klasyczny). W związku z tym dane wyjściowe modułu mogą się nieco różnić w zależności od projektanta i Studio (klasyczne). 


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób odbudowywania eksperymentu programu Studio (klasycznego) w Azure Machine Learning. Następnym krokiem jest [odbudowanie usług sieci Web w Azure Machine Learning](migrate-rebuild-web-service.md).


Zapoznaj się z innymi artykułami z serii migracji programu Studio (klasycznej):

1. [Omówienie migracji](migrate-overview.md).
1. [Migrowanie zestawu danych](migrate-register-dataset.md).
1. **Kompiluj potok szkoleniowy Studio (klasyczny)**.
1. [Kompiluj ponownie usługę sieci Web programu Studio (klasyczna)](migrate-rebuild-web-service.md).
1. [Zintegruj usługę sieci web Azure Machine Learning z aplikacjami klienckimi](migrate-rebuild-integrate-with-client-app.md).
1. [Migruj skrypt wykonania skryptu języka R](migrate-execute-r-script.md).
