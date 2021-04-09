---
title: Nieobsługiwane wdrożenia języka — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób wdrażania nieobsługiwanych par języka na platformie Azure Cognitive Services translatorem niestandardowym.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898074"
---
# <a name="unsupported-language-deployments"></a>Wdrożenia w nieobsługiwanych językach

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

W nadchodzącym wykorzystaniu centrum usługi Microsoft Translator firma Microsoft rozwiąże wszystkie modele aktualnie wdrożone za pomocą centrum. Wiele z modeli jest wdrożonych w centrum, których pary językowe nie są obsługiwane w Translatoru niestandardowym.  Nie chcemy, aby użytkownicy w tej sytuacji nie mieli możliwości tłumaczenia ich zawartości.

Mamy teraz proces, który pozwala na wdrożenie nieobsługiwanych modeli za pomocą translatora niestandardowego.  Ten proces umożliwia dalsze tłumaczenie zawartości przy użyciu najnowszego interfejsu API v3.  Te modele będą hostowane do momentu wybrania ich rozmieszczenia lub pary językowej staną się dostępne w usłudze translator niestandardowym.  W tym artykule opisano proces wdrażania modeli z nieobsługiwanymi parami języka.

## <a name="prerequisites"></a>Wymagania wstępne

Aby Twoje modele mogły być kandydatami do wdrożenia, muszą spełniać następujące kryteria:
* Projekt zawierający model musi być migrowany z centrum do translatora niestandardowego przy użyciu narzędzia migracji.  Proces migrowania projektów i obszarów roboczych można znaleźć [tutaj](how-to-migrate.md).
* Podczas migracji model musi znajdować się w stanie wdrożonym.  
* Para językowa modelu musi być nieobsługiwaną parą języka w Translatoru niestandardowym.  Pary językowe, w których język jest obsługiwany lub z angielskiego, ale para nie obejmuje języka angielskiego, są kandydatami dla nieobsługiwanych wdrożeń języka.  Na przykład model koncentratora dla pary języka francuskiego w języku niemieckim jest traktowany jako nieobsługiwana para językowa, mimo że język francuski w języku angielskim i angielskim ma obsługiwaną parę języków.

## <a name="process"></a>Proces
Po przeprowadzeniu migracji modeli z centrum, które są kandydatami do wdrożenia, można je znaleźć, przechodząc do strony **Ustawienia** obszaru roboczego i przewijając na koniec strony, w której zostanie wyświetlona sekcja **nieobsługiwane szkolenia w centrum usługi Translator** .  Ta sekcja pojawia się tylko wtedy, gdy istnieją projekty spełniające powyższe wymagania wstępne.

![Zrzut ekranu, który podświetla sekcję nieobsługiwane szkolenia centrum usługi Translator.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Na stronie wyboru **nieobsługiwanych szkoleń centrum usługi Translator** karta **zażądana szkoleń** zawiera modele, które kwalifikują się do wdrożenia.  Wybierz modele, które chcesz wdrożyć i przesłać żądanie.   Przed upływem ostatecznego terminu wdrożenia 30 kwietnia można wybrać dowolną liczbę modeli do wdrożenia.
 
![Zrzut ekranu przedstawiający kartę niewymagane szkolenia.](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Po przesłaniu model nie będzie już dostępny na karcie **niewymagane szkolenia** i zostanie wyświetlony na karcie **żądane szkolenia** .  Wymagane szkolenia można wyświetlić w dowolnym momencie.

![Jak przeprowadzić migrację z centrum](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Co dalej?

Modele wybrane do wdrożenia są zapisywane po zlikwidowaniu centrum i rozmieszczeniu wszystkich modeli.  Do momentu 24 maja przesłać żądania wdrożenia nieobsługiwanych modeli.  Firma Microsoft wdroży te modele od 15 czerwca, w tym momencie będą one dostępne za pomocą interfejsu API usługi Translator v3.  Ponadto będą one dostępne za pomocą interfejsu API v2 do 1 lipca.  

Aby uzyskać więcej informacji na temat ważnych dat wycofania centrum, zobacz [tutaj](https://www.microsoft.com/translator/business/hub/).
Po wdrożeniu będą stosowane normalne opłaty za hosting.  Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .  

W przeciwieństwie do standardowych modeli translatora niestandardowego modele centrów będą dostępne tylko w jednym regionie, więc opłaty za hosting w ramach usługi wiele regionów nie będą stosowane.  Po wdrożeniu można w dowolnym momencie cofnąć wdrożenie modelu centrum w migrowanym projekcie translatora niestandardowego.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](how-to-train-model.md).
- Zacznij korzystać ze wdrożonego niestandardowego modelu tłumaczenia za pośrednictwem [programu Microsoft interfejs API tłumaczenia tekstu w usłudze translator v3](../reference/v3-0-translate.md?tabs=curl).