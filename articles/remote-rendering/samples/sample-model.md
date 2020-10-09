---
title: Przykładowe modele
description: Wyświetla listę źródeł dla przykładowych modeli.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507539"
---
# <a name="sample-models"></a>Przykładowe modele

W tym artykule wymieniono niektóre zasoby dla przykładowych danych, których można użyć do testowania usługi zdalnego renderowania na platformie Azure.

## <a name="built-in-sample-model"></a>Wbudowany przykładowy model

Udostępniamy wbudowany przykładowy model, który zawsze można załadować przy użyciu adresu URL **BUILTIN://Engine**

![Przykładowy model](./media/sample-model.png "Przykładowy model")

Statystyki modelu:

| Nazwa | Wartość |
|-----------|:-----------|
| [Wymagany rozmiar serwera](../how-tos/session-rest-api.md#create-a-session) | Standardowa |
| Liczba trójkątów | 18 700 000 |
| Liczba ruchomych części | 2073 |
| Liczba materiałów | 94 |

## <a name="third-party-data"></a>Dane innych firm

Grupa Khronos przechowuje zestaw przykładowych modeli glTF do testowania. ARR obsługuje format glTF zarówno w tekście (*. glTF*), jak i w postaci binarnej (*. GLB*). Zalecamy używanie modeli PBR w celu uzyskania najlepszych wyników wizualizacji:

* [Przykładowe modele glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](../quickstarts/render-model.md)
* [Szybki Start: konwertowanie modelu do renderowania](../quickstarts/convert-model.md)
