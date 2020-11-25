---
title: Dostosowywanie modelu marek przy użyciu interfejsu API Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model marek przy użyciu interfejsu API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 5fc565ecc1b501f52e934784695594dcfef2a83a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020470"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Dostosowywanie modelu marek przy użyciu interfejsu API Video Indexer

Video Indexer obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowanych przez bazę danych marek usługi Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli zostanie wyświetlona w tekście wizualnym w filmie wideo, Video Indexer wykrywa ją jako markę zawartości. Niestandardowy model marek umożliwia wykluczenie niektórych marek z wykrycia i uwzględnienie marek, które powinny być częścią modelu, który może nie znajdować się w bazie danych marek usługi Bing.

Aby zapoznać się z szczegółowym omówieniem, zobacz [Omówienie](customize-brands-model-overview.md).

Za pomocą interfejsów API Video Indexer można tworzyć, używać i edytować niestandardowe modele marek wykryte w filmie wideo, zgodnie z opisem w tym temacie. Możesz również użyć witryny sieci Web Video Indexer, zgodnie z opisem w temacie [Dostosowywanie modelu marek przy użyciu witryny sieci web video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Tworzenie marki

Interfejs API [tworzenia marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) tworzy nową markę niestandardową i dodaje ją do niestandardowego modelu marek dla określonego konta.

> [!NOTE]
> Ustawienie `enabled` (w treści) na wartość true powoduje umieszczenie na liście *dołączanej* marki Video Indexer do wykrycia. Ustawienie `enabled` wartości false powoduje umieszczenie marki na liście *wykluczeń* , więc Video Indexer nie wykryje go.

Inne parametry, które można ustawić w treści:

* `referenceUrl`Wartość może być dowolnymi witrynami referencyjnymi dla marki, takimi jak link do strony Wikipedia.
* `tags`Wartość jest listą tagów dla marki. Ten tag jest wyświetlany w polu *Kategoria* marki w witrynie sieci Web Video Indexer. Na przykład znak "Azure" może być oznaczony jako "Chmura" lub skategoryzowany.

### <a name="response"></a>Reakcja

Odpowiedź zawiera informacje dotyczące marki, która została właśnie utworzona, po następującym formacie.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Usuń markę

Interfejs API [usuwania marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) usuwa markę z niestandardowego modelu marek dla określonego konta. Konto jest określone w `accountId` parametrze. Po pomyślnym wywołaniu marka nie będzie już znajdować się na listach *Dołącz* lub *Wyklucz* marki.

### <a name="response"></a>Reakcja

Po pomyślnym usunięciu marki nie jest zwracana zawartość.

## <a name="get-a-specific-brand"></a>Uzyskaj konkretną markę

Interfejs API [uzyskiwania marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) pozwala wyszukiwać szczegóły marki w modelu niestandardowych marek dla określonego konta przy użyciu identyfikatora marki.

### <a name="response"></a>Reakcja

Odpowiedź zawiera informacje na temat wyszukiwanego znaku towarowego (przy użyciu identyfikatora marki) zgodnie z formatem poniższego przykładu.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled` jest ustawiana na wartość `true` oznacza, że marka znajduje się na liście *dołączania* Video Indexer do wykrycia, a `enabled` wartość false oznacza, że marka znajduje się na liście *wykluczeń* , Video Indexer więc nie zostanie wykryta.

## <a name="update-a-specific-brand"></a>Zaktualizuj konkretną markę

Interfejs API [aktualizacji marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) pozwala wyszukiwać szczegóły marki w modelu niestandardowych marek dla określonego konta przy użyciu identyfikatora marki.

### <a name="response"></a>Reakcja

Odpowiedź zawiera zaktualizowane informacje dotyczące marki, które zostały zaktualizowane zgodnie z poniższym formatem.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Pobierz wszystkie marki

Interfejs API [Pobierz wszystkie marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) zwraca wszystkie marki w modelu Custom Marks dla określonego konta, niezależnie od tego, czy Marka powinna znajdować się na liście *Dołącz* lub *Wyklucz* marki.

### <a name="response"></a>Reakcja

Odpowiedź zawiera listę wszystkich marek w Twoim koncie oraz wszystkie ich szczegóły zgodnie z poniższym formatem.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Marka o nazwie *przykład* znajduje się na liście *dołączania* , aby wyVideo Indexer do wykrycia, a marka o nazwie *example2* znajduje się na liście *wykluczeń* , więc nie Video Indexer wykryć.

## <a name="get-brands-model-settings"></a>Pobierz ustawienia modelu marek

Interfejs API [ustawień pobierania marek](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) zwraca ustawienia modelu marek na określonym koncie. Ustawienia modelu marek przedstawiają, czy wykrywanie z bazy danych marek Bing jest włączone. Jeśli marki Bing nie są włączone, Video Indexer będzie wykrywać tylko marki z modelu niestandardowych marek określonego konta.

### <a name="response"></a>Reakcja

Odpowiedź wskazuje, czy marki Bing są włączone, zgodnie z poniższym formatem przykładu.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` ustawienie wartości true oznacza, że są włączone marki Bing. Jeśli `useBuiltin` ma wartość false, marki Bing są wyłączone. `state`Wartość można zignorować, ponieważ była przestarzała.

## <a name="update-brands-model-settings"></a>Zaktualizuj ustawienia modelu marek

Interfejs API [aktualizacji marek](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aktualizuje ustawienia modelu marek na określonym koncie. Ustawienia modelu marek przedstawiają, czy wykrywanie z bazy danych marek Bing jest włączone. Jeśli marki Bing nie są włączone, Video Indexer będzie wykrywać tylko marki z modelu niestandardowych marek określonego konta.

`useBuiltIn`Flaga ustawiona na wartość true oznacza, że marki Bing są włączone. Jeśli `useBuiltin` ma wartość false, marki Bing są wyłączone.

### <a name="response"></a>Reakcja

Nie jest zwracana zawartość, gdy ustawienie modelu marek zostanie pomyślnie zaktualizowane.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marek przy użyciu witryny sieci Web](customize-brands-model-with-website.md)
