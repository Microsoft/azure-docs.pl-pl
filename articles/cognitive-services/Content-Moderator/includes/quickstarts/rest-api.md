---
title: Interfejs API REST Content Moderator — Szybki Start
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z interfejsem API REST usługi Azure Content Moderator. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 4c7aa1db2c7f475025585b9d93636243ba9ae3fe
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444832"
---
Rozpocznij pracę z interfejsem API REST usługi Azure Content Moderator. 

Content Moderator to usługa AI, która umożliwia obsługę zawartości potencjalnie obraźliwej, ryzykownej lub niepożądanej. Użyj usługi moderowania zawartości opartej na AI do skanowania tekstu, obrazów i filmów wideo i automatycznego stosowania flag zawartości. Następnie Zintegruj swoją aplikację za pomocą narzędzia do przeglądu, środowiska moderatora online dla zespołu recenzentów ludzkich. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.

Użyj interfejsu API REST Content Moderator, aby:

* Tekst umiarkowany
* Obrazy umiarkowane

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" Utwórz zasób Content moderator "  target="_blank"> utwórz zasób Content Moderator </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć swoją aplikację z Content Moderator. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Program [PowerShell w wersji 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)lub podobnej aplikacji w wierszu polecenia.


## <a name="moderate-text"></a>Tekst umiarkowany

Użyjesz polecenia, takiego jak następujące, aby wywołać interfejs API Content Moderator, aby analizować treść tekstu i drukować wyniki w konsoli.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Skopiuj polecenie do edytora tekstów i wprowadź następujące zmiany:

1. Przypisz `Ocp-Apim-Subscription-Key` do poprawnego klucza subskrypcji czołowej.
1. Zmień pierwszą część adresu URL zapytania, aby pasowała do punktu końcowego, który odpowiada kluczowi subskrypcji.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Opcjonalnie można zmienić treść żądania na dowolny ciąg tekstu, który chcesz analizować.

Po dokonaniu zmian otwórz wiersz polecenia i wprowadź nowe polecenie. 

### <a name="examine-the-results"></a>Sprawdzanie wyników

W oknie konsoli powinny być widoczne wyniki moderowania tekstu wyświetlane jako dane JSON. Na przykład:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Aby uzyskać więcej informacji na temat atrybutów tekstu, które Content Moderator ekrany dla programu, zobacz Przewodnik dotyczący [pojęć związanych z moderowaniem tekstu](../../text-moderation-api.md) .

## <a name="moderate-images"></a>Obrazy umiarkowane

Użyjesz polecenia, takiego jak następujące, aby wywołać interfejs API Content Moderator, aby uzyskać średni obraz zdalny i wydrukować wyniki w konsoli programu.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Skopiuj polecenie do edytora tekstów i wprowadź następujące zmiany:

1. Przypisz `Ocp-Apim-Subscription-Key` do poprawnego klucza subskrypcji czołowej.
1. Zmień pierwszą część adresu URL zapytania, aby pasowała do punktu końcowego, który odpowiada kluczowi subskrypcji.
1. Opcjonalnie można zmienić `"Value"` adres URL w treści żądania na każdy obraz zdalny, który ma być umiarkowany.

> [!TIP]
> Możesz również moderować obrazy lokalne przez przekazanie ich danych bajtowych do treści żądania. Zapoznaj się z [dokumentacją referencyjną](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) , aby dowiedzieć się, jak to zrobić.

Po dokonaniu zmian otwórz wiersz polecenia i wprowadź nowe polecenie. 

### <a name="examine-the-results"></a>Sprawdzanie wyników

W oknie konsoli powinny być widoczne wyniki moderowania obrazu wyświetlane jako dane JSON. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Aby uzyskać więcej informacji na temat atrybutów obrazu, które Content Moderator ekrany dla programu, zobacz Przewodnik dotyczący [pojęć związanych z moderowaniem obrazu](../../image-moderation-api.md) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z interfejsu API REST Content Moderator do wykonywania zadań moderowania. Następnie Dowiedz się więcej o moderowaniu obrazów lub innych nośnikach, odczytując Przewodnik koncepcyjny.

* [Pojęcia związane z moderowaniem obrazu](../../image-moderation-api.md)
* [Pojęcia dotyczące moderowania tekstu](../../text-moderation-api.md)
* [Czym jest usługa Azure Content Moderator?](../../overview.md)