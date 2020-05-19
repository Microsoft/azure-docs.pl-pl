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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3ef881efc417a6c3816a92d9af7d77bba74355aa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592546"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko w przypadku rzeczowników złożonych, takich jak poprawne nazwy i nazwy produktów.

**Obowiązuje**

<msTrans: tłumaczenie słownika = "translation of phrase" >phrase</msTrans: dictionary>

**Wymagania**

* `From`Języki i `To` muszą zawierać angielską i inny obsługiwany język. 
* `From`Zamiast korzystać z funkcji autowykrywania należy uwzględnić parametr w żądaniu tłumaczenia interfejsu API. 

**Przykład: en-de:**

Dane wejściowe źródła:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Docelowy wynik:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Ta funkcja działa tak samo jak w przypadku trybu HTML i bez niego.

Korzystanie z tej funkcji jest oszczędne. Lepszym sposobem dostosowania tłumaczenia jest użycie translatora niestandardowego. W przypadku translatora niestandardowego w pełni stosowane są prawdopodobieństwa kontekstowe i statystyczne. Jeśli masz lub możesz utworzyć dane szkoleniowe, które pokazują swoją służbę lub frazę w kontekście, uzyskasz dużo lepszych wyników. Więcej informacji na temat usługi tłumaczenia niestandardowego można znaleźć pod adresem [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .