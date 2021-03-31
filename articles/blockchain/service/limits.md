---
title: Limity usługi Azure łańcucha bloków
description: Omówienie limitów usługi i funkcjonalności w usłudze Azure łańcucha bloków Service
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80676514"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity w usłudze Azure łańcucha bloków Service

Usługa Azure łańcucha bloków ma limity usługi i funkcjonalne, takie jak liczba węzłów, które może mieć członek, ograniczenia konsorcjum i kwoty magazynu.

## <a name="pricing-tier"></a>Warstwa cenowa

Maksymalne limity dotyczące transakcji i węzłów modułu sprawdzania poprawności zależą od tego, czy usługa Azure łańcucha bloków jest udostępniana w warstwach cenowych w warstwie Podstawowa czy standardowa.

| Warstwa cenowa | Maksymalna liczba węzłów transakcji | Maksymalna liczba węzłów modułu sprawdzania poprawności |
|:---|:---:|:---:|
| Podstawowa | 10 | 1 |
| Standardowa (Standard) | 10 | 2 |

Sieć konsorcjum powinna mieć co najmniej dwa węzły warstwy Standardowa usługi Azure łańcucha bloków. Węzły warstwy standardowej obejmują dwa węzły modułu sprawdzania poprawności. Cztery węzły modułu sprawdzania poprawności są wymagane do zaspokojenia [Byzantine z odpornością na uszkodzenia Stambuł](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Warstwa Podstawowa służy do tworzenia, testowania i sprawdzania poprawności koncepcji. Użyj warstwy Standardowa dla wdrożeń klasy produkcyjnej. W przypadku korzystania z łańcucha bloków Data Manager lub wysyłania dużej liczby transakcji prywatnych należy również użyć warstwy *standardowa* .

Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

## <a name="storage-capacity"></a>Pojemność magazynu

Maksymalna ilość miejsca do magazynowania, która może być używana na węzeł dla danych i dzienników księgi, to 1,8 terabajtów.

Zmniejszenie rozmiaru księgi i magazynu dzienników nie jest obsługiwane.
## <a name="consortium-limits"></a>Limity konsorcjum

* **Nazwy konsorcjum i składowe muszą być unikatowe** z innych konsorcjów i nazw członków w usłudze Azure łańcucha bloków.

* **Nie można zmienić nazw członków i konsorcjum**

* **Wszyscy członkowie konsorcjum muszą znajdować się w tej samej warstwie cenowej**

* **Wszyscy członkowie, którzy uczestniczą w konsorcjum, muszą znajdować się w tym samym regionie**

    Pierwszy członek utworzony w ramach konsorcjum wymusza region. Zaproszeni członkowie do konsorcjum muszą znajdować się w tym samym regionie co pierwszy członek. Ograniczenie wszystkich elementów członkowskich do tego samego regionu pomaga zapewnić, że konsensus nie wpłynie negatywnie na sieć.

* **Konsorcjum musi mieć co najmniej jednego administratora**

    Jeśli konsorcjum ma tylko jednego administratora, nie może usunąć siebie z konsorcjum ani usunąć ich członka do momentu dodania lub promocji innego administratora w ramach konsorcjum.

* **Nie można ponownie dodać członków usuniętych z konsorcjum**

    Należy pamiętać, aby dołączyć do konsorcjum i utworzyć nowego członka. Ich istniejące zasoby składowe nie zostaną usunięte, aby zachować historyczne transakcje.

* **Wszyscy członkowie w konsorcjum muszą używać tej samej wersji księgi**

    Aby uzyskać więcej informacji na temat poprawek, aktualizacji i wersji księgi dostępnych w usłudze Azure łańcucha bloków, zobacz [poprawki, aktualizacje i wersje](ledger-versions.md).

## <a name="performance"></a>Wydajność

Nie należy używać funkcji *ETH. oszacowanie* gazów dla każdego przesłania transakcji. Funkcja *ETH. oszacowanie* ma duże ilości pamięci. Wywołanie funkcji wielokrotnie zmniejsza liczbę transakcji na sekundę.

Jeśli to możliwe, należy użyć wartości zachowawczej gazu do przesyłania transakcji i zminimalizować użycie *ETH. oszacowanie*.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach dotyczących poprawek i uaktualnień systemów, [poprawek, aktualizacji i wersji](ledger-versions.md).
