---
title: Pozyskiwanie danych i zrozumienie procesu nauki o danych zespołowych
description: Cele, zadania i elementy dostarczane dla pozyskiwania danych i zrozumienie ich etapów dla projektów analizy danych
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
ms.openlocfilehash: f7770e929e51ee51b09060e4247c5f92b27d1035
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311872"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Pozyskiwanie danych i zrozumienie etapu procesu nauki o danych zespołowych

W tym artykule opisano cele, zadania i elementy dostarczane związane z pozyskiwaniem danych i zrozumienie etapu procesu nauki o danych zespołowych (przetwarzania TDSP). Ten proces zapewnia Zalecany cykl życia, którego można użyć do struktury projektów analizy danych. Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Tworzenie czystego zestawu danych o wysokiej jakości, którego relacja ze zmiennymi docelowymi jest zrozumiała. Znajdź zestaw danych w odpowiednim środowisku analitycznym, dzięki czemu możesz przystąpić do modelowania.
* Utwórz architekturę rozwiązania potoku danych, która odświeża i regularnie ocenia dane.

## <a name="how-to-do-it"></a>Jak to zrobić
W tym etapie istnieją trzy główne zadania:

   * Pozyskiwanie **danych** w docelowym środowisku analitycznym.
   * **Eksploruj dane,** aby określić, czy jakość danych jest odpowiednia do udzielenia odpowiedzi na pytanie. 
   * **Skonfiguruj Potok danych** do oceny nowych lub regularnie odświeżanych danych.

### <a name="ingest-the-data"></a>Pozyskiwanie danych
Skonfiguruj proces przenoszenia danych z lokalizacji źródłowych do lokalizacji docelowych, w których są uruchamiane operacje analityczne, takie jak szkolenia i przewidywania. Aby uzyskać szczegółowe informacje techniczne i opcje przenoszenia danych przy użyciu różnych usług danych platformy Azure, zobacz artykuł [ładowanie danych do środowiska magazynu na potrzeby analizy](ingest-data.md). 

### <a name="explore-the-data"></a>Eksplorowanie danych
Przed przeprowadzeniem szkolenia modeli należy opracować solidną wiedzę na temat danych. Rzeczywiste zestawy danych to często zakłócenia, brakujące wartości lub hosty innych rozbieżności. Możesz użyć podsumowania i wizualizacji danych, aby przeprowadzić inspekcję jakości danych i podać informacje potrzebne do przetworzenia danych, zanim będą gotowe do modelowania. Ten proces jest często iteracyjny.

PRZETWARZANIA TDSP zapewnia zautomatyzowane narzędzie, nazywane [pomysłem](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), które ułatwia wizualizowanie danych i przygotowywanie raportów podsumowujących dane. Zalecamy najpierw zacząć od POMYSŁu, aby eksplorować dane, aby pomóc w interaktywnej analizie danych. Następnie można napisać niestandardowy kod do eksploracji i wizualizacji danych. Aby uzyskać wskazówki dotyczące czyszczenia danych, zobacz [zadania przygotowujące dane do ulepszonej uczenia maszynowego](prepare-data.md).  

Po spełnieniu jakości oczyszczonych danych, następnym krokiem jest lepsze zrozumienie wzorców, które są związane z danymi. Ta analiza danych pomaga w wyborze i opracowaniu odpowiedniego modelu predykcyjnego dla celu. Poszukaj dowodów na to, jak dobrze połączono dane z elementem docelowym. Następnie określ, czy masz wystarczające dane, aby przejść do następnej procedury modelowania. Ten proces jest często iteracyjny. Może być konieczne znalezienie nowych źródeł danych z dokładniejszymi lub bardziej odpowiednimi danymi, aby rozszerzyć zestaw danych początkowo zidentyfikowany na poprzednim etapie. 

### <a name="set-up-a-data-pipeline"></a>Konfigurowanie potoku danych
Oprócz wstępnego pozyskiwania i czyszczenia danych zazwyczaj trzeba skonfigurować proces do oceny nowych danych lub regularnie odświeżać dane w ramach trwającego procesu nauki. Ocenianie może być wykonywane przy użyciu potoku danych lub przepływu pracy. Przykład [przenoszenia danych z wystąpienia SQL serverowego do Azure SQL Database z](move-sql-azure-adf.md) artykułem Azure Data Factory zawiera przykładowy sposób konfigurowania potoku za pomocą [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Na tym etapie opracowujesz architekturę rozwiązania potoku danych. Potok jest opracowywany równolegle z następnym etapem projektu analizy danych. W zależności od potrzeb firmy i ograniczeń istniejących systemów, do których to rozwiązanie jest zintegrowane, potok może być jedną z następujących opcji: 

   * Oparte na partii
   * Przesyłanie strumieniowe lub w czasie rzeczywistym 
   * Hybrydowa 

## <a name="artifacts"></a>Artifacts
Poniżej znajdują się elementy dostarczane na tym etapie:

   * [Raport dotyczący jakości danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): ten raport zawiera podsumowania danych, relacje między poszczególnymi atrybutami i elementem docelowym, klasyfikację zmiennych i inne. Narzędzie [pomysł](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) udostępniane jako część przetwarzania TDSP może szybko wygenerować ten raport na dowolnym zestawie danych tabelarycznych, na przykład w pliku CSV lub w tabeli relacyjnej. 
   * **Architektura rozwiązania** : Architektura rozwiązania może być diagramem lub opisem potoku danych, który służy do uruchamiania oceniania lub prognoz dotyczących nowych danych po skompilowaniu modelu. Zawiera również potok do ponownego uczenia modelu na podstawie nowych danych. Zapisz dokument w katalogu [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) , gdy używasz szablonu struktury katalogów przetwarzania TDSP.
   * **Decyzja dotycząca punktów kontrolnych** : przed rozpoczęciem tworzenia inżynierów i konstruowania modelu można przeprowadzić ocenę projektu, aby określić, czy oczekiwana wartość jest wystarczająca, aby kontynuować jej działanie. Być może na przykład będzie można kontynuować, musi zbierać więcej danych lub porzucić projekt, ponieważ nie istnieją dane, aby odpowiedzieć na pytanie.

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono linki do poszczególnych etapów cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning]().