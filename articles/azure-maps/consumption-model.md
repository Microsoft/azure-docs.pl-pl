---
title: Modele zużycia pojazdów dla routingu | Mapy Microsoft Azure
description: 'Poznaj modele użycia obsługiwane przez Azure Maps: spalanie i sterowanie elektryczne. Sprawdź, które parametry są wykorzystywane przez każdy model, i Wyświetl ograniczenia dotyczące parametrów.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013143"
---
# <a name="consumption-model"></a>Model użycia

Usługa routingu zawiera zestaw parametrów dla szczegółowego opisu modelu zużycia specyficznego dla pojazdu.
W zależności od wartości **vehicleEngineType** obsługiwane są dwa główne modele użycia: _spalanie_ i _elektryczny_. Nie można określić parametrów, które należą do różnych modeli w tym samym żądaniu. Ponadto parametry modelu zużycia nie mogą być używane z następującymi wartościami **skokumode** : _rower_ i _pies_.

## <a name="parameter-constraints-for-consumption-model"></a>Ograniczenia parametrów dla modelu zużycia

W obu modelach użycia istnieją pewne zależności podczas określania parametrów. Oznacza to, że jawne określenie niektórych parametrów może wymagać określenia innych parametrów. Poniżej znajdują się następujące zależności:

* Wszystkie parametry wymagają określenia **constantSpeedConsumption** przez użytkownika. Wystąpił błąd podczas określania dowolnego innego parametru modelu zużycia, jeśli nie określono **constantSpeedConsumption** . Parametr **vehicleWeight** jest wyjątkiem dla tego wymagania.
* elementy **accelerationEfficiency** i **decelerationEfficiency** muszą być zawsze określone jako para (to jest lub None).
* Jeśli **accelerationEfficiency** i **decelerationEfficiency** są określone, iloczyn ich wartości nie może być większy niż 1 (w celu uniknięcia bezterminowego ruchu).
* elementy **uphillEfficiency** i **downhillEfficiency** muszą być zawsze określone jako para (takie jak lub None).
* Jeśli **uphillEfficiency** i **downhillEfficiency** są określone, iloczyn ich wartości nie może być większy niż 1 (w celu uniknięcia bezterminowego ruchu).
* Jeśli parametry \* __wydajności__ są określone przez użytkownika, należy również określić **vehicleWeight** . Gdy **vehicleEngineType** jest _spalany_, należy również określić **fuelEnergyDensityInMJoulesPerLiter** .
* elementy **maxChargeInkWh** i **currentChargeInkWh** muszą być zawsze określone jako para (to jest lub None).

> [!NOTE]
> Jeśli **constantSpeedConsumption** jest określony, żadne inne aspekty użycia, takie jak zbocze i przyspieszenie pojazdów, są brane pod uwagę w przypadku obliczeń zużycia.

## <a name="combustion-consumption-model"></a>Model użycia spalania

Model zużycia spalania jest używany, gdy **vehicleEngineType** jest ustawiony na _spalanie_.
Poniżej znajduje się lista parametrów należących do tego modelu. Szczegółowy opis można znaleźć w sekcji parametry.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model zużycia elektrycznego

Model zużycia elektrycznego jest używany, gdy **vehicleEngineType** jest ustawiony na wartość _elektryczną_.
Poniżej znajduje się lista parametrów należących do tego modelu. Szczegółowy opis można znaleźć w sekcji parametry.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Rozsądne wartości parametrów zużycia

Określony zestaw parametrów zużycia można odrzucić, nawet jeśli zestaw może spełnić wszystkie jawne wymagania. Dzieje się tak, gdy wartość określonego parametru lub kombinacja wartości kilku parametrów jest traktowana jako prowadząca do nierozsądnych wielkości wartości zużycia. Jeśli tak się stanie, najprawdopodobniej wskazuje to na błąd danych wejściowych, ponieważ należy zwrócić uwagę na wszystkie rozsądne wartości parametrów zużycia. W przypadku odrzucenia określonego zestawu parametrów zużycia, towarzyszący komunikat o błędzie będzie zawierać tekstowe wyjaśnienie przyczyn.
Szczegółowe opisy parametrów zawierają przykłady rozsądnych wartości dla obu modeli.
