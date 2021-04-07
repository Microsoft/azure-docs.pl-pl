---
title: Co to jest Ontology?
titleSuffix: Azure Digital Twins
description: Dowiedz się więcej na temat DTDL Industry ontologie for Modeling w pewnej domenie
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034697"
---
# <a name="what-is-an-ontology"></a>Co to jest Ontology? 

Słownictwo rozwiązania Digital bliźniaczych reprezentacji na platformie Azure jest zdefiniowane przy użyciu [modeli](concepts-models.md), które opisują typy obiektów istniejących w danym środowisku.

Czasami, gdy rozwiązanie jest powiązane z konkretną branżą, może być prostsze i bardziej efektywne, aby zacząć od zestawu modeli dla tego przemysłu, który już istnieje, zamiast utworzyć własny zestaw modeli od podstaw. Te wstępnie istniejące zestawy modeli są nazywane **ontologie**. 

Ogólnie rzecz biorąc, Ontology to zestaw modeli dla danej domeny, takich jak struktura budynku, system IoT, inteligentne miasto, Siatka energii, zawartość sieci Web itd. Ontologie są często używane jako schematy dla wykresów wiedzy, ponieważ mogą one być włączone:
* Harmonizacja składników oprogramowania, dokumentacji, bibliotek zapytań itp.
* Zmniejszona inwestycja w Koncepcje modelowania i opracowywania systemu
* Łatwiejsze współdziałanie danych na poziomie semantycznym
* Użycie najlepszych rozwiązań zamiast rozpoczynania od podstaw lub "odtworzenia kółka"

W tym artykule wyjaśniono, dlaczego i w jaki sposób używać ontologie dla modeli cyfrowych bliźniaczych reprezentacji platformy Azure, a także jakie ontologie i narzędzia dla nich są dostępne już dzisiaj.

## <a name="using-ontologies-for-azure-digital-twins"></a>Korzystanie z programu ontologie for Azure Digital bliźniaczych reprezentacji

Ontologie oferują doskonały punkt wyjścia dla rozwiązań Digital bliźniaczy. Obejmują one zestaw modeli specyficznych dla domeny i relacje między jednostkami do projektowania, tworzenia i analizowania grafu dwuosiowego. Ontologie umożliwiają deweloperom rozwiązań rozpoczęcie rozwiązania Digital bliźniaczych reprezentacji od sprawdzonego punktu początkowego i skoncentrowanie się na rozwiązywaniu problemów z firmą. Ontologie udostępniane przez firmę Microsoft zostały również zaprojektowane tak, aby można je było łatwo rozszerzyć, aby można było dostosować je do swojego rozwiązania. 

Ponadto korzystając z tych ontologie w swoich rozwiązaniach, można skonfigurować je w celu zapewnienia bardziej bezproblemowej integracji między różnymi partnerami i dostawcami, ponieważ usługa ontologie może zapewnić wspólny słownik rozwiązań.

Ponieważ modele na platformie Azure Digital bliźniaczych reprezentacji są reprezentowane w [języku Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), ontologie do użycia z programem Azure Digital bliźniaczych reprezentacji są również zapisywane w DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Strategie integracji ontologie

Istnieją trzy możliwe strategie integracji standardowej ontologie z DTDL. Możesz wybrać ten, który najlepiej sprawdza się w zależności od potrzeb:

| Strategia | Opis | Zasoby |
| --- | --- | --- |
| **Wdrażanie** | Możesz rozpocząć rozwiązanie, korzystając z DTDL Ontology Open Source, który został zbudowany na powszechnie przyjętych standardach branżowych. Możesz użyć tych zestawów jako wbudowanych lub uzupełnić je własnymi dodatkami dla niestandardowego rozwiązania. | [*Pojęcia: &nbsp; przyjęcie &nbsp; &nbsp; standardowego ontologieu branżowego*](concepts-ontologies-adopt.md)<br><br>[*Pojęcia: &nbsp; rozszerzanie &nbsp; ontologie*](concepts-ontologies-extend.md) |
| **Zamian** | Jeśli masz już istniejące modele reprezentowane w innym formacie standardowym, możesz je przekonwertować na DTDL, aby używać ich w usłudze Azure Digital bliźniaczych reprezentacji. | [*Pojęcia: &nbsp; konwertowanie &nbsp; ontologie*](concepts-ontologies-convert.md)<br><br>[*Pojęcia: &nbsp; rozszerzanie &nbsp; ontologie*](concepts-ontologies-extend.md) |
| **Autor** | Możesz zawsze opracowywać własne niestandardowe modele DTDL od podstaw przy użyciu dowolnych odpowiednich standardów branżowych. | [*Koncepcje: modele DTDL*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Korzystanie z strategii Ontology w ścieżce projektowania modelu

Bez względu na to, którą strategię należy zintegrować z Ontology w usłudze Azure Digital bliźniaczych reprezentacji, możesz wykonać pełną ścieżkę poniżej, aby przeprowadzić Cię przez proces tworzenia i przekazywania Ontology jako modeli DTDL.

1. Zacznij od przejrzenia i zrozumienia [modelowania DTDL w usłudze Azure Digital bliźniaczych reprezentacji](concepts-models.md).
1. Kontynuuj wybór strategii integracji Ontology z powyższych: [**przyjmować**](concepts-ontologies-adopt.md), [**Konwertuj**](concepts-ontologies-convert.md)lub [**Twórz**](concepts-models.md) modele na podstawie Ontology.
    1. W razie potrzeby należy [zwiększyć](concepts-ontologies-extend.md) Ontology, aby dostosować go do Twoich potrzeb.
1. [Sprawdź poprawność](how-to-parse-models.md) modeli, aby sprawdzić, czy działają dokumenty DTDL.
1. Przekaż gotowe modele do usługi Azure Digital bliźniaczych reprezentacji, korzystając z [interfejsów API](how-to-manage-model.md#upload-models) lub przykładu, takiego jak [usługa Azure Digital bliźniaczych reprezentacji model obiektu przekazującego](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

Następnie powinno być możliwe korzystanie z modeli w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. 

Możesz wizualizować je za pomocą przykładów, takich jak program [Azure Digital bliźniaczych reprezentacji Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) lub [wizualizator modelu Digital bliźniaczych reprezentacji platformy Azure](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer), lub przejdź do, aby użyć ich do tworzenia [cyfrowych bliźniaczych reprezentacji](concepts-twins-graph.md).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat strategii przyjmowania, konwertowania i tworzenia ontologie:
* [*Koncepcje: przyjmowanie standardowych ontologieów branżowych*](concepts-ontologies-adopt.md)
* [*Pojęcia: konwertowanie ontologie*](concepts-ontologies-convert.md)
* [*Instrukcje: Zarządzanie modelami DTDL*](how-to-manage-model.md)

Można też dowiedzieć się, jak modele są używane do tworzenia cyfrowych bliźniaczych reprezentacji: [*pojęcia: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md).