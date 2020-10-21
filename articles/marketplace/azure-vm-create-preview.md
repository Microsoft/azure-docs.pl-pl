---
title: Dodaj odbiorców wersji zapoznawczej dla oferty maszyny wirtualnej w portalu Azure Marketplace
description: Dowiedz się, jak dodać odbiorców wersji zapoznawczej dla oferty maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 48d111decf2145a843733b1f61b1daa87b84b85c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284839"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>Jak dodać odbiorców wersji zapoznawczej dla oferty maszyny wirtualnej

Wybierz kartę **Podgląd** , a następnie wybierz ograniczony **odbiorcę w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo w szerszej komercyjnej grupie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w okienku **podglądu** wybierz pozycję **Przejdź na żywo** , aby opublikować ofertę dla komercyjnych odbiorców w portalu Marketplace.

Odbiorca w wersji zapoznawczej jest identyfikowany przez identyfikatory GUID subskrypcji platformy Azure, a także opcjonalny opis dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10 identyfikatorów) lub przekazując plik CSV (do 100 identyfikatorów). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli oferta już istnieje, możesz zdefiniować odbiorców wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty *przed* opublikowaniem jej na żywo w witrynie Azure Marketplace. Odbiorcy wersji zapoznawczej mogą wyświetlać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po całkowitym opublikowaniu oferty w portalu Azure Marketplace. Prywatni odbiorcy (zdefiniowani w okienku **ceny i dostępność** planu) mają wyłączny dostęp do konkretnego planu.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie i publikowanie ofert](review-publish-offer.md)
