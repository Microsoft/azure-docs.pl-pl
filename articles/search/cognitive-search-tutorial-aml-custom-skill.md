---
title: 'Samouczek: Tworzenie i wdrażanie niestandardowej umiejętności przy użyciu Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: W tym samouczku pokazano, jak za pomocą Azure Machine Learning kompilować i wdrażać niestandardową umiejętność potoku wzbogacenia systemu Azure Wyszukiwanie poznawcze.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/10/2020
ms.openlocfilehash: f673fd4b49a33c2faf6bc8b489520f2a877b0689
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513813"
---
# <a name="tutorial-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Samouczek: kompilowanie i wdrażanie niestandardowej umiejętności przy użyciu Azure Machine Learning 

W tym samouczku zostanie użyty [zestaw danych dla przeglądów hotelu](https://www.kaggle.com/datafiniti/hotel-reviews) (dystrybuowany w ramach licencji Creative Commons attributions [CC według-NC-sa 4,0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)), aby utworzyć [niestandardową umiejętność](https://docs.microsoft.com/azure/search/cognitive-search-aml-skill) przy użyciu Azure Machine Learning do wyodrębnienia tonacji opartego na aspektach z przeglądów. Pozwala to na przypisanie pozytywnych i ujemnych tonacji w ramach tego samego przeglądu, aby były prawidłowo przypisane do zidentyfikowanych jednostek, takich jak personel, pokój, lobby lub Pula.

Aby przeprowadzić uczenie modelu tonacji opartego na aspektach w Azure Machine Learning, będziesz używać [repozytorium z przepisami NLP](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa). Model zostanie następnie wdrożony jako punkt końcowy w klastrze usługi Azure Kubernetes. Po wdrożeniu punkt końcowy jest dodawany do potoku wzbogacenia jako umiejętność AML do użycia przez usługę Wyszukiwanie poznawcze.

Dostępne są dwa zestawy danych. Jeśli chcesz samodzielnie szkolić model, wymagany jest plik hotel_reviews_1000.csv. Wolisz pominąć krok szkolenia? Pobierz hotel_reviews_100.csv.

> [!div class="checklist"]
> * Tworzenie wystąpienia usługi Azure Wyszukiwanie poznawcze
> * Utwórz obszar roboczy Azure Machine Learning (usługa wyszukiwania i obszar roboczy powinny znajdować się w tej samej subskrypcji)
> * Uczenie i wdrażanie modelu w klastrze usługi Azure Kubernetes
> * Łączenie potoku wzbogacania AI ze wdrożonym modelem
> * Pozyskiwanie danych wyjściowych z wdrożonego modelu jako umiejętności niestandardowych

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — Uzyskaj [bezpłatną subskrypcję](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Usługa Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-get-started-arm)
* [Zasób Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows)
* [Konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
* [Obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)

## <a name="setup"></a>Konfigurowanie

* Klonowanie lub pobieranie zawartości [przykładowego repozytorium](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).
* Wyodrębnij zawartość, jeśli pobieranie jest plikiem zip. Upewnij się, że pliki są do odczytu i zapisu.
* Podczas konfigurowania kont i usług platformy Azure Skopiuj nazwy i klucze do łatwego dostępu do pliku tekstowego. Nazwy i klucze zostaną dodane do pierwszej komórki w notesie, gdzie są zdefiniowane zmienne umożliwiające dostęp do usług platformy Azure.
* Jeśli nie znasz Azure Machine Learning i jej wymagań, warto przejrzeć te dokumenty przed rozpoczęciem:
 * [Skonfiguruj środowisko programistyczne dla Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment)
 * [Tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi w Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
 * Podczas konfigurowania środowiska deweloperskiego dla Azure Machine Learning należy rozważyć użycie [wystąpienia obliczeniowego opartego na chmurze](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#compute-instance) w celu przyspieszenia i ułatwienia pracy.
* Przekaż plik zestawu danych do kontenera na koncie magazynu. Większy plik jest niezbędny, jeśli chcesz wykonać krok szkolenia w notesie. Jeśli wolisz pominąć etap szkolenia, zalecany jest mniejszy plik.

## <a name="open-notebook-and-connect-to-azure-services"></a>Otwieranie notesu i nawiązywanie połączenia z usługami platformy Azure

1. Umieść wszystkie wymagane informacje dla zmiennych, które umożliwią dostęp do usług platformy Azure w pierwszej komórce i uruchamiaj komórkę.
1. Uruchomienie drugiej komórki potwierdzi, że nawiązano połączenie z usługą wyszukiwania dla Twojej subskrypcji.
1. Sekcje 1,1-1,5 utworzy magazyn danych usługi wyszukiwania, zestawu umiejętności, indeks i indeksator.

W tym momencie możesz pominąć kroki, aby utworzyć zestaw danych szkoleniowych i eksperymentować w Azure Machine Learning i przejść bezpośrednio do rejestracji dwóch modeli, które są dostępne w folderze modele repozytorium GitHub. W przypadku pominięcia tych kroków w notesie przejdź do sekcji 3,5, Zapisz skrypt oceniania. Spowoduje to oszczędność czasu; ukończenie pobierania i przekazywania danych może potrwać do 30 minut.

## <a name="creating-and-training-the-models"></a>Tworzenie i uczenie modeli

Sekcja 2 ma sześć komórek, które pobierają plik osadzania dokładne z repozytorium z przepisami NLP. Po pobraniu plik jest następnie przekazywany do Azure Machine Learning magazynu danych. Plik. zip ma około 2G, a wykonywanie tych zadań zajmie trochę czasu. Po przekazaniu dane szkoleniowe są wyodrębniane, a teraz wszystko jest gotowe do przejścia do sekcji 3.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>Uczenie modelu tonacji opartego na aspektach i wdrażanie punktu końcowego

Sekcja 3 notesu będzie szkolić modele, które zostały utworzone w sekcji 2, zarejestrować te modele i wdrożyć je jako punkt końcowy w klastrze usługi Azure Kubernetes. Jeśli nie znasz usługi Azure Kubernetes, zdecydowanie zalecamy zapoznanie się z następującymi artykułami przed podjęciem próby utworzenia klastra wnioskowania:

* [Omówienie usługi Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)
* [Podstawowe pojęcia Kubernetes dla usługi Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)
* [Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/quotas-skus-regions)

Tworzenie i wdrażanie klastra wnioskowania może potrwać do 30 minut. Zaleca się przetestowanie usługi sieci Web przed przejściem do ostatniej procedury w celu zaktualizowania zestawu umiejętności i uruchomienia indeksatora.

## <a name="update-the-skillset"></a>Aktualizowanie zestawu umiejętności

Sekcja 4 w notesie ma cztery komórki, które aktualizują zestawu umiejętności i indeksator. Możesz również użyć portalu, aby wybrać i zastosować nową umiejętność do zestawu umiejętności, a następnie uruchomić indeksator w celu zaktualizowania usługi wyszukiwania.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

W portalu przejdź do zestawu umiejętności i wybierz łącze zestawu umiejętności Definition (JSON). W portalu zostanie wyświetlony kod JSON zestawu umiejętności, który został utworzony w pierwszej komórce notesu. Z prawej strony ekranu znajduje się menu rozwijane, w którym można wybrać szablon definicji umiejętności. Wybierz szablon Azure Machine Learning (AML). Podaj nazwę obszaru roboczego usługi Azure ML i punkt końcowy dla modelu wdrożonego w klastrze wnioskowania. Szablon zostanie zaktualizowany przy użyciu identyfikatora URI i klucza punktu końcowego.

> [!div class="mx-imgBorder"]
> ![Szablon definicji zestawu umiejętności](media/cognitive-search-aml-skill/portal-aml-skillset-definition.png)

Skopiuj szablon zestawu umiejętności z okna i wklej go do definicji zestawu umiejętności po lewej stronie. Edytuj szablon, aby podać brakujące wartości dla:

* Nazwa
* Opis
* Kontekst
* Nazwa i źródło danych wejściowych
* Nazwa "Outputs" i TargetName

Zapisz zestawu umiejętności.

Po zapisaniu zestawu umiejętności przejdź do indeksatora i wybierz łącze indeksatora definicji (JSON). W portalu zostanie wyświetlony kod JSON indeksatora, który został utworzony w pierwszej komórce notesu. Mapowania pól wyjściowych muszą zostać zaktualizowane przy użyciu dodatkowych mapowań pól, aby upewnić się, że indeksator może obsłużyć i przekazać je poprawnie. Zapisz zmiany, a następnie wybierz pozycję Uruchom. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z niestandardowym interfejsem API](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-web-api) 
>  sieci Web [Dowiedz się więcej na temat dodawania umiejętności niestandardowych do potoku wzbogacania](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface)