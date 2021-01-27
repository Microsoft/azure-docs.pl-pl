---
title: Co to jest rozszerzenie Custom Translator?
titleSuffix: Azure Cognitive Services
description: Rozszerzenie Custom Translator oferuje możliwości podobne do tych, które usługa Microsoft Translator Hub oferuje w przypadku systemów statystycznego tłumaczenia maszynowego (SMT), ale wyłącznie dla systemów neuronowego tłumaczenia maszynowego (NMT).
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 37d31d753812924607c4d31df6d4f4d5e7b32d69
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890717"
---
# <a name="what-is-custom-translator"></a>Co to jest rozszerzenie Custom Translator?

[Custom Translator](https://portal.customtranslator.azure.ai) to funkcja usługi Microsoft Translator, która umożliwia przedsiębiorstwom w usłudze Translator, deweloperom aplikacji i dostawcom usług językowych tworzenie dostosowanych systemów neuronowego tłumaczenia maszynowego (NMT). Dostosowane systemy tłumaczenia bezproblemowo integrują się z istniejącymi aplikacjami, przepływami pracy i witrynami internetowymi.

Systemy tłumaczenia wykorzystujące rozszerzenie [Custom Translator](https://portal.customtranslator.azure.ai) są dostępne za pośrednictwem tego samego opartego na chmurze, [bezpiecznego](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), wysoce wydajnego i wysoce skalowalnego [interfejsu API tłumaczenia tekstu w wersji V3](../reference/v3-0-translate.md?tabs=curl) w usłudze Microsoft Translator, który obsługuje miliardy tłumaczeń każdego dnia.

Rozszerzenie Custom Translator obsługuje ponad trzy tuziny języków i jest mapowane bezpośrednio na języki dostępne dla neuronowego tłumaczenia maszynowego. Aby uzyskać pełną listę, zobacz [Microsoft Translator Language (Języki w usłudze Microsoft Translator)](../language-support.md#customization).

## <a name="features"></a>Funkcje

Rozszerzenie Custom Translator udostępnia różne funkcje umożliwiające utworzenie niestandardowego systemu tłumaczenia, a następnie uzyskiwanie do niego dostępu.

|Cechy  |Opis  |
|---------|---------|
|[Wykorzystanie technologii neuronowego tłumaczenia maszynowego](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Ulepsz swoje tłumaczenia dzięki wykorzystaniu neuronowego tłumaczenia maszynowego (NMT) dostarczanego przez rozszerzenie Custom Translator.       |
|[Tworzenie systemów, które znają terminologię biznesową użytkownika](what-are-parallel-documents.md)     |  Dostosowywanie i tworzenie systemów tłumaczenia przy użyciu równoległych dokumentów, które rozumieją terminologie używane w firmie i w branży.       |
|[Używanie słownika do tworzenia modeli](what-is-dictionary.md)     |   Jeśli nie masz zestawu danych szkoleniowych, możesz trenować model przy użyciu tylko danych słownika.       |
|[Współpraca z innymi](how-to-manage-settings.md#share-your-workspace)     |   Współpracuj ze swoim zespołem, udostępniając swoją pracę różnym osobom.     |
|[Dostęp do niestandardowego modelu tłumaczenia](../reference/v3-0-translate.md?tabs=curl)     |  Dostęp do niestandardowego modelu tłumaczenia można uzyskać w dowolnym momencie przy użyciu istniejących aplikacji/programów za pomocą interfejsu API tłumaczenia tekstu w wersji V3 w usłudze Microsoft Translator.       |

## <a name="get-better-translations"></a>Uzyskiwanie lepszych tłumaczeń

[Neuronowe tłumaczenie maszynowe (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) w usłudze Microsoft Translator zostało udostępnione w 2016 roku. Technologia NMT zapewnia lepszą jakość tłumaczenia niż będąca standardem w branży technologia [statystycznego tłumaczenia maszynowego (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation). Ponieważ NMT lepiej przechwytuje kontekst pełnych zdań przed ich przetłumaczeniem, zapewnia wyższej jakości, bardziej naturalnie brzmiące i płynne tłumaczenia. Rozszerzenie [Custom Translator](https://portal.customtranslator.azure.ai) udostępnia technologię NMT dla modeli niestandardowych, zapewniając lepszą jakość tłumaczenia.

Możesz użyć wcześniej przetłumaczonych dokumentów do utworzenia systemu tłumaczenia. Te dokumenty obejmują terminologię i styl specyficzny dla domeny, lepszy niż standardowy system tłumaczenia. Użytkownicy mogą przesyłać dokumenty ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX i XLSX.

Rozszerzenie Custom Translator akceptuje także dane równoległe na poziomie dokumentu, co umożliwia bardziej efektywne zbieranie i przygotowanie danych. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości w wielu językach, ale w osobnych dokumentach, rozszerzenie Custom Translator będzie w stanie automatycznie dopasowywać zdania między dokumentami.

Jeśli zostanie dostarczony odpowiedni typ i ilość danych szkoleniowych, często można zobaczyć [wynik BLEU](what-is-bleu-score.md) od 5 do 10 punktów, korzystając z rozszerzenia Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Wydajność i niskie koszty

W przypadku korzystania z rozszerzenia [Custom Translator](https://portal.customtranslator.azure.ai) uczenie i wdrażanie niestandardowego systemu nie wymaga żadnych umiejętności programistycznych.

Przy użyciu bezpiecznego portalu rozszerzenia [Custom Translator](https://portal.customtranslator.azure.ai) użytkownicy mogą przekazywać dane szkoleniowe, szkolić systemy, testować systemy i wdrażać je w środowisku produkcyjnym za pośrednictwem intuicyjnego interfejsu użytkownika. System będzie następnie dostępny do użytku na dużą skalę w ciągu kilku godzin (rzeczywisty czas zależy od rozmiaru danych szkoleniowych).

Dostęp do rozszerzenia [Custom Translator](https://portal.customtranslator.azure.ai) może być również uzyskiwany programowo za pośrednictwem [dedykowanego interfejsu API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (obecnie w wersji zapoznawczej). Interfejs API umożliwia użytkownikom zarządzanie tworzeniem lub aktualizowanie szkolenia w regularnych odstępach czasu za pośrednictwem ich własnych aplikacji lub usługi internetowej.

Koszt używania modelu niestandardowego do tłumaczenia zawartości zależy od warstwy cenowej interfejsu API tłumaczenia tekstu w usłudze Translator użytkownika. Aby uzyskać szczegóły warstwy cenowej, zobacz [stronę internetową cennika interfejsu API tłumaczenia tekstu w usłudze Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Bezpiecznie tłumaczenie zawsze i wszędzie we wszystkich aplikacjach i usługach

Bezproblemowy dostęp do niestandardowych systemów i integrowanie ich z dowolnym produktem lub biznesowym przepływem pracy na dowolnym urządzeniu za pomocą interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator opartego na standardowej technologii REST.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [szczegóły dotyczące cen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Za pomocą [przewodnika Szybki start](quickstart-build-deploy-custom-model.md) naucz się tworzyć model tłumaczenia w rozszerzeniu Custom Translator.
