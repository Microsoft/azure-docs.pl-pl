---
title: Dodaj odbiorców wersji zapoznawczej dla oferty maszyny wirtualnej w portalu Azure Marketplace
description: Dowiedz się, jak dodać odbiorców wersji zapoznawczej dla oferty maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 854d0fa9b34d495f03a0c3c6203ceb227e4712d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629516"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>Jak dodać odbiorców wersji zapoznawczej dla oferty maszyny wirtualnej

Na stronie **Podgląd** (wybierz z menu po lewej stronie w centrum partnerskim) wybierz ograniczonego **odbiorcę w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo w szerszej gronie komercyjnych odbiorców portalu Marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w okienku **podglądu** wybierz pozycję **Przejdź na żywo** , aby opublikować ofertę dla komercyjnych odbiorców w portalu Marketplace.

Odbiorca w wersji zapoznawczej jest identyfikowany przez identyfikatory GUID **subskrypcji platformy Azure** , a także opcjonalny **Opis** dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10 identyfikatorów) lub przekazując plik CSV (do 100 identyfikatorów). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli oferta już istnieje, możesz zdefiniować odbiorców wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty *przed* opublikowaniem jej na żywo w witrynie Azure Marketplace. Odbiorcy wersji zapoznawczej mogą wyświetlać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po całkowitym opublikowaniu oferty w portalu Azure Marketplace. Prywatni odbiorcy (zdefiniowani w okienku **ceny i dostępność** planu) mają wyłączny dostęp do konkretnego planu.

Jeśli wprowadzono zmiany, wybierz opcję **Zapisz wersję roboczą** przed przejściem do następnej karty w menu po lewej stronie, **Zaplanuj przegląd**.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie planów](azure-vm-create-plans.md)
