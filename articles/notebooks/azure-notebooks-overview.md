---
title: Przegląd Azure Notebooks wersji zapoznawczej
description: Uruchamiaj notesy Jupyter w chmurze za pomocą usługi bezpłatna wersja zapoznawcza Azure Notebooks, w której nie jest wymagana instalacja ani konfiguracja.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: 7e622bdef785085384395bb0c8ac3efba2b2053a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400277"
---
# <a name="overview-of-azure-notebooks-preview"></a>Przegląd Azure Notebooks wersji zapoznawczej

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks to bezpłatna usługa hostowana umożliwiająca opracowywanie i uruchamianie notesów Jupyter w chmurze bez konieczności instalacji. [Jupyter](https://jupyter.org/) (dawniej IPython) to projekt typu "open source", który umożliwia łatwe łączenie tekstu z promocji, kodu wykonywalnego, danych trwałych, grafiki i wizualizacji na pojedynczą, możliwą do współużytkowaną kanwę, nazywaną *notesem* (grzecznościowa obraz Jupyter.org):

[![Przykłady notesów Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Dzięki zaawansowanemu połączeniu kodu, grafiki i opisowego tekstu notesy Jupyter znalazły wiele zastosowań, takich jak instrukcje przetwarzania danych do celów naukowych, czyszczenie i transformacje danych, symulacje numeryczne, modelowanie statystyczne oraz tworzenie modeli uczenia maszynowego.

## <a name="hassle-free-experience"></a>Bezproblemowa obsługa

Usługa Azure Notebooks pozwala szybko rozpocząć tworzenie prototypów, przetwarzanie danych do celów naukowych lub naukę programowania w języku Python, a także ułatwia prowadzenie badań akademickich:

- Analityk danych ma natychmiastowy dostęp do całego środowiska Anaconda bez instalowania narzędzi.
- Nauczyciel może udostępnić uczniom płynnie działające środowisko Python.
- Prelegent może przeprowadzić wykład na żywo lub seminarium internetowe, a uczestnicy nie muszą poświęcać 45 minut na instalowanie oprogramowania.
- Deweloper lub hobbysta może używać notesów do szybkiego sporządzania notatek związanych z kodem.

Korzystanie z notesów przynosi jeszcze więcej korzyści, jeśli można współpracować nad nimi za pośrednictwem dostępnej w przeglądarce usługi w chmurze, takiej jak Azure Notebooks (w wersji zapoznawczej). Pracując w chmurze, użytkownicy nie muszą lokalnie instalować oprogramowania Jupyter ani angażować zasobów w obsługę środowiska. Ponadto chmura ułatwia udostępnianie notesów (i skojarzonych plików danych) innym autoryzowanym użytkownikom, co pozwala uniknąć komplikacji związanych z korzystaniem z narzędzi zewnętrznych, takich jak repozytoria kontroli źródła. Za pomocą usługi Azure Notebooks użytkownicy mogą również skopiować (lub „sklonować”) notesy do własnego konta, aby je modyfikować lub eksperymentować z nimi. Jest to szczególnie przydatne przy tworzeniu instrukcji.

Ponieważ usługa Azure Notebooks jest ogólną platformą do tworzenia, wykonywania i udostępniania kodu, można jej używać w wielu różnych scenariuszach:

- Nauka nowego języka programowania — zapoznaj się z jednym z [samouczków dotyczących programu FrontPage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Nauka o danych — zobacz [książkę Jake’a VanderPlasa](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Prowadzenie kursu](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) dla kilkuset uczniów
- Prowadzenie seminarium internetowego lub prezentacji na konferencji bez instalacji oprogramowania 
- Umożliwianie użytkownikom usługi GitHub bezpośredniego ładowania i uruchamiania notesów dzięki [utworzeniu etykiety uruchamiania w usłudze GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Prowadzenie prezentacji [przypominających pokazy slajdów programu PowerPoint](https://notebooks.azure.com/help/jupyter-notebooks/slides) z możliwością uruchamiania kodu zawartego na slajdach

Krótko mówiąc, usługa Azure Notebooks pomaga wydajniej wykonywać swoją pracę, co przekłada się na lepsze wyniki.

> [!Note]
> Więcej informacji na temat oprogramowania Jupyter można znaleźć w witrynie [jupyter.org](https://jupyter.org/) oraz w [dokumentacji oprogramowania Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Cennik i przydziały

Azure Notebooks to bezpłatna usługa, jednak każdy projekt jest ograniczony do 4 GB pamięci i 1 GB miejsca na dane, co zapobiega nadużywaniu zasobów. Uprawnieni użytkownicy, którzy przekroczą te limity, muszą przejść weryfikację Captcha umożliwiającą kontynuowanie pracy z notesami.

Aby zwolnić wszystkie limity, zaloguj się do usługi Azure Notebooks przy użyciu konta korzystającego z usługi Azure Active Directory (na przykład konta firmowego). Jeśli to konto jest skojarzone z subskrypcją platformy Azure, możesz nawiązać połączenie z dowolnymi wystąpieniami usługi Azure Data Science Virtual Machine w ramach tej subskrypcji. 

Serwery notesów mają gwarantowane istnienie przez maksymalnie 8 godzin. W większości przypadków kontener nie podlega temu ograniczeniu i w dalszym ciągu działa dłużej, ale długotrwałe sesje mogą być czasami wyłączone w celu zapewnienia stabilności systemu.

## <a name="available-kernels-and-environments"></a>Dostępne jądra i środowiska

Dla każdego notesu można wybrać jądro (tj. środowisko uruchomieniowe), które jest używane do uruchamiania komórek z kodem. Usługa Azure Notebooks obsługuje następujące jądra:

- Python 2.7 + Anaconda2 5.3.0
- Python 3.6 + Anaconda3 5.3.0
- Python 3.5 + Anaconda3 4.2.0 (zostanie oznaczone jako przestarzałe)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Usługa Azure Notebooks udostępnia również dodatkowe pakiety, które nie znajdują się w podstawowych dystrybucjach. Na przykład jądra języka Python zawierają biblioteki numpy, pandas, scikit-learn, matplotlib i bokeh.

Można również dostosować projekt, tworząc środowisko dla wszystkich notesów.

Oprócz podstawowych dystrybucji usługa Azure Notebooks udostępnia wiele dodatkowych, wstępnie zainstalowanych pakietów, które są przydatne dla analityków danych. Można także instalować własne pakiety, korzystając z typowych procesów dla poszczególnych języków.

## <a name="pre-configured-jupyter-extensions"></a>Wstępnie skonfigurowane rozszerzenia Jupyter

Usługa Azure Notebooks zawiera następujące wstępnie skonfigurowane rozszerzenia Jupyter:

- [RISE](https://github.com/damianavila/RISE): rozszerzenie pokazu slajdów Jupyter (znane także jako live_reveal).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): pełne środowisko obliczeniowe do pracy z notesami Jupyter.
- [Altair](https://github.com/ellisonbg/altair): deklaratywna biblioteka języka Python do wizualizacji statystycznych.
- [BQPlot](https://github.com/bloomberg/bqplot): interaktywna platforma do kreślenia dla notesów Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): interaktywne widżety HTML dla notesów Jupyter.

## <a name="issues-and-getting-help"></a>Problemy i uzyskiwanie pomocy

Usługa Azure Notebooks wciąż jest dostępna w wersji zapoznawczej. Z tego względu mogą występować tymczasowe okresy niedostępności, które mogą zdarzać się częściej lub trwać dłużej niż w przypadku innych usług platformy Azure. Niektóre funkcje mogą być niekompletne lub zawierać błędy.

Aktualnie odradzamy korzystanie z wersji zapoznawczej usługi Azure Notebooks do obsługi aplikacji krytycznych dla działania firmy lub poufnych notesów i danych.

Aby przekazać nam pytania dotyczące usługi Azure Notebooks, prześlij zgłoszenie w [repozytorium GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Następne kroki  

- Przewodniki Szybki start:

  - [Logowanie się i ustawianie identyfikatora użytkownika](quickstart-sign-in-azure-notebooks.md)
  - [Eksportowanie Jupyter Notebook](quickstart-export-jupyter-notebook-project.md)
