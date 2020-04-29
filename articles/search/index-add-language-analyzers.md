---
title: Dodawanie analizatorów języka do pól ciągów
titleSuffix: Azure Cognitive Search
description: Wielojęzykowa analiza tekstu leksykalnego dla zapytań innych niż angielskie i indeksów w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283149"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Dodawanie analizatorów języka do pól ciągów w indeksie Wyszukiwanie poznawcze platformy Azure

*Analizator języka* jest określonym typem [analizatora tekstu](search-analyzers.md) , który wykonuje analizę leksykalną przy użyciu reguł lingwistycznych języka docelowego. Każde pole z możliwością wyszukiwania ma właściwość **analizatora** . Jeśli indeks zawiera przetłumaczone ciągi, takie jak oddzielne pola w języku angielskim i chińskim, można określić analizatory języka dla każdego pola, aby uzyskać dostęp do zaawansowanych funkcji językowych tych analizatorów.  

Platforma Azure Wyszukiwanie poznawcze obsługuje analizatory 35 objęte przez Lucene i analizatory 50 objęte umową Microsoft Natural language processing, która została zastosowana w pakiecie Office i Bing.

## <a name="comparing-analyzers"></a>Porównywanie analizatorów

Niektórzy deweloperzy mogą preferować bardziej znane, proste i łatwe w użyciu rozwiązanie typu open source. Analizatory języka Lucene są szybsze, ale analizatory firmy Microsoft mają zaawansowane możliwości, takie jak Lematyzacja, rozliczanie wyrazów (w językach takich jak niemiecki, duński, holenderski, szwedzki, norweski, estoński, Kończenie, węgierski, słowacki) i rozpoznawanie jednostek (adresy URL, wiadomości e-mail, daty i cyfry). Jeśli to możliwe, należy uruchomić porównania obu analizatorów firmy Microsoft i Lucene, aby zdecydować, która z nich jest lepsza. 

Indeksowanie za pomocą analizatorów firmy Microsoft jest średnio dwa do trzech razy wolniejsze niż ich odpowiedniki w Lucene, w zależności od języka. W przypadku zapytań o średnim rozmiarze nie powinno się znacznie wpłynąć na wydajność wyszukiwania. 

### <a name="english-analyzers"></a>Analizatory angielskie

Domyślną analizatorem jest standardowa Lucene, które dobrze sprawdzają się w języku angielskim, ale być może nie jest to również program w języku angielskim lub Microsoft English Analyzer. 
 
+ Analizator w języku angielskim Luces rozszerza analizatora standardowego. Usuwa possessives (końcowe) z wyrazów, stosuje rdzenie zgodnie z algorytmem rdzenia Porter i usuwa słowa zatrzymywania w języku angielskim.  

+ Program Microsoft English Analyzer wykonuje Lematyzacja zamiast rdzeni. Oznacza to, że może obsłużyć nietypowe i nieregularne formy wyrazów znacznie lepiej, co daje bardziej odpowiednie wyniki wyszukiwania 

## <a name="configuring-analyzers"></a>Konfigurowanie analizatorów

Analizatory języka są używane jako-is. Dla każdego pola w definicji indeksu można ustawić właściwość **analizatora** na nazwę analizatora, która określa język i stosy (Microsoft lub Lucene). Ten sam analizator zostanie zastosowany podczas indeksowania i wyszukiwania tego pola. Można na przykład mieć osobne pola w języku angielskim, francuskim i hiszpańskim, które znajdują się obok siebie w tym samym indeksie.

> [!NOTE]
> Nie można użyć innego analizatora języka w czasie indeksowania niż w czasie zapytania dla pola. Ta możliwość jest zarezerwowana dla [analizatorów niestandardowych](index-add-custom-analyzers.md). Z tego powodu, jeśli spróbujesz ustawić właściwości **searchAnalyzer** lub **indexAnalyzer** jako nazwę analizatora języka, interfejs API REST zwróci odpowiedź na błąd. Zamiast tego należy użyć właściwości **Analizator** .

