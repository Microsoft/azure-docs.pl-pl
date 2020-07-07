---
title: Co to jest rozszerzenie Custom Translator?
titleSuffix: Azure Cognitive Services
description: Rozszerzenie Custom Translator oferuje możliwości podobne do tych, które usługa Microsoft Translator Hub oferuje w przypadku systemów statystycznego tłumaczenia maszynowego (SMT), ale wyłącznie dla systemów neuronowego tłumaczenia maszynowego (NMT).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d78767474150bc9571b25fe1f26135d6f41d1f20
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961238"
---
# <a name="what-is-custom-translator"></a>Co to jest rozszerzenie Custom Translator?

[Translator niestandardowy](https://portal.customtranslator.azure.ai) to funkcja usługi translator, która umożliwia firmom, deweloperom aplikacji i dostawcom usług językowych tworzenie dostosowanych systemów neuronowych Machine Translation (NMT). Dostosowane systemy tłumaczenia bezproblemowo integrują się z istniejącymi aplikacjami, przepływami pracy i witrynami internetowymi. 

Systemy tłumaczenia skompilowane przy użyciu [translatora niestandardowego](https://portal.customtranslator.azure.ai) są dostępne w ramach tej samej, [bezpiecznej](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), wysokiej wydajności, wysoce skalowalnej w chmurze [usługi Azure Cognitive Services translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), która umożliwia miliardy tłumaczeń codziennie.

Rozszerzenie Custom Translator obsługuje ponad trzy tuziny języków i jest mapowane bezpośrednio na języki dostępne dla neuronowego tłumaczenia maszynowego. Aby uzyskać pełną listę, zobacz [Języki usługi Translator](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funkcje

Rozszerzenie Custom Translator udostępnia różne funkcje umożliwiające utworzenie niestandardowego systemu tłumaczenia, a następnie uzyskiwanie do niego dostępu.

|Cecha  |Opis  |
|---------|---------|
|[Wykorzystanie technologii neuronowego tłumaczenia maszynowego](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Ulepsz swoje tłumaczenia dzięki wykorzystaniu neuronowego tłumaczenia maszynowego (NMT) dostarczanego przez rozszerzenie Custom Translator.       |
|[Tworzenie systemów, które znają terminologię biznesową użytkownika](what-are-parallel-documents.md)     |  Dostosowywanie i tworzenie systemów tłumaczenia przy użyciu równoległych dokumentów, które rozumieją terminologie używane w firmie i w branży.       |
|[Używanie słownika do tworzenia modeli](what-is-dictionary.md)     |   Jeśli nie masz zestawu danych szkoleniowych, możesz trenować model przy użyciu tylko danych słownika.       |
|[Współpraca z innymi](how-to-manage-settings.md#share-your-workspace)     |   Współpracuj ze swoim zespołem, udostępniając swoją pracę różnym osobom.     |
|[Dostęp do niestandardowego modelu tłumaczenia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Dostęp do niestandardowego modelu tłumaczenia można uzyskać w dowolnym momencie przez istniejące aplikacje/programy za pośrednictwem translatora v3.       |

## <a name="get-better-translations"></a>Uzyskiwanie lepszych tłumaczeń

Translator wydał [neuronowych Machine Translation (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) w 2016. Technologia NMT zapewnia lepszą jakość tłumaczenia niż będąca standardem w branży technologia [statystycznego tłumaczenia maszynowego (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation). Ponieważ NMT lepiej przechwytuje kontekst pełnych zdań przed ich przetłumaczeniem, zapewnia wyższej jakości, bardziej naturalnie brzmiące i płynne tłumaczenia. Rozszerzenie [Custom Translator](https://portal.customtranslator.azure.ai) udostępnia technologię NMT dla modeli niestandardowych, zapewniając lepszą jakość tłumaczenia.

Możesz użyć wcześniej przetłumaczonych dokumentów do utworzenia systemu tłumaczenia. Te dokumenty obejmują terminologię i styl specyficzny dla domeny, lepszy niż standardowy system tłumaczenia. Użytkownicy mogą przesyłać dokumenty ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX i XLSX.

Rozszerzenie Custom Translator akceptuje także dane równoległe na poziomie dokumentu, co umożliwia bardziej efektywne zbieranie i przygotowanie danych. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości w wielu językach, ale w osobnych dokumentach, rozszerzenie Custom Translator będzie w stanie automatycznie dopasowywać zdania między dokumentami.

Jeśli zostanie dostarczony odpowiedni typ i ilość danych szkoleniowych, często można zobaczyć [wynik BLEU](what-is-bleu-score.md) od 5 do 10 punktów, korzystając z rozszerzenia Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Wydajność i niskie koszty

W przypadku korzystania z rozszerzenia [Custom Translator](https://portal.customtranslator.azure.ai) uczenie i wdrażanie niestandardowego systemu nie wymaga żadnych umiejętności programistycznych.

Przy użyciu bezpiecznego portalu rozszerzenia [Custom Translator](https://portal.customtranslator.azure.ai) użytkownicy mogą przekazywać dane szkoleniowe, szkolić systemy, testować systemy i wdrażać je w środowisku produkcyjnym za pośrednictwem intuicyjnego interfejsu użytkownika. System będzie następnie dostępny do użytku na dużą skalę w ciągu kilku godzin (rzeczywisty czas zależy od rozmiaru danych szkoleniowych).

Dostęp do rozszerzenia [Custom Translator](https://portal.customtranslator.azure.ai) może być również uzyskiwany programowo za pośrednictwem [dedykowanego interfejsu API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (obecnie w wersji zapoznawczej). Interfejs API umożliwia użytkownikom zarządzanie tworzeniem lub aktualizowanie szkolenia w regularnych odstępach czasu za pośrednictwem ich własnych aplikacji lub usługi internetowej.

Koszt użycia modelu niestandardowego do translacji zawartości jest oparty na warstwie cenowej translatora użytkownika. Szczegóły warstwy cenowej można znaleźć na [stronie internetowej cennika usługi Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) Cognitive Services.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Bezpiecznie tłumaczenie zawsze i wszędzie we wszystkich aplikacjach i usługach

Systemy niestandardowe można bezproblemowo uzyskiwać dostęp do dowolnego produktu lub przepływu pracy oraz na dowolnym urządzeniu, korzystając z usługi Translator przy użyciu standardowej technologii REST.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [szczegóły dotyczące cen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Za pomocą [przewodnika Szybki start](quickstart-build-deploy-custom-model.md) naucz się tworzyć model tłumaczenia w rozszerzeniu Custom Translator.
