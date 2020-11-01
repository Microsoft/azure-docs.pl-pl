---
title: Moderowanie wideo za pomocą narzędzia do przeglądu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Używanie narzędzia do oceny wideo z obsługą maszyn oraz umiarkowanej nieodpowiedniej zawartości
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 392cc06c6e0bce7ec2304da61033fc508d940bbb
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143767"
---
# <a name="video-moderation-with-the-review-tool"></a>Moderowanie wideo za pomocą narzędzia do przeglądu

Za pomocą narzędzia do [łagodzenia filmów wideo](video-moderation-api.md) Content moderator i [narzędzi do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md) można moderować wideo i transkrypcje dla dorosłych (jawnej) i erotycznej (z sugestią), aby uzyskać najlepsze wyniki dla Twojej firmy.

## <a name="view-videos-under-review"></a>Wyświetl filmy wideo w obszarze przegląd

Na pulpicie nawigacyjnym wybierz dowolną z kolejek przeglądu w obszarze Typ zawartości wideo. Zostanie rozpoczęte przeglądanie i zostanie otwarta strona moderowanie zawartości wideo.

> [!div class="mx-imgBorder"]
> ![Widok szczegółowy moderowania wideo w narzędziu do przeglądu](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Licznik przeglądania

Użyj suwaka w prawym górnym rogu, aby ustawić liczbę przeglądów, które chcesz wyświetlić na stronie.

### <a name="view-type"></a>Typ widoku

Różne wpisy zawartości można przeglądać jako kafelki lub w widoku szczegółowym. Widok **szczegółowy** umożliwi wyświetlenie klatek kluczowych i innych informacji o wybranym filmie wideo. 

> [!NOTE]
> Zamiast umieszczania ramek w regularnych odstępach czasu usługa moderowania wideo identyfikuje i wyprowadza tylko potencjalnie kompletne (dobre) ramki. Ta funkcja umożliwia wydajne generowanie ramek na potrzeby analizy osób dorosłych i erotycznej na poziomie ramki.

Widok z podziałem na **fragmenty** będzie wyświetlał każde wideo jako pojedynczy kafelek. Wybierz przycisk Rozwiń nad ramką wideo, aby powiększyć ten film wideo i ukryć pozostałe.

### <a name="content-obscuring-effects"></a>Efekty przesłaniania zawartości

Użyj przełączników **Rozmycie wszystkie** i **czarne i białe,** aby ustawić te efekty przesłaniania zawartości. Są one domyślnie włączone. W widoku **z** rozdziałami można przełączać efekty osobno dla każdego wideo.

## <a name="check-video-details"></a>Sprawdź szczegóły wideo

W widoku **szczegółowym** w okienku po prawej stronie zostaną wyświetlone kilka kart, które zawierają szczegółowe informacje o filmie wideo.

* Wybierz kartę **notatki** , aby dodać niestandardowe notatki do filmów wideo.
* Wybierz kartę **transkrypcja** , aby wyświetlić listę filmów wideo &mdash; Usługa automatycznie wyodrębnia transkrypcję dowolnego mowy w filmie wideo. Po wybraniu sekcji tekstu odtwarzacz wideo przeskoczy do tej części filmu wideo.
* Wybierz kartę **meta data** , aby wyświetlić metadane plików wideo.
* Wybierz kartę **historia** , aby wyświetlić historię przeglądu, na przykład czas utworzenia i sposób modyfikacji.

> [!div class="mx-imgBorder"]
> ![Przycisk tagów zbiorczych moderowania wideo](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Zastosuj Tagi moderowania

Głównym zadaniem recenzowania wideo jest stosowanie lub usuwanie tagów moderowania w filmach wideo lub częściach wideo.

### <a name="bulk-tagging"></a>Znakowanie zbiorcze

Pasek narzędzi **tagów zbiorczych** pozwala dodawać Tagi do wielu wybranych filmów wideo jednocześnie. Wybierz co najmniej jeden film wideo, a następnie wybierz Tagi, które chcesz zastosować, a następnie kliknij przycisk **Prześlij** . 

> [!div class="mx-imgBorder"]
> ![Przycisk tagów zbiorczych moderowania wideo](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Tagowanie klatek kluczowych

Możesz również dodać tagi moderowania do określonych klatek kluczowych. Wybierz ramki z okienka kafelka klatki kluczowej, a następnie wybierz pozycję **Tagi klatek kluczowych +** , aby zastosować odpowiednie Tagi.

> [!NOTE]
> Jeśli usługa nie może wyodrębnić klatek kluczowych, w okienku kafelka klatki kluczowej **nie będą wyświetlane żadne ramki** , a opcja zaznaczania klatek kluczowych będzie wyszarzona. W takim przypadku można zastosować tylko tagi do wideo jako całości (za pomocą przycisku **Tagi wideo +** ).

> [!div class="mx-imgBorder"]
> ![Widok szczegółowy moderowania wideo w narzędziu do przeglądu](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Przeprowadzenie przeglądu

Przycisk **Wstrzymaj** u dołu okienka wideo umożliwia przeprowadzenie przeglądu, aby można było go pobrać i zakończyć później. Można to zrobić, aby zapoznać się z przeglądem wymagającym konsultacji od innego członka zespołu lub Menedżera, który jest obecnie niedostępny. 

Filmy wideo można wyświetlić, klikając przycisk **Wstrzymaj** w górnej części ekranu. Po prawej stronie zostanie wyświetlone okienko wstrzymania. W tym miejscu możesz wybrać wiele przeglądów do wstrzymania i zwolnić je z powrotem do kolejki lub ustawić ich czas wygaśnięcia. Po upływie skonfigurowanego czasu przeglądy wstrzymane zostaną opublikowane z powrotem do kolejki. Wybierz pozycję **Zapisz** , aby rozpocząć zliczanie w dół od aktualnie wybranego czasu wygaśnięcia.

> [!div class="mx-imgBorder"]
> ![Widok szczegółowy moderowania wideo w narzędziu do przeglądu](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Prześlij przegląd

Po zastosowaniu tagów wybierz przycisk **Prześlij** u dołu okienka wideo. Jeśli Otagowano wiele filmów wideo, możesz je przesłać w ramach jednego przeglądu lub jako oddzielne przeglądów.

## <a name="limbo-state"></a>Stan Limbo

Po przesłaniu przeglądu wideo jest przenoszone do stanu **Limbo** , który można wyświetlić, wybierając przycisk **Limbo** w górnej części ekranu. Filmy wideo pozostają w stanie Limbo przez wstępnie skonfigurowany czas (który można zmienić w menu u dołu) lub do czasu, aż zostaną ponownie zrecenzowane lub przesłane ręcznie.

Gdy filmy wideo wygasną od Limbo, ich przeglądy są oznaczane jako ukończone.

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do [przewodnika Szybki Start dotyczącego moderowania wideo](video-moderation-api.md).
- Dowiedz się, jak generować [Recenzje wideo](video-reviews-quickstart-dotnet.md) dla recenzentów z moderowanych danych wyjściowych.
- Dodawaj [przeglądy transkrypcji wideo](video-transcript-reviews-quickstart-dotnet.md) do Twoich recenzji wideo.
- Zapoznaj się z szczegółowym samouczkiem dotyczącym tworzenia [kompletnego rozwiązania do moderowania wideo](video-transcript-moderation-review-tutorial-dotnet.md).