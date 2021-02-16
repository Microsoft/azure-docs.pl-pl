---
title: Przykładowe modele
description: Wyświetla listę źródeł dla przykładowych modeli.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530539"
---
# <a name="sample-models"></a>Przykładowe modele

W tym artykule wymieniono niektóre zasoby dla przykładowych danych, których można użyć do testowania usługi zdalnego renderowania na platformie Azure.

## <a name="built-in-sample-model"></a>Wbudowany przykładowy model

Udostępniamy wbudowany przykładowy model, który zawsze można załadować przy użyciu adresu URL **BUILTIN://Engine**

![Przykładowy model](./media/sample-model.png "Przykładowy model")

Statystyki modelu:

| Nazwa | Wartość |
|-----------|:-----------|
| [Wymagany rozmiar serwera](../reference/vm-sizes.md) | Standardowa |
| Liczba trójkątów | 18 700 000 |
| Liczba ruchomych części | 2073 |
| Liczba materiałów | 94 |

## <a name="third-party-data"></a>Dane innych firm

Grupa Khronos przechowuje zestaw przykładowych modeli glTF do testowania. ARR obsługuje format glTF zarówno w tekście (*. glTF*), jak i w postaci binarnej (*. GLB*). Zalecamy używanie modeli PBR w celu uzyskania najlepszych wyników wizualizacji:

* [Przykładowe modele glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](../quickstarts/render-model.md)
* [Szybki start: Konwertowanie modelu do renderowania](../quickstarts/convert-model.md)
