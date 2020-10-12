---
title: Tworzenie projektów za pomocą edytora Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie przedstawiono sposób użycia edytora Video Indexer do tworzenia projektów.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 5eaf17281fef32d682a2dac17b379faf7ec23a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970090"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Tworzenie projektów za pomocą edytora Video Indexer

Video Indexer witryna sieci Web umożliwia korzystanie z szczegółowego wglądu w dane wideo do: znajdowanie odpowiedniej zawartości multimedialnej, lokalizowanie interesujących Cię części i użycie wyników do utworzenia zupełnie nowego projektu. Po utworzeniu projekt może być renderowany i pobierany z Video Indexer i być używany we własnych aplikacjach do edycji lub podrzędnych przepływów pracy.

Niektóre scenariusze, w których może się okazać, że ta funkcja jest przydatna: 

* Tworzenie podświetlania filmów dla przyczep.
* Używanie starych klipów wideo w przypadku rzutowania wiadomości.
* Tworzenie krótszej zawartości dla mediów społecznościowych.

W tym artykule pokazano, jak utworzyć projekt od podstaw, a także jak utworzyć projekt na podstawie wideo na Twoim koncie.

## <a name="create-new-project-and-manage-videos"></a>Tworzenie nowego projektu i zarządzanie filmami wideo

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Wybierz kartę **projekty** . Jeśli wcześniej utworzono projekty, zobaczysz wszystkie inne projekty w tym miejscu.
1. Kliknij pozycję **Utwórz nowy projekt**.  

    ![Nowy projekt](./media/video-indexer-view-edit/new-project.png)
