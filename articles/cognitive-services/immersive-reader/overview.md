---
title: Co to jest czytnik immersyjny?
titleSuffix: Azure Cognitive Services
description: Czytnik immersyjny to narzędzie, które jest przeznaczone do ułatwienia innym osobom, które mają różne różnice, lub pomagają nowym czytelnikom i osobom korzystającym z czytelnych informacji.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: Czytelnicy, informacje o języku, wyświetlanie obrazów, ulepszanie odczytu, odczytywanie zawartości, tłumaczenie
ms.openlocfilehash: 1911d3ce62434f1dc24966798e9ace259ae072bc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801574"
---
# <a name="what-is-immersive-reader"></a>Co to jest czytnik immersyjny?

[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności dla nowych czytelników, nauk językowych i osób, które mają różnice szkoleniowe, takie jak Dyslexia. Za pomocą biblioteki klienta czytnika immersyjny można korzystać z tej samej technologii, która jest używana w programach Microsoft Word i Microsoft renote do ulepszania aplikacji sieci Web. 

Ta dokumentacja zawiera następujące typy artykułów:  

* Przewodniki **[Szybki Start](quickstarts/client-libraries.md)** to instrukcje umożliwiające wykonywanie żądań do usługi.
* **[Przewodniki z](how-to-create-immersive-reader.md)** instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Korzystanie z czytnika immersyjny w celu usprawnienia ułatwienia dostępu do odczytu 

Czytnik immersyjny został zaprojektowany w celu łatwiejszego czytania i ułatwienia dostępu dla wszystkich użytkowników. Przyjrzyjmy się kilku podstawowym funkcjom czytnika immersyjny.

### <a name="isolate-content-for-improved-readability"></a>Izoluj zawartość, aby zwiększyć czytelność

Czytnik immersyjny izoluje zawartość, aby zwiększyć czytelność. 

  ![Izoluj zawartość w celu zwiększenia czytelności dzięki czytnikom immersyjny](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Wyświetlanie obrazów dla typowych słów

W przypadku często używanych terminów czytnik immersyjny będzie wyświetlał obraz.

  ![Słownik obrazu z czytnikiem immersyjny](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Wyróżnij części mowy

Czytnik immersyjny może służyć do zdobywania informacji o częściach mowy i gramatyki, wyróżniania czasowników, rzeczowników, prorzeczowników itd.

  ![Pokaż części mowy za pomocą czytnika immersyjny](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Czytaj zawartość na głos

Synteza mowy (lub zamiana tekstu na mowę) jest rozszerzania w usłudze czytnika immersyjny, co umożliwia czytelnikom wybranie tekstu do odczytu na głos. 

  ![Czytaj tekst na głos z czytnikiem immersyjny](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Tłumaczenie zawartości w czasie rzeczywistym

Czytnik immersyjny może tłumaczyć tekst w wielu językach w czasie rzeczywistym. Jest to przydatne w celu ulepszania zrozumienia dla czytelników.

  ![Tłumaczenie tekstu za pomocą czytnika immersyjny](./media/translation.png)

### <a name="split-words-into-syllables"></a>Dzielenie wyrazów na sylaby

Za pomocą czytnika immersyjny można przerwać słowa w sylabach, aby zwiększyć czytelność lub wyszukać nowe słowa.

  ![Dzielenie wyrazów na sylaby za pomocą czytnika immersyjny](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak działa czytnik immersyjny?

Czytnik immersyjny to autonomiczna aplikacja internetowa. Po wywołaniu przy użyciu biblioteki klienta czytnika immersyjny jest wyświetlana na górze istniejącej aplikacji sieci Web w witrynie `iframe` . Gdy aplikacja WEP wywołuje usługę czytnika immersyjny, należy określić zawartość do wyświetlania czytnika. Biblioteka klienta czytnika immersyjny obsługuje tworzenie i określanie stylów `iframe` i komunikacji z usługą zaplecza czytnika immersyjny. Usługa czytnika immersyjny przetwarza zawartość dla części mowy, zamiany tekstu na mowę, tłumaczenia i nie tylko.

## <a name="get-started-with-immersive-reader"></a>Wprowadzenie do czytnika immersyjny

Biblioteka klienta czytnika immersyjny jest dostępna w językach C#, JavaScript, Java (Android), Kotlin (Android) i SWIFT (iOS). Rozpocznij pracę, korzystając z następujących zasobów:

* [Szybki Start: korzystanie z biblioteki klienta czytnika immersyjny](quickstarts/client-libraries.md)