Użyj parametru zapytania **searchFields** , aby określić, które pole specyficzne dla języka ma być wyszukiwane w zapytaniach. Przykłady zapytań, które obejmują Właściwość analizatora, można przejrzeć w [dokumentach wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Aby uzyskać więcej informacji o właściwościach indeksu, zobacz [create index &#40;Azure wyszukiwanie POZNAWCZE API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Aby uzyskać więcej informacji na temat analizy w usłudze Azure Wyszukiwanie poznawcze, zobacz [analizatory na platformie azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista analizatorów języka 
 Poniżej znajduje się lista obsługiwanych języków razem z nazwami Lucene i Microsoft Analyzer.  

|Język|Nazwa analizatora firmy Microsoft|Nazwa analizatora Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabski|ar. Microsoft|ar. Lucene|  
|Armeński||HY. Lucene|  
|Języku|mld USD. Microsoft||  
|Baskijski||UE. Lucene|  
|Bułgarski|BG. Microsoft|BG. Lucene|  
|Kataloński|CA. Microsoft|CA. Lucene|  
|Chiński (uproszczony)|zh-Hans. Microsoft|zh-Hans. Lucene|  
|Chiński (tradycyjny)|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Chorwacki|HR. Microsoft||  
|Czeski|cs. Microsoft|cs. Lucene|  
|duński|da. Microsoft|da. Lucene|  
|Niderlandzki|NL. Microsoft|NL. Lucene|  
|Angielski|pl. Microsoft|pl. Lucene|  
|Estoński|et. Microsoft||  
|fiński|Fi. Microsoft|Fi. Lucene|  
|Francuski|fr. Microsoft|fr. Lucene|  
|Galicyjski||GL. Lucene|  
|niemiecki|de. Microsoft|de. Lucene|  
|grecki|El. Microsoft|El. Lucene|  
|Gudżarati|gu. Microsoft||  
|Hebrajski|Firma Microsoft||  
|Hindi|Witaj. Microsoft|Witaj. Lucene|  
|węgierski|HU. Microsoft|HU. Lucene|  
|Islandzki|jest. Microsoft||  
|Indonezyjski (Bahasa)|Identyfikator. Microsoft|Identyfikator. Lucene|  
|Irlandzki||ga. Lucene|  
|Włoski|IT. Microsoft|IT. Lucene|  
|japoński|ja. Microsoft|ja. Lucene|  
|Kannada|kN. Microsoft||  
|koreański|ko. Microsoft|ko. Lucene|  
|Łotewski|LV. Microsoft|LV. Lucene|  
|Litewski|lt. Microsoft||  
|Malayalam|ml. Microsoft||  
|Malajski (łaciński)|MS. Microsoft||  
|Marathi|Mr. Microsoft||  
|Norweski|NB. Microsoft|nie. Lucene|  
|Perski||FA. Lucene|  
|Polski|pl. Microsoft|pl. Lucene|  
|portugalski (Brazylia)|pt-br. Microsoft|pt-br. Lucene|  
|Portugalski (Portugalia)|pt-pt. Microsoft|pt-pt. Lucene|  
|Pendżabski|PA. Microsoft||  
|Rumuński|ro. Microsoft|ro. Lucene|  
|Rosyjski|ru. Microsoft|ru. Lucene|  
|Serbski (cyrylica)|SR-cyrylica. Microsoft||  
|Serbski (łaciński)|SR-Latin. Microsoft||  
|Słowacki|SK. Microsoft||  
|Słoweński|SL. Microsoft||  
|Hiszpański|es. Microsoft|es. Lucene|  
|szwedzki|OHR. Microsoft|OHR. Lucene|  
|Tamilski|Ta. Microsoft||  
|Telugu|te. Microsoft||  
|Tajski|th. Microsoft|th. Lucene|  
|Turecki|TR. Microsoft|TR. Lucene|  
|Ukraiński|Wielka Brytania. Microsoft||  
|Urdu|Twoje. Microsoft||  
|Wietnamski|VI. Microsoft||  

 Wszystkie analizatory z nazwami oznaczonymi jako " **Lucene** " są obsługiwane przez [analizatory języka Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Zobacz także  

+ [Utwórz indeks &#40;interfejsu API REST usługi Azure Wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Analizatorname — Klasa](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

