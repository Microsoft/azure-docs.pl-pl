---
title: Wiele gałęzi w przepływie danych mapowania
description: Replikowanie strumieni danych w przepływie danych mapowania z wieloma gałęziami
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606391"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Tworzenie nowej gałęzi w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dodaj nową gałąź, aby wykonać wiele zestawów operacji i przekształceń względem tego samego strumienia danych. Dodawanie nowej gałęzi jest przydatne, gdy chcesz użyć tego samego źródła dla wielu pochłaniacze lub do samodzielnego łączenia danych razem.

Nową gałąź można dodać z listy transformacji podobne do innych przekształceń. **Nowa gałąź** będzie dostępna tylko jako akcja, gdy istnieje istniejąca transformacja po transformacji, którą próbujesz rozgałęzić.

![Dodawanie nowej gałęzi](media/data-flow/new-branch2.png "Dodawanie nowej gałęzi")

W poniższym przykładzie przepływ danych odczytuje dane podróży taksówką. Wymagane jest dane wyjściowe zagregowane zarówno przez dzień, jak i przez dostawcę. Zamiast tworzyć dwa oddzielne przepływy danych, które odczytują z tego samego źródła, można dodać nową gałąź. W ten sposób obie agregacje mogą być wykonywane jako część tego samego przepływu danych. 

![Dodawanie nowej gałęzi](media/data-flow/new-branch.png "Dodawanie nowej gałęzi")
