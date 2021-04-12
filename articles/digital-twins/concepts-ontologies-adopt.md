---
title: Przyjmowanie standardowych ontologieów branżowych
titleSuffix: Azure Digital Twins
description: Dowiedz się więcej o istniejących ontologie branżowych, które można przyjąć na potrzeby usługi Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102124231"
---
# <a name="adopting-an-industry-ontology"></a>Przyjmowanie branży Ontology

Ponieważ może być łatwiej zacząć od typu open source DTDL Ontology niż na podstawie pustej strony, firma Microsoft jest partnerem z ekspertami domeny w celu opublikowania ontologie, które reprezentuje powszechnie akceptowane konwencje branżowe i obsługują różne przypadki użycia klientów. 

W wyniku tego powstaje zestaw ontologie opartych na DTDLach typu open source, które uczyją się, jak tworzyć, dowiedzieć się lub bezpośrednio korzystać ze standardów branżowych. Ontologie są zaprojektowane do zaspokajania potrzeb deweloperów podrzędnych, z możliwością ich ogólnego przyjęcia i/lub przedłużonej przez branżę.

W tej chwili firma Microsoft pracowała z partnerami w celu opracowania Ontology dla [inteligentnych budynków](#realestatecore-smart-building-ontology) i Ontology dla [inteligentnych miast](#smart-cities-ontology), które zapewniają powszechną podstawę do modelowania w oparciu o standardy w tych branżach, aby uniknąć oddziału. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore inteligentnego kompilowania Ontology

*Znajdź Ontology tutaj: [**Digital bliźniaczych reprezentacji Definition Language-based Ontology RealEstateCore dla budynków inteligentnych**](https://github.com/Azure/opendigitaltwins-building)*.

Firma Microsoft połączyła się z [RealEstateCoreem](https://www.realestatecore.io/), szwedzkim konsorcjum właścicieli nieruchomości, dostawcami oprogramowania i instytucjami badawczymi, aby dostarczyć ten Ontology typu open source dla branży nieruchomości.

Te inteligentne budynki Ontology zapewniają wspólną podstawę do modelowania inteligentnych budynków, przy użyciu standardów branżowych (takich jak [schemat kostki](https://brickschema.org/ontology/) lub [topologia tworzenia W3C Ontology](https://w3c-lbd-cg.github.io/bot/index.html)), aby uniknąć oddziału. Ontology również zawiera najlepsze rozwiązania dotyczące korzystania z nich i ich prawidłowego zwiększania. 

Aby dowiedzieć się więcej o strukturze i konwencjach modelowania Ontology oraz o sposobie ich używania, sposobie ich rozbudowy oraz sposobie współtworzenia, odwiedź repozytorium Ontology w witrynie GitHub: [Azure/opendigitaltwins — Kompilowanie](https://github.com/Azure/opendigitaltwins-building). 

Więcej informacji na temat powiązania z RealEstateCoreą i celami tej inicjatywy można znaleźć w tym wpisie w blogu i dołączonym filmie wideo: [RealEstateCore, inteligentne Kompilowanie Ontology for Digital bliźniaczych reprezentacji, jest teraz dostępne](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Inteligentne miejscowości Ontology

*Znajdź Ontology tutaj: [**Digital bliźniaczych reprezentacji Definition Language (DTDL) Ontology dla inteligentnych miast**](https://github.com/Azure/opendigitaltwins-smartcities)*.

Firma Microsoft współpracuje z [otwartymi inteligentnymi miastami Agile (OASC)](https://oascities.org/) i [Sirus](https://sirus.be/) , aby zapewnić Ontology oparty na DTDL dla inteligentnych miast, rozpoczynając od [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Oprócz ETSI NGSI-LD oceniamy również Saref4City, CityGML, ISO i inne.

Bieżąca wersja Ontology jest skoncentrowana na początkowym zestawie modeli. Autorzy Ontology Zapraszamy do współtworzenia, aby zwiększyć początkowy zestaw przypadków użycia, a także poprawić istniejące modele. 

Aby dowiedzieć się więcej na temat Ontology, jak z niego korzystać i jak współtworzyć, odwiedź repozytorium Ontology w witrynie GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Więcej informacji na temat partnerstwa i podejścia do inteligentnych miast można znaleźć w tym wpisie w blogu i dołączonym filmie wideo: [inteligentne miasta Ontology for Digital bliźniaczych reprezentacji](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat rozszerzania standardowych ontologie branżowych, aby sprostać Twoim wymaganiom: [*koncepcje: rozszerzanie branżowych ontologie*](concepts-ontologies-extend.md).

* Lub Kontynuuj na ścieżce tworzenia modeli opartych na ontologie: [*Używanie strategii Ontology w ścieżce projektowania modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).