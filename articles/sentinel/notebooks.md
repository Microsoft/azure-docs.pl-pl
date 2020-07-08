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
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83652033"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Korzystanie z notesów Jupyter do wyszukiwania zagrożeń bezpieczeństwa

Podstawą platformy Azure jest magazyn danych; łączy ona wysoką wydajność zapytań, schematu dynamicznego i skaluje się do dużych ilości danych. Aby uzyskać dostęp do tego magazynu danych, Azure Portal i wszystkie narzędzia wskaźnikowe platformy Azure używają wspólnego interfejsu API. Ten sam interfejs API jest również dostępny dla narzędzi zewnętrznych, takich jak notesy [Jupyter](https://jupyter.org/) i Python. Chociaż wiele typowych zadań można wykonać w portalu, Jupyter rozszerza zakres tego, co można zrobić za pomocą tych danych. Łączy ona pełen programowalność z ogromną kolekcją bibliotek na potrzeby uczenia maszynowego, wizualizacji i analizy danych. Te atrybuty sprawiają, że Jupyter narzędzi do dochodzenia do zabezpieczeń i polowania.

![Przykładowy Notes](./media/notebooks/sentinel-notebooks-map.png)

Środowisko Jupyter zostało zintegrowane z Azure Portal, co ułatwia tworzenie i uruchamianie notesów w celu analizowania danych. Biblioteka *Kqlmagic* zapewnia klej, który umożliwia wykonywanie zapytań z platformy Azure, i uruchamianie ich bezpośrednio w notesie. Zapytania używają [języka zapytań Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Kilka notesów opracowanych przez niektórych analityków zabezpieczeń firmy Microsoft jest pakowanych z platformą Azure — wskaźnikiem. Niektóre z tych notesów zostały skompilowane w konkretnym scenariuszu i mogą być używane w taki sam sposób. Inne zawarto jako przykłady ilustrujące techniki i funkcje, które można skopiować lub dostosować do użycia we własnych notesach. Inne notesy mogą być również importowane z witryny Azure

Zintegrowane środowisko Jupyter korzysta z [Azure Notebooks](https://notebooks.azure.com/) do przechowywania, udostępniania i wykonywania notesów. Te notesy można również uruchomić lokalnie, jeśli masz środowisko Python i Jupyter na komputerze, lub w innych środowiskach JupterHub, takich jak Azure Databricks.

Notesy mają dwa składniki:

- Interfejs oparty na przeglądarce, w którym można wprowadzać i uruchamiać zapytania i kod oraz miejsce wyświetlania wyników wykonania.
- *Jądro* odpowiedzialne za analizowanie i wykonywanie kodu. 

W Azure Notebooks domyślnie to jądro działa na platformie Azure *i w bezpłatnej przestrzeni dyskowej w chmurze*. Jeśli notesy zawierają złożone modele uczenia maszynowego lub wizualizacje, warto rozważyć użycie bardziej wydajnych, dedykowanych zasobów obliczeniowych, takich jak [Virtual Machines analizy danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Notesy na Twoim koncie są przechowywane jako prywatne, chyba że zdecydujesz się je udostępnić.

W notesach usługi Azure Pandas są używane wiele popularnych bibliotek języka Python, takich jak, matplotlib, bokeh i inne. Istnieje doskonały wiele innych pakietów języka Python, które można wybrać, obejmujących obszary takie jak:

- Wizualizacje i grafika
- Przetwarzanie i analiza danych
- Statystyka i obliczenia liczbowe
- Uczenie maszynowe i uczenie głębokie

Wydano również niektóre narzędzia zabezpieczeń Jupyter typu open source w pakiecie o nazwie [msticpy](https://github.com/Microsoft/msticpy/). Ten pakiet jest używany w wielu z dołączonych notesów. Narzędzia Msticpy zostały zaprojektowane specjalnie w celu ułatwienia tworzenia notesów dla łowiectwa i badania, a my pracujemy nad nowymi funkcjami i ulepszeniami.

Notesy początkowe obejmują:

- **Badanie z przewodnikiem — alerty procesów**: umożliwiają szybkie Klasyfikacja alertów przez analizowanie aktywności na uszkodzonym hoście lub hostach.
- **Łowiectwo z przewodnikiem — Eksplorator hosta systemu Windows**: umożliwia Eksplorowanie aktywności konta, wykonywania procesów, aktywności sieci i innych zdarzeń na hoście.
- **Łowiectwo z przewodnikiem — Office 365**: w przypadku podejrzanych działań związanych z pakietem Office 365 w wielu zestawach danych z pakietu Office 365.

[Repozytorium Azure wskaźnikowego społeczności GitHub](https://github.com/Azure/Azure-Sentinel) jest lokalizacją dla każdego przyszłego notesu usługi Azure wskaźnikowego, utworzonego przez firmę Microsoft lub pochodzącego od społeczności.

Aby móc korzystać z notesów, musisz mieć konto Azure Notebooks. Aby uzyskać więcej informacji, zobacz [Szybki Start: Logowanie i Ustawianie identyfikatora użytkownika](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) z dokumentacji Azure Notebooks. Aby utworzyć to konto, możesz użyć opcji **Utwórz konto do Azure Notebooks** na pasku poleceń na **platformie Azure — notesy**:

> [!div class="mx-imgBorder"]
>![Opcja rejestracji w usłudze Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Możesz uruchomić Notes bezpośrednio z usługi Azure wskaźnikowej lub sklonować wszystkie notesy usługi Azure wskaźnikowego do nowego projektu Azure Notebooks.

## <a name="run-a-notebook-from-azure-sentinel"></a>Uruchamianie notesu z poziomu platformy Azure — wskaźnik
 
1. W Azure Portal przejdź do **Azure Sentinel**  >  notesu**zarządzania zagrożeniami**platformy Azure  >  **Notebooks**, w którym można zobaczyć notesy udostępniane przez platformę Azure. 

2. Wybierz poszczególne notesy, aby odczytać ich opisy, wymagane typy danych i źródła danych. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Uruchom Notes](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Wybierz Notes, którego chcesz użyć, a następnie wybierz pozycję **Uruchom Notes (wersja zapoznawcza)** , aby sklonować i skonfigurować Notes w nowym projekcie Azure Notebooks, który łączy się z obszarem roboczym wskaźnikowego platformy Azure. Po zakończeniu procesu Notes zostanie otwarty w Azure Notebooks, aby można było go uruchomić.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Klonowanie notesów wskaźnikowych platformy Azure do nowego projektu Azure Notebooks

Ta procedura powoduje utworzenie projektu Azure Notebooks dla Ciebie, który zawiera notesy usługi Azure wskaźnikowej. Następnie można uruchamiać notesy w stanie takim, w jakim się znajdują, lub wprowadzać w nich zmiany, a następnie uruchamiać je.

1. W Azure Portal przejdź do **Azure Sentinel**  >  notesu**zarządzania zagrożeniami**platformy Azure,  >  **Notebooks** a następnie wybierz pozycję **Klonuj notesy** na pasku poleceń:
  
    > [!div class="mx-imgBorder"]
    >![Opcja klonowania notesów](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Gdy zostanie wyświetlone następujące okno dialogowe, wybierz pozycję **Importuj** , aby sklonować repozytorium GitHub do projektu Azure Notebooks. Jeśli nie masz istniejącego konta Azure Notebooks, zostanie wyświetlony monit o utworzenie i zalogowanie się.

   ![Importuj Notes](./media/notebooks/sentinel-notebooks-clone.png)

3. W oknie dialogowym **przekazywanie repozytorium GitHub** nie wybieraj **rekurencyjnie klonowania** , ponieważ ta opcja odnosi się do połączonych repozytoriów GitHub. W polu Nazwa projektu Użyj nazwy domyślnej lub wpisz nową nazwę. Następnie kliknij przycisk **Importuj** , aby rozpocząć klonowanie zawartości usługi GitHub, co może potrwać kilka minut.

   ![Importuj Notes](./media/notebooks/sentinel-create-project.png)

4. Otwórz właśnie utworzony projekt, a następnie otwórz folder **notesy** , aby wyświetlić notesy. Przykład:

   ![Importuj repozytorium](./media/notebooks/sentinel-open-notebook1.png)

Następnie można uruchomić notesy z Azure Notebooks. Aby wrócić do tych notesów z obszaru wskaźnikowego platformy Azure, wybierz pozycję **Przejdź do notesu** na pasku poleceń w witrynie **Azure — notesy**:

> [!div class="mx-imgBorder"]
>![Przejdź do opcji notesów](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Korzystanie z notesów do wyszukiwania

Każdy Notes przeprowadzi Cię przez kroki w celu przeprowadzenia wyszukiwania lub badania. Biblioteki i inne zależności wymagające tego notesu można zainstalować z notesu lub za pomocą prostej procedury konfiguracji. Konfiguracja, która wiąże Twój projekt notesu z powrotem z subskrypcją usługi Azure wskaźnikowego, jest automatycznie obsługiwana w poprzednich krokach.

1. Jeśli nie jesteś jeszcze w Azure Notebooks, możesz użyć opcji **Przejdź do notesu** na pasku poleceń w obszarze **Azure — notesy**:
    
    > [!div class="mx-imgBorder"]
    >![Przejdź do opcji notesów](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    W Azure Notebooks wybierz pozycję **Moje projekty**, projekt zawierający notesy usługi Azure wskaźnikowej, a następnie folder **notesy** .
    
2. Przed otwarciem notesu należy pamiętać, że domyślnie do uruchamiania notesów jest wybrane bezpłatne obliczenie:
    
   ![Wybieranie notesu](./media/notebooks/sentinel-open-notebook2.png)
    
    Jeśli skonfigurowano Virtual Machines analizy danych (DSVM) do użycia zgodnie z opisem we wprowadzeniu, wybierz DSVM i Uwierzytelnij przed otwarciem pierwszego notesu. 

3. Wybierz Notes, aby go otworzyć.
    
    Przy pierwszym otwarciu notesu może zostać wyświetlony monit o wybranie wersji jądra. Jeśli nie zostanie wyświetlony monit, możesz wybrać wersję **jądra ze**  >   **zmiany**jądra, a następnie wybrać wersję, która ma co najmniej 3,6. Wybrana wersja jądra zostanie wyświetlona w prawym górnym rogu okna notesu:
    
   ![Wybieranie notesu](./media/notebooks/sentinel-select-kernel.png)

4. Przed wprowadzeniem jakichkolwiek zmian do pobranego notesu dobrym pomysłem jest wykonanie kopii oryginalnego notesu i rozpoczęcie pracy z kopią. W tym celu wybierz pozycję **plik**  >  **Zrób kopię**. Praca nad kopiami pozwala bezpiecznie aktualizować do przyszłych wersji notesów bez zastępowania żadnych danych.
    
    Teraz można przystąpić do uruchamiania lub edytowania wybranego notesu.

Mając

- Aby zapoznać się z szybkim wprowadzeniem do wykonywania zapytań dotyczących danych na platformie Azure, zapoznaj się z notesem [getstarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) w folderze głównym **notesów** . 

- Dodatkowe przykładowe notesy znajdują się w podfolderze **Samples-Notess** . Te przykładowe notesy zostały zapisane w danych, dzięki czemu można łatwiej zobaczyć zamierzone dane wyjściowe. Zalecamy wyświetlanie tych notesów w [nbviewer](https://nbviewer.jupyter.org/). 

- Folder **HOWTOs** zawiera notesy opisujące, na przykład: ustawienie domyślnej wersji języka Python, skonfigurowanie DSVM, tworzenie zakładek wskaźnikowych platformy Azure z notesu i innych tematów.

Udostępniane notesy są przeznaczone zarówno jako przydatne narzędzia, jak i przykłady kodu, których można używać do tworzenia własnych notesów.

Zapraszamy do przekazywania opinii, sugestii, żądań dotyczących funkcji, notesów programu, raportów błędów lub ulepszeń oraz dodatków do istniejących notesów. Przejdź do witryny [Azure wskaźnikowej społeczności GitHub](https://github.com/Azure/Azure-Sentinel) , aby utworzyć problem lub rozwidlenie i przekazać wkład.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rozpoczynania pracy z notesami Jupyter na platformie Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z zakładek do zapisywania interesujących informacji podczas polowania](bookmarks.md)
