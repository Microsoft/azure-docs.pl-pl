---
title: Przyjmowanie standardowych ontologieów branżowych
titleSuffix: Azure Digital Twins
description: Dowiedz się więcej o istniejących ontologie branżowych, które można przyjąć na potrzeby usługi Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561663"
---
# <a name="adopting-an-industry-ontology"></a>Przyjmowanie branży Ontology

Ponieważ może być łatwiej zacząć od typu open source DTDL Ontology niż na podstawie pustej strony, firma Microsoft jest partnerem z ekspertami domeny w celu opublikowania ontologie, które reprezentuje powszechnie akceptowane konwencje branżowe i obsługują różne przypadki użycia klientów. 

W wyniku tego powstaje zestaw ontologie opartych na DTDLach typu open source, które uczyją się, jak tworzyć, dowiedzieć się lub bezpośrednio korzystać ze standardów branżowych. Ontologie są zaprojektowane do zaspokajania potrzeb deweloperów podrzędnych, z możliwością ich ogólnego przyjęcia i/lub przedłużonej przez branżę.

W tej chwili firma Microsoft pracowała z partnerami nieruchomości w celu opracowania Ontology dla budynków inteligentnych, które zapewniają wspólną podstawę do modelowania inteligentnych budynków opartych na standardach branżowych, aby uniknąć oddziału. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore inteligentnego kompilowania Ontology

Firma Microsoft połączyła się z [RealEstateCoreem](https://www.realestatecore.io/), szwedzkim konsorcjum właścicieli nieruchomości, dostawcami oprogramowania i instytucjami badawczymi, aby dostarczyć Ontology DTDL typu open source dla branży nieruchomości: [**oparty na DTDL RealEstateCore Ontology dla budynków inteligentnych**](https://github.com/Azure/opendigitaltwins-building).

Te inteligentne budynki Ontology zapewniają wspólną podstawę do modelowania inteligentnych budynków, przy użyciu standardów branżowych (takich jak [schemat kostki](https://brickschema.org/ontology/) lub [topologia tworzenia W3C Ontology](https://w3c-lbd-cg.github.io/bot/index.html)), aby uniknąć oddziału. Ontology również zawiera najlepsze rozwiązania dotyczące korzystania z nich i ich prawidłowego zwiększania. 

Aby dowiedzieć się więcej o strukturze i konwencjach modelowania Ontology oraz o sposobie ich używania, sposobie ich rozbudowy oraz sposobie współtworzenia, odwiedź [repozytorium Ontology w witrynie GitHub](https://github.com/Azure/opendigitaltwins-building). 

Więcej informacji na temat powiązania z RealEstateCoreą i celami tej inicjatywy można znaleźć w tym wpisie w blogu i dołączonym filmie wideo: [*RealEstateCore, inteligentne Kompilowanie Ontology for Digital bliźniaczych reprezentacji, jest teraz dostępne*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat rozszerzania standardowych ontologie branżowych, aby sprostać Twoim wymaganiom: [*koncepcje: rozszerzanie branżowych ontologie*](concepts-ontologies-extend.md).

* Lub Kontynuuj na ścieżce tworzenia modeli opartych na ontologie: [*Używanie strategii Ontology w ścieżce projektowania modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).