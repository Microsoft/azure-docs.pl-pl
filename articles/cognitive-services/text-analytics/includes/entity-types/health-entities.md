---
title: Nazwane jednostki na potrzeby opieki zdrowotnej
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 44bce7c6dd2443ea0ed1851325a5b84f325f5fae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779917"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>Analiza tekstu kategorii kondycji, jednostek i atrybutów

[Analiza tekstu na potrzeby kondycji](../../how-tos/text-analytics-for-health.md) wykrywa koncepcje medyczne w następujących kategoriach.  (Należy pamiętać, że w tej wersji zapoznawczej kontenera jest obsługiwany tylko tekst w języku angielskim, a w każdym obrazie kontenera jest dostępny tylko jeden model).


| Kategoria  | Opis  |
|---------|---------|
| ANATOMY | pojęcia, które przechwytują informacje o systemach treści i anatomicznych, witrynach, lokalizacjach lub regionach. |
 | DANE demograficzne | pojęcia, które przechwytują informacje o płci i wieku. |
 | BADAWCZ | pojęcia, które przechwytują informacje o procedurach i testach diagnostycznych. |
 | GENOMICS | pojęcia, które przechwytują informacje o genów i odmianach. |
 | ZDROWOTNE | pojęcia, które przechwytują informacje o zdarzeniach administracyjnych, środowiskach opieki i zawódu opieki zdrowotnej. |
 | WARUNEK MEDYCZNY | pojęcia, które przechwytują informacje o diagnozowaniu, objawach lub znakach. |
 | LEKI | pojęcia, które przechwytują informacje dotyczące leczenia, w tym nazwy leczenia, klasy, dozowanie i drogi administracyjne. |
 | PRACODAWC | pojęcia, które przechwytują informacje o istotnych aspektach społecznościowych, takich jak związek rodziny. |
 | POSTĘPOWANIE | pojęcia, które przechwytują informacje o procedurach terapeutycznych. |
  
Każda kategoria może obejmować dwie grupy koncepcji:

* **Jednostki** — warunki, które przechwytują koncepcje medyczne, takie jak diagnostyka, nazwa leczenia lub nazwa leczenia.  Na przykład *Bronchitis* jest diagnostyką *i jest to nazwa* leków.  Wzmianki w tej grupie mogą być połączone z IDENTYFIKATORem koncepcji UMLS.
* Wyrażenia o **atrybutach** , które dostarczają więcej informacji o wykrytych jednostkach, na przykład *poważny* jest kwalifikator warunku dla *Bronchitis* lub *81 mg* , jest dozowaniem dla *programu.*  Wzmianki w tej kategorii nie zostaną połączone z IDENTYFIKATORem koncepcji UMLS.

Ponadto usługa rozpoznaje relacje między różnymi koncepcjami, w tym relacjami między atrybutami i jednostkami, na przykład *kierunek* *struktury treści* lub *dozowanie* do *nazwy leczenia* i relacje między jednostkami na przykład w przypadku wykrywania skrótów.

## <a name="anatomy"></a>Anatomy

### <a name="entities"></a>Jednostki

Systemy **BODY_STRUCTURE** , lokalizacje lub regiony anatomiczne oraz Lokacje podstawowe. Na przykład ARM, kolano, brzuch, nos, wątroby, głowy, układ oddechowy, limfocyty.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Przykład jednostki struktury treści.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="attributes"></a>Atrybuty

**Kierunkowe** warunki, takie jak: Left, boczne, wielkie i przedniej, które charakteryzuje strukturę treści.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="supported-relations"></a>Obsługiwane relacje

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Dane demograficzne

### <a name="entities"></a>Jednostki

**Wiek** — wszystkie warunki wieku i zwroty, w tym osoby pacjenta, członków rodziny i inne. Na przykład 40-Year-Old, 51 yo, 3 miesiące Old, dorosły, niemowląt, starszych, młodych, drobnych i średnich firm.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Przykład jednostki struktury treści.":::


Warunki **płciowe** , które ujawniają płeć podmiotu. Na przykład męski, Kobieta, Kobieta, Gentleman, Lady.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="attributes"></a>Atrybuty

