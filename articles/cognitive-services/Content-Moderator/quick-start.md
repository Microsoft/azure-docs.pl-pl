---
title: 'Szybki Start: Wypróbuj Content Moderator w sieci Web'
titleSuffix: Azure Cognitive Services
description: Użyj narzędzia do przeglądu Content Moderator w trybie online, aby przetestować podstawowe funkcje Content Moderator bez konieczności pisania kodu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content moderator, moderowanie zawartości
ms.openlocfilehash: 025c8fcf98a31d7b3380ee2530428d08428493fb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596802"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Szybki Start: Wypróbuj Content Moderator w sieci Web

W tym przewodniku szybki start użyjesz narzędzia do przeglądu Content Moderator w trybie online, aby przetestować podstawowe funkcje Content Moderator bez konieczności pisania kodu. Jeśli chcesz szybciej zintegrować tę usługę z aplikacją moderowania zawartości, zobacz inne Przewodniki Szybki Start w sekcji [następne kroki](#next-steps) .

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarka sieci Web

## <a name="set-up-the-review-tool"></a>Konfigurowanie narzędzia do przeglądu

Narzędzie do przeglądu Content Moderator jest narzędziem opartym na sieci Web, które pozwala recenzentom ludzkim ułatwić korzystanie z usługi poznawczej w podejmowaniu decyzji. W tym przewodniku przedstawiono krótki proces konfigurowania narzędzia do przeglądu, dzięki któremu można zobaczyć, jak działa usługa Content Moderator. Przejdź do witryny [Narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i Utwórz konto.

![Strona główna Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Następnie utwórz zespół recenzji. W scenariuszu roboczym będzie to grupa osób, które ręcznie sprawdzają decyzje o moderowaniu usługi. Aby utworzyć zespół, należy wybrać **region**i podać **nazwę zespołu** i **Identyfikator zespołu**. Jeśli chcesz zaprosić współpracowników do zespołu, możesz to zrobić, wprowadzając tutaj ich adresy e-mail.

> [!NOTE]
> **Nazwa zespołu** jest przyjazną nazwą Twojego zespołu ds. recenzji. Jest to nazwa wyświetlana w Azure Portal. **Identyfikator zespołu** jest używany do identyfikowania zespołu recenzji programowo.

> [!div class="mx-imgBorder"]
> ![Zaproś członka zespołu](images/create-team.png)

Jeśli zdecydujesz się na szyfrowanie danych przy użyciu klucza zarządzanego przez klienta (CMK), zostanie wyświetlony monit o podanie **identyfikatora zasobu** dla zasobu Content moderator w warstwie cenowej E0. Zasób, który należy podać, musi być nowy. 

> [!div class="mx-imgBorder"]
> ![Zapraszanie członka zespołu za pomocą CMK](images/create-team-cmk.png)

Jeśli spróbujesz ponownie użyć zasobu Content Moderator, zobaczysz następujące ostrzeżenie: 

> [!div class="mx-imgBorder"]
> ![Niepowodzenie CMK](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Przekaż przykładową zawartość

Teraz wszystko jest gotowe do przekazania przykładowej zawartości. Wybierz pozycję **wypróbuj > obraz**, **spróbuj > tekst**lub **spróbuj > wideo**.

![Wypróbuj moderowanie obrazu lub tekstu](images/tryimagesortext.png)

Prześlij zawartość pod kątem moderowania. Wewnętrznie narzędzie do przeglądu wywoła interfejsy API moderowania do skanowania zawartości. Po zakończeniu skanowania zobaczysz komunikat informujący o tym, że wyniki czekają na przegląd.

![Pliki umiarkowane](images/submitted.png)

## <a name="review-moderation-tags"></a>Przejrzyj Tagi moderowania

Przejrzyj zastosowane znaczniki moderowania. Możesz zobaczyć, które Tagi zostały zastosowane do zawartości, i co to jest wynik w każdej kategorii. Zobacz tematy dotyczące moderowania [obrazów](image-moderation-api.md), [tekstu](text-moderation-api.md)i [wideo](video-moderation-api.md) , aby dowiedzieć się więcej na temat tego, co wskazuje różne Tagi zawartości.

![Przegląd wyników](images/reviewresults_text.png)

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
- Implementowanie moderowania obrazu. Użyj [konsoli interfejsu API](try-image-api.md) lub postępuj zgodnie z poniższą [biblioteką klienta](client-libraries.md) , aby skanować obrazy i wykrywać potencjalną zawartość dla dorosłych i erotycznej przy użyciu tagów, wyników pewności i innych wyodrębnionych informacji.
- Implementowanie moderowania tekstu. Użyj [konsoli interfejsu API](try-text-api.md) lub Użyj [przewodnika Szybki Start biblioteki klienta](client-libraries.md) , aby skanować zawartość tekstową w celu uzyskania potencjalnej wulgarności, niepotrzebnej do użycia maszynowo klasyfikacji tekstu (wersja zapoznawcza) i danych osobowych.
- Implementowanie moderowania wideo. Postępuj zgodnie z [przewodnikiem dotyczącym moderowania wideo dla języka C#](video-moderation-api.md) , aby skanować wideo i wykrywać potencjalną zawartość dla dorosłych i erotycznej. 
