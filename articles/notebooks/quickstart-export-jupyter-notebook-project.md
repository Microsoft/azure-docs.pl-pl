---
title: Eksportowanie projektu Jupyter Notebook z wersji zapoznawczej Azure Notebooks
description: Szybko Eksportuj projekt Jupyter Notebook.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ea16bd61b542217fb6f2d5ba1d926a0bc19d4cce
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606136"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Szybki Start: Eksportowanie projektu Jupyter Notebook w wersji zapoznawczej Azure Notebooks

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

W tym przewodniku szybki start pobierzesz projekt Azure Notebooks do użycia w innych rozwiązaniach Jupyter Notebook. 

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący projekt Azure Notebooks.

## <a name="export-an-azure-notebooks-project"></a>Eksportowanie projektu Azure Notebooks

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Wybierz projekt.
1. Kliknij przycisk Pobierz, aby wyzwolić pobranie pliku zip, który zawiera wszystkie pliki projektu.
1. Alternatywnie z konkretnej strony projektu kliknij przycisk "Pobierz projekt", aby pobrać wszystkie pliki danego projektu.

Po pobraniu plików projektu można używać ich z innymi rozwiązaniami Jupyter Notebook. Niektóre opcje opisane w poniższych sekcjach obejmują: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Tworzenie środowiska dla notesów

Jeśli chcesz utworzyć środowisko zgodne z wersją zapoznawczą Azure Notebooks, możesz użyć pliku skryptu dostępnego w witrynie GitHub.

1. Przejdź do repozytorium Azure Notebooks [GitHub](https://github.com/microsoft/AzureNotebooks) lub [bezpośrednio uzyskaj dostęp do folderu Environment](https://aka.ms/aznbrequirementstxt).
1. W wierszu polecenia przejdź do katalogu, który ma być używany dla projektów.
1. Pobierz zawartość folderu środowiska i postępuj zgodnie z instrukcjami w pliku Readme, aby zainstalować zależności pakietu Azure Notebooks.


## <a name="use-notebooks-in-visual-studio-code"></a>Korzystanie z notesów w Visual Studio Code

[Vs Code](https://code.visualstudio.com/) to bezpłatny edytor kodu, który może być używany lokalnie lub podłączony do zdalnego obliczania. W połączeniu z rozszerzeniem języka Python oferuje ono pełne środowisko programowania w języku Python, w tym bogate natywne środowisko pracy z notesami Jupyter. 

![Obsługa Jupyter Notebook VS Code](media/vs-code-jupyter-notebook.png)

Po [pobraniu](#export-an-azure-notebooks-project) plików projektu można ich używać z vs Code. Aby uzyskać wskazówki dotyczące używania VS Code z notesami Jupyter, zobacz [Praca z notesami Jupyter w programie Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) i [nauka danych w](https://code.visualstudio.com/docs/python/data-science-tutorial) samouczkach Visual Studio Code.

Możesz również użyć [skryptu środowiska Azure Notebooks](#create-an-environment-for-notebooks) z Visual Studio Code, aby utworzyć środowisko zgodne z podglądem Azure Notebooks.

## <a name="use-notebooks-in-github-codespaces"></a>Korzystanie z notesów w usłudze GitHub Codespaces

Usługa GitHub Codespaces udostępnia środowiska hostowane w chmurze, w których można edytować notesy za pomocą Visual Studio Code lub w przeglądarce internetowej. Oferuje to samo doskonałe środowisko Jupyter, co VS Code, ale bez konieczności instalowania żadnych elementów na urządzeniu. Jeśli nie chcesz konfigurować środowiska lokalnego i wolisz rozwiązania do tworzenia kopii zapasowych w chmurze, tworzenie codespace jest doskonałym rozwiązaniem. Aby rozpocząć:
1. [Pobierz](#export-an-azure-notebooks-project) pliki projektu.
1. [Utwórz repozytorium GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) do przechowywania Twoich notesów.   
1. [Dodaj pliki](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) do repozytorium.
1. [Zażądaj dostępu do usługi GitHub Codespaces w wersji zapoznawczej](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Korzystanie z notesów z Azure Machine Learning

Azure Machine Learning zapewnia kompleksową platformę do uczenia maszynowego, która umożliwia użytkownikom szybsze tworzenie i wdrażanie modeli na platformie Azure. Usługa Azure ML umożliwia uruchamianie notesów Jupyter na maszynie wirtualnej lub w środowisku przetwarzania danych klastra. Jeśli korzystasz z rozwiązania opartego na chmurze do obciążeń związanych z chmurą przy użyciu śledzenia eksperymentów, zarządzania zestawami danych i nie tylko, zalecamy Azure Machine Learning. Aby rozpocząć pracę z platformą Azure ML:

1. [Pobierz](#export-an-azure-notebooks-project) pliki projektu.
1. [Utwórz obszar roboczy](../machine-learning/how-to-manage-workspace.md) w Azure Portal.

   ![Tworzenie obszaru roboczego](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Otwórz [usługę Azure Studio (wersja zapoznawcza)](https://ml.azure.com/).
1. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **Notes**.
1. Kliknij przycisk **Przekaż pliki** i przekaż pliki projektu pobrane z Azure Notebooks.

Aby uzyskać dodatkowe informacje na temat platformy Azure ML i uruchamiania notesów Jupyter, możesz przejrzeć [dokumentację](../machine-learning/how-to-run-jupyter-notebooks.md) lub spróbować wykonać [wprowadzenie do Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) module w Microsoft Learn.


## <a name="use-azure-lab-services"></a>Użyj Azure Lab Services

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) umożliwić nauczycielom łatwe konfigurowanie i zapewnianie dostępu na żądanie do wstępnie skonfigurowanych maszyn wirtualnych dla całej klasy. Jeśli szukasz sposobu pracy z notesami Jupyter i obliczeniowymi w chmurze w dostosowanym środowisku klasy, usługi laboratoryjne to świetna opcja.

![image (obraz)](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 Po [pobraniu](#export-an-azure-notebooks-project) plików projektu można ich używać z Azure Lab Services. Aby uzyskać wskazówki dotyczące konfigurowania laboratorium, zobacz [Konfigurowanie laboratorium do uczenia danych w języku Python i Jupyter notesów](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Korzystanie z usługi GitHub

GitHub oferuje bezpłatne, chronione za pomocą kontroli źródła sposób przechowywania notesów (i innych plików), udostępnia Twoje notesy innym osobom oraz pracuje wspólnie. Jeśli szukasz sposobu udostępniania projektów i współpracy z innymi osobami, serwis GitHub jest doskonałym rozwiązaniem i może być połączony z usługą [GitHub Codespaces](#use-notebooks-in-github-codespaces) , aby uzyskać doskonałe środowisko programistyczne. Aby rozpocząć pracę z usługą GitHub

1. [Pobierz](#export-an-azure-notebooks-project) pliki projektu.
1. [Utwórz repozytorium GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) do przechowywania Twoich notesów. 
1. [Dodaj pliki](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) do repozytorium.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o języku Python w Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Dowiedz się więcej na temat notesów Azure Machine Learning i Jupyter](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Więcej informacji na temat usługi GitHub Codespaces](https://github.com/features/codespaces)
- [Dowiedz się więcej o Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Dowiedz się więcej o usłudze GitHub](https://help.github.com/github/getting-started-with-github/)