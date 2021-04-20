---
title: Czym jest usługa Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi Content Moderator do śledzenia, flagowania, oceniania i filtrowania nieodpowiednich treści w zawartości wygenerowanej przez użytkownika.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/16/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, azure content moderator, online moderator, oprogramowanie do filtrowania zawartości, usługa moderowania zawartości, moderowanie zawartości
ms.openlocfilehash: b0ff5a241a76cd49d104e4145df37515b43c4e27
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726200"
---
# <a name="what-is-azure-content-moderator"></a>Czym jest usługa Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator to usługa AI, która umożliwia obsługę zawartości potencjalnie obraźliwej, ryzykownej lub w inny sposób niepożądanej. Obejmuje ona usługę moderowania zawartości bazującą na AI, która skanuje tekst, obrazy i wideo oraz automatycznie stosuje flagi zawartości, a także narzędzie Do przeglądu, czyli środowisko moderatora online dla zespołu recenzentów.

Możesz chcieć utworzyć oprogramowanie do filtrowania zawartości w swojej aplikacji, aby zachować zgodność z przepisami lub zachować środowisko przeznaczone dla użytkowników.

Ta dokumentacja zawiera następujące typy artykułów:  

* [**Przewodniki Szybki start to**](client-libraries.md) instrukcje z wprowadzeniem, które pokierują Cię przez proces tworzenia żądań do usługi.  
* [**Przewodniki z instrukcjami**](try-text-api.md) zawierają instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.  
* [**Pojęcia**](text-moderation-api.md) te zawierają szczegółowe wyjaśnienia dotyczące funkcjonalności i funkcji usługi.  
* [**Samouczki**](ecommerce-retail-catalog-moderation.md) to dłuższe przewodniki, które pokazują, jak używać usługi jako składnika w szerszych rozwiązaniach biznesowych.  

## <a name="where-its-used"></a>Gdzie jest używany

Poniżej przedstawiono kilka scenariuszy, w których deweloper oprogramowania lub zespół wymaga usługi moderowania zawartości:

- Platformy handlowe online moderowane katalogami produktów i inną zawartością wygenerowaną przez użytkowników.
- Firmy z branży gier moderowane artefaktami gier generowanymi przez użytkowników i pokoju rozmów.
- Platformy mediów społecznościowych, które moderują obrazy, tekst i filmy wideo dodane przez użytkowników.
- Przedsiębiorstwa z branży multimediów, które implementują scentralizowane moderowanie zawartości.
- Dostawcy rozwiązań dla edukacji k-12 filtrując zawartość nieodpowiednią dla uczniów i nauczycieli.

> [!IMPORTANT]
> Nie można używać Content Moderator do wykrywania niedozwolonych obrazów wykorzystywania dzieci. Jednak kwalifikowane organizacje mogą używać usługi [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") do ekranowania zawartości tego typu.

## <a name="what-it-includes"></a>Co zawiera

Usługa Content Moderator składa się z kilku interfejsów API usług internetowych dostępnych za pośrednictwem wywołań REST i zestawu .NET SDK. Zawiera również narzędzie review, które umożliwia recenzentom pomoc w usłudze oraz ulepszanie lub dostrajanie jej funkcji moderowania.

## <a name="moderation-apis"></a>Interfejsy API moderowania

Usługa Content Moderator obejmuje interfejsy API moderowania, które sprawdzają zawartość pod tematem materiałów, które mogą być nieodpowiednie lub niechętne.

![diagram blokowy dla Content Moderator api moderowania](images/content-moderator-mod-api.png)

W poniższej tabeli opisano różne typy interfejsów API moderowania.

| Grupa interfejsów API | Opis |
| ------ | ----------- |
|[**Moderowanie tekstu**](text-moderation-api.md)| Skanuje tekst pod poszukiwaniu obraźliwej zawartości, zawartości seksualnej lub sugeryjnej, wulgaryzmów i danych osobowych.|
|[**Niestandardowe listy terminów**](try-terms-list-api.md)| Skanuje tekst względem niestandardowej listy terminów wraz z terminami wbudowanymi. Użyj list niestandardowych, aby blokować zawartość lub zezwalać na nią zgodnie z własnymi zasadami dotyczącymi zawartości.|  
|[**Moderowanie obrazów**](image-moderation-api.md)| Skanuje obrazy pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywa tekst w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywa twarze.|
|[**Niestandardowe listy obrazów**](try-image-list-api.md)| Skanuje obrazy względem niestandardowej listy obrazów. Użyj niestandardowych list obrazów, aby filtrować wystąpienia często powtarzającej się zawartości, której nie chcesz klasyfikować ponownie.|
|[**Moderowanie filmów wideo**](video-moderation-api.md)| Skanuje filmy wideo pod kątem potencjalnej zawartości dla dorosłych lub zawartości erotycznej i zwraca znaczniki czasu dla wypowiadanej zawartości.|

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu umożliwiają zintegrowanie potoku moderowania z recenzentami. Operacje [Zadania,](review-api.md#jobs) [Przeglądy](review-api.md#reviews)i [Przepływ](review-api.md#workflows) pracy pozwalają tworzyć i automatyzować przepływy pracy wykonywane w pętli za pomocą narzędzia [do przeglądu](#review-tool) (poniżej).

> [!NOTE]
> Interfejs API przepływu pracy nie jest jeszcze dostępny w zestawie .NET SDK, ale może być używany z punktem końcowym REST.

![diagram blokowy na Content Moderator interfejsów API](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Narzędzie do przeglądu

Usługa Content Moderator obejmuje również internetowe narzędzie do przeglądu [,](Review-Tool-User-Guide/human-in-the-loop.md)które hostuje przeglądy zawartości do przetwarzania przez moderatorów. Dane wprowadzane przez ludzi nie trenują usługi, ale połączona praca zespołów ds. usługi i przeglądu przez ludzi pozwala deweloperom znaleźć odpowiednią równowagę między wydajnością i dokładnością. Narzędzie do przeglądu udostępnia również przyjazny dla użytkownika frontonie dla kilku Content Moderator zasobów.

![Content Moderator narzędzia do przeglądu](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Content Moderator powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z Content Moderator w portalu internetowym, postępuj zgodnie Content Moderator [w sieci Web.](quick-start.md) Możesz też ukończyć przewodnik Szybki start dla biblioteki klienta lub [interfejsu API REST,](client-libraries.md) aby zaimplementować podstawowe scenariusze w kodzie.