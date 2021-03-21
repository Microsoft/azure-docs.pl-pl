---
title: Etap wdrażania cyklu życia zespołowego procesu nauki danych
description: Cele, zadania i elementy dostarczane dla etapu wdrożenia projektów analizy danych
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324541"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Etap wdrażania cyklu życia zespołowego procesu nauki danych

W tym artykule opisano cele, zadania i elementy dostarczane powiązane z wdrożeniem procesu nauki o danych zespołowych (przetwarzania TDSP). Ten proces zapewnia Zalecany cykl życia, którego można użyć do struktury projektów analizy danych. Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
Wdrażaj modele przy użyciu potoku danych w środowisku produkcyjnym lub w podobnym do produkcyjnym. 

## <a name="how-to-do-it"></a>Jak to zrobić
Główne zadanie rozkierowane na ten etap:

**Operacjonalizować model**: Wdróż model i potok w środowisku produkcyjnym lub w podobnym do produkcyjnym dla użycia aplikacji.

### <a name="operationalize-a-model"></a>Operacjonalizowanie modelu
Po utworzeniu zestawu modeli, które działają prawidłowo, można operacjonalizować je do użycia przez inne aplikacje. W zależności od wymagań firmy przewidywania są wykonywane w czasie rzeczywistym lub na podstawie partii. Aby wdrożyć modele, należy uwidocznić je za pomocą interfejsu Open API. Interfejs umożliwia łatwe wykorzystanie modelu z różnych aplikacji, takich jak:

   * Witryny internetowe w trybie online
   * Arkusze kalkulacyjne 
   * Pulpity nawigacyjne
   * Aplikacje biznesowe 
   * Aplikacje zaplecza 

Przykłady modelu operacjonalizacji z usługą sieci Web Azure Machine Learning można znaleźć w temacie [Deploy a Azure Machine Learning Web Service](../classic/deploy-a-machine-learning-web-service.md). Najlepszym rozwiązaniem jest tworzenie danych telemetrycznych i monitorowanie ich w modelu produkcyjnym i w wdrażanym potoku danych. To rozwiązanie pomaga w kolejnym raportowaniu stanu systemu i rozwiązywaniu problemów.  

## <a name="artifacts"></a>Artifacts

* Pulpit nawigacyjny Stan, który wyświetla kondycję systemu i kluczowe metryki
* Ostateczny raport modelowania ze szczegółami wdrożenia
* Końcowy dokument architektury rozwiązania


## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono linki do poszczególnych etapów cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i zrozumienie danych](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](./index.yml).