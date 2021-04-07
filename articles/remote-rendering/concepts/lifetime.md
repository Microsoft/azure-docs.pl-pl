---
title: Okres istnienia obiektu i zasobu
description: Wyjaśnia zarządzanie okresem istnienia dla różnych typów
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593895"
---
# <a name="object-and-resource-lifetime"></a>Okres istnienia obiektu i zasobu

Zdalne renderowanie na platformie Azure rozróżnia dwa typy: **obiekty** i **zasoby**.

## <a name="object-lifetime"></a>Okres istnienia obiektu

*Obiekty* są uważane za elementy, które użytkownik może tworzyć, modyfikować i zniszczyć według własnego uznania. Obiekty mogą być duplikowane swobodnie, a każde wystąpienie może być przypadające w czasie. W związku z tym [jednostki](entities.md) i [składniki](components.md) są obiektami.

Okres istnienia obiektów jest w pełni objęty kontrolą użytkownika. Nie jest on związany z okresem istnienia reprezentacji po stronie klienta, chociaż. Klasy takie jak `Entity` i `Component` mają `Destroy` funkcję, która musi zostać wywołana w celu cofnięcia alokacji obiektu na hoście renderowania zdalnego. Ponadto program `Entity.Destroy()` niszczy jednostkę, jej elementy podrzędne i wszystkie składniki w tej hierarchii.

## <a name="resource-lifetime"></a>Okres istnienia zasobu

*Zasoby* są elementami, których okres istnienia jest całkowicie zarządzany przez hosta renderowania zdalnego. Zasoby są rozliczane wewnętrznie. Są one cofane, gdy nikt nie odwołuje się do nich dłużej.

Większość zasobów można utworzyć bezpośrednio, zazwyczaj przez załadowanie ich z pliku. Gdy ten sam plik jest ładowany wiele razy, zdalne renderowanie na platformie Azure zwróci to samo odwołanie i nie ładuje danych ponownie.

Wiele zasobów jest niezmiennych dla [siatek](meshes.md) wystąpień i [tekstur](textures.md). Niektóre zasoby są modyfikowalne, ale, na przykład [materiały](materials.md). Ze względu na to, że zasoby są często udostępniane, modyfikowanie zasobów może mieć wpływ na wiele obiektów. Na przykład zmiana koloru materiału spowoduje zmianę koloru wszystkich obiektów, które używają siatek, które z kolei odwołują się do tego materiału.

### <a name="built-in-resources"></a>Zasoby wbudowane

Zdalne renderowanie na platformie Azure zawiera niektóre wbudowane zasoby, które mogą zostać załadowane przez zaczekanie na odpowiedni identyfikator w `builtin://` trakcie wywołania do `RenderingSession.Connection.LoadXYZAsync()` . Dostępne zasoby wbudowane są wymienione w dokumentacji dotyczącej poszczególnych funkcji. Na przykład rozdział do [przestrzeni powietrznej](../overview/features/sky.md) zawiera listę wbudowanych tekstur.

## <a name="general-lifetime"></a>Ogólny okres istnienia

Okres istnienia wszystkich obiektów i zasobów jest powiązany z połączeniem. Po rozłączeniu wszystko jest odrzucane. Po ponownym nawiązaniu połączenia z tą samą sesją wykres sceny będzie pusty i wszystkie zasoby zostaną usunięte.

W tej chwili ładowanie tego samego zasobu do sesji po rozłączeniu jest zwykle szybsze niż po raz pierwszy. Dzieje się tak, ponieważ większość zasobów musi zostać pobrana z usługi Azure Storage po raz pierwszy, co nie jest konieczne po raz drugi.

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Składniki](components.md)
* [Modele](models.md)
