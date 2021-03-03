---
title: Co to jest tłumaczenie dokumentu?
description: Przegląd usługi tłumaczenia dokumentów wsadowych opartej na chmurze i procesu.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: fc75bfc3b79cd6aed51a44fc47787532a9d61e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713018"
---
# <a name="what-is-document-translation-preview"></a>Co to jest tłumaczenie dokumentu (wersja zapoznawcza)?

Tłumaczenie dokumentu to oparta na chmurze funkcja usługi [Azure translator](../translator-info-overview.md) , która jest częścią rodziny usługi Azure poznawczej w ramach interfejsów API REST. Interfejs API tłumaczenia dokumentów tłumaczy dokumenty na i z 90 języków i dialektów przy zachowaniu struktury dokumentu i formatu danych.

## <a name="document-translation-key-features"></a>Funkcje klucza tłumaczenia dokumentu

| Cecha | Opis |
| ---------| -------------|
| **Tłumaczenie dużych plików**| Asynchroniczne tłumaczenie całych dokumentów.|
|**Tłumaczenie wielu plików**|Przetłumacz wiele plików na i z 90 języków i dialektów.|
|**Zachowaj prezentację pliku źródłowego**| Tłumaczenie plików przy zachowaniu oryginalnego układu i formatu.|
|**Zastosuj tłumaczenie niestandardowe**| Tłumaczenie dokumentów przy użyciu ogólnych i [niestandardowych modeli tłumaczenia](../customization.md#custom-translator) .|
|**Zastosuj niestandardowy Glossaries**|Tłumaczenie dokumentów przy użyciu niestandardowych Glossaries.|

## <a name="how-to-get-started"></a>Wprowadzenie

W naszym przewodniku dowiesz się, jak szybko rozpocząć korzystanie z usługi Document translator. Aby rozpocząć, musisz mieć aktywne [konto platformy Azure](https://azure.microsoft.com/free/cognitive-services/).  Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Wprowadzenie](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów

Tłumaczenia dokumentów obsługują następujące typy plików dokumentów:

| Typ pliku| Rozszerzenie pliku|Opis|
|---|---|--|
|Adobe PDF|pdf|Przenośny format dokumentu programu Adobe Acrobat|
|HTML|.html,|Język znaczników tekstu funkcji Hyper-in.|
|Format pliku wymiany lokalizacji|.xlf. , XLIFF| Format dokumentu równoległego, eksportowanie systemów pamięci translacji. Używane języki są zdefiniowane w pliku.|
|Microsoft Excel|xlsx|Plik arkusza kalkulacyjnego służący do analizy i dokumentacji danych.|
|Microsoft Outlook|. msg|Wiadomość e-mail utworzona lub zapisana w programie Microsoft Outlook.|
|Microsoft PowerPoint|pptx| Plik prezentacji służący do wyświetlania zawartości w formacie pokazu slajdów.|
|Microsoft Word|docx| Plik dokumentu tekstowego.|
|Wartości rozdzielane tabulatorami/tabulator|. tsv/. karta| rozdzielany znakami tabulacji plik danych nieprzetworzonych, używany przez programy arkusza kalkulacyjnego.|
|Tekst|txt| Niesformatowany dokument tekstowy.|
|Wymiana pamięci translacji|.tmx|Otwarty standard XML używany do wymiany danych pamięci translacji (TM) utworzonych przez aplikacje do przetłumaczenia i lokalizowania komputerów (CAT).|

## <a name="supported-glossary-formats"></a>Obsługiwane formaty słownika

Następujące typy plików słownika są obsługiwane przez tłumaczenie dokumentu:

| Typ pliku| Rozszerzenie pliku|Opis|
|---|---|--|
|Format pliku wymiany lokalizacji|.xlf. , XLIFF| Format dokumentu równoległego, eksportowanie systemów pamięci translacji. Używane języki są zdefiniowane w pliku.|
|Wartości rozdzielane tabulatorami/tabulator|. tsv/. karta| rozdzielany znakami tabulacji plik danych nieprzetworzonych, używany przez programy arkusza kalkulacyjnego.|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentu](get-started-with-document-translation.md)
>
>
