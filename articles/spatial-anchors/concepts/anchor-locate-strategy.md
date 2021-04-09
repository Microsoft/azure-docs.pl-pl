---
title: Strategia lokalizowania kotwicy
description: Poznaj różne strategie podczas wywoływania interfejsu API lokalizowania
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048480"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Zrozumienie klasy AnchorLocateCriteria
W tym artykule opisano różne opcje, których można użyć podczas wykonywania zapytania o kotwicę. Przejdziemy do klasy AnchorLocateCriteria, jej opcji i prawidłowych kombinacji opcji.

## <a name="anchor-locate-criteria"></a>Kryteria lokalizowania kotwicy
[Klasa AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) ułatwia wykonywanie zapytań do usługi dla wcześniej utworzonych kotwic. Jeden obiekt AnchorLocateCriteria może być używany dla każdego obserwatora w dowolnym momencie. Każdy obiekt AnchorLocateCriteria musi zawierać **dokładnie jedną** z następujących właściwości: [Identifiers](#identifiers), [NearAnchor](#nearanchor)lub [NearDevice](#neardevice). W razie potrzeby można ustawić dodatkowe właściwości, takie jak [strategia](#strategy), [BypassCache](#bypasscache)i [RequestedCategories](#requestedcategories) . 

### <a name="properties"></a>Właściwości
Zdefiniuj **dokładnie jedną** z następujących właściwości w monitorze:
#### <a name="identifiers"></a>Identyfikatory
*Wartość domyślna: pusta tablica ciągów*

Korzystając z identyfikatorów, można zdefiniować listę identyfikatorów zakotwiczenia dla kotwic, które chcesz zlokalizować. Identyfikatory zakotwiczenia są początkowo zwracane po pomyślnym utworzeniu kotwicy. W przypadku określonych identyfikatorów AnchorLocateCriteria ogranicza zestaw żądanych kotwic do kotwic ze zgodnymi identyfikatorami zakotwiczenia. Ta właściwość jest określana przy użyciu tablicy ciągów. 

#### <a name="nearanchor"></a>NearAnchor
*Wartość domyślna: nie ustawiono*

Za pomocą NearAnchor można określić, że AnchorLocateCriteria ogranicza zestaw żądanych kotwic do kotwic w obrębie żądanej odległości od wybranej kotwicy. Musisz podać wybrane kotwicę jako kotwicę źródłową. Możesz również ustawić pożądaną odległość od kotwicy źródłowej i maksymalną liczbę zwracanych kotwic, aby dodatkowo ograniczyć wyszukiwanie.
Ta właściwość jest określana za pomocą obiektu NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Wartość domyślna: nie ustawiono*

Za pomocą NearDevice można określić, że AnchorLocateCriteria ogranicza zestaw żądanych kotwic do tych blisko fizycznej lokalizacji urządzenia. Wszystkie włączone czujniki zostaną użyte w celu ułatwienia wykrycia zakotwiczenia na urządzeniu. Aby zapewnić najlepszą szansę znajdowania kotwic, należy skonfigurować SensorCapabilities, aby zapewnić sesji dostęp do wszystkich odpowiednich czujników. Aby uzyskać więcej informacji na temat konfigurowania i używania tej właściwości, zobacz [niestandardowa relokalizacja — kotwice przestrzenne platformy Azure | Microsoft docs](./coarse-reloc.md) i *sposób tworzenia i lokalizowania kotwic przy użyciu bardzo dużej lokalizacji* w [językach C#](../how-tos/set-up-coarse-reloc-unity.md), [obiektyw-C](../how-tos/set-up-coarse-reloc-unity.md), [SWIFT](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [c++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)i [c++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Ta właściwość jest określana za pomocą obiektu NearDeviceCriteria.

### <a name="additional-properties"></a>Dodatkowe właściwości
#### <a name="bypasscache"></a>BypassCache
*Wartość domyślna: FAŁSZ*

Gdy kotwica została utworzona lub znaleziona w sesji, jest również przechowywana w pamięci podręcznej.  Gdy ta właściwość ma wartość false, wszystkie kolejne zapytania w tej samej sesji będą zwracały buforowaną wartość. Nie przeprowadzono żądania do usługi ASA.

#### <a name="requestedcategories"></a>RequestedCategories
*Wartość domyślna: właściwości | Przestrzennych*

Ta właściwość służy do określania, jakie dane są zwracane z zapytania przy użyciu AnchorLocateCriteria. Wartość domyślna zwraca zarówno właściwości, jak i dane przestrzenne, dlatego nie powinna być zmieniana, jeśli właściwości i dane przestrzenne są wymagane. Tę właściwość można określić za pomocą wyliczenia AnchorDataCategory.

Wartość wyliczenia AnchorDataCategory | Zwrócone dane
-----|------------
Brak | Nie zwrócono żadnych danych
Właściwości| Są zwracane właściwości zakotwiczenia, w tym AppProperties.
Przestrzenne| Zwrócono informacje przestrzenne dotyczące kotwicy.

#### <a name="strategy"></a>Strategia
*Wartość domyślna: AnyStrategy*

Strategia w dalszej postaci definiuje, jak powinny znajdować się kotwice. Właściwość strategii można określić za pomocą wyliczenia LocateStrategy.

Wartość wyliczenia LocateStrategy | Opis
---------------|------------
AnyStrategy | Ta strategia umożliwia systemowi używanie kombinacji VisualInformation i strategii relacji do znajdowania kotwic. 
VisualInformation|Ta strategia próbuje znaleźć kotwice poprzez dopasowanie informacji wizualnych z bieżącego otoczenia do elementów wizualizacji zakotwiczenia. Wizualizacja zakotwiczenia odwołuje się do informacji wizualnych aktualnie skojarzonych z zakotwiczeniem. Te informacje wizualne są zwykle, ale nie są zbierane wyłącznie podczas tworzenia kotwicy. Obecnie ta strategia jest dozwolona tylko w połączeniu z właściwościami NearDevice lub identyfikatorami.
Relacja|Ta strategia próbuje znaleźć kotwice, wykorzystując istniejące [połączone kotwice](./anchor-relationships-way-finding.md#connect-anchors). Obecnie ta strategia jest dozwolona tylko w połączeniu z właściwościami NearAnchor lub identyfikatorami. Gdy jest używany z właściwością identyfikatory, jest wymagane, aby w tej samej sesji użytkownik miał wcześniej zlokalizowane kotwice, które mają już ustanowione relacje łączące z kotwicami, których identyfikatory zostały określone w tablicy identyfikatorów. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Prawidłowe kombinacje właściwości LocateStrategy i AnchorLocateCriteria 

Nie wszystkie kombinacje strategii i właściwości AnchorLocateCriteria są obecnie dozwolone przez system. W poniższej tabeli przedstawiono dozwolone kombinacje:



Właściwość | AnyStrategy | Relacja | VisualInformation
-------- | ------------|--------------|-------------------
Identyfikatory | &check;    | &check;     | &check;
NearAnchor  | &check;   (domyślnie jest to relacja) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Następne kroki

Zobacz [jak utworzyć i zlokalizować kotwice przy użyciu kotwic przestrzennych platformy Azure](../create-locate-anchors-overview.md) , aby uzyskać więcej przykładów przy użyciu klasy AnchorLocateCriteria.