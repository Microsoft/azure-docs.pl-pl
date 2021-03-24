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
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content moderator, Azure Content moderator, moderator online, oprogramowanie do filtrowania zawartości, usługa moderowania treści, moderowanie zawartości
ms.openlocfilehash: a53611fdad84f06661f3b8928296b6a45851cea4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867274"
---
# <a name="what-is-azure-content-moderator"></a>Czym jest usługa Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator to usługa AI, która umożliwia obsługę zawartości potencjalnie obraźliwej, ryzykownej lub niepożądanej. Obejmuje ona usługę moderowania zawartości, która skanuje tekst, obrazy i wideo oraz automatycznie stosuje flagi zawartości, a także narzędzie do przeglądu, środowisko moderatora online dla zespołu recenzentów ludzkich.

Możesz chcieć utworzyć oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.

Ta dokumentacja zawiera następujące typy artykułów:  

* Przewodniki [**Szybki Start**](client-libraries.md) to instrukcje umożliwiające wykonywanie żądań do usługi.  
* [**Przewodniki z**](try-text-api.md) instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.  
* [**Koncepcje**](text-moderation-api.md) zawierają szczegółowe wyjaśnienia funkcji i funkcji usługi.  
* [**Samouczki**](ecommerce-retail-catalog-moderation.md) są więcej przewodnikami, które pokazują, jak korzystać z usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.  

## <a name="where-its-used"></a>Miejsce użycia

Poniżej przedstawiono kilka scenariuszy, w których deweloper oprogramowania lub zespół wymaga usługi moderowania zawartości:

- Portale online, które moderowają katalogi produktów i inną zawartość wygenerowaną przez użytkownika.
- Firmy zajmujące się giermi, które moderowają wygenerowane przez użytkownika artefakty gier i pokoje rozmowy.
- Platformy do obsługi wiadomości społecznościowych, które mają umiarkowane obrazy, tekst i filmy wideo dodane przez ich użytkowników.
- Firmy zajmujące się multimediami przedsiębiorstwa, które implementują scentralizowane moderowanie dla ich zawartości.
- K-12 dostawcy rozwiązań edukacyjnych, który umożliwia filtrowanie zawartości nieodpowiedniej dla uczniów i wykładowców.

> [!IMPORTANT]
> Nie można użyć Content Moderator do wykrywania niedozwolonych podrzędnych obrazów wykorzystujących luki w zabezpieczeniach. Jednak kwalifikowana organizacja może używać [usługi PhotoDNA w chmurze](https://www.microsoft.com/photodna "Usługa w chmurze firmy Microsoft PhotoDNA") na ekranie dla tego typu zawartości.

## <a name="what-it-includes"></a>Co zawiera

Usługa Content Moderator składa się z kilku interfejsów API usług internetowych dostępnych za pośrednictwem wywołań REST i zestawu .NET SDK. Zawiera również narzędzie do przeglądu, które pozwala recenzentom ludzkim ułatwić korzystanie z usługi i ulepszanie lub dostosowywanie jej funkcji moderowania.

## <a name="moderation-apis"></a>Interfejsy API moderowania

Usługa Content Moderator obejmuje interfejsy API moderowania, które sprawdzają zawartość materiałów, które są potencjalnie nieodpowiednie lub niepożądane.

![Diagram blokowy dla interfejsów API moderowania Content Moderator](images/content-moderator-mod-api.png)

W poniższej tabeli opisano różne typy interfejsów API moderowania.

| Grupa interfejsów API | Opis |
| ------ | ----------- |
|[**Moderowanie tekstu**](text-moderation-api.md)| Skanuje tekst w poszukiwaniu obraźliwej zawartości, niepopłciowo jawnej lub sugerującej zawartość, wulgarność i dane osobowe.|
|[**Niestandardowe listy terminów**](try-terms-list-api.md)| Skanuje tekst do niestandardowej listy warunków wraz z wbudowanymi postanowieniami. Użyj list niestandardowych, aby blokować zawartość lub zezwalać na nią zgodnie z własnymi zasadami dotyczącymi zawartości.|  
|[**Moderowanie obrazów**](image-moderation-api.md)| Skanuje obrazy pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywa tekst w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywa twarze.|
|[**Niestandardowe listy obrazów**](try-image-list-api.md)| Skanuje obrazy względem niestandardowej listy obrazów. Użyj niestandardowych list obrazów, aby filtrować wystąpienia często powtarzającej się zawartości, której nie chcesz klasyfikować ponownie.|
|[**Moderowanie filmów wideo**](video-moderation-api.md)| Skanuje filmy wideo pod kątem potencjalnej zawartości dla dorosłych lub zawartości erotycznej i zwraca znaczniki czasu dla wypowiadanej zawartości.|

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu pozwalają zintegrować potok moderowania z recenzentami ludzkich. Za pomocą [zadań](review-api.md#jobs), [przeglądów](review-api.md#reviews)i operacji [przepływu pracy](review-api.md#workflows) można tworzyć i automatyzować przepływy pracy w pętli, korzystając z [Narzędzia do przeglądu](#review-tool) (poniżej).

> [!NOTE]
> Interfejs API przepływu pracy nie jest jeszcze dostępny w zestawie SDK platformy .NET, ale może być używany z punktem końcowym REST.

![Diagram blokowy dla interfejsów API przeglądu Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Narzędzie do przeglądu

Usługa Content Moderator obejmuje również [Narzędzie do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md)w sieci Web, które hostuje przeglądy zawartości dla moderatorów ludzkich do przetworzenia. Wprowadzanie danych przez człowieka nie nadaje się do usługi, ale połączona z nimi pracownicy usługi i zespoły przeglądów ludzkich umożliwiają deweloperom uzyskanie równowagi między wydajnością i dokładnością. Narzędzie Recenzja zapewnia również przyjazne dla użytkownika fronton dla kilku zasobów Content Moderator.

![Strona główna narzędzia do przeglądu Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Content Moderator powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z Content Moderator w portalu internetowym, należy wykonać [Content moderator w sieci Web](quick-start.md). Można też zakończyć pracę z [biblioteką klienta lub interfejsem API REST](client-libraries.md) , aby zaimplementować podstawowe scenariusze w kodzie.