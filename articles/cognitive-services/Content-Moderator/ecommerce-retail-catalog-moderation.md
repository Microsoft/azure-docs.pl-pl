---
title: 'Samouczek: umiarkowane obrazy produktów handlu elektronicznego — Content Moderator'
titleSuffix: Azure Cognitive Services
description: W tym samouczku pokazano, jak skonfigurować aplikację do analizowania i klasyfikowania obrazów produktów za pomocą określonych etykiet (przy użyciu usługi Azure przetwarzanie obrazów i Custom Vision). Oznacz niepożądane obrazy do dalszej weryfikacji (przy użyciu usługi Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: e67a9b35291615b87edf918b4146ab8d77a0ae7f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065147"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Samouczek: umiarkowane obrazy produktów handlu elektronicznego za pomocą usługi Azure Content Moderator

W tym samouczku dowiesz się, jak korzystać z usługi Azure Cognitive Services, w tym Content Moderator, do klasyfikowania i umiarkowanego tworzenia obrazów produktów dla scenariusza handlu elektronicznego. Będziesz używać przetwarzanie obrazów i Custom Vision do stosowania tagów (etykiet) do obrazów, a następnie utworzysz przegląd zespołu, który łączy Content Moderator technologii opartych na uczeniu maszynowym z zespołami ds. recenzji, aby zapewnić inteligentny system moderowania.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Rejestracja w usłudze Content Moderator i tworzenie zespołu do przeprowadzania przeglądu.
> * Używanie interfejsu API obrazów usługi Content Moderator do skanowania materiału pod kątem potencjalnej zawartości erotycznej lub dla dorosłych.
> * Używanie usługi przetwarzania obrazów do skanowania pod kątem znanych osobistości (lub innych tagów wykrywanych przez interfejs API przetwarzania obrazów).
> * Używanie usługi Custom Vision do skanowania pod kątem flag, zabawek, długopisów (lub innych tagów niestandardowych).
> * Przekazywanie połączonych wyników skanowania do przeglądu z udziałem człowieka i ostatecznej decyzji.

Kompletny przykładowy kod jest dostępny w repozytorium [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) (Przykłady dotyczące moderowania katalogu handlu elektronicznego) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby zasubskrybować usługę Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](../cognitive-services-apis-create-account.md).
- Klucz subskrypcji usługi przetwarzania obrazów (instrukcje jak wyżej).
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).
- Zestaw obrazów dla każdej etykiety używanej przez klasyfikator usługi Custom Vision (w tym przypadku: zabawki, długopisy i flagi USA).

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Zapoznaj się z instrukcją [Try Content moderator w sieci Web](quick-start.md) przewodnik szybkiego startu, aby uzyskać instrukcje dotyczące rejestrowania się w celu uzyskania [narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i tworzenia zespołu przeglądu. Zwróć uwagę na wartość **Identyfikator zespołu** na stronie **Poświadczenia**.

## <a name="create-custom-moderation-tags"></a>Tworzenie niestandardowych tagów moderacji

Następnie Utwórz niestandardowe znaczniki w narzędziu do przeglądu (Zobacz artykuł [Tagi](./review-tool-user-guide/configure.md#tags) , jeśli potrzebujesz pomocy w przypadku tego procesu). W tym przypadku zostaną dodane następujące tagi: **celebrity** (osobistości), **USA**, **flag** (flaga), **toy** (zabawka) i **pen** (długopis). Nie wszystkie Tagi muszą być wykrywalne kategorie w przetwarzanie obrazów (na przykład **osobistości**); Możesz dodać własne niestandardowe znaczniki, o ile przeszkolesz klasyfikator Custom Vision, aby wykryć go później.

![Konfigurowanie tagów niestandardowych](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#**, a następnie wybierz pozycję **Aplikacja konsolowa (.NET Framework)**.
1. Nadaj aplikacji nazwę **EcommerceModeration**, a następnie wybierz przycisk **OK**.
1. Jeśli dodajesz ten projekt do istniejącego rozwiązania, wybierz ten projekt jako pojedynczy projekt startowy.

W tym samouczku przedstawiono kod, który jest centralny dla projektu, ale nie będzie on obejmować wszystkich wierszy kodu. Skopiuj całą zawartość pliku _Program.cs_ z przykładowego projektu ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) do pliku _Program.cs_ w swoim nowym projekcie. Następnie przejrzyj kolejne sekcje, aby poznać sposób działania tego projektu i dowiedzieć się, jak z niego korzystać.

## <a name="define-api-keys-and-endpoints"></a>Definiowanie kluczy i punktów końcowych interfejsu API

W tym samouczku są stosowane trzy usługi poznawcze: w związku z tym wymaga trzy odpowiednie klucze i punkty końcowe interfejsu API. Zwróć uwagę na następujące pola w klasie **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Należy zaktualizować `___Key` pola za pomocą wartości kluczy subskrypcji i należy zmienić `___Uri` pola na prawidłowe adresy URL punktów końcowych (później uzyskasz Custom Vision klucz i punkt końcowy). Te wartości można znaleźć na kartach **szybkiego startu** poszczególnych zasobów platformy Azure. W części `YOURTEAMID` pola `ReviewUri` wpisz identyfikator zespołu do przeprowadzania przeglądu, który utworzono wcześniej. Końcowa część pola zostanie wypełniona `CustomVisionUri` później.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Główne wywołania metod

Zwróć uwagę na następujący kod w metodzie **Main**, wykonujący pętlę na liście adresów URL obrazów. Analizuje każdy obraz przy użyciu trzech różnych usług, rejestruje zastosowane znaczniki w tablicy **ReviewTags** , a następnie tworzy przegląd dla moderatorów ludzkich, wysyłając obrazy do narzędzia do przeglądu Content Moderator. Te metody przedstawimy w kolejnych sekcjach. Jeśli chcesz, możesz kontrolować, które obrazy są wysyłane do przeglądu, używając tablicy **ReviewTags** w instrukcji warunkowej, aby sprawdzić, które Tagi zostały zastosowane.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metoda EvaluateAdultRacy

Zwróć uwagę na metodę **EvaluateAdultRacy** w klasie **Program**. Parametrami tej metody są adres URL obrazu i tablica par wartości kluczy. Wywołuje ona interfejs API REST wyszukiwania obrazów usługi Content Moderator w celu pobrania wyników analizy tego obrazu pod kątem treści erotycznych i dla dorosłych. Jeśli wynik dla elementu jest większy niż 0,4 (zakres jest z zakresu od 0 do 1), ustawia odpowiednią wartość w tablicy **ReviewTags** na **wartość true**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags, Metoda

Kolejna metoda przyjmuje adres URL obrazu i dane subskrypcji interfejsu API przetwarzania obrazów i analizuje obraz pod kątem obecności znanych osobistości. W przypadku znalezienia co najmniej jednej osobistości w tablicy **ReviewTags** zostaje ustawiona wartość **True** dla odpowiedniego parametru.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metoda EvaluateCustomVisionTags

Następnie zwróć uwagę na metodę **EvaluateCustomVisionTags**, która klasyfikuje poszczególne produkty &mdash; w tym przypadku flagi, zabawki i długopisy. Postępuj zgodnie z instrukcjami w przewodniku [jak utworzyć klasyfikator](../custom-vision-service/getting-started-build-a-classifier.md) , aby utworzyć własny klasyfikator obrazu niestandardowego i wykryć flagi, zabawki i pióra (lub dowolne wybrane jako Tagi niestandardowe) w obrazach. Możesz użyć obrazów w folderze **Sample images** [repozytorium GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) , aby szybko nauczyć niektóre z kategorii w tym przykładzie.

![Strona internetowa usługi Custom Vision z obrazami szkoleniowymi przedstawiającymi długopisy, zabawki i flagi](images/tutorial-ecommerce-custom-vision.PNG)

Po przeszkoleniu klasyfikatora Pobierz klucz predykcyjny i adres URL punktu końcowego przewidywania (zobacz [pobieranie adresu URL i klucza predykcyjnego](../custom-vision-service/use-prediction-api.md#get-the-url-and-prediction-key) , jeśli potrzebujesz pomocy przy pobieraniu ich) i przypisz te wartości odpowiednio do `CustomVisionKey` `CustomVisionUri` pól i. Te wartości są używane w tej metodzie do wysyłania zapytań do klasyfikatora. Jeśli klasyfikator znajdzie na obrazie zawartość odpowiadającą co najmniej jednemu z tych tagów niestandardowych, metoda ustawi wartość **True** dla odpowiednich parametrów w tablicy **ReviewTags**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Tworzenie przeglądów dla narzędzia do przeprowadzania przeglądu

W poprzednich sekcjach przedstawiono sposób, w jaki aplikacja skanuje obrazy przychodzące dla dorosłych i erotycznej zawartości (Content Moderator), osobistości (przetwarzanie obrazów) i różnych innych obiektów (Custom Vision). Następnie zapoznaj się z tematem **Metoda do przeglądu,** która przekazuje obrazy ze wszystkimi zastosowanymi tagami (przekazane jako _metadane_) do narzędzia do przeglądu Content Moderator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Te obrazy będą widoczne na karcie Review (Przegląd) [narzędzia do przeprowadzania przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Zrzut ekranu przedstawiający narzędzie do przeprowadzania przeglądu usługi Content Moderator wraz z kilkoma obrazami i wyróżnionymi tagami](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Przesyłanie listy obrazów testowych

Jak widać w metodzie **Main**, program szuka katalogu „C:Test” z plikiem _Urls.txt_, zawierającym listę adresów URL obrazów. Utwórz ten plik i katalog lub zmień ścieżkę, aby wskazywała plik tekstowy. Następnie wypełnij ten plik adresami URL obrazów, które chcesz przetestować.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Uruchamianie programu

Jeśli wykonano wszystkie powyższe kroki, program powinien przetwarzać każdy obraz (wykonując zapytania dotyczące wszystkich trzech usług pod kątem odpowiednich tagów), a następnie przekazać obrazy z informacjami o tagach do narzędzia do przeglądu Content Moderator.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz program do analizowania obrazów produktów, znakowania ich według typu produktu i umożliwienia zespołowi ds. przeglądowi podejmowania świadomych decyzji o moderowaniu zawartości. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Review moderated images (Przeprowadzanie przeglądu moderowanych obrazów)](./review-tool-user-guide/review-moderated-images.md)