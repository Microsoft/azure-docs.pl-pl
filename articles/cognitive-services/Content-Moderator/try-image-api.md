---
title: Umiarkowane obrazy za pomocą konsoli interfejsu API Content Moderator
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API moderowania obrazów na platformie Azure Content Moderator, aby inicjować przepływy pracy dla moderowania skanowania i przeglądania dla zawartości obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75448258"
---
# <a name="moderate-images-from-the-api-console"></a>Umiarkowane obrazy z konsoli interfejsu API

Użyj [interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) na platformie Azure Content moderator, aby inicjować przepływy pracy dla moderowania skanowania i przeglądania dla zawartości obrazu. Zadanie moderowania skanuje zawartość pod kątem niewulgarności i porównuje ją z niestandardowymi i udostępnionymi listy blokowania adresów.

## <a name="use-the-api-console"></a>Korzystanie z konsoli interfejsu API
Aby można było przetestować interfejs API w konsoli online, musisz mieć swój klucz subskrypcji. Ta lokalizacja znajduje się na karcie **Ustawienia** w polu **OCP-APIM-Subscription-Key** . Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

1. Przejdź do [dokumentacji interfejsu API moderowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Zostanie otwarta strona **szacowanie** obrazu.

2. W obszarze **Otwórz konsolę testowania interfejsu API**wybierz region, który najlepiej opisuje lokalizację. 

   ![Wypróbuj obraz-Oceń wybór obszaru strony](images/test-drive-region.png)
  
   Zostanie otwarta konsola interfejsu API **szacowania obrazu** .

3. W polu **OCP-APIM-Subscription-Key** wprowadź swój klucz subskrypcji.

   ![Wypróbuj obraz-Oceń klucz subskrypcji konsoli](images/try-image-api-1.PNG)

4. W polu **treść żądania** Użyj domyślnego obrazu przykładowego lub określ obraz do skanowania. Możesz przesłać obraz jako dane binarne danych binarnych lub określić publicznie dostępny adres URL obrazu. 

   Na potrzeby tego przykładu użyj ścieżki podanej w polu **treść żądania** , a następnie wybierz pozycję **Wyślij**. 

   ![Wypróbuj obraz-Oceń treść żądania konsoli](images/try-image-api-2.PNG)

   To jest obraz pod tym adresem URL:

   ![Wypróbuj obraz-Oceń przykład konsoli](images/sample-image.jpg) 

5. Wybierz pozycję **Wyślij**.

6. Interfejs API zwraca wynik prawdopodobieństwa dla każdej klasyfikacji. Zwraca również określenie, czy obraz spełnia warunki (**true** lub **false**). 

   ![Wypróbuj obraz-Oceń wynik prawdopodobieństwa konsoli i określanie warunku](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Wykrywanie twarzy

Możesz użyć interfejsu API moderowania obrazów, aby zlokalizować twarze na obrazie. Ta opcja może być przydatna, jeśli masz problemy z ochroną prywatności i chcesz zapobiec opublikowaniu na platformie określonego elementu. 

1. W [dokumentacji interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)w menu po lewej stronie w obszarze **obraz**wybierz pozycję **Znajdź twarze**. 

   Zostanie otwarta strona **Wyszukiwanie** twarzy.

2. W obszarze **Otwórz konsolę testowania interfejsu API**wybierz region, który najlepiej opisuje lokalizację. 

   ![Wypróbuj obraz — wybierz region na stronie twarzy](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **wyszukiwania** twarzy.

3. Określ obraz do skanowania. Możesz przesłać obraz jako dane binarne danych binarnych lub określić publicznie dostępny adres URL do obrazu. Ten przykład łączy do obrazu, który jest używany w wątku CNN.

   ![Spróbuj użyć obrazu przykładowego obrazu](images/try-image-api-face-image.jpg)

   ![Spróbuj uzyskać przykładowe żądanie wyszukiwania obrazów](images/try-image-api-face-request.png)

4. Wybierz pozycję **Wyślij**. W tym przykładzie interfejs API znajduje dwie twarze i zwraca ich współrzędne w obrazie.

   ![Wypróbuj obraz — pole zawartości przykładowej odpowiedzi](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Wykrywanie tekstu za pomocą funkcji OCR

W celu wykrycia tekstu w obrazach można użyć funkcji Content Moderator OCR.

1. W [dokumentacji interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)w menu po lewej stronie w obszarze **obraz**wybierz pozycję **OCR**. 

   Zostanie otwarta strona **OCR** .

2. W obszarze **Otwórz konsolę testowania interfejsu API**wybierz region, który najlepiej opisuje lokalizację. 

   ![Obraz — wybór obszaru strony OCR](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **rozpoznawania obrazu** .

3. W polu **OCP-APIM-Subscription-Key** wprowadź swój klucz subskrypcji.

4. W polu **treść żądania** Użyj domyślnego obrazu przykładowego. Jest to ten sam obraz, który jest używany w poprzedniej sekcji.

5. Wybierz pozycję **Wyślij**. Wyodrębniony tekst jest wyświetlany w formacie JSON:

   ![Obraz — przykładowe pole zawartości odpowiedzi na OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub postępuj zgodnie z [przewodnikiem Szybki Start dla zestawu .NET SDK](dotnet-sdk-quickstart.md) , aby dodać moderowanie obrazów do aplikacji.
