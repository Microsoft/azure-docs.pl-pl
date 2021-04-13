---
title: Właściwości marketingowe
description: Opis różnych pól marketingowych zebranych w portalu i sposobu ich wyświetlania w wykazie certyfikowanych urządzeń platformy Azure
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 262616ca82e9c06baec0d7a1b81a0e3dff2ed8db
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304515"
---
# <a name="marketing-properties"></a>Właściwości marketingowe

W procesie [dodawania szczegółów urządzenia](tutorial-02-adding-device-details.md)konieczne będzie podanie informacji marketingowych, które będą wyświetlane w [katalogu certyfikowanych urządzeń platformy Azure](https://devicecatalog.azure.com). Te informacje są zbierane w portalu urządzenia z certyfikatem platformy Azure podczas procesu dostarczania certyfikacji i będą używane jako parametry filtru w wykazie. Ten artykuł zawiera mapowanie między polami zebranymi w portalu a sposobem ich wyświetlania w wykazie. Po przeczytaniu tego artykułu partnerzy powinni lepiej zrozumieć, jakie informacje należy podać podczas procesu certyfikacji, aby najlepiej przedstawić swój produkt w katalogu.

![Omówienie narzędzia PDP](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Kafelek produktów katalogu certyfikowanych urządzeń platformy Azure

Osoby odwiedzające do wykazu będą najpierw korzystać z Twojego urządzenia jako kafelka produktu katalogu na stronie wyszukiwania. Zapewni to podstawowe Omówienie urządzenia i certyfikacji, które zostały mu przyznane.

![Szablon kafelka produktu](./media/concepts-marketing/product-tile.png)

| Pole | Opis                  | Gdzie można dodać do portalu                |
|---------------|-------------------------|----------------------------------|
| Nazwa urządzenia | Publiczna nazwa certyfikowanego urządzenia         | Karta podstawowe informacje o urządzeniu|
| Nazwa firmy| Nazwa publiczna firmy  | Nie można edytować w portalu. Wyodrębnione z nazwy konta MPN |
| Fotografia produktu  | Obraz urządzenia o minimalnej rozdzielczości 200p x 200p  | Szczegóły marketingu |
| Klasyfikacja certyfikacji  | Obowiązkowa etykieta certyfikacji urządzenia z certyfikatem platformy Azure i opcjonalne identyfikatory certyfikatów  | Karta podstawowe informacje o urządzeniu. Należy przekazać odpowiednie testy w sekcji Connect & test. |

## <a name="product-description-page-information"></a>Informacje o stronie opisu produktu

Po kliknięciu przez klienta kafelka urządzenia na stronie wyszukiwania katalogu zostaną one przejściu do strony Opis produktu na urządzeniu. Jest to miejsce, w którym zostanie znaleziony zbiorcze informacje podane podczas procesu certyfikacji.

W górnej części strony Opis produktu są wyróżniane kluczowe cechy, z których część została już użyta dla kafelka produktu.

![Górny pasek PDP](./media/concepts-marketing/pdp-top.png)

| Pole | Opis                  | Gdzie można dodać do portalu                |
|---------------|-------------------------|----------------------------------|
| Klasa urządzenia | Klasyfikacja współczynnika formularza i podstawowy cel urządzenia ([Dowiedz się więcej](./resources-glossary.md))       | Karta podstawowe informacje o urządzeniu|
| Typ urządzenia | Klasyfikacja urządzenia w oparciu o gotowość implementacji ([Dowiedz się więcej](./resources-glossary.md)) | Karta podstawowe informacje o urządzeniu |
| Dostępność geograficzna | Regiony, w których urządzenie jest dostępne do zakupu  | Szczegóły marketingu |
| Systemy operacyjne  | Systemy operacyjne obsługiwane przez urządzenie  | Karta Szczegóły produktu szczegóły urządzenia |
| Docelowe branże  | 3 najpopularniejsze branże, dla których Twoje urządzenie jest zoptymalizowane  | Szczegóły marketingu |
| Opis produktu  | Bezpłatne pole tekstowe umożliwiające zapisanie opisu marketingowego produktu. Może to przechwycić szczegóły, które nie znajdują się na liście w portalu, lub dodać dodatkowy kontekst dla korzyści płynących z używania Twojego urządzenia. | Szczegóły marketingu|

Pozostała część strony koncentruje się na wyświetlaniu specyfikacji technicznych urządzenia w formacie tabeli, które ułatwią klientom lepsze zrozumienie Twojego produktu. Dla wygody wyświetlane są również informacje znajdujące się u góry strony. Pozostała część tabeli jest częścią składników określonych w portalu.

![Strona dolnej krawędzi](./media/concepts-marketing/pdp-bottom.png)

| Pole | Opis                  | Gdzie można dodać do portalu                |
|---------------|-------------------------|----------------------------------|
| Typ składnika | Klasyfikacja współczynnika formularza i podstawowy cel urządzenia ([Dowiedz się więcej](./resources-glossary.md))       | Szczegóły produktu szczegóły urządzenia|
| Nazwa składnika| Nazwa opisywanego składnika | Szczegóły produktu szczegóły urządzenia |
| Dodatkowe informacje o składnikach | Dodatkowe specyfikacje sprzętu, takie jak w przypadku zawartych czujników, łączności, akceleratorów itp.  | Dodatkowe informacje o składniku szczegółów urządzenia ([Dowiedz się więcej](./how-to-using-the-components-feature.md))  |
| Tekst zależności urządzenia | Tekst dostarczony przez partnera opisujący różne zależności wymagane przez produkt do łączenia się z platformą Azure ([Dowiedz się więcej](./how-to-indirectly-connected-devices.md))   | Sekcja Komentarze dla klientów na karcie zależności szczegółów urządzenia |
| Link zależności urządzenia  | Link do certyfikowanego urządzenia wymaganego przez bieżący produkt  | Karta zależności w obszarze Szczegóły urządzenia |

## <a name="shop-links"></a>Linki do sklepu
Dostępne zarówno na kafelku produktu, jak i na stronie opis produktu jest przyciskiem warsztatu. Po kliknięciu przez klienta zostanie otwarte okno umożliwiające wybranie dystrybutora (można wyświetlić do 5 dystrybutorów). Po wybraniu klient zostanie przekierowany do adresu URL podanego przez partnera.

![Obraz przedstawiający środowisko pracy w sklepie](./media/concepts-marketing/shop.png)

| Pole | Opis                  | Gdzie można dodać do portalu                |
|---------------|-------------------------|----------------------------------|
| Nazwa dystrybutora | Nazwa dystrybutora, który sprzedaje produkt | Szczegóły marketingu|
| Pobierz urządzenie| Połącz się z zewnętrzną witryną internetową, aby zakupić urządzenie (lub zażądać oferty od dystrybutora). Może być taka sama jak Strona producenta, jeśli dystrybutor jest tym samym, co producent urządzenia. Jeśli strona zakupu nie jest dostępna, zostanie przekierowany na stronę dystrybutora, aby klient mógł się z nim skontaktować bezpośrednio.  | Adres URL strony produktu dystrybutora w szczegółach marketingowych. Jeśli żadna strona zakupu nie jest dostępna, link będzie domyślnie adresem URL dystrybutora w szczegółach marketingowych. |

## <a name="external-links"></a>Linki zewnętrzne
Uwzględniono również na stronie opisu produktu łącza do witryn i plików udostępnianych przez partnerów, które pomagają klientowi lepiej zrozumieć produkt. Pojawiają się one w górnej części strony, poniżej tekstu opisu produktu. Wyświetlane linki różnią się w zależności od różnych typów urządzeń i programów certyfikacji.

| Link | Opis                  | Gdzie można dodać do portalu                |
|---------------|-------------------------|----------------------------------|
| Wprowadzenie — Przewodnik * | Plik PDF z instrukcjami użytkownika w celu nawiązania połączenia z urządzeniem i korzystania z niego przy użyciu usług platformy Azure | Dodawanie sekcji przewodnika "wprowadzenie" w portalu|
| Strona producenta *|Link do strony producenta. Ta strona może być konkretną stroną produktu lub stroną główną firmy, jeśli strona marketingowa jest niedostępna. | Strona marketingowa producenta w szczegółach marketingu |
| Model urządzenia | Publiczne modele DTDL dla rozwiązań Plug and Play IoT  | Nie można edytować w portalu. Model urządzenia musi zostać przekazany do ([repozytorium modelu publicznego](https://aka.ms/modelrepo) )  |
| Kod źródłowy urządzenia | Adres URL do kodu źródłowego urządzenia dla typów urządzeń dev Kit| Karta podstawowe informacje o urządzeniu  |

 **Wymagane dla wszystkich opublikowanych urządzeń*

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak korzystamy z informacji podanych podczas certyfikacji, możesz teraz przygotować się do certyfikowania urządzenia. Rozpocznij projekt certyfikacji lub Wróć do etapu szczegóły urządzenia, aby dodać własne informacje marketingowe.

- [Rozpocznij podróż certyfikacyjną](./tutorial-00-selecting-your-certification.md)
- [Dodawanie szczegółów urządzenia](./tutorial-02-adding-device-details.md)
