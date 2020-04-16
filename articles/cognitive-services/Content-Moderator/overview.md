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
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f28f2bcf5d04c9a6354b8135bd39546b9d8b9bf3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404299"
---
# <a name="what-is-azure-content-moderator"></a>Czym jest usługa Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Moderator zawartości platformy Azure to usługa kognitywna, która sprawdza zawartość tekstową, obrazową i wideo pod kątem materiałów, które są potencjalnie obraźliwe, ryzykowne lub w inny sposób niepożądane. Po znalezieniu tego materiału usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników. Zobacz sekcję [Moderowanie interfejsów API,](#moderation-apis) aby dowiedzieć się więcej o tym, co wskazują różne flagi zawartości.

## <a name="where-its-used"></a>Gdzie jest używany

Poniżej przedstawiono kilka scenariuszy, w których deweloper lub zespół ds. oprogramowania będą używać pakietu Content Moderator:

- Internetowe platformy handlowe, które moderuje katalogi produktów i inne treści generowane przez użytkowników.
- Firmy zajmujące się grami, które moderowane są artefaktami gier generowanymi przez użytkowników i pokojami rozmów.
- Platformy wiadomości społecznościowych, które moderują obrazy, tekst i filmy dodane przez użytkowników.
- Firmy medialne dla przedsiębiorstw, które implementują scentralizowane moderowanie ich zawartości.
- Dostawcy rozwiązań edukacyjnych K-12 odfiltrowują treści nieodpowiednie dla uczniów i nauczycieli.

> [!NOTE]
> Nie można używać moderatora zawartości do wykrywania obrazów nielegalnego wykorzystywania dzieci. Jednak wykwalifikowane organizacje mogą korzystać z [usługi PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Usługa w chmurze PhotoDNA firmy Microsoft") do ekranu dla tego typu zawartości.

## <a name="what-it-includes"></a>Co zawiera

Usługa Content Moderator składa się z kilku interfejsów API usług internetowych dostępnych za pośrednictwem wywołań REST i zestawu .NET SDK. Zawiera również narzędzie Review, które pozwala recenzentom ludzkim na pomoc w usłudze i poprawienie lub dostosowanie jej funkcji moderacji.

## <a name="moderation-apis"></a>Interfejsy API moderowania

Usługa Content Moderator zawiera interfejsy API moderowania, które sprawdzają zawartość materiałów, które są potencjalnie nieodpowiednie lub budzące zastrzeżenia.

![diagram blokowy dla interfejsów API moderowania moderatora zawartości](images/content-moderator-mod-api.png)

W poniższej tabeli opisano różne typy interfejsów API moderowania.

| Grupa INTERFEJSU API | Opis |
| ------ | ----------- |
|[**Moderowanie tekstu**](text-moderation-api.md)| Skanuje tekst w poszukiwaniu obraźliwych treści, treści o charakterze jednoznacznie seksualnym lub sugestywnym, wulgaryzmów i danych osobowych.|
|[**Niestandardowe listy terminów**](try-terms-list-api.md)| Skanuje tekst względem niestandardowej listy terminów wraz z wbudowanymi warunkami. Użyj list niestandardowych, aby blokować zawartość lub zezwalać na nią zgodnie z własnymi zasadami dotyczącymi zawartości.|  
|[**Moderowanie obrazu**](image-moderation-api.md)| Skanuje obrazy pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywa tekst w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywa twarze.|
|[**Niestandardowe listy obrazów**](try-image-list-api.md)| Skanuje obrazy względem niestandardowej listy obrazów. Użyj niestandardowych list obrazów, aby filtrować wystąpienia często powtarzającej się zawartości, której nie chcesz klasyfikować ponownie.|
|[**Moderowanie wideo**](video-moderation-api.md)| Skanuje filmy wideo pod kątem potencjalnej zawartości dla dorosłych lub zawartości erotycznej i zwraca znaczniki czasu dla wypowiadanej zawartości.|

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu umożliwiają integrowanie potoku moderowania z ludzkimi recenzentami. Operacje [Zadania,](review-api.md#jobs) [Recenzje](review-api.md#reviews)i [Przepływ pracy umożliwia](review-api.md#workflows) tworzenie i automatyzowania przepływów pracy typu human-in-the-loop za pomocą narzędzia [Recenzja](#review-tool) (poniżej).

> [!NOTE]
> Interfejs API przepływu pracy nie jest jeszcze dostępny w pliku .NET SDK, ale może być używany z punktem końcowym REST.

![diagram blokowy dla interfejsów API przeglądu moderatora zawartości](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Narzędzie do przeglądu

Usługa Content Moderator zawiera również internetowe [narzędzie do recenzji,](Review-Tool-User-Guide/human-in-the-loop.md)które obsługuje opinie o zawartości, które mogą być przetwarzane przez moderatorów. Dane wejściowe człowieka nie szkoli usługi, ale połączona praca zespołów przeglądu usługi i człowieka pozwala deweloperom znaleźć właściwą równowagę między wydajnością i dokładnością. Narzędzie Recenzja zapewnia również przyjazny dla użytkownika front-end dla kilku zasobów moderatora zawartości.

![Strona główna narzędzia Recenzja moderatora zawartości](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Content Moderator powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z usługi Content Moderator, postępując zgodnie z instrukcjami w przewodniku [Wypróbowywanie usługi Content Moderator w Internecie](quick-start.md).
