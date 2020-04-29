---
title: Szablony tokenów usługi Azure łańcucha bloków
description: Szablony tokenów usługi Azure łańcucha bloków są znormalizowanymi i wielokrotnym użytku szablonów, które upraszczają tworzenie i wdrażanie tokenów opartych na księdze.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252209"
---
# <a name="azure-blockchain-tokens-templates"></a>Szablony tokenów usługi Azure łańcucha bloków

[!INCLUDE [Preview note](./includes/preview.md)]

Szablon tokenów usługi Azure łańcucha bloków to standardowy szablon do wielokrotnego użytku, który upraszcza tworzenie i wdrażanie tokenów opartych na księdze. Szablon składa się z formuły, która opiera się na gramatyki w [strukturze taksonomii tokenów (ttf)](overview.md#token-taxonomy-framework) . Gramatyka obejmuje podstawowy typ tokenu i zestaw zachowań dla tokenu.  

Na przykład szablon tokenu **τϜ {d, m, b, r}** opisuje token podstawowy zamienne, który jest podzielona, mintable, nagrany i ma obsługę roli.
  
## <a name="base-token-types"></a>Podstawowe typy tokenów

Podczas definiowania i tworzenia tokenu opartego na księdze dla danego elementu zawartości należy wziąć pod uwagę, który token podstawowy ma być używany.

### <a name="fungible"></a>Zamienne

Tokeny zamienne (τF) mają wartość zamienną ze sobą, o ile znajdują się one w tej samej klasie lub serii. Jeden token ma taką samą wartość jak inny token lub dana liczba tokenów ma taką samą wartość jak inna równa się liczba. Na przykład Dolar jest tokenem zamienne. Jeśli dwie osoby mają przytrzymanie rachunku dolara, mogą oni wymieniać te rachunki dolarów bez konieczności. Rachunki dolarów mają równą wartość. 

### <a name="non-fungible"></a>Nie zamienne

Tokeny inne niż zamienne (τN) nie są zamienne z innymi tokenami tego samego typu, ponieważ zazwyczaj mają różne wartości. Na przykład tytuł właściwości jest tokenem innym niż zamienne. Tytuły właściwości do dwóch różnych apartamentach w złożonej części Apartment nie muszą mieć równej wartości, ze względu na lokalizację jednostki lub podłogę, w której znajduje się jednostka. Postrzegana wartość dwóch tokenów tytułu właściwości nie jest równa.

### <a name="hybrid"></a>Połączenie hybrydowe

Tokeny hybrydowe to tokeny, które mają składniki zarówno tokenów zamienne, jak i tokenów innych niż zamienne. Token hybrydowy jest podstawowym typem tokenu, który jest właścicielem klasy innego typu tokenu.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybrydowa baza nie zamienne z segmentami zamienne

Baza hybrydowa niezamiennea z tokenem segmentów zamienne ma bazę o identyfikatorze innym niż zamienne z segmentami tokenów zamienne.
Na przykład bilet z uzgadnianiem jest tokenem hybrydowym, w którym Data i godzina uzgodnienia jest tokenem bazowym innym niż zamienne. Bilety w różnych sekcjach siedzeń dla danego uzgodnienia to segmenty z tokenami zamienne. Bilety są wymieniane w poszczególnych sekcjach siedzeń, ale nie w różnych sekcjach.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Baza hybrydowej zamienne z segmentami niezamiennenymi

Baza hybrydowej zamienne z tokenem segmentów innych niż zamienne ma bazę zamienne z segmentami tokenów innych niż zamienne. Na przykład hipoteka zabezpieczeń z kopii zapasowych jest tokenem hybrydowym, w którym wielu właścicieli to baza zamienne, która jest dzielona przez wielu właścicieli. Zabezpieczenia są zamienne. Poszczególnym hipotekm są segmenty inne niż zamienne, które reprezentują określone hipoteczne zabezpieczenia z kopii zapasowej.

## <a name="token-behaviors"></a>Zachowania tokenu

Zachowanie tokenu definiuje możliwości lub ograniczenia tokenu. Zachowanie obejmuje właściwości pomocnicze, które są częścią definicji tokenu. Zachowania można stosować dla wszystkich typów tokenów lub tylko jeden. Zachowania mogą być wewnętrzne lub zewnętrzne w zależności od efektów zachowania. Zachowanie wewnętrzne włącza lub ogranicza właściwości samego tokenu. Zachowanie zewnętrzne włącza lub ogranicza wywoływanie zachowania z aktora zewnętrznego.

Aby uzyskać więcej informacji na temat usługi Azure łańcucha bloków Tokens obsługiwane są zachowania tokenu taksonomii tokenów (TTF), zobacz [redagowanie tokenu](composability.md).

## <a name="pre-built-token-templates"></a>Wstępnie skompilowane szablony tokenów

Tokeny usługi Azure łańcucha bloków udostępniają cztery wstępnie skompilowane szablony tokenów, które mogą być używane bez modyfikacji. Możesz wywołać te wstępnie skompilowane szablony dla większości przypadków użycia, aby szybko rozpocząć tworzenie i wdrażanie tokenów oraz zarządzanie nimi.

### <a name="commodity-tokens"></a>Tokeny asortymentu

Tokeny asortymentowe mają spójną wartość i są transodroczone. Na przykład zbiornik z olejem lub jednostką energii.

**τF {~ d, t, m, b, r}** -zamienne, cały, zbywalny, mintable, wypalanie i obsługa roli

Wiele scenariuszy łańcucha bloków wymaga przejrzystości i widoczności w łańcuchu dostaw lub w wielu organizacjach. Tokeny asortymentowe są oparte na tych typowych przypadkach użycia. Tokeny są zamienne i spójne. Szablon tokenu asortymentu jest elastyczny i dostosowywalny przy użyciu metadanych.

### <a name="qualified-tokens"></a>Tokeny kwalifikowane

Tokeny kwalifikowane reprezentują coś i są zazwyczaj skojarzone z jedną jednostką i nie mogą być transferowane. Na przykład dyplom lub naruszenie zasad parkingowych.

**τN {s, ~ t}** -zamienne, singleton i niezbywalny

Różne scenariusze inspekcji i zaświadczania wymagają, aby nie można było zmienić własności tokenu. Istnieje zestaw przypadków użycia, które wymagają podania kwalifikowanego tokenu niezależnie od tego, czy skojarzenie jest dobre, czy złe.

### <a name="asset-tokens"></a>Tokeny zasobów

Tokeny zasobów mają unikatową wartość zależną od elementu i nie są commoditized. Na przykład artefakt muzeów lub tytuł właściwości.

**τN {s, t}** -non-zamienne, singleton i zbywalny

Tokeny zasobów można mylić z tokenami asortymentu. Główna różnica między dwoma tokenami polega na tym, że tokeny zasobów są z natury unikatowe, a wartość jest niezależna od typu tokenu, który jest. Na przykład, fragment sztuki, taki jak oliwa oleju według ustalonego wykonawcy, jest tokenem zasobu. Jednak drukowanie dzieła Mona Lisa jest uznawane za token asortymentowy. Podobnie tytuł właściwości jest tokenem zasobu, ponieważ istnieje wartość w subiektywnych właściwościach właściwości.

### <a name="ticket-tokens"></a>Tokeny biletu

Tokeny biletów mają spójną wartość, ale zazwyczaj wygasają. Na przykład bilet z płaszczyzną.

**τN {m, b, r}** -non-zamienne, mintable, wypalane i obsługujące role.

Tokeny biletów zazwyczaj mają datę wygaśnięcia, która sprawia, że różni się od zwykłego tokenu asortymentu. Na przykład bilet samolotu, bilet uzgadniany lub bilet sportowy ma wszystkie opcje przypisane do stanowiska z określonymi terminami użytkowania. Nie można łatwo wymiany biletów między datami lub obszarami miejsc.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz większej elastyczności dla danego scenariusza, Dowiedz się więcej na temat tworzenia własnych szablonów tokenów przy użyciu możliwości [redagowania tokenu](composability.md).
