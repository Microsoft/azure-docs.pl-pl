---
title: Przykładowe modele
description: Wyświetla listę źródeł dla przykładowych modeli.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679485"
---
# <a name="sample-models"></a>Przykładowe modele

W tym artykule wymieniono niektóre zasoby dla przykładowych danych, których można użyć do testowania usługi zdalnego renderowania na platformie Azure.

## <a name="built-in-sample-model"></a>Wbudowany przykładowy model

Udostępniamy wbudowany przykładowy model, który zawsze można załadować przy użyciu adresu URL **BUILTIN://Engine**

![Przykładowy model](./media/sample-model.png "Przykładowy model")

Statystyki modelu:

| Nazwa | Wartość |
|-----------|:-----------|
| [Wymagany rozmiar maszyny wirtualnej](../how-tos/session-rest-api.md#create-a-session) | Standardowa |
| Liczba trójkątów | 18 700 000 |
| Liczba ruchomych części | 2073 |
| Liczba materiałów | 94 |

## <a name="third-party-data"></a>Dane innych firm

Grupa Khronos przechowuje zestaw przykładowych modeli glTF do testowania. ARR obsługuje format glTF zarówno w tekście (*. glTF*), jak i w postaci binarnej (*. GLB*). Zalecamy używanie modeli PBR w celu uzyskania najlepszych wyników wizualizacji:

* [Przykładowe modele glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](../quickstarts/render-model.md)
* [Szybki Start: konwertowanie modelu do renderowania](../quickstarts/convert-model.md)
