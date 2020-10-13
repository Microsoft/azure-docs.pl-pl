---
title: Dostosowywanie modelu języka za pomocą witryny sieci Web Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model języka za pomocą witryny sieci Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: f9eedba6c8aee5c8aab73e2ed598a4511b4c5982
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020460"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Dostosowywanie modelu języka za pomocą witryny sieci Web Video Indexer

Video Indexer umożliwia tworzenie niestandardowych modeli języka w celu dostosowania rozpoznawania mowy przez przekazywanie tekstu adaptacji, czyli tekstu z domeny, do którego słownika ma być dołączany aparat. Po przeprowadzeniu szkolenia modelu zostaną rozpoznane nowe wyrazy pojawiające się w tekście adaptacyjnym.

Aby zapoznać się z szczegółowym omówieniem i najlepszymi rozwiązaniami dotyczącymi niestandardowych modeli języków, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

Za pomocą witryny sieci Web Video Indexer można tworzyć i edytować niestandardowe modele języka na koncie, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu języka za pomocą interfejsów API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

1. Przejdź do witryny sieci Web [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Aby dostosować model na koncie, wybierz przycisk **Dostosowywanie modelu zawartości** w prawym górnym rogu strony.

   ![Dostosuj model zawartości w Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Wybierz kartę **Język** .

    Zostanie wyświetlona lista obsługiwanych języków.

    ![Lista modeli języków w Video Indexer](./media/customize-language-model/customize-language-model.png)

4. W obszarze język, który chcesz wybrać, wybierz pozycję **Dodaj model**.
5. Wpisz nazwę modelu języka i naciśnij klawisz ENTER.

    Ten krok powoduje utworzenie modelu i nadaje opcję przekazania plików tekstowych do modelu.

6. Aby dodać plik tekstowy, wybierz pozycję **Dodaj plik**. Zostanie otwarty Eksplorator plików.

7. Przejdź do i wybierz plik tekstowy. Można dodać wiele plików tekstowych do modelu języka.

    Możesz również dodać plik tekstowy, wybierając przycisk **...** po prawej stronie modelu języka i wybierając pozycję **Dodaj plik**.

8. Po zakończeniu przekazywania plików tekstowych wybierz opcję zielony **pociąg** .

    ![Model języka uczenia w Video Indexer](./media/customize-language-model/train-model.png)

Proces uczenia może potrwać kilka minut. Po zakończeniu szkolenia zobaczysz **przeszkolony** obok modelu. Możesz wyświetlić podgląd, pobrać i usunąć plik z modelu.

![Model przeszkolonego języka w Video Indexer](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Używanie modelu języka na nowym filmie wideo

Aby użyć modelu języka na nowym wideo, wykonaj jedną z następujących czynności:

* Wybierz przycisk **Przekaż** w górnej części strony.

    ![Video Indexer przycisku przekazywania](./media/customize-language-model/upload.png)

* Upuść plik audio lub wideo do okręgu lub Wyszukaj plik.

    ![Przekaż plik multimedialny Video Indexer](./media/customize-language-model/upload2.png)

Masz możliwość wybrania **języka źródłowego wideo**. Wybierz listę rozwijaną i wybierz model języka, który został utworzony na podstawie listy. Powinien on wypowiedzieć język modelu języka i nazwę nadaną w nawiasach.

Wybierz opcję **przekazywania** w dolnej części strony, a nowe wideo zostanie indeksowane przy użyciu modelu języka.

### <a name="using-a-language-model-to-reindex"></a>Używanie modelu języka do ponownego indeksowania

Aby użyć modelu języka do ponownego indeksowania wideo w kolekcji, przejdź do swoich **filmów wideo** na stronie głównej [Video Indexer](https://www.videoindexer.ai/) i umieść kursor nad nazwą filmu wideo, który chcesz ponownie zindeksować.

Zobaczysz opcje pozwalające edytować wideo, usunąć wideo i ponownie zindeksować wideo. Wybierz opcję ponownego indeksowania wideo.

![Ponowne indeksowanie za pomocą Video Indexer](./media/customize-language-model/reindex1.png)

Masz możliwość wybrania **języka źródła wideo** , aby ponownie zindeksować wideo. Wybierz listę rozwijaną i wybierz model języka, który został utworzony na podstawie listy. Powinien on wypowiedzieć język modelu języka i nazwę nadaną w nawiasach.

![Wybierz język źródłowy wideo — ponownie Indeksuj wideo za pomocą Video Indexer](./media/customize-language-model/reindex.png)

Wybierz przycisk **ponownego indeksowania** , a film wideo zostanie ponownie indeksem przy użyciu modelu języka.

## <a name="edit-a-language-model"></a>Edytowanie modelu języka

Można edytować model języka, zmieniając jego nazwę, dodając do niego pliki i usuwając z niego pliki.

W przypadku dodania lub usunięcia plików z modelu języka należy ponownie przeprowadzić uczenie modelu, wybierając opcję zielony **pociąg** .

### <a name="rename-the-language-model"></a>Zmień nazwę modelu języka

Nazwę modelu języka można zmienić, wybierając przycisk wielokropka (**...**) po prawej stronie modelu języka i wybierając polecenie **Zmień nazwę**.

Wpisz nową nazwę i naciśnij klawisz ENTER.

### <a name="add-files"></a>Dodaj pliki

Aby dodać plik tekstowy, wybierz pozycję **Dodaj plik**. Zostanie otwarty Eksplorator plików.

Przejdź do i wybierz plik tekstowy. Można dodać wiele plików tekstowych do modelu języka.

Możesz również dodać plik tekstowy, wybierając przycisk wielokropka (**...**) po prawej stronie modelu języka i wybierając polecenie **Dodaj plik**.

### <a name="delete-files"></a>Usuwanie plików

Aby usunąć plik z modelu języka, wybierz przycisk wielokropka (**...**) po prawej stronie pliku tekstowego, a następnie wybierz pozycję **Usuń**. Nowe okno pojawia się z informacją o tym, że nie można cofnąć operacji usuwania. Wybierz opcję **Usuń** w nowym oknie.

Ta akcja powoduje usunięcie pliku w całości z modelu języka.

## <a name="delete-a-language-model"></a>Usuwanie modelu języka

Aby usunąć model języka z konta, wybierz przycisk wielokropka (**...**) po prawej stronie modelu języka, a następnie wybierz pozycję **Usuń**.

Nowe okno pojawia się z informacją o tym, że nie można cofnąć operacji usuwania. Wybierz opcję **Usuń** w nowym oknie.

Ta akcja spowoduje całkowite usunięcie modelu języka z Twojego konta. Wszystkie filmy wideo, które były używane w modelu języka, będą zachować ten sam indeks do momentu ponownego indeksowania wideo. Jeśli ponownie indeksujesz wideo, możesz przypisać nowy model języka do wideo. W przeciwnym razie Video Indexer będzie używać domyślnego modelu do ponownego indeksowania wideo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Dostosowywanie modeli języków przez skorygowanie transkrypcji

Video Indexer obsługuje automatyczne dostosowywanie modeli języka na podstawie rzeczywistych poprawek, które użytkownicy wprowadzili do transkrypcji filmów wideo.

1. Aby wprowadzić poprawki do transkrypcji, Otwórz film wideo, który chcesz edytować z poziomu wideo na koncie. Wybierz kartę **oś czasu** .

    ![Dostosuj kartę oś czasu modelu języka — Video Indexer](./media/customize-language-model/timeline.png)

1. Wybierz ikonę ołówka, aby edytować transkrypcję transkrypcji.

    ![Dostosuj transkrypcję edycji modelu języka — Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer przechwytuje wszystkie wiersze, które są poprawione przez użytkownika w transkrypcji wideo, i automatycznie dodaje je do pliku tekstowego o nazwie "z edycji transkrypcji". Te zmiany są używane do ponownego uczenia określonego modelu języka, który został użyty do indeksowania tego wideo. 
    
    Uwzględniono również zmiany wprowadzone w osi czasu [widżetu](video-indexer-embed-widgets.md) .
    
    Jeśli nie określono modelu języka podczas indeksowania tego wideo, wszystkie modyfikacje tego wideo będą przechowywane w domyślnym modelu języka o nazwie "dostosowania konta" w wykrytym języku wideo.
    
    W przypadku dokonania wielokrotnych zmian w tym samym wierszu do zaktualizowania modelu języka zostanie użyta tylko Ostatnia wersja poprawionego wiersza.  
    
    > [!NOTE]
    > Do dostosowania są używane tylko poprawki tekstowe. Poprawki, które nie zawierają rzeczywistych wyrazów (na przykład znaki interpunkcyjne lub spacje) nie są uwzględniane.
    
1. Zobaczysz, że poprawki transkrypcji zostaną wyświetlone na karcie język na stronie dostosowywania modelu zawartości.

    ![Dostosuj model języka — Video Indexer](./media/customize-language-model/customize.png)

   Aby przyjrzeć się plikowi "from transkrypcji edycji" dla każdego z modeli języka, wybierz go, aby go otworzyć.

    ![Z edycji transkrypcji — Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)
