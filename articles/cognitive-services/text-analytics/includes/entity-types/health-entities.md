---
title: Nazwane jednostki na potrzeby opieki zdrowotnej
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122347"
---
## <a name="health-entity-categories"></a>Kategorie jednostek kondycji:

Następujące kategorie jednostek są zwracane przez [Analiza tekstu dla kondycji](../../how-tos/text-analytics-for-health.md).  Należy pamiętać, że w tej wersji zapoznawczej kontenera jest obsługiwany tylko tekst w języku angielskim i w każdym obrazie kontenera jest dostępny tylko jeden model.

### <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych

|Kategoria  |Opis   |
|----------|--------------|
| Wiek | Ważności. Na przykład w *wieku 30 lat*. |
| AdministrativeEvent | Zdarzenie administracyjne. |
| BodyStructure | Części ciała ludzkiego, w tym organy i inne struktury. Na przykład *ARM*lub *serce*. | 
| CareEnvironment | Środowisko, w którym jest administrowane opieką lub traktowaniem. Na przykład *Pokój awaryjny* | 
| ConditionQualifier | Poziomy warunków. Na przykład *niedelikatny*, *rozszerzony*lub *rozpraszający*. | 
| Różnic | Warunki medyczne. Na przykład *naciągnięcie*. | 
| Kierunek | Ze. Na przykład *po lewej* lub *przedniej*. | 
| Dawkę | Rozmiar lub ilość leków. Na przykład *25mg*.  | 
| Badaniename | Metoda lub procedura badania. Na przykład *X-ray*. | 
| RelationalOperator | Operator, który definiuje relację między dwoma jednostkami. Na przykład *mniejsze niż*lub `>=` .  | 
| MeasurementUnit | Jednostka miary (taka jak wartość procentowa). | 
| MeasurementValue | Wartość liczbowa jednostki miary. | 
| FamilyRelation | Relacja familial. Na przykład jako *siostrzane*.  | 
| Częstotliwość | Takt.   | 
| Płeć | Ich płci. | 
| Pierwiastk | Jednostka genu, taka jak *TP53*.   | 
| HealthcareProfession | Metoda administrowania leczeniem. Na przykład *Administracja doustna*. | 
| MedicationClass | Klasy leków. Na przykład *antybiotyki*.  | 
| MedicationForm | Forma leków. Na przykład *kapsułka*. | 
| "Lekiname"  | Rodzajowy i Marka o nazwie medications. Na przykład *ibuprofen*. | 
| MedicationRoute | Metoda administrowania leczeniem. Na przykład *Administracja doustna*. | 
| SymptomOrSign  | Objawy choroby. Na przykład *Sore Throat*. | 
| Czas | Trzykrotn. Na przykład *8 lat* lub *2:10:30 tego rano* |
| "Leczeniename"  | Nazwy leczenia. Na przykład *.* | 
| Wariant | Genetyczna odmiana jednostki genu. | 

### <a name="relation-extraction"></a>Wyodrębnianie relacji

Wyodrębnienie relacji identyfikuje znaczące połączenia między pojęciami wymienionymi w tekście. Na przykład relacja "czas warunku" można znaleźć, kojarząc nazwę warunku z godziną. Analiza tekstu dla kondycji mogą identyfikować następujące relacje:

|Kategoria  |Opis   |
|----------|--------------|
| DirectionOfBodyStructure | Kierunek struktury treści. |
| DirectionOfCondition | Kierunek warunku. |
| DirectionOfExamination | Kierunek badania. |
| DirectionOfTreatment | Kierunek traktowania. |
| TimeOfCondition | Czas skojarzony z wystąpieniem początku warunku. |
| QualifierOfCondition | Skojarzony kwalifikator warunku. |
| DosageOfMedication | Dozowanie leków. |
| FormOfMedication | Forma leków. |
| RouteOfMedication | Trasa lub tryb konsumowania medycyny. Na przykład *doustnie*. |
| FrequencyOfMedication | Częstotliwość, z jaką jest zużywana leczenie. | 
| ValueOfCondition | Wartość liczbowa skojarzona z warunkiem. |
| UnitOfCondition | Jednostka (na przykład czas) skojarzona z warunkiem. |
| TimeOfMedication | Godzina, o której zostało zużyte leczenie. |
| TimeOfTreatment | Czas administrowania traktowaniem. | 
| FrequencyOfTreatment | Częstotliwość, z jaką jest administrowane traktowanie. |
| ValueOfExamination | Wartość liczbowa skojarzona z badaniem. | 
| UnitOfExamination | Jednostka (na przykład wartość procentowa) skojarzona z badaniem. |
| RelationOfExamination | Relacja między jednostką a badaniem. | 
| TimeOfExamination | Czas skojarzony z badaniem. |
| Skrót | Skrót.  | 
