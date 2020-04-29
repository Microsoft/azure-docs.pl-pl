---
title: Słownik dynamiczny — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak używać funkcji słownika dynamicznego w interfejs API tłumaczenia tekstu w usłudze Translator Cognitive Services platformy Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75446708"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko w przypadku rzeczowników złożonych, takich jak poprawne nazwy i nazwy produktów.

**Obowiązuje**

<msTrans: tłumaczenie słownika = "translation of phrase" >phrase</msTrans: dictionary>

**Wymagania**

* Języki `From` i `To` muszą zawierać angielską i inny obsługiwany język. 
* Zamiast korzystać z funkcji `From` autowykrywania należy uwzględnić parametr w żądaniu tłumaczenia interfejsu API. 

**Przykład: en-de:**

Dane wejściowe źródła:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Docelowy wynik:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Ta funkcja działa tak samo jak w przypadku trybu HTML i bez niego.

Korzystanie z tej funkcji jest oszczędne. Lepszym sposobem dostosowania tłumaczenia jest użycie translatora niestandardowego. W przypadku translatora niestandardowego w pełni stosowane są prawdopodobieństwa kontekstowe i statystyczne. Jeśli masz lub możesz utworzyć dane szkoleniowe, które pokazują swoją służbę lub frazę w kontekście, uzyskasz dużo lepszych wyników. Więcej informacji na temat usługi tłumaczenia niestandardowego można znaleźć [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)pod adresem.
