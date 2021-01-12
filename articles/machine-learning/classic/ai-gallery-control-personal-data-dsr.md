---
title: 'ML Studio (klasyczny): zarządzanie danymi Azure AI Gallery — Azure'
description: Możesz eksportować i usuwać dane użytkownika w ramach produktu z Azure AI Gallery przy użyciu interfejsu API katalogu lub galerii AI. W tym artykule pokazano, jak to zrobić.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 1c549cd4a4d76e96f16eb04f3cf5aa3368725b8c
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070545"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Wyświetlanie i usuwanie danych użytkownika w ramach produktu z Azure AI Gallery



Dane użytkownika w ramach produktu można wyświetlać i usuwać z Azure AI Gallery przy użyciu interfejsu API katalogu lub galerii AI. Ten artykuł zawiera informacje o tym, jak to zrobić.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Wyświetlanie danych w galerii AI przy użyciu interfejsu użytkownika

Możesz wyświetlić elementy opublikowane za pomocą interfejsu użytkownika Azure AI Gallery witryny sieci Web. Użytkownicy mogą wyświetlać zarówno rozwiązania publiczne, jak i nieznajdujące się na liście, projekty, eksperymenty i inne opublikowane elementy:

1.    Zaloguj się do [Azure AI Gallery](https://gallery.azure.ai/).
2.    Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta do załadowania strony profilu.
3.    Na stronie profil zostaną wyświetlone wszystkie elementy opublikowane w galerii, w tym wpisy nieznajdujące się na liście.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Wyświetlanie danych za pomocą interfejsu API wykazu galerii AI

Można programowo przeglądać dane zbierane za pomocą interfejsu API wykazu galerii AI, który jest dostępny pod adresem https://catalog.cortanaanalytics.com/entities . Aby wyświetlić dane, musisz mieć identyfikator autora. Aby wyświetlić nieznajdujące się na liście jednostki za pomocą interfejsu API wykazu, wymagany jest token dostępu.

Odpowiedzi wykazu są zwracane w formacie JSON.

### <a name="get-an-author-id"></a>Uzyskaj identyfikator autora
Identyfikator autora jest oparty na adresie e-mail używanym podczas publikowania w Azure AI Gallery. Nie zmienia się:

1. Zaloguj się do [Azure AI Gallery](https://gallery.azure.ai/).
2. Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta do załadowania strony profilu.
3. Adres URL na pasku adresu zawiera następujący identyfikator alfanumeryczny `authorId=` . Na przykład dla adresu URL: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    Identyfikator autora: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Uzyskaj token dostępu

Do wyświetlania jednostek nieznajdujących się na liście za pomocą interfejsu API wykazu jest wymagany token dostępu. Bez tokenu dostępu użytkownicy nadal mogą wyświetlać jednostki publiczne i inne informacje o użytkowniku.

Aby uzyskać token dostępu, należy sprawdzić `DataLabAccessToken` nagłówek żądania HTTP, które przeglądarka udostępni w interfejsie API katalogu podczas logowania:

1.    Zaloguj się do [Azure AI Gallery](https://gallery.azure.ai/).
2.    Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta do załadowania strony profilu.
3.    Otwórz okienko Narzędzia deweloperskie przeglądarki, naciskając klawisz F12, wybierz kartę Sieć i Odśwież stronę. 
4. Przefiltruj żądania w *wykazie* ciągów, wpisując je w polu tekstowym filtr.
5.    W obszarze żądania kierowane do adresu URL `https://catalog.cortanaanalytics.com/entities` Znajdź żądanie Get i wybierz kartę *nagłówki* . Przewiń w dół do sekcji *żądania nagłówków* .
6.    Pod nagłówkiem `DataLabAccessToken` jest alfanumeryczny token. Aby zapewnić bezpieczeństwo danych, nie udostępniaj tego tokenu.

### <a name="view-user-information"></a>Wyświetlanie informacji o użytkowniku
Używając identyfikatora autora podanego w poprzednich krokach, Wyświetl informacje w profilu użytkownika, zastępując `[AuthorId]` następujący adres URL:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Na przykład to żądanie adresu URL:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Zwraca odpowiedź, taką jak:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Wyświetl jednostki publiczne

Interfejs API wykazu przechowuje informacje o opublikowanych jednostkach do Azure AI Gallery, które można także wyświetlić bezpośrednio w [witrynie sieci Web galerii AI](https://gallery.azure.ai/). 

Aby wyświetlić opublikowane jednostki, odwiedź następujący adres URL, zastępując `[AuthorId]` Identyfikator autora uzyskany powyżej. [](#get-an-author-id)

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Na przykład:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Wyświetlanie jednostek nieznajdujących się na liście i publicznych

To zapytanie wyświetla tylko jednostki publiczne. Aby wyświetlić wszystkie jednostki, w tym nieznajdujące się na liście, podaj token dostępu uzyskany w poprzedniej sekcji.

1.    Za pomocą narzędzia takiego jak [Poster](https://www.getpostman.com)Utwórz żądanie HTTP GET w adresie URL wykazu, zgodnie z opisem w artykule [pobieranie tokenu dostępu](#get-your-access-token).
2.    Tworzenie nagłówka żądania HTTP o nazwie `DataLabAccessToken` z wartością ustawioną na token dostępu.
3.    Prześlij żądanie HTTP.

> [!TIP]
> Jeśli niewymienione jednostki nie są wyświetlane w odpowiedziach z interfejsu API wykazu, użytkownik może mieć nieprawidłowy lub wygasły token dostępu. Wyloguj się z Azure AI Gallery, a następnie powtórz kroki opisane w sekcji [Uzyskiwanie tokenu dostępu](#get-your-access-token) w celu odnowienia tokenu. 
