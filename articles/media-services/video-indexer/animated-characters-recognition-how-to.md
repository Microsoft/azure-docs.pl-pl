---
title: Wykrywanie znaków animowanych Video Indexer z daniem
titleSuffix: Azure Media Services
description: W ten sposób pokazano, jak używać wykrywania znaków animowanych za pomocą Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531561"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Korzystanie z wykrywania znaków animowanych (wersja zapoznawcza) za pomocą portalu i interfejsu API 

Azure Media Services Video Indexer obsługuje wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości, ta funkcja jest dostępna za pośrednictwem interfejsu Azure Portal api. Zapoznaj się [z tym tematem z omówieniem.](animated-characters-recognition.md)

W tym artykule pokazano, jak używać wykrywania znaków animowanych za pomocą Azure Portal i Video Indexer API.

## <a name="use-the-animated-character-detection-with-portal"></a>Korzystanie z wykrywania znaków animowanych w portalu 

Na kontach w wersji próbnej Custom Vision zarządzana przez usługę Video Indexer, można rozpocząć tworzenie modelu znaków animowanych i używanie go. Jeśli korzystasz z konta w wersji próbnej, możesz pominąć następującą sekcję ("Łączenie Custom Vision konta").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Łączenie konta Custom Vision (tylko konta płatne)

