---
title: Nazwane jednostki na potrzeby opieki zdrowotnej
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599315"
---
[Analiza tekstu procesów kondycji](../../how-tos/text-analytics-for-health.md) i wyodrębniania szczegółowych informacji z danych medycznych bez struktury. Usługa wykrywa i wyświetla koncepcje medyczne, przypisuje potwierdzenia do koncepcji, ustala relacje semantyczne między pojęciami i łączy je ze wspólnym ontologie lekarskim.

Analiza tekstu na potrzeby kondycji wykrywa koncepcje medyczne w następujących kategoriach. W tej wersji zapoznawczej jest obsługiwany tylko tekst w języku angielskim i dostępna jest tylko jedna wersja modelu.

| Kategoria  | Opis  |
|---------|---------|
| [ANATOMY](#anatomy) | pojęcia, które przechwytują informacje o systemach treści i anatomicznych, witrynach, lokalizacjach lub regionach. |
 | [DANE demograficzne](#demographics) | pojęcia, które przechwytują informacje o płci i wieku. |
 | [BADAWCZ](#examinations) | pojęcia, które przechwytują informacje o procedurach i testach diagnostycznych. |
 | [OGÓLNE ATRYBUTY](#general-attributes) | pojęcia, które zawierają więcej informacji na temat innych koncepcji z powyższych kategorii. |
 | [GENOMICS](#genomics) | pojęcia, które przechwytują informacje o genów i odmianach. |
 | [ZDROWOTNE](#healthcare) | pojęcia, które przechwytują informacje o zdarzeniach administracyjnych, środowiskach opieki i zawódu opieki zdrowotnej. |
 | [WARUNEK MEDYCZNY](#medical-condition) | pojęcia, które przechwytują informacje o diagnozowaniu, objawy lub znakach. |
 | [LEKI](#medication) | koncepcje, które przechwytują informacje dotyczące leczenia, w tym nazwy leczenia, klasy, dozowanie i trasy administracyjne. |
 | [PRACODAWC](#social) | pojęcia, które przechwytują informacje o istotnych aspektach społecznościowych, takich jak związek rodziny. |
 | [POSTĘPOWANIE](#treatment) | pojęcia, które przechwytują informacje o procedurach terapeutycznych. |

Więcej informacji i przykłady znajdziesz poniżej.

## <a name="anatomy"></a>Anatomy

### <a name="entities"></a>Jednostki

Systemy **BODY_STRUCTURE** , lokalizacje lub regiony anatomiczne oraz Lokacje podstawowe. Na przykład ARM, kolano, brzuch, nos, wątroby, głowy, układ oddechowy, limfocyty.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Przykład jednostki struktury treści.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Rozwinięty przykład jednostki struktury treści.":::

## <a name="demographics"></a>Dane demograficzne

### <a name="entities"></a>Jednostki

**Wiek** — wszystkie warunki wieku i frazy, w tym dla pacjentów, członków rodziny i innych. Na przykład 40-Year-Old, 51 yo, 3 miesiące Old, dorosły, niemowląt, starszych, młodych, drobnych i średnich firm.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Przykład jednostki wiekowej.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Inny przykład jednostki wiekowej.":::


Warunki **płciowe** , które ujawniają płeć podmiotu. Na przykład męski, Kobieta, Kobieta, Gentleman, Lady.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Przykład jednostki płci.":::

## <a name="examinations"></a>Badania

### <a name="entities"></a>Jednostki

**EXAMINATION_NAME** — procedury diagnostyczne i testy, w tym istotne oznaki i pomiary ciała. Na przykład MRI, ECG, test HIV, hemoglobin, liczba płytek, systemy skalowania, takie jak *skalowanie Bristol*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Przykład jednostki egzaminu.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Inny przykład jednostki nazwa badania.":::

## <a name="general-attributes"></a>Ogólne atrybuty

### <a name="entities"></a>Jednostki

**Data** — pełna Data odnosząca się do stanu medycznego, badania, leczenia, leczenia lub zdarzenia administracyjnego.

**Kierunek** — warunki kierunkowe, które mogą odnosić się do struktury treści, warunków leczenia, badania lub leczenia, takich jak: Left, boczne, wielkie i poboczne.

**Częstotliwość** — opisuje, jak często wystąpił stan medyczny, badanie, leczenie lub leczenie, występuje lub powinien wystąpić.

**MEASUREMENT_VALUE** — wartość związana z badaniem lub pomiarem warunków medycznych.

**MEASUREMENT_UNIT** — jednostka miary związana z badaniem lub pomiarem warunków medycznych.

Wyrażenia **RELATIONAL_OPERATOR** , które wyrażają relację ilościową między jednostką a dodatkowymi informacjami.

**Czas** okresowe terminy odnoszące się do początku i/lub długości (czasu trwania) warunków leczenia, badania, traktowanie, leczenie lub zdarzenie administracyjne. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Jednostki

**GENE_OR_PROTEIN** — wszystkie wzmianki dotyczące nazw i symboli ludzkich genów oraz chromosomów i części chromosomów oraz białek. Na przykład MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Przykład jednostki genu.":::

**Variant** — wszystkie wzmianki o odmianach genu i mutacjach. Na przykład `c.524C>T` , `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Opieka zdrowotna

### <a name="entities"></a>Jednostki
  
**ADMINISTRATIVE_EVENT** — zdarzenia odnoszące się do systemu opieki zdrowotnej, ale w charakterze administracyjnym/pośrednim. Na przykład rejestracja, przyjmowanie, okres próbny, wpis badania, transfer, zrzut, hospitalizacja, pobyt szpitalowy. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Przykład jednostki zdarzeń opieki zdrowotnej.":::

**CARE_ENVIRONMENT** — środowisko lub lokalizację, w której są nadawane pacjentów. Na przykład pomieszczenie awaryjne, lekarska, Biuro cardio, Hospice, szpital.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Ten zrzut ekranu przedstawia przykład jednostki środowiska opieki zdrowotnej.":::

**HEALTHCARE_PROFESSION** — prawnik praktykujący lub nielicencjonowany. Na przykład, z wcięciami, pathologist, neurologist, radiologist, Pharmacist, odżywiania, fizyczne Therapist, Chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Ten zrzut ekranu przedstawia inny przykład jednostki środowiska opieki zdrowotnej.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Inny przykład jednostki środowiska opieki zdrowotnej.":::

## <a name="medical-condition"></a>Warunek medyczny

### <a name="entities"></a>Jednostki

**Diagnostyka** — choroba, Syndrome, zatrucie. Na przykład raka piersika, Alzheimer, HTN, CHF, uszkodzenie rdzenia.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Przykład jednostki warunku medycznego.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Inny przykład jednostki warunku medycznego.":::

**SYMPTOM_OR_SIGN** — subiektywne lub obiektywne dowody chorobowe lub inne diagnozy. Na przykład klatka piersiowa, kłopotliwej, Dizziness, Rash, SOB, brzuch była miękka, dobre bowel Sounds, dobrze nourished.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Przykład znaku warunku medycznego lub podmiotu objawu.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Inny przykład znaku warunku medycznego lub elementu objawu.":::

**CONDITION_QUALIFIER** — warunki jakościowe, które są używane do opisywania warunków medycznych. Wszystkie następujące podkategorie są uważane za kwalifikatory:

1.  Wyrażenia dotyczące czasu: są to terminy opisujące wymiar czasu, na przykład nagły, ostry, przewlekły, od dawna dba. 
2.  Wyrażenia jakości: są to warunki opisujące "naturę" warunków medycznych, takich jak nagrywanie, ostrość.
3.  Wyrażenia ważności: poważny, łagodny, bitowy, niekontrolowany.
4.  Wyrażenia Extensivity: lokalna, ogniskowa, rozpraszanie.
5.  Wyrażenia promieniowania: promieniujące, promieniowanie.
6.  Skala warunku: w niektórych przypadkach warunek jest scharakteryzowany przez skalę, która jest skończoną uporządkowaną listą wartości. Na przykład pacjentów z pancreatic raka fazy III.
7.  Warunek kursu: termin, który odnosi się do kursu lub postępu w przypadku warunku, takiego jak poprawa, pogorszenie, rozwiązanie, remisja. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Przykład atrybutu kwalifikatora warunku i jednostki diagnostyki.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Inny przykład atrybutu kwalifikatora warunku i jednostki diagnostyki.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Przykład atrybutu kwalifikatora warunku z objawami i środkami leków.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Ten zrzut ekranu przedstawia inny przykład atrybutu kwalifikatora warunku z jednostką diagnostyki.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Ten zrzut ekranu przedstawia dodatkowy przykład atrybutu kwalifikatora warunku z jednostką diagnostyki.":::

## <a name="medication"></a>Leki

### <a name="entities"></a>Jednostki

**MEDICATION_CLASS** — zestaw medications, który ma podobny mechanizm działania, związany tryb działania, podobną strukturę chemiczną i/lub są używane do traktowania tej samej choroby. Na przykład, inhibitor ACE, opioid, antybiotyki, bólu.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Przykład jednostki klasy leków.":::

**MEDICATION_NAME** — wzmianki dotyczące leków, w tym nazwy marki z prawami autorskimi i nazwy nienależące do marki. Na przykład ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Przykład jednostki nazw leków.":::

**Dozowanie** — ilość zamówionej leków. Na przykład Dodaj roztworu chlorku sodowego *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Przykład atrybutu dozowania leków.":::

**MEDICATION_FORM** — forma leczenia. Na przykład rozwiązanie, Pill, kapsułka, tablet, poprawka, żel, pasta, odrzucanie, śmietanka, syrop.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Przykład atrybutu formularza leków.":::

**MEDICATION_ROUTE** — Metoda administracyjna leków. Na przykład Doustne, vaginal, IV, epidural, istotna, wdychane.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Przykład atrybutu trasy leków.":::

## <a name="social"></a>Funkcje społecznościowe

### <a name="entities"></a>Jednostki

**FAMILY_RELATION** — wzmianki dotyczące rodziny tematu. Na przykład, ojciec, element równorzędny, elementy nadrzędne.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Zrzut ekranu przedstawia inny przykład atrybutu czasu obróbki.":::

## <a name="treatment"></a>Postępowanie

### <a name="entities"></a>Jednostki

**TREATMENT_NAME** — procedury terapeutyczne. Na przykład, Chirurgia zastępowanie kolan, szpik kostny Transplant, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Przykład jednostki nazw traktowanych.":::
