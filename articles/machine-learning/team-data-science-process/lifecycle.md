---
title: Cykl życia procesu nauki o danych zespołowych
description: Zespół ds. analizy danych (przetwarzania TDSP) zapewnia Zalecany cykl życia, który służy do tworzenia struktury projektów analizy danych.
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
ms.openlocfilehash: ed54fda744978ac43bbffc6a70d6b331e85dc5e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305597"
---
# <a name="the-team-data-science-process-lifecycle"></a>Cykl życia procesu nauki o danych zespołowych

Zespół ds. analizy danych (przetwarzania TDSP) zapewnia Zalecany cykl życia, który służy do tworzenia struktury projektów analizy danych. Cykl życia przedstawia pełne kroki, które są wykonywane przez pomyślne projekty. W przypadku korzystania z innego cyklu życia nauki dotyczącego danych, takiego jak standardowy proces produkcji na potrzeby wyszukiwania danych [("wyrazisty DM")](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), odnajdywania wiedzy w bazach danych [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)lub własnego procesu niestandardowego w organizacji, można nadal używać przetwarzania TDSP opartego na zadaniach. 

Ten cykl życia jest przeznaczony dla projektów analizy danych przeznaczonych do dostarczania w ramach inteligentnych aplikacji. Te aplikacje wdrażają Uczenie maszynowe lub sztuczne modele analiz do analizy predykcyjnej. Projekty naukowo-badawcze i projekty analizy Improvised mogą również korzystać z tego procesu. Jednak w przypadku tych projektów niektóre kroki opisane w tym miejscu mogą nie być wymagane. 

## <a name="five-lifecycle-stages"></a>Pięć etapów cyklu życia

Cykl życia przetwarzania TDSP składa się z pięciu głównych etapów, które są wykonywane iteracyjnie. Te etapy obejmują:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Cykl życia przetwarzania TDSP jest modelowany jako sekwencja iteracji kroków, które zapewniają wskazówki dotyczące zadań wymaganych do korzystania z modeli predykcyjnych. Należy wdrożyć modele predykcyjne w środowisku produkcyjnym, które planujesz użyć do kompilowania inteligentnych aplikacji. Celem tego cyklu procesu jest kontynuowanie przenoszenia projektu analizy danych w kierunku jasnego punktu końcowego zaangażowania. Nauka danych jest ćwiczeniem do badań i odnajdywania. Możliwość przekazywania zadań do zespołu i klientów przy użyciu dobrze zdefiniowanego zestawu artefaktów, które używają standardowych szablonów, pomaga uniknąć nieprawidłowych interpretacji. Korzystanie z tych szablonów zwiększa również prawdopodobieństwo pomyślnego ukończenia złożonego projektu analizy danych.

Dla każdego etapu udostępniamy następujące informacje:

   * **Cele**: określone cele.
   * **Jak to zrobić**: zarys określonych zadań i wskazówki dotyczące ich kończenia.
   * **Artefakty**: elementy dostarczane i pomoc techniczna do ich tworzenia.

## <a name="next-steps"></a>Następne kroki

Zapewniamy kompleksowe przewodniki przedstawiające wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](./index.yml).