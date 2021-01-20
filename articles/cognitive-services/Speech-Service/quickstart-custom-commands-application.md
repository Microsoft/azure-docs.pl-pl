---
title: 'Szybki Start: Tworzenie asystenta głosowego za pomocą poleceń niestandardowych'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz i testujesz podstawową aplikację poleceń niestandardowych przy użyciu programu Speech Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: 2cc37cc66c06dc9e6997c6a031a9c60ad050c01a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601761"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Tworzenie asystenta głosowego przy użyciu poleceń niestandardowych

W tym przewodniku szybki start utworzysz i testujesz podstawową aplikację poleceń niestandardowych przy użyciu programu Speech Studio. Będziesz również mieć dostęp do tej aplikacji z aplikacji klienckiej systemu Windows.

## <a name="region-availability"></a>Dostępność regionów
W tej chwili polecenia niestandardowe obsługują subskrypcje mowy utworzone w następujących regionach:
* Zachodnie stany USA
* Zachodnie stany USA 2
* East US
* Wschodnie stany USA 2
* Zachodnio-środkowe stany USA
* Europa Północna
* West Europe
* Azja Wschodnia
* Southeast Asia
* Indie Środkowe

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Utwórz zasób usługi Azure Speech w regionie, który obsługuje polecenia niestandardowe.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Listę obsługiwanych regionów znajduje się w powyższej sekcji dotyczącej **dostępności regionów** .
> * Pobierz przykładowy plik JSON dla przykładowego [pokoju inteligentnego](https://aka.ms/speech/cc-quickstart) .
> * Pobierz najnowszą wersję [klienta asystenta głosowego systemu Windows](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Przejdź do programu Speech Studio dla poleceń niestandardowych

1. W przeglądarce internetowej przejdź do pozycji [Speech Studio](https://speech.microsoft.com/).
1. Wprowadź swoje poświadczenia, aby zalogować się do portalu.

   Widok domyślny to lista subskrypcji mowy.
   > [!NOTE]
   > Jeśli nie widzisz strony Wybieranie subskrypcji, możesz tam przejść przez wybranie opcji "zasoby mowy" z menu Ustawienia na górnym pasku.

1. Wybierz swoją subskrypcję mowy, a następnie wybierz pozycję **Przejdź do Studio**.
1. Wybierz **polecenia niestandardowe**.

   Widok domyślny to lista aplikacji poleceń niestandardowych, które znajdują się w ramach wybranej subskrypcji.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Importowanie istniejącej aplikacji jako nowego projektu poleceń niestandardowych

1. Wybierz pozycję **Nowy projekt** , aby utworzyć projekt.

1. W polu **Nazwa** wprowadź nazwę projektu jako `Smart-Room-Lite` (lub coś innego do wyboru).
1. Na liście **Język** wybierz pozycję **angielski (Stany Zjednoczone)**.
1. Wybierz pozycję **Przeglądaj pliki** i w oknie przeglądania wybierz **SmartRoomLite.jsw** pliku.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie projektu](media/custom-commands/import-project.png)

1.  Z listy **zasobów tworzenie Luis** wybierz zasób tworzenia. Jeśli nie ma prawidłowych zasobów tworzenia, utwórz je, wybierając pozycję  **Utwórz nowy zasób tworzenia Luis**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie zasobu](media/custom-commands/create-new-luis-resource.png)
    
    
    1. W polu **nazwa zasobu** wprowadź nazwę zasobu.
    1. Z listy **Grupa zasobów** wybierz grupę zasobów.
    1. Z listy **Lokalizacja** wybierz lokalizację.
    1. Na liście **warstwa cenowa** wybierz warstwę.
    
    
    > [!NOTE]
    > Grupy zasobów można utworzyć, wprowadzając odpowiednią nazwę grupy zasobów w polu "Grupa zasobów". Grupa zasobów zostanie utworzona, gdy zostanie wybrana wartość **Utwórz** .


1. Następnie wybierz pozycję **Utwórz** , aby utworzyć projekt.
1. Po utworzeniu projektu wybierz projekt.
Teraz powinien pojawić się przegląd nowej aplikacji poleceń niestandardowych.

## <a name="try-out-some-voice-commands"></a>Wypróbuj niektóre polecenia głosowe
1. Wybierz pozycję **uczenie** w górnej części okienka po prawej stronie.
1. Po zakończeniu szkolenia wybierz pozycję **Testuj** i wypróbuj następujące wyrażenia długości:
    - Włączanie telewizora
    - Ustaw temperaturę na 80 stopni
    - Wyłącz
    - Telewizja
    - Ustaw alarm 5 PM

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrowanie aplikacji poleceń niestandardowych w Asystencie
Aby można było uzyskać dostęp do tej aplikacji z programu Speech Studio, należy opublikować aplikację. W przypadku publikowania aplikacji należy skonfigurować zasób usługi przewidywania LUIS.  

### <a name="update-prediction-luis-resource"></a>Aktualizuj zasób LUIS predykcyjny


1. W lewym okienku wybierz pozycję **Ustawienia** , a następnie wybierz pozycję  **Luis zasoby** w środkowym okienku.
1. Wybierz zasób predykcyjny lub utwórz go, wybierając pozycję **Utwórz nowy zasób**.
1. Wybierz pozycję **Zapisz**.
    
    > [!div class="mx-imgBorder"]
    > ![Ustawianie zasobów LUIS](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Ponieważ zasób tworzenia obsługuje tylko żądania punktów końcowych przewidywania 1 000 miesięcznie, mandatorily będzie trzeba ustawić zasób przewidywania LUIS przed opublikowaniem aplikacji poleceń niestandardowych.

### <a name="publish-the-application"></a>Publikowanie aplikacji

Wybierz pozycję  **Publikuj** w górnej części okienka po prawej stronie. Po zakończeniu publikowania zostanie wyświetlone nowe okno. Zanotuj w dół wartość **Identyfikator aplikacji** i **klucz zasobu mowy** z tego klucza. Te dwie wartości będą potrzebne do uzyskania dostępu do aplikacji spoza programu Speech Studio.

Alternatywnie można również uzyskać te wartości, wybierając pozycję **Ustawienia**  >  **Ogólne** .

### <a name="access-application-from-client"></a>Dostęp do aplikacji z klienta

W zakresie tego artykułu będziemy używać klienta asystenta głosowego systemu Windows pobranego w ramach wymagań wstępnych. Rozpakuj folder.
1. Uruchom **VoiceAssistantClient.exe**.
1. Utwórz nowy profil publikowania i wprowadź wartość dla **profilu połączenia**. W sekcji **Ustawienia ogólne** wprowadź **klucz subskrypcji** wartości (jest to taka sama jak wartość **klucza zasobu mowy** zapisana podczas publikowania aplikacji), **region klucza subskrypcji** i **Identyfikator aplikacji poleceń niestandardowych**.
    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający sekcję ustawień ogólnych służącą do tworzenia profilu WVAC.](media/custom-commands/create-profile.png)
1. Wybierz pozycję **Zapisz i Zastosuj profil**.
1. Teraz wypróbuj następujące dane wejściowe za pośrednictwem mowy/tekstu
    > [!div class="mx-imgBorder"]
    > ![WVAC Utwórz profil](media/custom-commands/conversation.png)


> [!TIP]
> Możesz kliknąć pozycję wpisy w **dzienniku aktywności** , aby sprawdzić nieprzetworzone odpowiedzi wysyłane z usługi poleceń niestandardowych.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto istniejącej aplikacji. Następnie w [sekcjach jak](how-to-custom-commands-create-application-with-simple-commands.md)można dowiedzieć się, jak projektować, opracowywać, debugować, testować i integrować aplikację poleceń niestandardowych od podstaw.
