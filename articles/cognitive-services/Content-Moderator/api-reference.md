---
title: Dokumentacja interfejsu API — Content Moderator
titleSuffix: Azure Cognitive Services
description: Poznaj różne interfejsy API moderowania zawartości i przeglądów dla Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72757375"
---
# <a name="content-moderator-api-reference"></a>Dokumentacja interfejsu API Content Moderator

Możesz zacząć korzystać z interfejsów API usługi Azure Content Moderator w następujący sposób:

- W Azure Portal [Zasubskrybuj interfejs API Content moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Zobacz [Try Content moderator w sieci Web](quick-start.md) , aby zarejestrować się przy użyciu [narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>Interfejsy API moderowania

Aby skonfigurować przepływy pracy po moderowaniu, można użyć następujących Content Moderator interfejsów API.

| Opis | Tematy pomocy |
| -------------------- |-------------|
| **Interfejs API moderowania obrazów**<br /><br />Skanuj obrazy i wykrywaj potencjalną zawartość dla dorosłych i erotycznej przy użyciu tagów, wyników pewności i innych wyodrębnionych informacji. <br /><br />Te informacje służą do publikowania, odrzucania i przeglądania zawartości w przepływie pracy po moderowaniu. <br /><br />| [Dokumentacja interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Dokumentacja interfejsu API moderowania obrazów")   |
| **Interfejs API moderowania tekstu**<br /><br />Skanuj zawartość tekstową. Zwracane są postanowienia dotyczące niewulgarności i dane osobowe. <br /><br />Te informacje służą do publikowania, odrzucania i przeglądania zawartości w przepływie pracy po moderowaniu.<br /><br /> | [Dokumentacja interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Dokumentacja interfejsu API moderowania tekstu")   |
| **Interfejs API moderowania wideo**<br /><br />Skanuj wideo i wykrywaj potencjalną zawartość dla dorosłych i erotycznej. <br /><br />Te informacje służą do publikowania, odrzucania i przeglądania zawartości w przepływie pracy po moderowaniu.<br /><br /> | [Interfejs API moderowania wideo — Omówienie](video-moderation-api.md "Interfejs API moderowania wideo — Omówienie")   |
| **Interfejs API zarządzania listami**<br /><br />Twórz i Zarządzaj niestandardowymi listami wykluczeń i obrazów oraz tekstu. Jeśli ta funkcja jest włączona, operacje **dopasowania obrazu** i **ekranu tekstowego** wykonują rozmyte Dopasowywanie przesłanej zawartości do list niestandardowych. <br /><br />Aby uzyskać sprawność, możesz pominąć etap moderowania oparty na uczeniu maszynowym.<br /><br /> | [Dokumentacja interfejsu API zarządzania listami](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Dokumentacja interfejsu API zarządzania listami")   |

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu mają następujące składniki:

| Opis | Tematy pomocy |
| -------------------- |-------------|
| **Zadania**<br /><br /> Inicjuj przepływy pracy do skanowania i weryfikacji dla zawartości obrazów i tekstu. Zadanie moderowania skanuje zawartość przy użyciu interfejsu API moderowania obrazów i interfejsu API moderowania tekstu. Zadania moderowania używają zdefiniowanych i domyślnych przepływów pracy do generowania przeglądów. <br /><br />Gdy moderator ludzki przejrzał znaczniki i dane prognozowania, a następnie przesłał decyzję o moderowaniu zawartości, interfejs API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.<br /><br /> | [Odwołanie do zadania](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Odwołanie do zadania")   |
| **Przeglądy**<br /><br />Użyj narzędzia Recenzja, aby bezpośrednio utworzyć przeglądy obrazu lub tekstu dla moderatorów ludzkich.<br /><br /> Gdy moderator ludzki przejrzał znaczniki i dane prognozowania, a następnie przesłał decyzję o moderowaniu zawartości, interfejs API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.<br /><br /> | [Przegląd odwołania](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Przegląd odwołania")   |
| **Przepływy**<br /><br />Twórz, Aktualizuj i pobieraj szczegóły dotyczące niestandardowych przepływów pracy tworzonych przez zespół. Przepływy pracy można definiować za pomocą narzędzia do przeglądu. <br /> <br />Przepływy pracy zwykle używają Content Moderator, ale mogą również używać niektórych innych interfejsów API, które są dostępne jako łączniki w narzędziu do przeglądu.<br /><br /> | [Informacje o przepływie pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Informacje o przepływie pracy")   |