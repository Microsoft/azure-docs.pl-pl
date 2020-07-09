---
title: Nazwane jednostki na potrzeby opieki zdrowotnej
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108509"
---
## <a name="health-entity-categories"></a>Kategorie jednostek kondycji:

Następujące kategorie jednostek są zwracane przez [Analiza tekstu dla kondycji](../../how-tos/text-analytics-for-health.md).  Należy pamiętać, że w tej wersji zapoznawczej kontenera jest obsługiwany tylko tekst w języku angielskim i w każdym obrazie kontenera jest dostępny tylko jeden model.

### <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych

|Kategoria  |Opis   |
|----------|--------------|
| WAŻNOŚCI | Ważności. |
| BODY_STRUCTURE | Części ciała ludzkiego, w tym organy i inne struktury. | 
| CONDITION_QUALIFIER | Poziomy warunków, takie jak *łagodny*, *rozszerzony*lub *rozpraszający*. | 
| RÓŻNIC | Warunki medyczne. Na przykład *naciągnięcie* . | 
| WSKAZYWA | Wskazówki, takie jak *lewo* lub *przedniej*. | 
| DAWKĘ | Rozmiar lub ilość leków.  | 
| EXAMINATION_NAME | Metoda lub procedura badania. | 
| EXAMINATION_RELATION | skojarzenie między jednostką miary a badaniem.  | 
| EXAMINATION_UNIT | Jednostka pomiarowa do badania. | 
| EXAMINATION_VALUE | Wartość jednostki pomiarowej badania. | 
| FAMILY_RELATION | Relacja familial, taka jak *siostrzany*.  | 
| JAKĄ | Takt.   | 
| SKORYGOWAN | Ich płci. | 
| PIERWIASTK | Jednostka genu, taka jak *TP53*.   | 
| MEDICATION_CLASS | Klasy leków. Na przykład *antybiotyki*.  | 
| MEDICATION_NAME  | Rodzajowy i Marka o nazwie medications.| 
| ROUTE_OR_MODE  | Metoda administrowania leczeniem. | 
| SYMPTOM_OR_SIGN  | Objawy choroby. | 
| CZAS  | Trzykrotn. Na przykład "8 lat" lub "2:10:30 ten rano" |
| TREATMENT_NAME  | Nazwy leczenia. | 
| VARIANT  | Genetyczna odmiana jednostki genu | 

### <a name="relation-extraction"></a>Wyodrębnianie relacji

Wyodrębnienie relacji identyfikuje znaczące połączenia między pojęciami wymienionymi w tekście. Na przykład relacja "czas warunku" można znaleźć, kojarząc nazwę warunku z godziną. Analiza tekstu dla kondycji mogą identyfikować następujące relacje:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* JEDNOSTEK 
