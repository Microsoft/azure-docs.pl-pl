---
title: Migracja z programu Media Services V2 do wersji v3
description: Ten artykuł stanowi wprowadzenie do migrowania z programu Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 32e502ff175a9222faa0eb79fb53f2cc3f76bfb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559744"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migracja z programu Media Services V2 do wersji v3

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Przewodnik migracji Media Services ułatwia Migrowanie z interfejsów API programu Media Services V2 do wersji v3 na podstawie migracji wykorzystującej nowe funkcje i funkcje, które są teraz dostępne. Należy używać najlepszych orzeczeń i określić, co najlepiej pasuje do Twojego scenariusza.

## <a name="how-to-use-this-guide"></a>Jak korzystać z tego przewodnika

### <a name="navigating"></a>Przechodząc

W całym przewodniku zobaczysz poniższą ilustrację.

![kroki migracji](./media/migration-guide/steps.svg)<br/>

Ten krok będzie wskazywany przez zmianę koloru w numerze kroku, np.:

![kroki migracji 2](./media/migration-guide/steps-2.svg)<br/>

Na końcu każdej strony zobaczysz linki do pozostałej części dokumentów migracji, które można odczytać poniżej nagłówka **następnych kroków** .

### <a name="guidance"></a>Wskazówki

Wskazówki podane tutaj są *Ogólne*. Obejmuje ona zawartość, która pozwala ulepszyć informacje o tym, co jest teraz dostępne w wersji 3, a także o zmianach w przepływach pracy Media Services.

Aby uzyskać bardziej szczegółowe wskazówki, w tym zrzuty ekranu i grafiki koncepcyjne, istnieją linki do koncepcji, samouczków, przewodników Szybki Start, przykładów i odwołań do interfejsów API w poszczególnych tematach opartych na scenariuszach. Zostały również wymienione przykłady, które ułatwiają porównanie interfejsu API v2 z interfejsem API v3.

## <a name="migration-guidance-overview"></a>Przegląd wskazówek dotyczących migracji

Istnieją cztery ogólne kroki, które należy wykonać podczas migracji:

## <a name="step-1-benefits"></a>Zalety krok 1

<hr color="#5ea0ef" size="10">

[Poznaj zalety](migrate-v-2-v-3-migration-benefits.md) migracji do Media Services API v3.

## <a name="step-2-differences"></a>Różnice krok 2

<hr color="#5ea0ef" size="10">

Zapoznaj się z różnicami między interfejsem API Media Services V2 a interfejsem API v3.

- [Dostęp za pomocą interfejsu API](migrate-v-2-v-3-differences-api-access.md)
- [Luki w funkcjach](migrate-v-2-v-3-differences-feature-gaps.md)
- [Terminologia i zmiany jednostek](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Krok 3. Konfiguracja zestawu SDK

<hr color="#5ea0ef" size="10">

Zapoznaj się z różnicami w zestawie SDK i [skonfiguruj go do migracji do interfejsu API REST v3 lub zestawu SDK klienta](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Krok 4. wskazówki dotyczące scenariusza

<hr color="#5ea0ef" size="10">

Aplikacja Media Services V2 może być unikatowa. W związku z tym udostępniamy wskazówki oparte na scenariuszu na podstawie *sposobu korzystania z* usługi Media Services w przeszłości i kroków dla każdej funkcji usługi, takiej jak:

- [Kodowanie](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Transmisja strumieniowa na żywo](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Tworzenie pakietów i dostarczanie](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Ochrona zawartości](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Jednostki zarezerwowane multimediów (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)
