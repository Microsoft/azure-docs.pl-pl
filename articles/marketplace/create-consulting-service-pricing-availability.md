---
title: Jak skonfigurować ceny i dostępność usługi konsultingowej w centrum partnerskim firmy Microsoft
description: Dowiedz się, jak skonfigurować szczegółowe informacje o cenie oferty i dostępność rynkową w portalu komercyjnym firmy Microsoft przy użyciu Centrum partnerskiego.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780233"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Jak skonfigurować ceny i dostępność usługi konsultingowej

W tym artykule wyjaśniono, jak zdefiniować dostępność na rynku oraz szczegóły cennika oferty usługi konsultingowej w portalu komercyjnym firmy Microsoft.

> [!NOTE]
> Oferty usługi konsultingowej są oparte tylko na liście. Wszystkie transakcje muszą być zarządzane między klientem a klientami spoza komercyjnej witryny Marketplace.

## <a name="markets"></a>Wprowadza

W sekcji **rynków** należy wybrać kraje lub regiony, w których będzie dostępna usługa doradcza.

1. Na stronie **rynki** wybierz łącze **Edytuj rynki** .
2. W wyświetlonym oknie dialogowym Wybierz lokalizacje rynku, w których chcesz udostępnić swoją ofertę. Należy wybrać co najmniej jeden i maksymalnie 141 rynków.
3. Wybierz pozycję **Zapisz** , aby zamknąć okno dialogowe.

## <a name="preview-audience"></a>Podgląd odbiorców

Po opublikowaniu lub zaktualizowaniu oferty usługi konsultingowej w centrum partnerskim zostanie utworzona wersja zapoznawcza. Ta wersja zapoznawcza jest widoczna tylko dla użytkowników, którzy mają **klucz Ukryj**.

W polu **Ukryj klucz** wprowadź ciąg alfanumeryczny, który będzie używany w celu uzyskania dostępu do wersji zapoznawczej oferty.

## <a name="pricing-informational-only"></a>Cennik (tylko informacyjny)

W sekcji **Cennik** Określ, czy jest to bezpłatna czy płatna oferta.

W przypadku ofert płatnych należy określić, czy cena jest stała, czy Szacowana. Jeśli cena jest szacowana, opis oferty musi opisywać, jakie czynniki wpłyną na cenę.

W przypadku wybrania jednego kraju lub regionu w sekcji **rynków** Podaj cenę w walucie obsługiwaną dla tego rynku i wybierz pozycję **Zapisz wersję roboczą**. Listę obsługiwanych walut można znaleźć w temacie [dostępność geograficzna i obsługa waluty dla portalu komercyjnego](./marketplace-geo-availability-currencies.md) .

W przypadku wybrania wielu krajów lub regionów w sekcji **rynków** Podaj cenę w Stany Zjednoczone dolarów (USD) i wybierz pozycję **Zapisz wersję roboczą**. Centrum partnerskie przekształci tę cenę na walutę lokalną wszystkich wybranych rynków przy użyciu kursów wymiany dostępnych po zapisaniu wersji roboczej.

Aby sprawdzić poprawność konwersji lub ustawić ceny niestandardowe na indywidualnym rynku, należy wyeksportować, zmodyfikować, a następnie zaimportować arkusz cen:

1. W obszarze **Cennik** wybierz łącze **Eksportuj dane cennika** . Spowoduje to pobranie pliku na urządzenie.
1. Otwórz plik exportedPrice.xlsx w programie Microsoft Excel.
1. W arkuszu kalkulacyjnym można dostosować ceny i waluty dla każdego rynku. Listę obsługiwanych walut można znaleźć w temacie [dostępność geograficzna i obsługa waluty dla portalu komercyjnego](./marketplace-geo-availability-currencies.md) . Gdy wszystko będzie gotowe, Zapisz plik.
1. W centrum partnerskim w obszarze **Cennik** wybierz łącze **Importuj dane cennika** . Importowanie pliku spowoduje zastąpienie poprzednich informacji o cenach.

> [!IMPORTANT]
> Ceny zdefiniowane w centrum partnerskim są statyczne i nie są zgodne z wahaniami kursów wymiany. Aby zmienić cenę na jednym lub większej liczbie rynków po opublikowaniu, zaktualizuj i ponownie prześlij swoją ofertę w centrum partnerskim.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="next-steps"></a>Następne kroki

* [Przeglądanie i publikowanie](review-publish-offer.md)