Wyrażenia **RELATIONAL_OPERATOR** , które wyrażają relację między jednostką demograficzną a dodatkowymi informacjami.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Przykład jednostki struktury treści.":::

## <a name="examinations"></a>Badania

### <a name="entities"></a>Jednostki

**EXAMINATION_NAME** — procedury diagnostyczne i testy. Na przykład MRI, ECG, test HIV, hemoglobin, liczba płytek, systemy skalowania, takie jak *skalowanie Bristol*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="attributes"></a>Atrybuty

**Kierunek** — warunki kierunkowe, które charakteryzują się badaniem.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**MEASUREMENT_UNIT** — jednostka badania. Na przykład w *hemoglobin > 9,5 g/dl*, termin *g/dl* jest jednostką dla testu *hemoglobin* .

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**MEASUREMENT_VALUE** — wartość badania. Na przykład w *hemoglobin > 9,5 g/dl*, termin *9,5* jest wartością dla testu *hemoglobin* .

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**RELATIONAL_OPERATOR** — frazy, które wyrażają relację między badaniem i dodatkowymi informacjami. Na przykład wymagana wartość pomiaru dla badanego celu.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**Czas** — terminy okresowe odnoszące się do początku i/lub długości (czas trwania) badania. Na przykład, gdy wystąpił test.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="supported-relations"></a>Obsługiwane relacje

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Jednostki

**Genu** — wszystkie wzmianki o genów. Na przykład MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Przykład jednostki struktury treści.":::

**Variant** — wszystkie wzmianki o odmianach genu. Na przykład c. 524C>T, (MTRR): r.1462_1557del96
  
## <a name="healthcare"></a>Opieka zdrowotna

### <a name="entities"></a>Jednostki
  
**ADMINISTRATIVE_EVENT** — zdarzenia odnoszące się do systemu opieki zdrowotnej, ale w charakterze administracyjnym/pośrednim. Na przykład rejestracja, przyjmowanie, okres próbny, wpis badania, transfer, zrzut, hospitalizacja, pobyt szpitalowy. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Przykład jednostki struktury treści.":::

**CARE_ENVIRONMENT** — środowisko lub lokalizację, w której są nadawane pacjentów. Na przykład pomieszczenie awaryjne, lekarska, Biuro cardio, Hospice, szpital.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Przykład jednostki struktury treści.":::

**HEALTHCARE_PROFESSION** — prawnik praktykujący lub nielicencjonowany. Na przykład, z wcięciami, pathologist, neurologist, radiologist, Pharmacist, odżywiania, fizyczne Therapist, Chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Przykład jednostki struktury treści.":::

## <a name="medical-condition"></a>Warunek medyczny

### <a name="entities"></a>Jednostki

**Diagnostyka** — choroba, Syndrome, zatrucie. Na przykład raka piersika, Alzheimer, HTN, CHF, uszkodzenie rdzenia.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Przykład jednostki struktury treści.":::

**SYMPTOM_OR_SIGN** — subiektywne lub obiektywne dowody chorobowe lub inne diagnozy. Na przykład klatka piersiowa, kłopotliwej, Dizziness, Rash, SOB, brzuch była miękka, dobre bowel Sounds, dobrze nourished.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="attributes"></a>Atrybuty

**CONDITION_QUALIFIER** — warunki dotyczące jakości, które są używane do opisywania warunków medycznych. Wszystkie następujące podkategorie są uważane za kwalifikatory:

1.  Wyrażenia dotyczące czasu: są to terminy opisujące wymiar czasu, na przykład nagły, ostry, przewlekły, od dawna dba. 
2.  Wyrażenia jakości: są to warunki opisujące "naturę" warunków medycznych, takich jak nagrywanie, ostrość.
3.  Wyrażenia ważności: poważny, łagodny, bitowy, niekontrolowany.
4.  Wyrażenia Extensivity: lokalna, ogniskowa, rozpraszanie.
5.  Wyrażenia promieniowania: promieniujące, promieniowanie.
6.  Skala warunku: w niektórych przypadkach warunek jest scharakteryzowany przez skalę, która jest skończoną uporządkowaną listą wartości. Na przykład pacjentów z pancreatic raka fazy III.
7.  Warunek kursu: termin, który odnosi się do kursu lub postępu w przypadku warunku, takiego jak poprawa, pogorszenie, rozwiązanie, remisja. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Przykład jednostki struktury treści.":::

