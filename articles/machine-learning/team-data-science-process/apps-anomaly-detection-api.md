---
title: Interfejs API wykrywania anomalii Azure Machine Learning — proces nauki danych zespołu
description: Interfejs API wykrywania anomalii jest przykładem opartym na Microsoft Azure Machine Learning, które wykrywa anomalie w danych szeregów czasowych z wartościami liczbowymi, które są jednolicie przemieszczone w czasie.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305694"
---
# <a name="machine-learning-anomaly-detection-api"></a>Interfejs API wykrywania anomalii Machine Learning

> [!NOTE]
> Ten element jest w trakcie konserwacji. Zachęcamy do używania [usługi interfejsu API wykrywania anomalii](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) obsługiwanej przez galerię Machine Learning algorytmów w usłudze Azure Cognitive Services w celu wykrywania anomalii z metryk biznesowych, operacyjnych i IoT.

## <a name="overview"></a>Omówienie
[Interfejs API wykrywania anomalii](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) jest przykładem opartym na Azure Machine Learning, które wykrywa anomalie w danych szeregów czasowych z wartościami liczbowymi, które są jednolicie przemieszczone w czasie.

Ten interfejs API może wykrywać następujące typy nietypowych wzorców w danych szeregów czasowych:

* **Trendy pozytywne i negatywne**: na przykład podczas monitorowania użycia pamięci podczas obliczania trendu w górę może być istotne, ponieważ może to stanowić wskazówkę przecieku pamięci.
* **Zmiany w dynamicznym zakresie wartości**: na przykład podczas monitorowania wyjątków zgłoszonych przez usługę w chmurze wszelkie zmiany w dynamicznym zakresie wartości mogą wskazywać na niestabilność kondycji usługi i
* Liczby **i wartości DIP**: na przykład podczas monitorowania liczby niepowodzeń logowania w usłudze lub liczby wyewidencjonowania w witrynie handlu elektronicznego, skoki lub DIP mogą wskazywać nietypowe zachowanie.

Te detektory uczenia maszynowego śledzą takie zmiany w wartościach w czasie i raportują bieżące zmiany wartości jako wyniki anomalii. Nie wymagają strojenia progu AdHoc, a ich wyniki mogą służyć do kontrolowania fałszywej dodatniej stawki. Interfejs API wykrywania anomalii jest przydatny w kilku scenariuszach, takich jak monitorowanie usługi przez śledzenie kluczowych wskaźników wydajności w czasie, monitorowanie użycia za pomocą metryk, takich jak liczba wyszukiwań, liczba kliknięć, monitorowanie wydajności za pomocą liczników, takich jak pamięć, procesor, odczyty plików itp.

Oferta wykrywania anomalii zawiera przydatne narzędzia umożliwiające rozpoczęcie pracy.

