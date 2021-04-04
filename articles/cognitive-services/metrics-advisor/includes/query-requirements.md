---
title: Wymagania dotyczące zapytań
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043213"
---
W zapytaniu Użyj `@StartTime` parametru, aby uzyskać dane metryk dla pojedynczej sygnatury czasowej. Doradca metryk zastąpi parametr `yyyy-MM-ddTHH:mm:ss` ciągiem formatu podczas uruchamiania zapytania.

> [!IMPORTANT]
> Zapytanie powinno zwrócić co najwyżej jeden rekord dla każdej kombinacji wymiarów w każdym znaczniku czasu. Wszystkie rekordy zwrócone przez zapytanie muszą mieć te same sygnatury czasowe. Doradca metryk uruchomi to zapytanie dla każdej sygnatury czasowej w celu pozyskania danych. Zapoznaj się z [sekcją często zadawanych pytań na temat zapytań](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) , aby uzyskać więcej informacji i przykłady. 