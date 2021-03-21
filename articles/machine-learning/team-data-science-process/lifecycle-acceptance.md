---
title: Etap akceptacji klienta w cyklu życia procesu nauki o danych zespołowych
description: Cele, zadania i elementy dostarczane dla etapu akceptacji klientów w projektach analizy danych
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
ms.openlocfilehash: f2294ccb1d958b229a71e45bb502b8134d8d5c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93305669"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Etap akceptacji klienta w cyklu życia procesu nauki o danych zespołowych

W tym artykule opisano cele, zadania i elementy dostarczane powiązane z etapem akceptacji klienta zespołu ds. analizy danych (przetwarzania TDSP). Ten proces zapewnia Zalecany cykl życia, którego można użyć do struktury projektów analizy danych. Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
**Finalizowanie elementów dostarczanych projektu**: Upewnij się, że potok, model i ich wdrożenie w środowisku produkcyjnym spełniają cele klienta.

## <a name="how-to-do-it"></a>Jak to zrobić
W tym etapie są uwzględnione dwa podstawowe zadania:

   * **Weryfikacja systemu**: Upewnij się, że wdrożony model i potok spełniają wymagania klienta.
   * **Odręczny projekt**: umożliwia oddanie projektu do jednostki, która ma uruchamiać system w środowisku produkcyjnym.

Klient powinien sprawdzić, czy system spełnia Twoje wymagania biznesowe i czy ma odpowiedzi na pytania z akceptowalną dokładnością, aby wdrożyć system w środowisku produkcyjnym do użytku przez aplikację klienta. Wszystkie dokumenty są finalizowane i analizowane. Projekt jest przekazywany do jednostki odpowiedzialnej za operacje. Ta jednostka może być na przykład zespołem ds. IT lub klientem, który jest odpowiedzialny za uruchamianie systemu w środowisku produkcyjnym. 

## <a name="artifacts"></a>Artifacts
Głównym artefaktem utworzonym w tym ostatnim etapie jest **raport zakończenia projektu dla klienta**. Ten raport techniczny zawiera wszystkie szczegóły dotyczące projektu, które są przydatne do uczenia się, jak korzystać z systemu. PRZETWARZANIA TDSP udostępnia szablon [raportu zakończenia](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) . Możesz użyć szablonu jako lub można go dostosować do konkretnych potrzeb klienta. 


## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono linki do poszczególnych etapów cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](./index.yml).