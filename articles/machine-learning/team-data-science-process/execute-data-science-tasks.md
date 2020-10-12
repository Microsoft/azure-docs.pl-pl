---
title: Wykonywanie zadań analizy danych — proces nauki o danych zespołowych
description: Jak analityk danych może wykonywać projekt analizy danych w sposób umożliwiający śledzenie, kontrolowane wersje i współpracę.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5fe11b20ff8e96bc4b0d997c49201aafdd2e2cac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320527"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Wykonywanie zadań naukowych dotyczących danych: eksploracja, modelowanie i wdrażanie

Typowe zadania analizy danych obejmują eksplorację, modelowanie i wdrażanie danych. W tym artykule pokazano, jak używać **interaktywnej eksploracji danych, analizy i raportowania (pomysł)** oraz **zautomatyzowanych narzędzi do modelowania i raportowania (Amar)** do wykonywania kilku typowych zadań związanych z nauką o danych, takich jak interaktywna Eksploracja danych, analiza danych, raportowanie i tworzenie modelu. Opcje wdrażania modelu w środowisku produkcyjnym mogą obejmować:

- [Azure Machine Learning](../index.yml)
- [SQL — serwer z usługami ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Eksploracja 

Analityk danych może przeprowadzić eksplorację i raportowanie na wiele sposobów: korzystając z bibliotek i pakietów dostępnych dla języka Python (na przykład matplotlib) lub w języku R (ggplot lub kratowych). Analityki danych mogą dostosować taki kod w celu dopasowania do potrzeb eksploracji danych w określonych scenariuszach. Wymagania dotyczące pracy z danymi strukturalnymi różnią się w przypadku danych bez struktury, takich jak tekst lub obrazy. 

Produkty takie jak Azure Machine Learning również zapewniają [Zaawansowane Przygotowywanie danych](../how-to-create-register-datasets.md) do przetwarzanie i eksploracji danych, w tym tworzenie funkcji. Użytkownik powinien zdecydować o narzędziach, bibliotekach i pakietach, które najlepiej odpowiadają potrzebom. 

Element dostarczany na końcu tej fazy jest raportem eksploracji danych. Raport powinien zapewnić dość obszerny wgląd w dane, które mają być używane do modelowania, oraz ocenę, czy dane są odpowiednie do przechodzenia do etapu modelowania. Narzędzia Team Data Scienceing Process (przetwarzania TDSP) omówione w poniższych sekcjach dla częściowo zautomatyzowanej eksploracji, modelowania i raportowania zapewniają również ustandaryzowaną eksplorację i raporty dotyczące modelowania danych. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktywna eksploracja, analiza i raportowanie danych przy użyciu narzędzia POMYSŁu

W tym narzędziu opartym na obstawce w języku R lub w środowisku Python jest dostępne elastyczne i interaktywne narzędzie do analizowania i eksplorowania zestawów danych. Użytkownicy mogą szybko generować raporty z zestawu danych przy minimalnym kodowaniu. Użytkownicy mogą klikać przyciski, aby eksportować wyniki eksploracji do ostatecznego raportu, który można dostarczyć klientom lub użyć do podejmowania decyzji dotyczących zmiennych, które mają zostać uwzględnione w kolejnym kroku modelowania.

W tej chwili narzędzie działa tylko w przypadku ramek danych w pamięci. Plik YAML jest wymagany do określenia parametrów zestawu danych, który ma zostać zbadany. Aby uzyskać więcej informacji, zobacz [pomysł w przetwarzania TDSP narzędzia do nauki o danych](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modelowanie

Istnieje wiele zestawów narzędzi i pakietów dla modeli szkoleniowych w różnych językach. Osoby zajmujące się danymi mogą korzystać z nich, które kiedykolwiek są wygodne, tak długo, jak zagadnienia dotyczące wydajności dotyczące dokładności i opóźnień są spełnione dla odpowiednich przypadków użycia biznesowego i scenariuszy produkcyjnych.

W następnej sekcji pokazano, jak używać narzędzia przetwarzania TDSP opartego na języku R na potrzeby automatycznego modelowania. Za pomocą tego narzędzia AMAR można szybko generować podstawowe modele linii, a także parametry, które należy dostrajać, aby zapewnić lepszy model.
W poniższej sekcji Zarządzanie modelami przedstawiono, jak mieć system do rejestracji wielu modeli i zarządzania nimi.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Uczenie modeli: Modelowanie i raportowanie przy użyciu narzędzia AMAR

[Narzędzie zautomatyzowanego modelowania i raportowania (Amar)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) udostępnia dostosowywalne, częściowo zautomatyzowane narzędzie do tworzenia modeli przy użyciu funkcji czyszczenia parametrów i w celu porównania dokładności tych modeli. 

Narzędzie tworzenia modelu jest plikiem R Markdown, który można uruchomić w celu utworzenia własnych danych wyjściowych HTML z spisem treści w celu łatwego nawigowania w różnych sekcjach. Trzy algorytmy są wykonywane po uruchomieniu pliku z promocjami (spojit): rozkładanie regularne przy użyciu pakietu glmnet, losowego lasu korzystającego z pakietu randomForest i zwiększania drzew przy użyciu pakietu xgboost). Każdy z tych algorytmów produkuje model szkolony. Dokładność tych modeli jest porównywana i są raportowane względne znaczenie funkcji. Obecnie istnieją dwa narzędzia: jeden jest przeznaczony dla zadania klasyfikacji binarnej, a drugi to zadanie regresji. Główne różnice między nimi to sposób, w jaki parametry kontroli i metryki dokładności są określone dla tych zadań szkoleniowych. 

Plik YAML służy do określenia:

- dane wejściowe (Źródło SQL lub plik danych języka R) 
- jaka część danych jest używana do uczenia się i czego częścią do testowania
- które algorytmy do uruchomienia 
- wybór parametrów kontroli dla optymalizacji modelu:
    - wzajemne sprawdzanie poprawności 
    - Uruchamianie
    - zgięcia krzyżowego sprawdzania poprawności
- dla każdego algorytmu są ustawiane parametry funkcji Hyper-Parameter. 

W pliku YAML można także zmodyfikować liczbę algorytmów, liczbę założenia do optymalizacji, parametry funkcji Hyper-Parameter oraz liczbę zestawów parametrów funkcji Hyper-parametru do odłożenia. Na przykład można uruchamiać z mniejszą liczbą zgięciów OKS, niższą liczbą zestawów parametrów. Jeśli jest to możliwe, można również uruchomić bardziej kompleksowo z większą liczbą operacji CV lub większej liczby zestawów parametrów.

Aby uzyskać więcej informacji, zobacz [zautomatyzowane narzędzia do modelowania i raportowania w przetwarzania TDSP narzędzia do nauki o danych](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Zarządzanie modelem
Po skompilowaniu wielu modeli zwykle trzeba mieć system do rejestrowania modeli i zarządzania nimi. Zwykle potrzebna jest kombinacja skryptów lub interfejsów API, bazy danych zaplecza lub systemu przechowywania wersji. Poniżej przedstawiono kilka opcji, które można wziąć pod uwagę w przypadku następujących zadań zarządzania:

1. [Usługa zarządzania modelami Azure Machine Learning](../index.yml)
2. [ModelDB z MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [Serwer SQL — system zarządzania modelami](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> wdrożenie

Wdrożenie produkcyjne umożliwia modelowi odtworzenie aktywnej roli w firmie. Przewidywania ze wdrożonego modelu mogą być używane na potrzeby podejmowania decyzji dla firmy.

### <a name="production-platforms"></a>Platformy produkcyjne
Istnieją różne podejścia i platformy umożliwiające umieszczanie modeli w środowisku produkcyjnym. Oto kilka opcji:


- [Wdrażanie modelu w Azure Machine Learning](../how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Przed wdrożeniem należy upewnić się, że opóźnienie oceny modelu jest wystarczająco małe, aby było używane w środowisku produkcyjnym.
>
>

Dalsze przykłady są dostępne w przewodnikach, które pokazują wszystkie kroki procesu dla **konkretnych scenariuszy**. Są one wyświetlane i połączone z opisami miniatur w artykule [przykładowe instruktaże](walkthroughs.md) . Ilustrują one sposób łączenia chmur, narzędzi lokalnych i usług w przepływ pracy lub potoku w celu utworzenia inteligentnej aplikacji.

> [!NOTE]
> Aby uzyskać wdrożenie przy użyciu Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Testowanie A/B:
Gdy wiele modeli jest w środowisku produkcyjnym, przydatne może być przeprowadzenie [testowania A/B](https://en.wikipedia.org/wiki/A/B_testing) w celu porównania wydajności modeli. 

 
## <a name="next-steps"></a>Następne kroki

[Śledź postęp projektów analizy danych](track-progress.md) pokazuje, jak analityk danych może śledzić postęp projektu analizy danych.

[Operacja modelowa i ciągłe/CD](ci-cd-flask.md) pokazują, jak Ci/CD można wykonać z rozwiniętymi modelami.
