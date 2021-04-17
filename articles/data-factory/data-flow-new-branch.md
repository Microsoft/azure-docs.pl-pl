---
title: Wiele gałęzi w przepływie danych mapowania
description: Replikowanie strumieni danych w przepływie mapowania danych z wieloma gałęziami
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588755"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Tworzenie nowej gałęzi w przepływie mapowania danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dodaj nową gałąź, aby wykonać wiele zestawów operacji i przekształceń względem tego samego strumienia danych. Dodanie nowej gałęzi jest przydatne, gdy chcesz użyć tego samego źródła do dla wielu ujść lub do samodzielnego łączenia danych ze sobą.

Z listy przekształceń można dodać nową gałąź podobną do innych przekształceń. **Nowa gałąź** będzie dostępna jako akcja tylko wtedy, gdy istnieje przekształcenie po przekształceniu, które próbujesz rozgałęzieć.

![Zrzut ekranu przedstawia opcję Nowa gałąź w menu Wiele danych wejściowych/wyjściowych.](media/data-flow/new-branch2.png "Dodawanie nowej gałęzi")

W poniższym przykładzie przepływ danych odczytuje dane dotyczące przejazdów taksówkami. Dane wyjściowe zagregowane według dnia i dostawcy są wymagane. Zamiast tworzyć dwa oddzielne przepływy danych odczytane z tego samego źródła, można dodać nową gałąź. W ten sposób obie agregacje mogą być wykonywane w ramach tego samego przepływu danych. 

![Zrzut ekranu przedstawia przepływ danych z dwiema gałęziami ze źródła.](media/data-flow/new-branch.png "Dodawanie nowej gałęzi")

> [!NOTE]
> Po kliknięciu plusa (+) w celu dodania przekształceń do grafu opcja Nowa gałąź będzie dostępna tylko wtedy, gdy istnieją kolejne bloki przekształcania. Wynika to z tego, że nowa gałąź tworzy odwołanie do istniejącego strumienia i wymaga dalszego przetwarzania nadrzędnego do działania. Jeśli nie widzisz opcji Nowa gałąź, najpierw dodaj kolumnę pochodną lub inne przekształcenie, a następnie wróć do poprzedniego bloku i zobaczysz opcję Nowa gałąź.

## <a name="next-steps"></a>Następne kroki

Po rozgałęzieniu możesz użyć [przekształceń przepływu danych](data-flow-transformation-overview.md)