* [Aplikacja sieci Web](https://anomalydetection-aml.azurewebsites.net/) pomaga w ocenie i wizualizowaniu wyników interfejsów API wykrywania anomalii na danych.

> [!NOTE]
> Wypróbuj **rozwiązanie "anomalia Insights** " obsługiwane przez [ten interfejs API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Wdrażanie interfejsu API
Aby można było korzystać z interfejsu API, należy wdrożyć go w ramach subskrypcji platformy Azure, w której będzie ona hostowana jako usługa sieci Web Azure Machine Learning.  Można to zrobić z poziomu [Azure AI Gallery](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2).  Spowoduje to wdrożenie dwóch Azure Machine Learning Studio (klasycznych) usług sieci Web (i ich powiązanych zasobów) w ramach subskrypcji platformy Azure — jednej do wykrywania anomalii z wykrywaniem sezonowości i jednego bez wykrywania sezonowości.  Po zakończeniu wdrożenia będziesz mieć możliwość zarządzania interfejsami API na stronie [usług sieci web Azure Machine Learning Studio (klasyczna)](https://services.azureml.net/webservices/) .  Na tej stronie będzie można znaleźć lokalizacje punktów końcowych, klucze interfejsu API, a także przykładowy kod służący do wywoływania interfejsu API.  Bardziej szczegółowe instrukcje są dostępne [tutaj](../classic/manage-new-webservice.md).

## <a name="scaling-the-api"></a>Skalowanie interfejsu API
Domyślnie wdrożenie będzie miało bezpłatny plan rozliczania dla tworzenia i testowania, który obejmuje 1 000 transakcji na miesiąc i 2 godziny obliczeniowe miesięcznie.  Możesz przeprowadzić uaktualnienie do innego planu zgodnie z potrzebami.  Szczegółowe informacje na temat cen różnych planów są dostępne w [tym miejscu](https://azure.microsoft.com/pricing/details/machine-learning/) w obszarze "ceny produkcyjnego interfejsu API sieci Web".

## <a name="managing-aml-plans"></a>Zarządzanie planami AML
Plan rozliczeniowy możesz zarządzać [tym miejscu](https://services.azureml.net/plans/).  Nazwa planu będzie określana na podstawie nazwy grupy zasobów wybranej podczas wdrażania interfejsu API oraz ciągu, który jest unikatowy dla Twojej subskrypcji.  Instrukcje dotyczące sposobu uaktualniania planu są dostępne w [tym miejscu](../classic/manage-new-webservice.md) w sekcji "Zarządzanie planami rozliczeń".

## <a name="api-definition"></a>Definicja interfejsu API
Usługa sieci Web udostępnia interfejs API oparty na protokole REST za pośrednictwem protokołu HTTPS, który można wykorzystać na różne sposoby, w tym aplikacje internetowe lub mobilne, R, Python, Excel itd.  Dane szeregów czasowych są wysyłane do tej usługi za pośrednictwem wywołania interfejsu API REST i uruchamiają kombinację trzech typów anomalii opisanych poniżej.

## <a name="calling-the-api"></a>Wywoływanie interfejsu API
Aby wywołać interfejs API, należy znać lokalizację punktu końcowego i klucz interfejsu API.  Te dwa wymagania oraz przykładowy kod służący do wywoływania interfejsu API są dostępne na stronie [usług sieci web Azure Machine Learning Studio (klasycznych)](https://services.azureml.net/webservices/) .  Przejdź do żądanego interfejsu API, a następnie kliknij kartę "zużyte", aby je znaleźć.  Interfejs API można wywołać jako interfejs API struktury Swagger (czyli przy użyciu parametru adresu URL `format=swagger` ) lub jako interfejs API spoza struktury Swagger (czyli bez `format` parametru adresu URL).  Przykładowy kod używa formatu Swagger.  Poniżej znajduje się przykładowe żądanie i odpowiedź w formacie innym niż Swagger.  Te przykłady odnoszą się do punktu końcowego sezonowości.  Punkt końcowy inny niż sezonowości jest podobny.

### <a name="sample-request-body"></a>Przykładowa treść żądania
Żądanie zawiera dwa obiekty: `Inputs` i `GlobalParameters` .  W przykładowym żądaniu poniżej niektóre parametry są wysyłane jawnie, podczas gdy inne nie są (przewiń w dół, aby uzyskać pełną listę parametrów dla każdego punktu końcowego).  Parametry, które nie są jawnie wysyłane w żądaniu, będą używały wartości domyślnych podanej poniżej.

```json
{
            "Inputs": {
                    "input1": {
                            "ColumnNames": ["Time", "Data"],
                            "Values": [
                                    ["5/30/2010 18:07:00", "1"],
                                    ["5/30/2010 18:08:00", "1.4"],
                                    ["5/30/2010 18:09:00", "1.1"]
                            ]
                    }
            },
    "GlobalParameters": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "bileveldetector.sensitivity": "3.25",
        "detectors.spikesdips": "Both"
    }
}
```

### <a name="sample-response"></a>Przykładowa odpowiedź
Aby wyświetlić `ColumnNames` pole, należy dołączyć `details=true` jako parametr adresu URL w żądaniu.  Zapoznaj się z poniższymi tabelami znaczenie dla każdego z tych pól.

```json
{
    "Results": {
        "output1": {
            "type": "table",
            "value": {
                "Values": [
                    ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                    ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                    ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                ],
                "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
            }
        }
    }
}
```


## <a name="score-api"></a>Interfejs API oceny
Interfejs API oceny służy do uruchamiania wykrywania anomalii w danych szeregów czasowych innych niż sezonowe. Interfejs API uruchamia wiele wykryć anomalie danych i zwraca wyniki anomalii.
Na poniższym rysunku przedstawiono przykład anomalii, które może wykryć interfejs API oceny. Ta seria czasowa ma dwie zmiany odrębnego poziomu i trzy skoki. Czerwona kropka pokazuje czas, w którym wykryto zmianę poziomu, podczas gdy czarne kropki pokazują wykryte skoki.
![Interfejs API oceny][1]

### <a name="detectors"></a>Detektory
Interfejs API wykrywania anomalii obsługuje detektory w trzech różnych kategoriach. Szczegółowe informacje o określonych parametrach wejściowych i danych wyjściowych dla każdego detektora można znaleźć w poniższej tabeli.

| Kategoria detektora | EAS | Opis | Parametry wejściowe | Dane wyjściowe |
| --- | --- | --- | --- | --- |
| Przeskoki detektory |Detektor TSpike |Wykrywaj skoki i DIP na podstawie wartości z pierwszego i trzeciego kwartyl |*tspikedetector. czułość:* Pobiera wartość całkowitą z zakresu 1-10, wartość domyślna: 3; Wyższe wartości będą przechwytywać bardziej rygorystyczne wartości w taki sposób, aby była mniej wrażliwa |TSpike: wartości binarne — "1", jeśli wykryto skok/DIP, "0" w przeciwnym razie |
| Przeskoki detektory | Detektor ZSpike |Wykrywanie skoków i wartości DIP na podstawie ich średniej |*zspikedetector. czułość:* Weź wartość całkowitą z zakresu 1-10, wartość domyślna: 3; Wyższe wartości spowodują przechwycenie bardziej rygorystycznych wartości, co zmniejsza ich czułość |ZSpike: wartości binarne — "1", jeśli wykryto skok/DIP, "0" w przeciwnym razie |
| Detektor wolnych trendów |Detektor wolnych trendów |Wykrywanie powolnej dodatniej tendencji zgodnie z ustawieniem czułości |*trenddetector. czułość:* próg dla wyniku czujnika (wartość domyślna: 3,25, 3,25 – 5 jest rozsądnym zakresem do wyboru; Im wyższa mniejsza czułość) |tscore: liczba zmiennoprzecinkowa reprezentująca wynik anomalii na trendzie |
| Detektory zmiany poziomu | Detektor zmiany poziomu dwukierunkowego |Wykrywaj zmiany w górę i w dół zgodnie z ustawieniem czułości |*bileveldetector. czułość:* próg dla wyniku czujnika (wartość domyślna: 3,25, 3,25 – 5 jest rozsądnym zakresem do wyboru; Im wyższa mniejsza czułość) |rpscore: liczba zmiennoprzecinkowa reprezentująca wynik nietypowej zmiany poziomu w górę i w dół |

### <a name="parameters"></a>Parametry
Więcej szczegółowych informacji na temat tych parametrów wejściowych przedstawiono w poniższej tabeli:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| detektory. historywindow |Historia (w liczbie punktów danych) używana do obliczania wyniku anomalii |500 |liczba całkowita |10-2000 |Zależne od serii czasu |
| detektory. spikesdips | Określa, czy mają być wykrywane tylko wartości graniczne, tylko wartości DIP czy oba |Oba |wyliczany |Oba, skoki, DIP |Oba |
| bileveldetector. czułość |Czułość wykrywania zmian poziomu dwukierunkowego. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej informacji poufnych) |
| trenddetector. czułość |Czułość pozytywnego detektora trendu. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej informacji poufnych) |
| tspikedetector. czułość |Czułość czujnika TSpike |3 |liczba całkowita |1-10 |3-5 (mniejsze wartości oznaczają bardziej poufne) |
| zspikedetector. czułość |Czułość czujnika ZSpike |3 |liczba całkowita |1-10 |3-5 (mniejsze wartości oznaczają bardziej poufne) |
| postprocess.tailRows |Liczba najnowszych punktów danych, które mają być przechowywane w wynikach wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych) lub określ liczbę punktów, które mają być zachowane w wynikach |Nie dotyczy |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie detektory danych szeregów czasowych i zwraca wyniki anomalii oraz wskaźniki wartości binarnych dla każdego punktu w czasie. W poniższej tabeli przedstawiono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Godzina |Sygnatury czasowe z danych nieprzetworzonych lub zagregowane (i/lub) dane kalkulacyjne, jeśli agregacja (i/lub) nie ma zastosowania przypisywania danych |
| Dane |Wartości z danych pierwotnych lub agregowane (i/lub) dane kalkulacyjne w przypadku agregacji (i/lub) braku przypisywania danych |
| TSpike |Wskaźnik binarny wskazujący, czy skok został wykryty przez detektor TSpike |
| ZSpike |Wskaźnik binarny wskazujący, czy skok został wykryty przez detektor ZSpike |
| rpscore |Liczba zmiennoprzecinkowa reprezentująca wynik anomalii na zmianę poziomu dwukierunkowego |
| rpalert |1/0 wartość wskazującą, że istnieje zmiana poziomu dwukierunkowego na podstawie czułości danych wejściowych |
| tscore |Liczba zmiennoprzecinkowa reprezentująca wynik anomalii na dodatnim trendzie |
| talert |1/0 wartość wskazującą, że występuje pozytywna anomalia trendu na podstawie czułości danych wejściowych |

## <a name="scorewithseasonality-api"></a>Interfejs API ScoreWithSeasonality
Interfejs API ScoreWithSeasonality jest używany do uruchamiania wykrywania anomalii w szeregach czasowych, które mają wzorce sezonowe. Ten interfejs API jest przydatny do wykrywania odchyleń w wzorcach sezonowych.
Na poniższej ilustracji przedstawiono przykład anomalii wykrytych w szeregach czasowych. Szeregi czasowe ma jeden skok (pierwsza czarna kropka), dwa DIP (druga czarna kropka i jeden na końcu) i jedną zmianę poziomu (czerwona kropka). Zarówno adres DIP w środku szeregów czasowych, jak i zmiana poziomu są discernable po usunięciu składników sezonowych z serii.
![Interfejs API sezonowości][2]

### <a name="detectors"></a>Detektory
Detektory w punkcie końcowym sezonowości są podobne do tych w punkcie końcowym innym niż sezonowości, ale mają nieco inne nazwy parametrów (wymienione poniżej).

### <a name="parameters"></a>Parametry

Więcej szczegółowych informacji na temat tych parametrów wejściowych przedstawiono w poniższej tabeli:

| Parametry wejściowe | Opis | Ustawienie domyślne | Typ | Prawidłowy zakres | Sugerowany zakres |
| --- | --- | --- | --- | --- | --- |
| Przetwarzanie wstępne. aggregationInterval |Interwał agregacji w sekundach dla agregacji wejściowej serii czasowej |0 (nie wykonano agregacji) |liczba całkowita |0: Pomiń agregację, > 0 w przeciwnym razie |od 5 minut do 1 dnia, zależne od serii czasu |
| Przetwarzanie wstępne. aggregationFunc |Funkcja używana do agregowania danych w określonym AggregationInterval |średnia |wyliczany |Średnia, suma, Długość |Nie dotyczy |
| Przetwarzanie wstępne. replaceMissing |Wartości używane do przerzucania brakujących danych |LKV (Ostatnia znana wartość) |wyliczany |zero, LKV, średnia |Nie dotyczy |
| detektory. historywindow |Historia (w liczbie punktów danych) używana do obliczania wyniku anomalii |500 |liczba całkowita |10-2000 |Zależne od serii czasu |
| detektory. spikesdips | Określa, czy mają być wykrywane tylko wartości graniczne, tylko wartości DIP czy oba |Oba |wyliczany |Oba, skoki, DIP |Oba |
| bileveldetector. czułość |Czułość wykrywania zmian poziomu dwukierunkowego. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej informacji poufnych) |
| postrenddetector. czułość |Czułość pozytywnego detektora trendu. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej informacji poufnych) |
| negtrenddetector. czułość |Czułość ujemnego detektora trendu. |3.25 |double |Brak |3,25-5 (mniejsze wartości oznaczają więcej informacji poufnych) |
| tspikedetector. czułość |Czułość czujnika TSpike |3 |liczba całkowita |1-10 |3-5 (mniejsze wartości oznaczają bardziej poufne) |
| zspikedetector. czułość |Czułość czujnika ZSpike |3 |liczba całkowita |1-10 |3-5 (mniejsze wartości oznaczają bardziej poufne) |
| sezonowości. Enable |Czy analiza sezonowości ma być wykonywana |true |boolean |wartość true, false |Zależne od serii czasu |
| sezonowości. numSeasonality |Maksymalna liczba okresowych cykli do wykrycia |1 |liczba całkowita |1, 2 |1-2 |
| sezonowości. Transform |Czy składniki trendów sezonowych (i) zostaną usunięte przed zastosowaniem wykrywania anomalii |w okresie |wyliczany |Brak, depora, deseasontrend |Nie dotyczy |
| postprocess.tailRows |Liczba najnowszych punktów danych, które mają być przechowywane w wynikach wyjściowych |0 |liczba całkowita |0 (Zachowaj wszystkie punkty danych) lub określ liczbę punktów, które mają być zachowane w wynikach |Nie dotyczy |

### <a name="output"></a>Dane wyjściowe
Interfejs API uruchamia wszystkie detektory danych szeregów czasowych i zwraca wyniki anomalii oraz wskaźniki wartości binarnych dla każdego punktu w czasie. W poniższej tabeli przedstawiono dane wyjściowe z interfejsu API.

| Dane wyjściowe | Opis |
| --- | --- |
| Godzina |Sygnatury czasowe z danych nieprzetworzonych lub zagregowane (i/lub) dane kalkulacyjne, jeśli agregacja (i/lub) nie ma zastosowania przypisywania danych |
| OriginalData |Wartości z danych pierwotnych lub agregowane (i/lub) dane kalkulacyjne w przypadku agregacji (i/lub) braku przypisywania danych |
| ProcessedData |Jedną z następujących opcji: <ul><li>Okresowe dostosowanie szeregów czasowych w przypadku wykrycia znaczących sezonowości i wybrania opcji Rozpory.</li><li>szeregowo skorygowane i nietrendy czasowe w przypadku wykrycia znaczącej sezonowości i wybrania opcji deseasontrend</li><li>w przeciwnym razie ta opcja jest taka sama jak OriginalData</li> |
| TSpike |Wskaźnik binarny wskazujący, czy skok został wykryty przez detektor TSpike |
| ZSpike |Wskaźnik binarny wskazujący, czy skok został wykryty przez detektor ZSpike |
| BiLevelChangeScore |Liczba zmiennoprzecinkowa reprezentująca wynik anomalii na poziomie zmiany |
| BiLevelChangeAlert |1/0 wartość wskazującą, że istnieje zmiana poziomu anomalii na podstawie czułości danych wejściowych |
| PosTrendScore |Liczba zmiennoprzecinkowa reprezentująca wynik anomalii na dodatnim trendzie |
| PosTrendAlert |1/0 wartość wskazującą, że występuje pozytywna anomalia trendu na podstawie czułości danych wejściowych |
| NegTrendScore |Liczba zmiennoprzecinkowa reprezentująca wynik anomalii dla ujemnego trendu |
| NegTrendAlert |1/0 wartość wskazującą, że występuje negatywna anomalia trendu na podstawie czułości danych wejściowych |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png