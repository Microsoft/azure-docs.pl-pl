---
title: Nauka wzmacniania — Personalizacja
titleSuffix: Azure Cognitive Services
description: Program personalizujer używa informacji o akcjach i bieżącym kontekście w celu uzyskania lepszych sugestii dotyczących klasyfikacji. Informacje o tych akcjach i kontekście to atrybuty lub właściwości, które są określane jako funkcje.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132742"
---
# <a name="what-is-reinforcement-learning"></a>Co to jest uczenie wzmacniania?

Uczenie wzmacniające to podejście do uczenia maszynowego, które uczy się zachowania, uzyskując opinie od ich użycia.
 
Uczenie wzmacniające działa przez:

* Zapewnienie możliwości lub stopnia swobody wprowadzenia zachowania, takiego jak podejmowanie decyzji lub wybór.
* Dostarczanie kontekstowych informacji o środowisku i opcjach.
* Przesyłanie opinii na temat tego, jak dobre zachowanie osiąga określony cel.

Chociaż istnieje wiele podtypów i stylów nauki wzmacniania, jest to sposób działania koncepcji w programie personalizacji:

* Aplikacja umożliwia wyświetlenie jednej części zawartości z listy alternatyw.
* Aplikacja zawiera informacje o każdym alternatywie i kontekście użytkownika.
* Twoja aplikacja oblicza _wynik nagrody_.

W przeciwieństwie do niektórych podejścia do uczenia wzmacniania, Personalizacja nie wymaga symulacji do pracy w programie. Jego algorytmy szkoleniowe zostały zaprojektowane w celu reagowania na zewnętrzny świat (w przeciwieństwie do jego kontroli) i uczenia się od każdego punktu danych, dzięki czemu jest to unikatowa szansa, która pozwala zaoszczędzić czas i pieniądze.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Jakiego typu algorytmy szkoleniowe są używane przez program Personalizuj?

Bieżąca wersja programu Personalizacja korzysta z **Bandits kontekstowego**, podejścia do uczenia wzmacniania, które obejmuje podjęcie decyzji lub wyborów między dyskretnymi akcjami w danym kontekście.

_Pamięć decyzji_, model, który został przeszkolony do przechwytywania najlepszej możliwej decyzji, z uwzględnieniem kontekstu, korzysta z zestawu modeli liniowych. Często pokazują one wyniki biznesowe i są sprawdzonym podejściem, częściowo ponieważ mogą uczyć się od rzeczywistego świata, bez konieczności szkolenia wielu testów i częściowo, ponieważ mogą one uzupełniać modele uczenia nadzorowanego i głębokie modele sieci neuronowych.

Alokacja ruchu w celu eksplorowania/wykorzystywania odbywa się losowo po ustawieniu wartości procentowej dla eksploracji, a domyślnym algorytmem eksploracji jest Epsilon-zachłanne.

### <a name="history-of-contextual-bandits"></a>Historia Bandits kontekstowych

Jan Langford zanagradzał nazwę kontekstową Bandits (Langford i Zhang [2007]) w celu opisania podzbioru kształcenia wzmacniania i pracowała nad połowami papierów, które ulepszają zrozumienie, jak poznać ten model:

* Beygelzimer et al. [2011]
* Dudík et al. [2011, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer i Langford [2009]
* Li et al. [2010]

Jan nauczył również kilka samouczków, takich jak wspólne przewidywania (ICML 2015), kontekstowe teorii Bandit (NIPS 2013), aktywne uczenie (ICML 2009) i przykładowe ograniczenia złożoności (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Które platformy uczenia maszynowego są używane przez program do personalizacji?

Personalizacja obecnie używa [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) jako podstawy dla uczenia maszynowego. Ta platforma pozwala na maksymalną przepływność i najmniejsze opóźnienia przy tworzeniu rangi personalizacji i uczeniu modelu ze wszystkimi zdarzeniami.

## <a name="references"></a>Odwołania

* [Podejmowanie decyzji kontekstowych z niską Długem technicznym](https://arxiv.org/abs/1606.03966)
* [Zmniejszenie podejścia do uczciwej klasyfikacji](https://arxiv.org/abs/1803.02453)
* [Wydajne Bandits kontekstowe w nieruchomych światach](https://arxiv.org/abs/1708.01799)
* [Przewidywanie strat resztkowych: wzmacnianie: uczenie się bez informacji zwrotnych](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapowanie instrukcji i wizualnych obserwacji do akcji z uczeniem wzmacniania](https://arxiv.org/abs/1704.08795)
* [Uczenie się lepiej niż nauczyciel](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Następne kroki

[Ocena w trybie offline](concepts-offline-evaluation.md) 