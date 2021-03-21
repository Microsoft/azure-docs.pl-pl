---
title: Jak dodać odbiorców wersji zapoznawczej dla oferty aplikacji platformy Azure
description: Dowiedz się, jak dodać użytkowników wersji zapoznawczej do oferty aplikacji platformy Azure w centrum partnerskim.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370254"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Jak dodać odbiorców wersji zapoznawczej dla oferty aplikacji platformy Azure

W tym artykule opisano sposób konfigurowania odbiorców wersji zapoznawczej dla oferty aplikacji platformy Azure w portalu komercyjnym. Musisz zdefiniować odbiorców wersji zapoznawczej, którzy mogą przejrzeć aukcję oferty przed jej rozpoczęciem.

## <a name="define-a-preview-audience"></a>Zdefiniuj odbiorcę w wersji zapoznawczej

Na stronie użytkownicy **wersji zapoznawczej** można zdefiniować ograniczonych odbiorców, którzy mogą przeglądać ofertę aplikacji platformy Azure przed opublikowaniem jej na żywo dla szerszego grona odbiorców w portalu Marketplace. Możesz zdefiniować odbiorców wersji zapoznawczej przy użyciu identyfikatorów subskrypcji platformy Azure, a także opcjonalny opis dla każdego z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** w Azure Portal.

Dodaj co najmniej jeden i maksymalnie 10 identyfikatorów subskrypcji platformy Azure — pojedynczo (do 10) lub przekazując plik CSV (do 100), aby zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli Twoja oferta jest już aktywna, możesz nadal zdefiniować odbiorcę w wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty przed opublikowaniem jej na żywo w sklepach online. Mogą oni wyświetlać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po opublikowaniu oferty w portalu Marketplace. Plan może być dostępny tylko dla odbiorców prywatnych. Prywatni odbiorcy (zdefiniowani w karcie **dostępność** planu) mają wyłączny dostęp do konkretnego planu.

### <a name="add-email-addresses-manually"></a>Ręczne dodawanie adresów e-mail

1. Na stronie **Podgląd odbiorców** Dodaj pojedynczy identyfikator subskrypcji platformy Azure i opcjonalny opis w dostarczonych polach.
1. Aby dodać inny adres e-mail, wybierz link **Dodaj identyfikator (maks. 10)** .
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: konfiguracja techniczna.
1. Przejdź do [sekcji Następne kroki](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>Dodawanie adresów e-mail przy użyciu pliku CSV

1. Na stronie **Podgląd odbiorców** wybierz łącze **Eksportuj odbiorców (CSV)** .
1. Otwórz okno. Plik CSV w aplikacji, na przykład program Microsoft Excel.
1. W. Plik CSV, w kolumnie **Identyfikator** wprowadź identyfikatory subskrypcji platformy Azure, które chcesz dodać do odbiorców w wersji zapoznawczej.
1. W kolumnie **Opis** możesz opcjonalnie dodać opis dla każdego adresu e-mail.
1. W kolumnie **Typ** Dodaj identyfikator **subskrypcji** do każdego wiersza, który ma adres e-mail.
1. Zapisz plik jako. Plik CSV.
1. Na stronie **Podgląd odbiorców** wybierz łącze **Importuj odbiorców (CSV)** .
1. W oknie dialogowym **potwierdzenia** wybierz pozycję **tak**.
1. Wybierz pozycję. Plik CSV, a następnie wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: konfiguracja techniczna.

## <a name="next-steps"></a>Następne kroki

- [Jak dodać szczegóły techniczne dla oferty aplikacji platformy Azure](create-new-azure-apps-offer-technical.md)
