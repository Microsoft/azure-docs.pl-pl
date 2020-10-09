---
title: Wiele gałęzi w mapowaniu przepływu danych
description: Replikowanie strumieni danych w mapowaniu przepływu danych z wieloma gałęziami
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606391"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Tworzenie nowej gałęzi w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dodaj nową gałąź, aby wykonać wiele zestawów operacji i transformacji dla tego samego strumienia danych. Dodawanie nowej gałęzi jest przydatne, gdy chcesz użyć tego samego źródła dla wielu zleweń lub do samodzielnego łączenia danych.

Nową gałąź można dodać z listy transformacji podobnej do innych transformacji. **Nowa gałąź** będzie dostępna tylko jako akcja, gdy istnieje istniejąca transformacja po transformacji, którą próbujesz rozgałęzić.

![Dodawanie nowej gałęzi](media/data-flow/new-branch2.png "Dodawanie nowej gałęzi")

W poniższym przykładzie przepływ danych odczytuje dane podróży z taksówką. Dane wyjściowe zagregowane według dnia i dostawcy są wymagane. Zamiast tworzyć dwa oddzielne przepływy danych odczytane z tego samego źródła, można dodać nową gałąź. W ten sposób obie agregacje mogą być wykonywane w ramach tego samego przepływu danych. 

![Dodawanie nowej gałęzi](media/data-flow/new-branch.png "Dodawanie nowej gałęzi")
