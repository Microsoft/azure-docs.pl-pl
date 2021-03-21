---
title: Korzystanie z notesów z platformą Azure wskaźnikowego na potrzeby łowiectwa zabezpieczeń
description: W tym artykule opisano, jak używać notesów z możliwościami polowania na platformie Azure.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092197"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Wyszukiwanie zagrożeń bezpieczeństwa za pomocą notesów Jupyter

Podstawą platformy Azure jest magazyn danych; łączy ona wysoce wydajne zapytania, schemat dynamiczny i skaluje się do dużych ilości danych. Aby uzyskać dostęp do tego magazynu danych, Azure Portal i wszystkie narzędzia wskaźnikowe platformy Azure używają wspólnego interfejsu API. Ten sam interfejs API jest również dostępny dla narzędzi zewnętrznych, takich jak notesy [Jupyter](https://jupyter.org/) i Python. Chociaż wiele typowych zadań można wykonać w portalu, Jupyter rozszerza zakres tego, co można zrobić za pomocą tych danych. Łączy ona pełen programowalność z ogromną kolekcją bibliotek na potrzeby uczenia maszynowego, wizualizacji i analizy danych. Te atrybuty sprawiają, że Jupyter narzędzi do dochodzenia do zabezpieczeń i polowania.

![Przykładowy Notes](./media/notebooks/sentinel-notebooks-map.png)

Środowisko Jupyter zostało zintegrowane z Azure Portal, co ułatwia tworzenie i uruchamianie notesów w celu analizowania danych. Biblioteka *Kqlmagic* zapewnia klej, który umożliwia wykonywanie zapytań z platformy Azure, i uruchamianie ich bezpośrednio w notesie. Zapytania używają [języka zapytań Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Kilka notesów opracowanych przez niektórych analityków zabezpieczeń firmy Microsoft jest pakowanych z platformą Azure — wskaźnikiem. Niektóre z tych notesów zostały skompilowane w konkretnym scenariuszu i mogą być używane w taki sam sposób. Inne zawarto jako przykłady ilustrujące techniki i funkcje, które można skopiować lub dostosować do użycia we własnych notesach. Inne notesy mogą być również importowane z witryny Azure

Zintegrowane środowisko Jupyter korzysta z [Azure Notebooks](https://notebooks.azure.com/) do przechowywania, udostępniania i wykonywania notesów. Te notesy można również uruchomić lokalnie, jeśli masz środowisko Python i Jupyter na komputerze, lub w innych środowiskach JupterHub, takich jak Azure Databricks.

Notesy mają dwa składniki:

- Interfejs oparty na przeglądarce, w którym można wprowadzać i uruchamiać zapytania i kod oraz miejsce wyświetlania wyników wykonania.
- *Jądro* odpowiedzialne za analizowanie i wykonywanie kodu.

Jądro notesu systemu Azure wskaźnikowego działa na maszynie wirtualnej platformy Azure. Istnieje kilka opcji licencjonowania, które wykorzystują wydajniejsze maszyny wirtualne, jeśli notesy zawierają złożone modele uczenia maszynowego.

W notesach usługi Azure Pandas są używane wiele popularnych bibliotek języka Python, takich jak, matplotlib, bokeh i inne. Istnieje doskonały wiele innych pakietów języka Python, które można wybrać, obejmujących obszary takie jak:

- Wizualizacje i grafika
- Przetwarzanie i analiza danych
- Statystyka i obliczenia liczbowe
- Uczenie maszynowe i uczenie głębokie

Wydano również niektóre narzędzia zabezpieczeń Jupyter typu open source w pakiecie o nazwie [msticpy](https://github.com/Microsoft/msticpy/). Ten pakiet jest używany w wielu z dołączonych notesów. Narzędzia Msticpy zostały zaprojektowane specjalnie w celu ułatwienia tworzenia notesów dla łowiectwa i badania, a my pracujemy nad nowymi funkcjami i ulepszeniami.

[Repozytorium Azure wskaźnikowego społeczności GitHub](https://github.com/Azure/Azure-Sentinel) jest lokalizacją dla każdego przyszłego notesu usługi Azure wskaźnikowego, utworzonego przez firmę Microsoft lub pochodzącego od społeczności.

Aby móc korzystać z notesów, musisz najpierw utworzyć obszar roboczy Azure Machine Learning (ML).

## <a name="create-an-azure-ml-workspace"></a>Tworzenie obszaru roboczego usługi Azure ML

1. W Azure Portal przejdź do   >  notesu **zarządzania zagrożeniami** platformy Azure,  >   a następnie wybierz pozycję **Uruchom Notes**.

    > [!div class="mx-imgBorder"]
    > ![Uruchom Notes, aby uruchomić obszar roboczy usługi Azure ml](./media/notebooks/sentinel-notebooks-launch.png)

1. W obszarze **obszar roboczy Azure** wybierz pozycję **Utwórz nowy**.

    > [!div class="mx-imgBorder"]
    > ![Utwórz obszar roboczy](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Na stronie **Machine Learning** podaj następujące informacje, a następnie wybierz pozycję **Recenzja + Utwórz**.

    |Pole|Opis|
    |--|--|
    |Subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|Użyj grupy zasobów istniejącej w Twojej subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używamy **AzureMLRG**.|
    |Nazwa obszaru roboczego|Wprowadź unikatową nazwę identyfikującą obszar roboczy. W tym przykładzie używamy **testworkspace1**. Nazwy muszą być unikatowe w ramach grupy zasobów. Użyj nazwy, która jest łatwa do odzyskania i odróżniania od obszarów roboczych utworzonych przez inne osoby.|
    |Region (Region)|Wybierz lokalizację znajdującą się najbliżej użytkowników i zasoby danych, aby utworzyć obszar roboczy.|
    |Wersja obszaru roboczego|W tym przykładzie wybierz pozycję **Basic** jako typ obszaru roboczego. Typ obszaru roboczego (podstawowa & Enterprise) określa funkcje, do których będziesz mieć dostęp i Cennik.|

    > [!div class="mx-imgBorder"]
    > ![Podaj szczegóły obszaru roboczego](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Przejrzyj informacje, sprawdź, czy są poprawne, a następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie obszaru roboczego.

    > [!div class="mx-imgBorder"]
    > ![Przejrzyj szczegóły obszaru roboczego](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Utworzenie obszaru roboczego w chmurze może potrwać kilka minut, podczas gdy na stronie **Przegląd** zostanie wyświetlony bieżący stan wdrożenia.

    > [!div class="mx-imgBorder"]
    > ![wdrożenie obszaru roboczego](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Po zakończeniu wdrażania możesz uruchamiać notesy w nowym obszarze roboczym usługi Azure ML.

> [!div class="mx-imgBorder"]
> ![Wdrażanie obszaru roboczego powiodło się](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Uruchamianie notesu przy użyciu obszaru roboczego usługi Azure ML

1. W Azure Portal przejdź do   >  notesu **zarządzania zagrożeniami** platformy Azure  >  , w którym można zobaczyć notesy udostępniane przez platformę Azure.

    > [!TIP]
    > Wybierz pozycję **prowadnice & Opinie** , aby otworzyć okienko z dodatkową pomoc i wskazówki dotyczące notesów.
    > ![Wyświetl prowadnice notesu](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Wybierz poszczególne notesy, aby wyświetlić ich opisy, wymagane typy danych i źródła danych.

    > [!div class="mx-imgBorder"]
    > ![Wyświetl szczegóły notesu](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Wybierz Notes, którego chcesz użyć, a następnie wybierz pozycję **Uruchom Notes** , aby sklonować i skonfigurować Notes w nowym projekcie Azure Notebooks, który łączy się z obszarem roboczym wskaźnikowego platformy Azure. Po zakończeniu procesu Notes zostanie otwarty w Azure Notebooks, aby można było go uruchomić.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie notesu](./media/notebooks/sentinel-azure-notebooks-select.png)

1. W obszarze obszar roboczy usługi Azure wybierz swój obszar roboczy Azure ML, a następnie wybierz pozycję **Uruchom**.

    > [!div class="mx-imgBorder"]
    > ![Uruchom Notes](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Wybierz wystąpienie obliczeniowe. Jeśli nie masz wystąpienia obliczeniowego, wykonaj następujące czynności:
    1. Wybierz znak plus (+), aby uruchomić kreatora **nowego wystąpienia obliczeniowego** .

        > [!div class="mx-imgBorder"]
        > ![Uruchom Kreatora wystąpienia obliczeniowego](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Na stronie **nowe wystąpienie obliczeniowe** podaj wymagane informacje, a następnie wybierz pozycję **Utwórz**.

        > [!div class="mx-imgBorder"]
        > ![Tworzenie wystąpienia obliczeniowego](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Po utworzeniu serwera notesu w każdej komórce wybierz ikonę Uruchom, aby wykonać kod w notesach.

    > [!div class="mx-imgBorder"]
    > ![Uruchom Notes](./media/notebooks/sentinel-azure-notebooks-run.png)

Zalecenia:

- Aby zapoznać się z szybkim wprowadzeniem do wykonywania zapytań dotyczących danych na platformie Azure, zapoznaj się [z wprowadzenie za pomocą notesów platformy Azure i przewodnika wskaźnikowego platformy Azure](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) .

- Dodatkowe przykładowe notesy znajdują się w podfolderze [**przykładowych notesów**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) w serwisie GitHub. Te przykładowe notesy zostały zapisane w danych, dzięki czemu można łatwiej zobaczyć zamierzone dane wyjściowe. Zalecamy wyświetlanie tych notesów w [nbviewer](https://nbviewer.jupyter.org/).

- Podfolder usługi GitHub [**HOWTOs**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) zawiera notesy opisujące, na przykład: ustawienie domyślnej wersji języka Python, skonfigurowanie DSVM, tworzenie zakładek wskaźnikowego platformy Azure z notesu i innych tematów.

Udostępniane notesy są przeznaczone zarówno jako przydatne narzędzia, jak i przykłady kodu, których można używać do tworzenia własnych notesów.

Zapraszamy do przekazywania opinii, sugestii, żądań dotyczących funkcji, notesów programu, raportów błędów lub ulepszeń oraz dodatków do istniejących notesów. Przejdź do witryny [Azure wskaźnikowej społeczności GitHub](https://github.com/Azure/Azure-Sentinel) , aby utworzyć problem lub rozwidlenie i przekazać wkład.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rozpoczynania korzystania z Jupyter Notebook na platformie Azure — wskaźnik. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z zakładek do zapisywania interesujących informacji podczas polowania](bookmarks.md)
