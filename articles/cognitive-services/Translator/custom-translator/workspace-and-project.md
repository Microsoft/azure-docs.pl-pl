---
title: Co to jest obszar roboczy i projekt? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano różnice między obszarem roboczym i projektem, a także kategorie projektu i etykiety dla usługi translatora niestandardowego.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 28bc07cadf9ffea56059755e243cb3e828077588
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368971"
---
# <a name="what-is-a-custom-translator-workspace"></a>Co to jest obszar roboczy usługi tłumaczenia niestandardowego?

Obszar roboczy to obszar roboczy służący do redagowania i kompilowania niestandardowego systemu tłumaczenia. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów. Cała służbowa zawartość w usłudze translator niestandardowy znajduje się w określonym obszarze roboczym.

Obszar roboczy jest prywatny dla Ciebie i zapraszanych osób do obszaru roboczego. Nieproszone osoby nie mają dostępu do zawartości obszaru roboczego. Możesz zapraszać dowolną liczbę osób w Twoim obszarze roboczym i modyfikować lub usuwać swoje prawa dostępu w dowolnym momencie. Możesz również utworzyć nowy obszar roboczy. Domyślnie obszar roboczy nie będzie zawierał żadnych projektów ani dokumentów znajdujących się w innych obszarach roboczych.

## <a name="what-is-a-custom-translator-project"></a>Co to jest projekt translatora niestandardowego?

Projekt jest otoką dla modelu, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkie dokumenty, które są przekazywane do tego obszaru roboczego, który ma poprawną parę językową. Na przykład, jeśli istnieje zarówno język angielski dla projektu hiszpańskiego, jak i projekt hiszpański dla języka angielskiego, te same dokumenty zostaną uwzględnione w obu projektach. Każdy projekt ma skojarzony z nim IDKategorii, który jest używany podczas wykonywania zapytań do [interfejsu API v3](../reference/v3-0-translate.md?tabs=curl) w celu tłumaczenia. IDKategorii jest parametrem używanym do uzyskiwania tłumaczeń z dostosowanego systemu utworzonego za pomocą translatora niestandardowego.

## <a name="project-categories"></a>Kategorie projektu

Kategoria identyfikuje domenę — obszar terminologii i stylu, który ma być używany dla projektu. Wybierz kategorię, która jest najbardziej odpowiednia dla dokumentów. W niektórych przypadkach wybór kategorii bezpośrednio wpływa na zachowanie translatora niestandardowego.

Mamy dwa zestawy modeli bazowych. Są one ogólne i technologie. W przypadku wybrania **technologii** kategorii będą używane modele linii bazowej technologii. W przypadku każdej innej kategorii są używane ogólne modele bazowe. Model linii bazowej technologii jest dobrze w domenie technologicznej, ale pokazuje niższą jakość, jeśli zdania używane do tłumaczenia nie wchodzą w skład domeny technologii. Zalecamy klientom wybór technologii kategorii tylko wtedy, gdy zdania są ściśle w domenie technologii.

W tym samym obszarze roboczym możesz tworzyć projekty dla tej samej pary językowej w różnych kategoriach. Niestandardowa translator uniemożliwia Tworzenie zduplikowanego projektu z tą samą parą językową i kategorią. Zastosowanie etykiety do projektu pozwala uniknąć tego ograniczenia. Nie używaj etykiet, chyba że tworzysz systemy tłumaczeń dla wielu klientów, ponieważ dodawanie unikatowej etykiety do projektu zostanie odzwierciedlone w swoich projektach IDKategorii.

## <a name="project-labels"></a>Etykiety projektu

Translator niestandardowy umożliwia przypisanie etykiety projektu do projektu. Etykieta projektu odróżnia wiele projektów z tą samą parą językową i kategorią. Najlepszym rozwiązaniem jest unikanie używania etykiet projektu, chyba że jest to konieczne.

Etykieta projektu jest używana jako część IDKategorii. Jeśli etykieta projektu jest pozostawiona nieustawiona lub jest ustawiana identycznie między projektami, projekty o tej samej kategorii i *różne* pary języka będą współużytkować ten sam IDkategorii. Takie podejście jest korzystne, ponieważ umożliwia użytkownikowi lub klientowi przechodzenie między językami w przypadku korzystania z interfejsu API usługi Text translator bez obaw o IDKategorii, który jest unikatowy dla każdego projektu.

Na przykład jeśli chciałem włączyć tłumaczenia w domenie technologii z języka angielskiego na francuski i z francuskiego na angielski, chcę utworzyć dwa projekty: jeden dla języka angielskiego — \> francuski i drugi dla języka francuskiego \> . Należy określić tę samą kategorię (technologię) dla obu i pozostawić pustą etykietę projektu. IDKategorii dla obu projektów jest zgodny, dlatego można wysyłać zapytania do interfejsu API w przypadku tłumaczeń w języku angielskim i francuskim bez konieczności modyfikowania mojego IDKategorii.

Jeśli jesteś dostawcą usług językowych i chcesz obsłużyć wielu klientów z różnymi modelami, które zachowują ten sam atrybut kategorii i języka, użyj etykiety projektu w celu rozróżnienia klientów.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [szkoleniach i modelu](training-and-model.md) , aby poznać, jak efektywnie kompilować model tłumaczenia.