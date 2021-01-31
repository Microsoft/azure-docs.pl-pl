---
title: 'Szybki Start: Wypróbuj Content Moderator w sieci Web'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz narzędzia do przeglądu Content Moderator w trybie online, aby przetestować podstawowe funkcje Content Moderator bez konieczności pisania kodu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content moderator, moderowanie zawartości
ms.openlocfilehash: f8ad9c135fea4e582e6ba47764d0401936f8c295
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221180"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Szybki Start: Wypróbuj Content Moderator w sieci Web

W tym przewodniku szybki start użyjesz narzędzia do przeglądu Content Moderator w trybie online, aby przetestować podstawowe funkcje Content Moderator bez konieczności pisania kodu. Jeśli chcesz szybciej zintegrować tę usługę z aplikacją moderowania zawartości, zobacz inne Przewodniki Szybki Start w sekcji [następne kroki](#next-steps) .

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarka sieci Web

## <a name="set-up-the-review-tool"></a>Konfigurowanie narzędzia do przeglądu
Narzędzie do przeglądu Content Moderator jest narzędziem opartym na sieci Web, które pozwala recenzentom ludzkim ułatwić korzystanie z usługi poznawczej w podejmowaniu decyzji. W tym przewodniku przedstawiono krótki proces konfigurowania narzędzia do przeglądu, dzięki któremu można zobaczyć, jak działa usługa Content Moderator. Przejdź do witryny [Narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i Utwórz konto.

![Strona główna Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Następnie utwórz zespół recenzji. W scenariuszu roboczym ten zespół będzie grupą osób, które ręcznie sprawdzają decyzje o moderowaniu usługi. Aby utworzyć zespół, należy wybrać **region** i podać **nazwę zespołu** i **Identyfikator zespołu**. Jeśli chcesz zaprosić współpracowników do zespołu, możesz to zrobić, wprowadzając tutaj ich adresy e-mail.

> [!NOTE]
> **Nazwa zespołu** jest przyjazną nazwą Twojego zespołu ds. recenzji. Jest to nazwa wyświetlana w Azure Portal. **Identyfikator zespołu** jest używany do identyfikowania zespołu recenzji programowo.

> [!div class="mx-imgBorder"]
> ![Zaproś członka zespołu](images/create-team.png)

Jeśli zdecydujesz się na szyfrowanie danych przy użyciu klucza zarządzanego przez klienta (CMK), zostanie wyświetlony monit o podanie **identyfikatora zasobu** dla zasobu Content moderator w warstwie cenowej E0. Wymagany zasób musi być unikatowy dla tego zespołu. 

> [!div class="mx-imgBorder"]
> ![Zapraszanie członka zespołu za pomocą CMK](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Przekaż przykładową zawartość

Teraz wszystko jest gotowe do przekazania przykładowej zawartości. Wybierz pozycję **wypróbuj > obraz**, **spróbuj > tekst** lub **spróbuj > wideo**.

> [!div class="mx-imgBorder"]
> ![Wypróbuj moderowanie obrazu lub tekstu](images/tryimagesortext.png)

Prześlij zawartość pod kątem moderowania. Możesz użyć następującej przykładowej zawartości tekstowej:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Wewnętrznie narzędzie do przeglądu wywoła interfejsy API moderowania do skanowania zawartości. Po zakończeniu skanowania zobaczysz komunikat informujący o tym, że wyniki czekają na przegląd.

> [!div class="mx-imgBorder"]
> ![Pliki umiarkowane](images/submitted.png)

## <a name="review-moderation-tags"></a>Przejrzyj Tagi moderowania

Przejrzyj zastosowane znaczniki moderowania. Możesz zobaczyć, które Tagi zostały zastosowane do zawartości, i co to jest wynik w każdej kategorii. Zapoznaj się z artykułami dotyczącymi moderowania [obrazów](image-moderation-api.md), [tekstu](text-moderation-api.md)i [wideo](video-moderation-api.md) , aby dowiedzieć się więcej na temat tego, co wskazuje różne Tagi zawartości.

<!-- ![Review results](images/reviewresults_text.png) -->

W projekcie ty lub Twój zespół recenzji może zmienić te znaczniki lub dodać więcej tagów w razie potrzeb. Te zmiany zostaną przesłane przy użyciu przycisku **dalej** . Ponieważ aplikacja biznesowa wywołuje interfejsy API moderatora, otagowana zawartość będzie w tym miejscu gotowa do przejrzenia przez zespoły przeglądów ludzkich. Możesz szybko przejrzeć duże ilości zawartości przy użyciu tego podejścia.

W tym momencie użyto narzędzia do przeglądu Content Moderator, aby zobaczyć przykłady możliwości usługi Content Moderator. Następnie można dowiedzieć się więcej na temat narzędzia do przeglądu i sposobu integrowania go z projektem oprogramowania przy użyciu interfejsów API przeglądu lub można przejść do sekcji [następne kroki](#next-steps) , aby dowiedzieć się, jak używać interfejsów API moderowania w aplikacji.

## <a name="learn-more-about-the-review-tool"></a>Dowiedz się więcej o narzędziu do przeglądu

Aby dowiedzieć się więcej na temat korzystania z narzędzia do przeglądu Content Moderator, zapoznaj się z przewodnikiem po [narzędziu](Review-Tool-User-Guide/human-in-the-loop.md) do przeglądu i zobacz Interfejsy API narzędzia do przeglądu, aby dowiedzieć się, jak dostosować środowisko przeglądu ludzkiego:
- [Interfejs API zadań](try-review-api-job.md) skanuje zawartość przy użyciu interfejsów API moderowania i generuje przeglądy w narzędziu do przeglądu. 
- [Interfejs API przeglądu](try-review-api-review.md) bezpośrednio tworzy obrazy, tekst lub Recenzje wideo dla moderatorów ludzkich bez wcześniejszego skanowania zawartości. 
- [Interfejs API przepływu pracy](try-review-api-workflow.md) tworzy, aktualizuje i pobiera szczegóły dotyczące niestandardowych przepływów pracy tworzonych przez zespół.

Lub przejdź do następnych kroków, aby rozpocząć korzystanie z interfejsów API moderowania w kodzie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać interfejsów API moderowania w aplikacji.
- Implementowanie moderowania obrazu. Użyj [konsoli interfejsu API](try-image-api.md) lub postępuj zgodnie z [przewodnikiem Szybki Start](client-libraries.md) , aby skanować obrazy i wykrywać potencjalną zawartość dla dorosłych i erotycznej przy użyciu tagów, wyników pewności i innych wyodrębnionych informacji.
- Implementowanie moderowania tekstu. Użyj [konsoli interfejsu API](try-text-api.md) lub postępuj zgodnie z [przewodnikiem Szybki Start](client-libraries.md) , aby skanować zawartość tekstową w celu uzyskania potencjalnej niepotrzebnej zawartości, danych osobowych i innego niepożądanego tekstu
- Implementowanie moderowania wideo. Postępuj zgodnie z [przewodnikiem dotyczącym moderowania wideo dla języka C#](video-moderation-api.md) , aby skanować wideo i wykrywać potencjalną zawartość dla dorosłych i erotycznej. 
