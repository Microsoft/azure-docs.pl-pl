---
title: Terminologia — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Lista terminów używanych w artykułach dotyczących translatorów niestandardowych.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657829"
---
# <a name="custom-translator-terminology"></a>Terminologia dotycząca translatora niestandardowego

W poniższej tabeli przedstawiono listę warunków, które mogą znajdować się podczas pracy z [translatorem niestandardowym](https://portal.customtranslator.azure.ai).

| Słowo lub fraza|Definicja|
|------------------|-----------|
| Język źródłowy | Język źródłowy to język początkowy, który ma zostać przekonwertowany do innego języka ("target").|
| Język docelowy| Język docelowy to język, który ma być udostępniany przez tłumaczenie maszynowe po odebraniu języka źródłowego. |
| Plik w językach | Plik w postaci jednojęzykowej nie jest sparowany z innym plikiem innego języka. |
| Pliki równoległe | Plik równoległy jest kombinacją dwóch plików z odpowiednim tekstem. Jeden plik ma język źródłowy. Drugi ma język docelowy.|
| Wyrównanie zdania| Równoległy zestaw danych musi mieć wyrównane zdania do zdań, które reprezentują ten sam tekst w obu językach. Na przykład w źródłowym pliku równoległym pierwsze zdanie powinno być w teorii mapowane na pierwsze zdanie w docelowym pliku równoległym.|
| Tekst wyrównany | Jednym z najważniejszych kroków weryfikacji plików jest wyrównanie zdań w dokumentach równoległych. Elementy są wyrażane inaczej w różnych językach. Również różne języki mają różne zamówienia programu Word. Ten krok polega na tym, że zadanie dopasowuje zdania z tą samą zawartością, aby można było ich używać do uczenia się. Wyrównanie do niskiego zdania wskazuje, że wystąpił problem z co najmniej jednym z tych plików. |
| Dzielenie wyrazów/rozbicie | Dzielenie wyrazów jest funkcją oznaczającą granice między wyrazami. Wiele systemów pisania używa spacji, aby zauważyć granicę między wyrazami. Cofnięcie podziału słowa odwołuje się do usunięcia dowolnego widocznego znacznika, który mógł zostać wstawiony między wyrazami w poprzednim kroku. |
| Ograniczniki   | Ograniczniki są sposobem dzielenia zdania na segmenty lub rozgraniczania marginesu między zdaniami. Na przykład w przypadku spacji w języku angielskim oddzielaj słowa, dwukropki i rozgraniczenia klauzule i kropki. |
| Pliki szkoleniowe | Plik szkoleniowy jest używany do uczenia systemu tłumaczenia maszynowego, jak mapować z jednego języka (źródłowego) do języka docelowego (obiekt docelowy). Im więcej danych zapewniasz, tym lepiej system będzie wykonywany. |
| Dostrajanie plików | Te pliki są często losowo wyprowadzane z zestawu szkoleniowego (jeśli nie wybrano zestawu strojenia). Zdania są autowybierane i używane do dostrajania systemu i gwarantują, że działa prawidłowo. Jeśli chcesz utworzyć model tłumaczenia ogólnego przeznaczenia i utworzyć własne pliki dostrajania, upewnij się, że są one losowo ustawionymi zdaniami w różnych domenach. |
| Testowanie plików| Te pliki są często plikami pochodnymi, losowo wybierane z zestawu szkoleniowego (jeśli nie wybrano żadnego zestawu testów). Celem tych zdań jest oszacowanie dokładności modelu tłumaczenia. Ponieważ te zdania są konieczne, aby upewnić się, że system prawidłowo się tłumaczy, warto utworzyć zestaw testów i przekazać go do translatora. Wykonanie tej czynności zapewni, że te zdania są używane w ocenie systemu (generowanie wyniku BLEU).   |
| Plik kombi   | Typ pliku, w którym zdanie źródłowe i tłumaczone są zawarte w tym samym pliku. Obsługiwane formaty plików (. TMX,. XLIFF,. xlf,. ICI,. xlsx). |
| Plik archiwum | Plik, który zawiera inne pliki. Obsługiwane formaty plików (zip, GZ, tgz).  |
| Wynik BLEU   | [Bleu](what-is-bleu-score.md) to standardowa metoda oceniania "precyzji" lub dokładności modelu tłumaczenia. Chociaż istnieją inne metody oceny, firma Microsoft Translator opiera się na metodzie BLEU do zgłaszania dokładności właścicielom projektu.