Jeśli jesteś właścicielem Video Indexer płatnego konta, musisz najpierw Custom Vision konto. Jeśli nie masz jeszcze konta Custom Vision, utwórz je. Aby uzyskać więcej informacji, [zobacz Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Oba konta muszą być w tym samym regionie. Integracja Custom Vision nie jest obecnie obsługiwana w regionie Japonia.

Płatne konta, które mają dostęp do konta Custom Vision, mogą tam zobaczyć modele i otagowane obrazy. Dowiedz się więcej [o ulepszaniu klasyfikatora w Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). 

Należy pamiętać, że trenowanie modelu powinno odbywać się tylko za pośrednictwem Video Indexer, a nie za pośrednictwem Custom Vision internetowej. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Łączenie konta Custom Vision z interfejsem API 

Wykonaj następujące kroki, aby połączyć Custom Vision z Video Indexer lub zmienić konto Custom Vision, które jest aktualnie połączone z Video Indexer:

1. Przejdź do [strony www.customvision.ai](https://www.customvision.ai) i zaloguj się.
1. Skopiuj klucze dla zasobów trenowania i przewidywania:

    > [!NOTE]
    > Aby udostępnić wszystkie klucze, musisz mieć dwa oddzielne zasoby w Custom Vision, jeden do trenowania i jeden do przewidywania.
1. Podaj inne informacje:

    * Punkt końcowy 
    * Identyfikator zasobu przewidywania
1. Przejdź do witryny i zaloguj się [do Video Indexer](https://vi.microsoft.com/).
1. Kliknij znak zapytania w prawym górnym rogu strony i wybierz pozycję **Dokumentacja interfejsu API.**
1. Upewnij się, że subskrybujesz subskrypcję API Management klikając **kartę** Produkty. Jeśli masz połączony interfejs API, możesz przejść do następnego kroku, w przeciwnym razie zasubskrybuj. 
1. W portalu dla deweloperów kliknij pozycję Complete API Reference (Pełna dokumentacja **interfejsu API)** i przejdź do strony **Operations (Operacje).**  
1. Wybierz **pozycję Połącz Custom Vision konta (WERSJA ZAPOZNAWCZA)** i kliknij pozycję **Wypróbuj.**
1. Wypełnij wymagane pola, a także token dostępu, a następnie kliknij pozycję **Wyślij**. 

    Aby uzyskać więcej informacji na temat uzyskiwania tokenu [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)Video Indexer, przejdź do portalu dla deweloperów i zapoznaj się z odpowiednią [dokumentacją](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Gdy wywołanie zwróci odpowiedź 200 OK, Twoje konto zostanie połączone.
1. Aby sprawdzić połączenie, przejdź do [portalu Video Indexer](https://vi.microsoft.com/)):
1. Kliknij przycisk **Dostosowywanie modelu zawartości** w prawym górnym rogu.
1. Przejdź do karty **Animowane** znaki.
1. Po kliknięciu przycisku Zarządzaj modelami w Custom Vision zostaną przeniesione do Custom Vision właśnie połączonego konta.

> [!NOTE]
> Obecnie obsługiwane są tylko modele, które Video Indexer za pośrednictwem usługi . Modele tworzone za pomocą Custom Vision nie będą dostępne. Ponadto najlepszym rozwiązaniem jest edytowanie modeli utworzonych za pośrednictwem platformy Video Indexer tylko za pośrednictwem platformy Video Indexer, ponieważ zmiany wprowadzone za pośrednictwem Custom Vision mogą spowodować niezamierzone wyniki.

### <a name="create-an-animated-characters-model"></a>Tworzenie modelu znaków animowanych

1. Przejdź do witryny internetowej [Video Indexer](https://vi.microsoft.com/) i zaloguj się.
1. Aby dostosować model na koncie, wybierz przycisk **Dostosowywanie modelu zawartości** w lewej części strony.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Dostosowywanie modelu zawartości w Video Indexer":::
1. Przejdź do karty **Animowane znaki** w sekcji dostosowywania modelu.
1. Kliknij pozycję **Dodaj model.**
1. Nadaj modelowi nazwę i kliknij klawisz Enter, aby zapisać nazwę.

> [!NOTE]
> Najlepszym rozwiązaniem jest, aby mieć jeden model custom vision dla każdej animowanej serii. 

### <a name="index-a-video-with-an-animated-model"></a>Indeksowanie wideo za pomocą animowanego modelu

W przypadku początkowego szkolenia przekaż co najmniej dwa filmy wideo. Każdy z nich powinien być dłuższy niż 15 minut, zanim będzie można oczekiwać dobrego modelu rozpoznawania. Jeśli masz krótsze filmy wideo, zalecamy przekazanie co najmniej 30 minut zawartości wideo przed rozpoczęciem szkolenia. Umożliwi to scalanie grup należących do tego samego znaku z różnych scen i tła, a tym samym zwiększy prawdopodobieństwo wykrycia znaku w następujących indeksowanych przez Ciebie filmach. Aby wytszkolić model na wielu filmach wideo (filmach), musisz je wszystkie indeksować przy użyciu tego samego modelu animacji. 

1. Kliknij przycisk **Przekaż.**
1. Wybierz wideo do przekazania (z pliku lub adresu URL).
1. Kliknij pozycję **Opcje zaawansowane.**
1. W **obszarze People/Animated characters (Osoby/animowane znaki)** **wybierz pozycję Animation models (Modele animacji).**
1. Jeśli masz jeden model, zostanie on wybrany automatycznie, a jeśli masz wiele modeli, możesz wybrać odpowiedni model z menu rozwijanego.
1. Kliknij pozycję upload (Przekaż).
1. Po zindeksacji filmu wideo wykryte znaki zostaną wyświetlony w sekcji Znaki **animowane** w **okienku Szczegółowe** informacje.

Przed otagowaniem i wyt szkoleniem modelu wszystkie animowane znaki będą mieć nazwę "Unknown #X". Po wytębniu modelu zostaną one również rozpoznane.

### <a name="customize-the-animated-characters-models"></a>Dostosowywanie modeli znaków animowanych

1. Nazwij znaki w Video Indexer.

    1. Po utworzeniu modelu grupy znaków zaleca się przejrzenie tych grup w Custom Vision. 
    1. Aby otagować animowany znak w wideo, przejdź do karty **Szczegółowe** informacje i kliknij przycisk Edytuj w prawym górnym rogu    ****   okna. 
    1. W **okienku Szczegółowe** informacje kliknij dowolny z wykrytych animowanych znaków i zmień ich nazwy z "Unknown #X" na nazwę tymczasową (lub nazwę, która została wcześniej przypisana do   znaku). 
    1. Po wpisaniu nowej nazwy kliknij ikonę wyboru obok nowej nazwy. Dzięki temu nowa nazwa w modelu jest zapisywana w Video Indexer. 
1. Tylko płatne konta: przejrzyj grupy w Custom Vision 

    > [!NOTE]
    > Płatne konta, które mają dostęp do konta Custom Vision, mogą tam zobaczyć modele i otagowane obrazy. Dowiedz się więcej [o ulepszaniu klasyfikatora w Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). Należy pamiętać, że trenowanie modelu powinno odbywać się tylko za pośrednictwem usługi Video Indexer (zgodnie z opisem w tym identyfikatorze topid), a nie za pośrednictwem Custom Vision internetowej. 

    1. Przejdź do strony **Modele niestandardowe** w Video Indexer i wybierz **kartę Animowane** znaki. 
    1. Kliknij przycisk Edytuj dla modelu, nad który pracujesz, aby zarządzać nim w Custom Vision. 
    1. Przejrzyj każdą grupę znaków: 

        * Jeśli grupa zawiera niepowiązane obrazy, zaleca się ich usunięcie w witrynie Custom Vision internetowej. 
        * Jeśli istnieją obrazy należące do innego znaku, zmień tag na tych konkretnych obrazach, klikając obraz, dodając właściwy tag i usuwając nieprawidłowy tag. 
        * Jeśli grupa nie jest poprawna, co oznacza, że zawiera głównie obrazy nieokreślowe lub obrazy z wielu znaków, możesz je usunąć w witrynie Custom Vision internetowej lub w Video Indexer szczegółowych informacji. 
        * Algorytm grupowania czasami dzieli znaki na różne grupy. W związku z tym zaleca się nadaj wszystkim grupom, które należą do tego samego znaku, tę samą nazwę (w uwitrynie Video Indexer Insights), co natychmiast spowoduje, że wszystkie te grupy będą wyświetlane tak jak w Custom Vision internetowej. 
    1. Gdy grupa zostanie udoskonalona, upewnij się, że nazwa początkowa otagowana za pomocą tagu odzwierciedla znak w grupie. 
1. Trenowanie modelu 

    1. Po zakończeniu edytowania wszystkich potrzebnych nazw musisz wytszkolić model. 
    1. Gdy znak zostanie wytrenowany do modelu, zostanie rozpoznany jako kolejny film wideo poindeksowany przy użyciu tego modelu. 
    1. Otwórz stronę dostosowywania i kliknij kartę **Animowane znaki,** a następnie kliknij przycisk   Train (Trenuj), aby wytszkolić model. **** Aby zachować połączenie między filmami wideo 
    
Indeksator i model nie trenują modelu w witrynie internetowej Custom Vision (płatne konta mają dostęp do Custom Vision internetowej), tylko w Video Indexer. Po wytrenowanych wideo, które zostaną zindeksowane lub ponownie indeksowane przy użyciu tego modelu, rozpozna wytrenowane znaki. 

## <a name="delete-an-animated-character-and-the-model"></a>Usuwanie animowanego znaku i modelu

1. Usuwanie animowanego znaku.

    1. Aby usunąć animowany znak ze szczegółowych informacji o wideo, przejdź  do karty **Szczegółowe** informacje i kliknij przycisk Edytuj w prawym górnym rogu okna.
    1. Wybierz animowany znak, a następnie kliknij przycisk **Usuń** pod jego nazwą.

    > [!NOTE]
    > Spowoduje to usunięcie szczegółowych informacji z tego wideo, ale nie wpłynie na model.
1. Usuwanie modelu.

    1. Kliknij przycisk **Dostosowywanie modelu zawartości w** górnym menu i przejdź do karty Znaki **animowane.**
    1. Kliknij ikonę wielokropka po prawej stronie modelu, który chcesz usunąć, a następnie na przycisku usuwania.
    
    * Płatne konto: model zostanie odłączony od Video Indexer i nie będzie można go ponownie połączyć.
    * Konto w wersji próbnej: model zostanie również usunięty z wizji służbowej. 
    
        > [!NOTE]
        > Na koncie wersji próbnej masz tylko jeden model, z których można korzystać. Po jego usunięciu nie można trenować innych modeli.

## <a name="use-the-animated-character-detection-with-api"></a>Korzystanie z wykrywania znaków animowanych za pomocą interfejsu API 

1. Połącz Custom Vision konto.

    Jeśli jesteś właścicielem Video Indexer płatnego konta, musisz najpierw połączyć Custom Vision konto. <br/>
    Jeśli nie masz jeszcze konta Custom Vision, utwórz je. Aby uzyskać więcej informacji, [zobacz Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

    [Połącz swoje konto Custom Vision przy użyciu interfejsu API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. Tworzenie modelu znaków animowanych.

    Użyj [interfejsu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model) API tworzenia modelu animacji.
1. Indeksowanie lub ponowne indeksowanie wideo.

    Użyj [interfejsu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API ponownego indeksowania. 
1. Dostosowywanie modeli znaków animowanych.

    Użyj [interfejsu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model) API trenowania modelu animacji.

### <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

Zobacz animowane znaki w wygenerowanym pliku JSON.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Ograniczenia

* Obecnie możliwość "identyfikacji animacji" nie jest obsługiwana w East-Asia regionu.
* Znaki, które wydają się być małe lub bardzo daleko w filmie wideo, mogą nie być prawidłowo identyfikowane, jeśli jakość wideo jest niska.
* Zaleca się używanie modelu na zestaw znaków animowanych (na przykład na animowaną serię).

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)