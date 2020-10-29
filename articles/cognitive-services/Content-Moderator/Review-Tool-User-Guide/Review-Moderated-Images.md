---
title: Korzystanie z przeglądów zawartości za pomocą narzędzia do przeglądu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak narzędzie do przeglądu pozwala moderatorom ludzkim przeglądać obrazy w portalu internetowym.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: cfda4d7970c734d92c9f2355d553721ef6165e43
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911855"
---
# <a name="create-human-reviews"></a>Tworzenie przeglądów ludzkich

W tym przewodniku dowiesz się, jak skonfigurować [przeglądy](../review-api.md#reviews) w witrynie sieci Web narzędzia do przeglądu. Przegląda i wyświetla zawartość dla moderatorów ludzkich do oceny. Moderatorzy mogą zmienić zastosowane znaczniki i zastosować własne niestandardowe znaczniki odpowiednio do potrzeb. Gdy użytkownik ukończy przegląd, wyniki są wysyłane do określonego punktu końcowego wywołania zwrotnego, a zawartość zostanie usunięta z lokacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w witrynie Content Moderator [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="image-reviews"></a>Przeglądy obrazów

1. Przejdź do [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/), wybierz kartę **Wypróbuj** i przekaż kilka obrazów do przejrzenia.
1. Po zakończeniu przetwarzania przekazanych obrazów przejdź do karty **Recenzja** i wybierz pozycję **obraz** .

    ![Przeglądarka Chrome wyświetlająca narzędzie Recenzja z wyróżnioną opcją obrazu przegląd](images/review-images-1.png)

    Obrazy są wyświetlane z dowolnymi etykietami, które zostały przypisane przez proces automatycznego moderowania. Obrazy przesłane za pomocą narzędzia do przeglądu nie są widoczne dla innych recenzentów.

1. Opcjonalnie Przenieś **przeglądy do** suwaka wyświetlania (1), aby dostosować liczbę obrazów wyświetlanych na ekranie. Kliknij przyciski **otagowane** lub **nieoznakowane** (2), aby odpowiednio posortować obrazy. Kliknij Panel tagów (3), aby go włączyć lub wyłączyć.

    ![Przeglądarka Chrome pokazująca narzędzie do przeglądu z otagowanymi obrazami do przeglądu](images/review-images-2.png)

1. Aby wyświetlić więcej informacji na temat obrazu, kliknij wielokropek na miniaturie i wybierz pozycję **Wyświetl szczegóły** . Można przypisać obraz do podzespołu z opcją **Move to** (zobacz sekcję [zespoły](./configure.md#manage-team-and-subteams) , aby dowiedzieć się więcej o podzespołów).

    ![Obraz z wyróżnioną opcją Wyświetl szczegóły](images/review-images-3.png)

1. Przejrzyj informacje o moderowaniu obrazu na stronie szczegółów.

    ![Obraz ze szczegółami moderowania wymieniony w osobnym okienku](images/review-images-4.png)

1. Po przejrzeniu i zaktualizowaniu przypisań tagów w razie potrzeby kliknij przycisk **dalej** , aby przesłać Recenzje. Po przesłaniu masz około pięciu sekund na kliknięcie przycisku **poprzedni** , aby powrócić do poprzedniego ekranu i ponownie przejrzeć obrazy. Po tym obrazie nie będą już w kolejce przesyłania, a przycisk **poprzedni** nie jest już dostępny.

## <a name="text-reviews"></a>Przeglądy tekstu

Przeglądy tekstu działają podobnie jak przeglądy obrazów. Zamiast przekazywania zawartości wystarczy napisać lub wkleić tekst (do 1 024 znaków). Następnie Content Moderator analizuje tekst i stosuje Tagi (oprócz innych informacji moderowania, takich jak wulgarność i dane osobowe). W przeglądach tekstu można przełączać zastosowane znaczniki i/lub zastosować niestandardowe znaczniki przed przesłaniem recenzji.

![Zrzut ekranu przedstawiający narzędzie Recenzja z oflagowanym tekstem w oknie przeglądarki Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób konfigurowania i używania przeglądów z poziomu [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com)Content Moderator. Następnie zapoznaj się z [przewodnikiem interfejsu API REST](../try-review-api-review.md) lub [zestawem SDK platformy .NET](../client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) w celu zapoznania się z programem programistycznym.