**Kierunkowe** warunki, które charakteryzują się warunkiem medycyny ciała.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**Częstotliwość** — jak często wystąpił stan medyczny, występuje lub powinien wystąpić.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Przykład jednostki struktury treści.":::

**MEASUREMENT_UNIT** — jednostka, która charakteryzuje się warunkiem lekarskim. Na przykład w *1,5 x2x1 cm Tumor*, termin *cm* jest jednostką miary dla *Tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**MEASUREMENT_VALUE** — wartość, która charakteryzuje się warunkiem lekarskim. Na przykład w przypadku wartości *1,5 x2x1 cm Tumor*termin *1,5 x2x1* jest wartością miary dla *Tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Przykład jednostki struktury treści.":::

**RELATIONAL_OPERATOR** — wyrażenia, które wyrażają relację między warunkiem lekarskim, dodatkowe informacje. Na przykład wartość czasu lub pomiaru. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Przykład jednostki struktury treści.":::

**Czas** — terminy okresowe odnoszące się do początku i/lub długości (czasu trwania) warunku medycznego. Na przykład kiedy pojawił się objaw (początek) lub gdy wystąpiła choroba.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="supported-relations"></a>Obsługiwane relacje

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Leki

### <a name="entities"></a>Jednostki

**MEDICATION_CLASS** — zestaw medications, który ma podobny mechanizm działania, związany tryb działania, podobną strukturę chemiczną i/lub są używane do traktowania tej samej choroby. Na przykład, inhibitor ACE, opioid, antybiotyki, bólu.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Przykład jednostki struktury treści.":::

**MEDICATION_NAME** — wzmianki dotyczące leków, w tym nazwy marki z prawami autorskimi i nazwy nienależące do marki. Na przykład Advil, ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="attributes"></a>Atrybuty

**Dozowanie** — ilość zamówionej leków. Na przykład Dodaj roztworu chlorku sodowego *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Przykład jednostki struktury treści.":::

**Częstotliwość** — jak często należy podjąć leczenie.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Przykład jednostki struktury treści.":::

**MEDICATION_FORM** — forma leczenia. Na przykład rozwiązanie, Pill, kapsułka, tablet, poprawka, żel, pasta, odrzucanie, śmietanka, syrop.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Przykład jednostki struktury treści.":::

**MEDICATION_ROUTE** — Metoda administracyjna leków. Na przykład Doustne, vaginal, IV, epidural, istotna, wdychane.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Przykład jednostki struktury treści.":::

Wyrażenia **RELATIONAL_OPERATOR** , które wyrażają relację między leków a dodatkowymi informacjami. Na przykład wymagana wartość pomiaru.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Przykład jednostki struktury treści.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="supported-relations"></a>Obsługiwane relacje

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**
  
## <a name="treatment"></a>Postępowanie

### <a name="entities"></a>Jednostki

**TREATMENT_NAME** — procedury terapeutyczne. Na przykład, Chirurgia zastępowanie kolan, szpik kostny Transplant, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Przykład jednostki struktury treści.":::

### <a name="attributes"></a>Atrybuty

**Kierunkowe** warunki, które charakteryzują się traktowaniem.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Przykład jednostki struktury treści.":::

**Częstotliwość** — jak często występuje traktowanie lub powinno wystąpić.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Przykład jednostki struktury treści.":::
 
Wyrażenia **RELATIONAL_OPERATOR** , które wyrażają zależność między traktowaniem a dodatkowymi informacjami.  Na przykład ilość czasu przeniesiona z poprzedniej procedury.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Przykład jednostki struktury treści.":::

**Czas bezterminowy** dotyczący początku i/lub długości (czasu trwania) obróbki. Na przykład Data podania.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Przykład jednostki struktury treści.":::


### <a name="supported-relations"></a>Obsługiwane relacje

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**

## <a name="social"></a>Funkcje społecznościowe

### <a name="entities"></a>Jednostki

**FAMILY_RELATION** — wzmianki dotyczące rodziny tematu. Na przykład, ojciec, element równorzędny, elementy nadrzędne.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Przykład jednostki struktury treści.":::
