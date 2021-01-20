---
title: Użyj edytora Video Indexer, aby tworzyć projekty i dodawać klipy wideo
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak za pomocą edytora Video Indexer utworzyć projekty i dodać klipy wideo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: b25341fb58c1e758d807e3c7b4345fd0ab1baa53
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610424"
---
# <a name="add-video-clips-to-your-projects"></a>Dodawanie klipów wideo do projektów

Witryna sieci Web [Video Indexer](https://www.videoindexer.ai/) umożliwia korzystanie z szczegółowego wglądu w dane wideo do: znajdowanie odpowiedniej zawartości multimedialnej, lokalizowanie interesujących Cię części i używanie wyników do tworzenia zupełnie nowego projektu. 

Po utworzeniu projekt może być renderowany i pobierany z Video Indexer i być używany we własnych aplikacjach do edycji lub podrzędnych przepływów pracy.

Niektóre scenariusze, w których może się okazać, że ta funkcja jest przydatna: 

* Tworzenie podświetlania filmów dla przyczep.
* Używanie starych klipów wideo w przypadku rzutowania wiadomości.
* Tworzenie krótszej zawartości dla mediów społecznościowych.

W tym artykule pokazano, jak utworzyć projekt i dodać wybrane klipy z filmów wideo do projektu. 

## <a name="create-new-project-and-manage-videos"></a>Tworzenie nowego projektu i zarządzanie filmami wideo

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Wybierz kartę **projekty** . Jeśli wcześniej utworzono projekty, zobaczysz wszystkie inne projekty w tym miejscu.
1. Kliknij pozycję **Utwórz nowy projekt**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Tworzenie nowego projektu":::
1. Nadaj projektowi nazwę, klikając ikonę ołówka. Zastąp tekst "niezatytułowany projekt" nazwą projektu i kliknij sprawdzanie.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Nowy projekt":::
    
### <a name="add-videos-to-the-project"></a>Dodawanie filmów wideo do projektu

> [!NOTE]
> Obecnie projekty mogą zawierać tylko wideo indeksowane w tym samym języku. </br>Po wybraniu wideo w jednym języku nie można dodać filmów wideo na koncie, które znajdują się w innym języku, wideo z innymi językami będą wyszarzone lub wyłączone.

1. Dodaj filmy wideo, z którymi chcesz korzystać w tym projekcie, wybierając pozycję **Dodaj wideo**.

    Zobaczysz wszystkie filmy wideo na Twoim koncie i pole wyszukiwania informujące o wyszukiwaniu tekstu, słów kluczowych lub zawartości wizualnej. W transkrypcji i OCR można wyszukiwać filmy wideo z określoną osobą, etykietą, marką, słowem kluczowym lub wystąpieniem.
    
    Na przykład na poniższym obrazie szukamy filmów wideo, które zawierają informacje "niestandardowa wizja" w transkrypcji tylko (Użyj **filtru** , jeśli chcesz filtrować wyniki wyszukiwania).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie filmów wideo, które zawierają informacje o niestandardowej wizji":::
1. Kliknij przycisk **Dodaj** , aby dodać filmy wideo do projektu.
1. Teraz zostaną wyświetlone wszystkie wybrane filmy wideo. Są to filmy wideo, z których zamierzasz wybierać klipy dla projektu.

    Możesz zmienić kolejność filmów wideo, przeciągając je i upuszczając lub wybierając przycisk menu listy i wybierając pozycję **Przenieś w dół** lub Przenieś w **górę**. Z menu Lista będzie można również usunąć wideo z tego projektu. 
    
    W dowolnym momencie możesz dodać więcej filmów wideo do tego projektu, wybierając pozycję **Dodaj wideo**. Możesz również dodać wiele wystąpień tego samego filmu wideo do projektu. Możesz to zrobić, jeśli chcesz pokazać klip z jednego filmu wideo, a następnie klip z innego, a następnie inny klip z pierwszego filmu wideo. 

### <a name="select-clips-to-use-in-your-project"></a>Wybierz klipy do użycia w projekcie

Jeśli klikniesz strzałkę w dół po prawej stronie każdego filmu wideo, będziesz otwierać szczegółowe informacje w filmie wideo na podstawie sygnatur czasowych (klipów wideo). 

1. Aby utworzyć zapytania dla określonych klipów, użyj pola wyszukiwania, które mówi "wyszukiwanie w transkrypcji, tekst wizualny, osoby i etykiety".
1. Wybierz pozycję **Wyświetl szczegółowe** dane, aby dostosować, które szczegółowe informacje mają być widoczne, i które nie mają być wyświetlane. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie filmów wideo, które mówią usługi poznawczej":::
1. Dodaj filtry, aby bardziej szczegółowo określić, które sceny szukają, wybierając **opcje filtra**.

    Można dodać wiele filtrów. 
1. Gdy będziesz zadowolony z wyników, wybierz klipy, które chcesz dodać do projektu, wybierając segment, który chcesz dodać. Możesz usunąć zaznaczenie tego klipu, klikając segment ponownie.
    
    Dodaj wszystkie segmenty filmu wideo (lub wszystkie zwrócone po przeszukiwaniu), klikając opcję menu Lista obok filmu wideo i wybierając **pozycję Zaznacz wszystko**. 

Po wybraniu i uporządkowaniu klipów możesz wyświetlić podgląd filmu wideo w odtwarzaczu po prawej stronie. 

> [!IMPORTANT]
> Pamiętaj, aby zapisać projekt po wprowadzeniu zmian, wybierając pozycję **Zapisz projekt** w górnej części strony. 

### <a name="render-and-download-the-project"></a>Renderowanie i pobieranie projektu

> [!NOTE]
> W przypadku Video Indexer płatnych kont renderowanie projektu ma koszty kodowania. Konta próbne Video Indexer są ograniczone do 5 godzin renderowania.

1. Po zakończeniu upewnij się, że projekt został zapisany. Teraz możesz renderować ten projekt. Kliknięcie przycisku **Renderuj** powoduje wyświetlenie okna dialogowego z informacją o tym, że indeksator wideo będzie renderować plik, a następnie link do pobierania zostanie wysłany do wiadomości e-mail. Wybierz pozycję Zastosuj. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Zrzut ekranu przedstawia Video Indexer z opcją renderowania i pobierania projektu":::
    
    Zobaczysz również powiadomienie, że projekt jest renderowany w górnej części strony. Po zakończeniu renderowania zobaczysz nowe powiadomienie, że projekt został pomyślnie renderowany. Kliknij powiadomienie, aby pobrać projekt. Spowoduje to pobranie projektu w formacie MP4.
1. Dostęp do zapisanych projektów można uzyskać z poziomu karty **Projects (projekty** ). 

    W przypadku wybrania tego projektu zobaczysz wszystkie szczegółowe informacje i oś czasu tego projektu. W przypadku wybrania **edytora wideo** można kontynuować wprowadzanie zmian do tego projektu. Edycje obejmują dodawanie lub usuwanie wideo oraz klipów lub zmianę nazwy projektu.
    
## <a name="create-a-project-from-your-video"></a>Tworzenie projektu na podstawie wideo

Nowy projekt można utworzyć bezpośrednio z poziomu filmu wideo na Twoim koncie. 

1. Przejdź do karty **Biblioteka** w witrynie sieci Web Video Indexer.
1. Otwórz film wideo, którego chcesz użyć do utworzenia projektu. Na stronie szczegółowe informacje i oś czasu wybierz przycisk **Edytor wideo** .

    Spowoduje to przejście do tej samej strony, która została użyta do utworzenia nowego projektu. W przeciwieństwie do nowego projektu, zobaczysz segmenty usługi Insights z informacjami o sygnaturach czasowych wideo, które wcześniej rozpoczęły edycję.

## <a name="see-also"></a>Zobacz też

[Omówienie usługi Video Indexer](video-indexer-overview.md)

