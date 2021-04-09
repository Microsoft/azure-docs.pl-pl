---
title: Wiele gałęzi w mapowaniu przepływu danych
description: Replikowanie strumieni danych w mapowaniu przepływu danych z wieloma gałęziami
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a11dbfbd6d6510b5c421e54cd2547c3aedb1bfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378200"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Tworzenie nowej gałęzi w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dodaj nową gałąź, aby wykonać wiele zestawów operacji i transformacji dla tego samego strumienia danych. Dodawanie nowej gałęzi jest przydatne, gdy chcesz użyć tego samego źródła dla wielu zleweń lub do samodzielnego łączenia danych.

Nową gałąź można dodać z listy transformacji podobnej do innych transformacji. **Nowa gałąź** będzie dostępna tylko jako akcja, gdy istnieje istniejąca transformacja po transformacji, którą próbujesz rozgałęzić.

![Zrzut ekranu przedstawia opcję nowe rozgałęzienie w menu wielokrotne dane wejściowe/wyjściowe.](media/data-flow/new-branch2.png "Dodawanie nowej gałęzi")

W poniższym przykładzie przepływ danych odczytuje dane podróży z taksówką. Dane wyjściowe zagregowane według dnia i dostawcy są wymagane. Zamiast tworzyć dwa oddzielne przepływy danych odczytane z tego samego źródła, można dodać nową gałąź. W ten sposób obie agregacje mogą być wykonywane w ramach tego samego przepływu danych. 

![Zrzut ekranu przedstawia przepływ danych z dwoma gałęziami ze źródła.](media/data-flow/new-branch.png "Dodawanie nowej gałęzi")
