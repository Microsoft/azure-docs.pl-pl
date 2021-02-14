---
title: Wykonywanie zadań analizy danych — proces nauki o danych zespołowych
description: Jak analityk danych może wykonywać projekt analizy danych w sposób umożliwiający śledzenie, kontrolowane wersje i współpracę.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371961"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Wykonywanie zadań naukowych dotyczących danych: eksploracja, modelowanie i wdrażanie

Typowe zadania analizy danych obejmują eksplorację, modelowanie i wdrażanie danych. W tym artykule opisano zadania w celu wykonania kilku typowych zadań związanych z nauką o danych, takich jak interaktywna Eksploracja danych, analiza danych, raportowanie i tworzenie modelu. Opcje wdrażania modelu w środowisku produkcyjnym mogą obejmować:

- [Azure Machine Learning](../index.yml)
- [SQL — serwer z usługami ML](/sql/advanced-analytics/r/r-services)
- [Serwer Microsoft Machine Learning](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Eksploracja 

Analityk danych może przeprowadzić eksplorację i raportowanie na wiele sposobów: korzystając z bibliotek i pakietów dostępnych dla języka Python (na przykład matplotlib) lub w języku R (ggplot lub kratowych). Analityki danych mogą dostosować taki kod w celu dopasowania do potrzeb eksploracji danych w określonych scenariuszach. Wymagania dotyczące pracy z danymi strukturalnymi różnią się w przypadku danych bez struktury, takich jak tekst lub obrazy. 

Produkty takie jak Azure Machine Learning również zapewniają [Zaawansowane Przygotowywanie danych](../how-to-create-register-datasets.md) do przetwarzanie i eksploracji danych, w tym tworzenie funkcji. Użytkownik powinien zdecydować o narzędziach, bibliotekach i pakietach, które najlepiej odpowiadają potrzebom. 

Element dostarczany na końcu tej fazy jest raportem eksploracji danych. Raport powinien zapewnić dość obszerny wgląd w dane, które mają być używane do modelowania, oraz ocenę, czy dane są odpowiednie do przechodzenia do etapu modelowania. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modelowanie

Istnieje wiele zestawów narzędzi i pakietów dla modeli szkoleniowych w różnych językach. Osoby zajmujące się danymi mogą korzystać z nich, które kiedykolwiek są wygodne, tak długo, jak zagadnienia dotyczące wydajności dotyczące dokładności i opóźnień są spełnione dla odpowiednich przypadków użycia biznesowego i scenariuszy produkcyjnych.

### <a name="model-management"></a>Zarządzanie modelem
Po skompilowaniu wielu modeli zwykle trzeba mieć system do rejestrowania modeli i zarządzania nimi. Zwykle potrzebna jest kombinacja skryptów lub interfejsów API, bazy danych zaplecza lub systemu przechowywania wersji. Poniżej przedstawiono kilka opcji, które można wziąć pod uwagę w przypadku następujących zadań zarządzania:

1. [Usługa zarządzania modelami Azure Machine Learning](../index.yml)
2. [ModelDB z MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [Serwer SQL — system zarządzania modelami](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Serwer Microsoft Machine Learning](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> wdrożenie

Wdrożenie produkcyjne umożliwia modelowi odtworzenie aktywnej roli w firmie. Przewidywania ze wdrożonego modelu mogą być używane na potrzeby podejmowania decyzji dla firmy.

### <a name="production-platforms"></a>Platformy produkcyjne
Istnieją różne podejścia i platformy umożliwiające umieszczanie modeli w środowisku produkcyjnym. Oto kilka opcji:


- [Wdrażanie modelu w Azure Machine Learning](../how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Serwer Microsoft Machine Learning](/sql/advanced-analytics/r/r-server-standalone)

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