1. Nadaj projektowi nazwę, klikając ikonę ołówka. Zastąp tekst "niezatytułowany projekt" nazwą projektu i kliknij sprawdzanie.

    ![Nowy projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Dodawanie filmów wideo do projektu

> [!NOTE]
> Obecnie projekty mogą zawierać tylko wideo indeksowane w tym samym języku. Po wybraniu filmu wideo w jednym języku nie można dodać do niego filmów wideo, które znajdują się w innym języku.

1. Dodaj filmy wideo, z którymi chcesz korzystać w tym projekcie, wybierając pozycję **Dodaj wideo**.

    Zobaczysz wszystkie filmy wideo na Twoim koncie i pole wyszukiwania informujące o wyszukiwaniu tekstu, słów kluczowych lub zawartości wizualnej. Aby wyszukać filmy wideo z określoną osobą, etykietą, marką, słowem kluczowym lub wystąpieniem w transkrypcji i OCR.
    
    Na przykład na poniższym obrazie szukamy filmów wideo, które zawierają informacje o witrynie GitHub.
    
    ![Zrzut ekranu przedstawia wyszukiwanie filmów wideo zawierających dwie wyniki.](./media/video-indexer-view-edit/github.png)

    Możesz bardziej filtrować wyniki, wybierając pozycję **Filtruj wyniki**. Można filtrować, aby pokazać wideo, które mają określoną osobę lub określić, że mają być widoczne tylko wyniki wideo, które są w określonym języku lub mają określonego właściciela. <br/> Możesz również określić zakres zapytania. Jeśli na przykład chcesz wyszukać ciąg "GitHub" w OCR, wybierz pozycję **tekst wizualny**.

    ![Filtrowanie](./media/video-indexer-view-edit/visual-text.png)

    Do zapytania można przydzielić wiele filtrów. Użyj **+** / **-** przycisków, aby dodać/usunąć filtry. Użyj **Wyczyść filtry** , aby usunąć wszystkie filtry.
1. Aby dodać filmy wideo, zaznacz je, a następnie wybierz pozycję **Dodaj**.
1. Teraz zostaną wyświetlone wszystkie wybrane filmy wideo. Są to filmy wideo, z których zamierzasz wybierać klipy dla projektu.

    Możesz zmienić kolejność filmów wideo, przeciągając je i upuszczając lub wybierając przycisk menu listy i wybierając pozycję **Przenieś w dół** lub Przenieś w **górę**. Z menu Lista będzie można również usunąć wideo z tego projektu. 

    ![Zrzut ekranu przedstawia Video Indexer z menu kontekstowym jednego z filmów wideo do usunięcia, czyszczenia zaznaczenia lub przenoszenia w dół.](./media/video-indexer-view-edit/rearrange.png)
    
    Możesz w dowolnym momencie dodać więcej filmów wideo do tego projektu, wybierając pozycję **Dodaj wideo**. Możesz również dodać wiele wystąpień tego samego filmu wideo do projektu. Możesz to zrobić, jeśli chcesz pokazać klip z jednego filmu wideo, a następnie klip z innego, a następnie inny klip z pierwszego filmu wideo. 

### <a name="select-clips-to-use-in-your-project"></a>Wybierz klipy do użycia w projekcie

Jeśli klikniesz strzałkę w dół po prawej stronie każdego filmu wideo, będziesz otwierać szczegółowe informacje w filmie wideo na podstawie sygnatur czasowych (klipów wideo). 

1. Wybierz pozycję **Wyświetl szczegółowe** dane, aby dostosować, które szczegółowe informacje mają być widoczne, i które nie mają być wyświetlane. 

    ![Wyświetl szczegółowe informacje](./media/video-indexer-view-edit/insights.png)
1. Aby utworzyć zapytania dla określonych klipów, użyj pola wyszukiwania, które mówi "wyszukiwanie w transkrypcji, tekst wizualny, osoby i etykiety".
1. Dodaj filtry, aby bardziej szczegółowo określić, które sceny szukają, wybierając **opcje filtra**.

    ![Opcje filtru](./media/video-indexer-view-edit/filter-options.png)

    Na przykład możesz chcieć zobaczyć klipy, w których znajduje się usługa GitHub, gdy na ekranie jest Donovan brązowy. W tym celu należy dodać filtr "include", który ma "osoby" jako typ szczegółowych informacji. Następnie musisz wpisać ciąg "Donovan Brown" w polu wyszukiwania filtru.
    
    ![Zrzut ekranu przedstawia Video Indexer z osobami wybranymi dla filtru dołączania.](./media/video-indexer-view-edit/include.png)
    
    Jeśli chcesz, aby klipy, w których znajduje się usługa GitHub, gdy Donovan Brown _nie_ znajduje się na ekranie, wystarczy zmienić filtr "include" na filtr "exclude" przy użyciu listy rozwijanej. 

1. Dodaj klip do projektu, wybierając segment, który chcesz dodać. Możesz usunąć zaznaczenie tego klipu, klikając segment ponownie.
    
    Dodaj wszystkie segmenty filmu wideo, klikając opcję menu Lista obok filmu wideo i wybierając **pozycję Wybierz wszystkie segmenty**. 

    ![Dodaj wszystko](./media/video-indexer-view-edit/add-all.png)

    Wszystkie wybrane opcje można wyczyścić, zaznaczając pole wyboru Wyczyść zaznaczenie.

> [!TIP]
> Po wybraniu i uporządkowaniu klipów możesz wyświetlić podgląd filmu wideo w odtwarzaczu po prawej stronie. 

![Zrzut ekranu przedstawia Video Indexer z podglądem filmu wideo po prawej stronie okna.](./media/video-indexer-view-edit/preview.png)

Pamiętaj, aby zapisać projekt po wprowadzeniu zmian, wybierając pozycję **Zapisz projekt**. 

### <a name="render-and-download-the-project"></a>Renderowanie i pobieranie projektu

> [!NOTE]
> W przypadku Video Indexer płatnych kont renderowanie projektu ma koszty kodowania. Konta próbne Video Indexer są ograniczone do 5 godzin renderowania.

1. Po zakończeniu upewnij się, że projekt został zapisany. Teraz możesz renderować ten projekt. Wybierz opcję **Renderuj i Pobierz**. 

    ![Zrzut ekranu przedstawia Video Indexer z opcją renderowania i pobierania projektu.](./media/video-indexer-view-edit/save.png)

    Zostanie wyświetlony podręczny komunikat informujący o tym, że indeksator wideo będzie renderować plik, a następnie link do pobierania zostanie wysłany do wiadomości e-mail. Wybierz pozycję Zastosuj. 
    
    Zobaczysz również powiadomienie, że projekt jest renderowany w górnej części strony. Po zakończeniu renderowania zobaczysz nowe powiadomienie, że projekt został pomyślnie renderowany. Kliknij powiadomienie, aby pobrać projekt. Spowoduje to pobranie projektu w formacie MP4.

    ![Renderowanie zakończone](./media/video-indexer-view-edit/rendering-done.png)

1. Dostęp do zapisanych projektów można uzyskać z poziomu karty **Projects (projekty** ). 

    W przypadku wybrania tego projektu zobaczysz wszystkie szczegółowe informacje i oś czasu tego projektu. W przypadku wybrania **edytora wideo**można kontynuować wprowadzanie zmian do tego projektu. Edycje obejmują dodawanie lub usuwanie wideo oraz klipów lub zmianę nazwy projektu.

    ![Edytor wideo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Tworzenie projektu na podstawie wideo

Nowy projekt można utworzyć bezpośrednio z poziomu filmu wideo na Twoim koncie. 

1. Przejdź do karty **Biblioteka** w witrynie sieci Web Video Indexer.
1. Otwórz film wideo, którego chcesz użyć do utworzenia projektu. Na stronie szczegółowe informacje i oś czasu wybierz przycisk **Edytor wideo** .

    Spowoduje to przejście do tej samej strony, która została użyta do utworzenia nowego projektu. W przeciwieństwie do nowego projektu, zobaczysz segmenty usługi Insights z informacjami o sygnaturach czasowych wideo, które wcześniej rozpoczęły edycję.

## <a name="see-also"></a>Zobacz też

[Omówienie usługi Video Indexer](video-indexer-overview.md)

