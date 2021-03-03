---
title: 'Samouczek: nawigowanie po stronie głównej platformy Azure kontrolą i wyszukiwanie zasobu'
description: W tym samouczku opisano sposób korzystania z funkcji na stronie głównej usługi Azure kontrolą i wyszukiwania w wykazie.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677841"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Samouczek: nawigowanie po stronie głównej usługi Azure kontrolą (wersja zapoznawcza) i wyszukiwanie zasobu

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tym samouczku poznasz usługę Azure kontrolą, przechodząc do funkcji strony głównej, a następnie wyszukując zasób w katalogu.

Ten artykuł stanowi *część 2 serii samouczków z pięcioma częścią* , w której przedstawiono podstawowe informacje dotyczące zarządzania nadzorem danych w ramach obszaru danych przy użyciu usługi Azure kontrolą. Ten samouczek kompiluje pracę wykonaną w [części 1: skanowanie danych za pomocą usługi Azure kontrolą](tutorial-scan-data.md)

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> * Przejdź do strony głównej usługi Azure kontrolą.
> * Wyszukaj element zawartości.
> * Dodaj właściciela zasobu.

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletny [Samouczek: skanowanie danych za pomocą usługi Azure kontrolą](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Przejdź do strony głównej usługi Azure kontrolą

Poniższe kroki przeprowadzą Cię przez stronę główną usługi Azure kontrolą.

1. Przejdź do zasobu usługi Azure kontrolą w Azure Portal i wybierz pozycję **Otwórz kontrolą Studio**. Nastąpi automatyczne przekierowanie do strony głównej programu kontrolą Studio.

   W górnej części strony głównej wyświetlana jest nazwa katalogu i zbiór analiz katalogu. Uwzględniono liczbę źródeł, zasobów danych i terminów słownika. Podsumowując, można zobaczyć, że istnieje ponad 200 zasobów w sumie i 113 terminologii dotyczącej słownika. Ta liczba jest aktualizowana podczas skanowania organizacji i dodaje więcej terminów do usługi Azure kontrolą.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Zrzut ekranu przedstawiający stronę główną usługi Azure kontrolą.":::

1. Wybierz pozycję **Przeglądaj zasoby** , aby wyświetlić wszystkie zasoby.

## <a name="search-for-an-asset"></a>Wyszukaj element zawartości

Przed rozpoczęciem warto zapoznać się z informacjami na temat podstawowej terminologii:

* Element **zawartości**: pojedynczy obiekt w katalogu, który jest zwięzły i zawiera definicję każdej jednostki w obszarze danych. Przykłady jednostek obejmują tabelę SQL, raport Power BI i działanie fabryki danych.
  
* **Schemat**: znany również jako kolumna lub atrybut, schemat reprezentuje strukturę zasobu lub zestawu zasobów.

* **Zestaw zasobów**: pojedynczy obiekt w katalogu, który reprezentuje wiele obiektów fizycznych w magazynie. Obiekty te zwykle udostępniają wspólny schemat, a w większości przypadków konwencję nazewnictwa lub strukturę folderów. Na przykład format daty to *rrrr/mm/dd*. Aby uzyskać więcej informacji, zobacz [Omówienie zestawów zasobów](concept-resource-sets.md).

* **Typ zasobu**: grupowanie zasobów i zestawów zasobów w ramach nazwy logicznej, która zwykle jest mapowana na nazwę platformy danych.

Wykonaj następujące kroki, aby wyszukać element zawartości i oznaczyć siebie jako właściciela:

1. W polu **Wyszukaj katalog** na stronie głównej wprowadź nazwę grupy zasobów zawierającej daną pozycję danych (grupę zasobów utworzoną w poprzednim samouczku). Zostanie wyświetlona lista sugestii.

1. Wybierz pozycję **Wyświetl wyniki wyszukiwania**. Ponieważ wszystkie zasoby zaczynają się od nazwy grupy zasobów, wszystkie są wyświetlane w wynikach wyszukiwania. Poza tym samouczkiem można wyszukiwać określone nazwy zasobów, a nie grupy zasobów.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Zrzut ekranu przedstawiający listę sugestii wyszukiwania w katalogu.":::

1. Możesz użyć filtrów w lewym okienku nawigacji, aby filtrować według typu zasobu, klasyfikacji, kontaktu, etykiety i okresu słownika.

1. Aby wyszukać zestaw zasobów, zacznij wpisywać nazwę zestawu. Zostanie wyświetlona lista sugestii wyszukiwania z prawidłowymi słowami kluczowymi. Możesz również wyszukać nazwy bezwzględne, umieszczając je w cudzysłowie.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Wyszukiwanie zasobów słowa kluczowego kontrolą platformy Azure":::

## <a name="edit-an-asset"></a>Edytowanie elementu zawartości

1. Wybierz jeden z zasobów z wyników wyszukiwania. Następnie wybierz pozycję **Edytuj**

1. Na karcie **Przegląd** możesz dodać opis.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Zrzut ekranu przedstawiający pole Opis na karcie Przegląd.":::

1. Wybierz kartę **kontakty** . Obok pozycji **właściciele** w polu **Wybierz użytkownika lub grupę** Zacznij wpisywać firmowy adres e-mail.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Zrzut ekranu przedstawiający wypełnione pola.":::

    Zostanie automatycznie wyświetlona sugestia nazwy.

1. Obok pozycji **eksperci** w polu **Wybierz użytkownika lub grupę** wprowadź nazwę (na przykład nazwę menedżera), a następnie wybierz pozycję **Zapisz**.

    Pola Opis, nazwa właściciela i nazwa eksperta są teraz wypełnione.

## <a name="next-steps"></a>Następne kroki

Przed przejściem do następnego samouczka w tej serii Poświęć trochę czasu na samodzielne Eksplorowanie strony głównej usługi Azure kontrolą. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Przejdź do strony głównej usługi Azure kontrolą.
> * Wyszukaj element zawartości.
> * Dodaj właściciela zasobu.

Przejdź do następnego samouczka, aby dowiedzieć się, jak przeglądać zasoby w usłudze Azure kontrolą i odnajdywać element zawartości.

> [!div class="nextstepaction"]
> [Przeglądaj zasoby i wyświetlaj ich elementy powiązane](tutorial-browse-and-view-lineage.md)
