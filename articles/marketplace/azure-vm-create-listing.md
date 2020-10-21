---
title: Szczegóły dotyczące konfigurowania oferty maszyny wirtualnej w witrynie Azure Marketplace
description: Dowiedz się, jak skonfigurować szczegóły oferty maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284856"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Jak skonfigurować szczegóły listy ofert dla maszyn wirtualnych

Na stronie z **listą ofert** zdefiniujesz szczegóły oferty, takie jak nazwa oferty, opis, linki i kontakty.

> [!NOTE]
> Twoja oferta zawiera informacje, takie jak opis, dokumenty, zrzuty ekranu i warunki użytkowania, nie musi być w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w systemie \<non-English language> ". Możesz także podać adres URL, aby połączyć się z witryną, która oferuje zawartość w języku innym niż ten, który jest używany w zawartości oferty.

## <a name="marketplace-details"></a>Szczegóły witryny Marketplace

### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu jest pokazywana klientom jako tytuł oferty. To pole jest wypełniane przy użyciu nazwy wprowadzonej w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić. Nazwa:

- Mogą być znakami towarowymi. Można dołączyć znaki towarowe i praw autorskich.
- Nie może zawierać więcej niż 50 znaków.
- Nie może zawierać znaków emoji.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty, który ma być wyświetlany w wynikach wyszukiwania w portalu Azure Marketplace. Może zawierać maksymalnie 100 znaków.

### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty, która będzie wyświetlana w wynikach wyszukiwania w portalu Azure Marketplace. Może zawierać maksymalnie 256 znaków.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Użyj tagów HTML, aby sformatować swój opis, aby był bardziej interesujący. Aby uzyskać listę dozwolonych tagów, zobacz [obsługiwane Tagi HTML](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres sieci Web zasad zachowania poufności informacji organizacji. Upewnij się, że oferta jest zgodna z przepisami i przepisami dotyczącymi ochrony prywatności. Należy również opublikować prawidłowe zasady zachowania poufności informacji w witrynie sieci Web.

## <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące Twojej oferty. Aby dodać łącze, wybierz opcję **Dodaj łącze**, a następnie wykonaj następujące pola:

- **Nazwa**: klienci będą widzieli nazwę na stronie szczegółów.
- **Link (adres URL)**: wprowadź link umożliwiający klientom Wyświetlanie dokumentu w trybie online.

## <a name="customer-support-links"></a>Linki obsługi klienta

Podaj witrynę sieci Web pomocy technicznej, w której klienci mogą skontaktować się z zespołem pomocy technicznej.

- Witryna internetowa pomocy technicznej platformy Azure
- Witryna sieci Web pomocy technicznej Azure Government

## <a name="partner-support-contact"></a>Kontakt z pomocą techniczną partnera

Podaj informacje kontaktowe dla partnerów firmy Microsoft, które mają być używane, gdy klienci otworzą bilet pomocy technicznej. Te informacje nie są wymienione w witrynie Azure Marketplace.

- Nazwa
- E-mail
- Telefon

## <a name="engineering-contact"></a>Kontakt inżynieryjny

Podaj informacje kontaktowe firmy Microsoft, które mają być używane w przypadku problemów z ofertą, w tym problemy z certyfikatem. Te informacje nie są wymienione w witrynie Azure Marketplace.

- Nazwa
- E-mail
- Telefon

## <a name="azure-marketplace-media"></a>Nośniki portalu Azure Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Nierozmyte obrazy spowodują odrzucenie zgłoszenia.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, należy się upewnić, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

### <a name="azure-marketplace-logos"></a>Logo portalu Azure Marketplace

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Średnia** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Zrzuty ekranu

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy zrzut ekranu musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG. Każdy zrzut ekranu musi zawierać podpis.

### <a name="videos"></a>Filmy wideo

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Filmy wideo powinny być hostowane w zewnętrznej usłudze wideo. Wprowadź nazwę, adres sieci Web i obraz miniatury PNG wideo o godzinie 1280 x 720 pikseli.

Aby uzyskać dodatkowe zasoby dotyczące aukcji z witryny Marketplace, zobacz [najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](gtm-offer-listing-best-practices.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie planów](azure-vm-create-plans.md)
