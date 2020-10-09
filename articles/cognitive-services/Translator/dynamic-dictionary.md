---
title: Słownik dynamiczny — translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak używać funkcji słownika dynamicznego w usłudze Azure Cognitive Services translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: de45867e717f001ab54e16c4b21f04494affd326
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996985"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko w przypadku rzeczowników złożonych, takich jak poprawne nazwy i nazwy produktów.

**Obowiązuje**

<msTrans: tłumaczenie słownika = "translation of phrase" >phrase</msTrans: dictionary>

**Wymagania**

* `From`Języki i `To` muszą zawierać angielską i inny obsługiwany język. 
* `From`Zamiast korzystać z funkcji autowykrywania należy uwzględnić parametr w żądaniu tłumaczenia interfejsu API. 

**Przykład: en-de:**

Dane wejściowe źródła: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Docelowy wynik: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Ta funkcja działa tak samo jak w przypadku trybu HTML i bez niego.

Korzystanie z tej funkcji jest oszczędne. Lepszym sposobem dostosowania tłumaczenia jest użycie translatora niestandardowego. W przypadku translatora niestandardowego w pełni stosowane są prawdopodobieństwa kontekstowe i statystyczne. Jeśli masz lub możesz utworzyć dane szkoleniowe, które pokazują swoją służbę lub frazę w kontekście, uzyskasz dużo lepszych wyników. Więcej informacji na temat usługi tłumaczenia niestandardowego można znaleźć pod adresem [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .