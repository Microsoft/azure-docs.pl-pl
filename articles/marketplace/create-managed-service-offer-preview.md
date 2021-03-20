---
title: Jak dodać odbiorcę w wersji zapoznawczej do oferty usługi zarządzanej
description: Dowiedz się, jak dodać użytkowników wersji zapoznawczej do oferty usługi zarządzanej w centrum partnerskim firmy Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918419"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Jak dodać odbiorcę w wersji zapoznawczej do oferty usługi zarządzanej

W tym artykule opisano sposób konfigurowania odbiorców wersji zapoznawczej dla oferty usługi zarządzanej w komercyjnej witrynie Marketplace przy użyciu Centrum partnerskiego. Odbiorca w wersji zapoznawczej może przejrzeć swoją ofertę przed jej rozpoczęciem.

## <a name="define-a-preview-audience"></a>Zdefiniuj odbiorcę w wersji zapoznawczej

Na stronie dostęp do **wersji zapoznawczej** można zdefiniować ograniczonych odbiorców, którzy mogą zapoznać się z ofertą usługi zarządzanej przed opublikowaniem jej na żywo dla szerszego grona odbiorców w portalu Marketplace. Możesz zdefiniować odbiorców wersji zapoznawczej przy użyciu identyfikatorów subskrypcji platformy Azure, a także opcjonalny opis dla każdego z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** na Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10) lub przekazując plik CSV (do 100), aby określić, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli Twoja oferta jest już aktywna, możesz nadal zdefiniować odbiorcę w wersji zapoznawczej do testowania aktualizacji oferty.

> [!NOTE]
> Odbiorca w *wersji zapoznawczej* różni się od odbiorców *prywatnych* . Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty przed opublikowaniem jej na żywo w sklepach online. Mogą przeglądać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych, gdy oferta zostanie w pełni opublikowana w portalu Marketplace. Plan może być dostępny tylko dla odbiorców prywatnych. Prywatni odbiorcy (zdefiniowani w karcie dostępność planu) mają wyłączny dostęp do konkretnego planu.

## <a name="add-email-addresses-manually"></a>Ręczne dodawanie adresów e-mail

1. Na stronie **Podgląd odbiorców** Dodaj pojedynczy identyfikator subskrypcji platformy Azure i opcjonalny opis w dostarczonych polach.
2. Aby dodać inny adres e-mail, wybierz link **Dodaj identyfikator (maks. 10)** .
3. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty.

## <a name="add-email-addresses-using-a-csv-file"></a>Dodawanie adresów e-mail przy użyciu pliku CSV

1. Na stronie odbiorców wersji zapoznawczej **Podgląd** odbiorców wybierz łącze **Eksportuj odbiorców (CSV)** .
2. Otwórz plik CSV. W kolumnie **Identyfikator** wprowadź identyfikatory subskrypcji platformy Azure, które chcesz dodać do odbiorców w wersji zapoznawczej.
3. W kolumnie **Opis** można dodać opis dla każdego wpisu.
4. W kolumnie **Typ** Dodaj identyfikator **subskrypcji** do każdego wiersza, który ma ID.
5. Zapisz plik jako plik CSV.
6. Na stronie **Podgląd odbiorców** wybierz łącze **Importuj odbiorców (CSV)** .
7. W oknie dialogowym **potwierdzenia** wybierz pozycję **tak**, a następnie Przekaż plik CSV.
8. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie planów](create-managed-service-offer-plans.md)
