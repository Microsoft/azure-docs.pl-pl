---
title: Zarządzanie źródłami danych w klasyfikatorach metryk
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zarządzać źródłami danych, które zostały dodane do usługi Metric Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: de8d5d8e9fc5ce1df05cfd4c67ef146760e2c7e9
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043174"
---
# <a name="how-to-manage-your-data-feeds"></a>Instrukcje: Zarządzanie źródłami danych

Dowiedz się, jak zarządzać dołączonymi źródłami danych w usłudze Advisor metryk. Ten artykuł przeprowadzi Cię przez proces zarządzania źródłami danych w monitorze metryk.

## <a name="edit-a-data-feed"></a>Edytowanie strumieniowego źródła danych

> [!NOTE]
> Następujące szczegóły nie mogą zostać zmienione po utworzeniu strumieniowego źródła danych. 
> * Identyfikator strumieniowego źródła danych
> * Czas utworzenia
> * Wymiar
> * Typ źródła
> * Poziom szczegółowości

Tylko Administrator źródła danych może wprowadzać w nim zmiany. 

Aby wstrzymać lub ponownie uaktywnić strumieniowe źródło danych:

1. Na stronie Lista strumieniowego źródła danych kliknij operację, którą chcesz wykonać na strumieniowym źródle danych.

2. Na stronie Szczegóły strumieniowego źródła danych kliknij przycisk przełączania **stanu** .

Aby usunąć strumieniowe źródło danych: 

1. Na stronie Lista strumieniowego źródła danych kliknij pozycję **Usuń** w strumieniowym źródle danych.

2. Na stronie Szczegóły strumieniowego źródła danych kliknij przycisk **Usuń**.

Podczas zmiany czasu rozpoczęcia należy ponownie sprawdzić schemat. Można to zmienić przy użyciu **parametrów edycji**.

##  <a name="backfill-your-data-feed"></a>Wypełnianie strumieniowe źródła danych

Wybierz przycisk  **wypełnianie** , aby wyzwolić natychmiastowe pozyskiwanie w sygnaturze czasowej, aby naprawić nieudane pobieranie lub zastąpić istniejące dane.
- Godzina rozpoczęcia jest włącznie.
- Czas zakończenia jest na wyłączność.
- Wykrywanie anomalii jest wyzwalane tylko dla wybranego zakresu.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Strumieniowe źródło danych":::

## <a name="manage-permission-of-a-data-feed"></a>Zarządzanie uprawnieniami strumieniowego źródła danych

Dostęp do obszaru roboczego jest kontrolowany przez zasób klasyfikatora metryk, który używa Azure Active Directory do uwierzytelniania. Do danych metryk są stosowane inne warstwy kontroli uprawnień.

Klasyfikator metryk umożliwia przyznanie uprawnień różnym grupom osób w różnych źródłach danych. Istnieją dwa typy ról: 

- Administrator: kto ma pełne uprawnienia do zarządzania strumieniowym źródłem danych, w tym modyfikowaniem i usuwaniem.
- Podgląd: kto ma dostęp do widoku źródła danych tylko do odczytu.
 

## <a name="advanced-settings"></a>Ustawienia zaawansowane

Istnieje kilka opcjonalnych ustawień zaawansowanych podczas tworzenia nowego źródła danych, które można modyfikować na stronie szczegółów strumieniowego źródła danych.

### <a name="ingestion-options"></a>Opcje pozyskiwania

* **Przesunięcie czasu**pozyskiwania: Domyślnie dane są pozyskane zgodnie z podaną szczegółowością. Na przykład Metryka z *dzienną* sygnaturą czasową będzie pobierana jeden dzień po sygnaturze czasowej. Możesz użyć przesunięcia, aby opóźnić czas pozyskiwania z liczbą *dodatnią* , lub uzyskać wartość *ujemną* .

* **Maks. współbieżność**: Ustaw ten parametr, jeśli źródło danych obsługuje ograniczoną współbieżność. W przeciwnym razie pozostaw ustawienie domyślne.

* **Zatrzymaj ponowną próbę po**: w przypadku niepowodzenia pozyskiwania danych ponowienie zostanie ponowione automatycznie w danym okresie. Początek okresu to godzina, o której nastąpiło pierwsze pozyskiwanie danych. Długość okresu jest definiowana według stopnia szczegółowości. W przypadku pozostawienia wartości domyślnej (-1) wartość zostanie określona zgodnie z szczegółowością poniżej.
    
    | Poziom szczegółowości       | Zatrzymaj ponowną próbę po           |
    | :------------ | :--------------- |
    | Codziennie, niestandardowy (>= 1 dzień), co tydzień, co miesiąc, co rok     | 7 dni          |
    | Co godzinę, niestandardowy (< 1 dzień)       | 72 godzin |

* Minimalny **interwał ponawiania prób**: można określić minimalny interwał podczas ponawiania próby ściągania danych ze źródła. W przypadku pozostawienia wartości domyślnej (-1) interwał ponownych prób zostanie określony zgodnie z szczegółowością poniżej.
    
    | Poziom szczegółowości       | Minimalny interwał ponawiania prób           |
    | :------------ | :--------------- |
    | Codziennie, niestandardowy (>= 1 dzień), co tydzień, co miesiąc     | 30 minut          |
    | Co godzinę, niestandardowy (< 1 dzień)      | 10 minut |
    | Rocznie | 1 dzień          |
 
### <a name="fill-gap-when-detecting"></a>Przerwanie wypełniania podczas wykrywania: 

> [!NOTE]
> To ustawienie nie będzie miało wpływu na źródło danych i nie będzie miało wpływu na wykresy danych wyświetlane w portalu. Funkcja automatycznego wypełniania występuje tylko podczas wykrywania anomalii.

Niektóre serie czasowe nie są ciągłe. Gdy brakuje punktów danych, Doradca metryk użyje określonej wartości, aby wypełnić je przed anomalią wykrywania w celu uzyskania lepszej dokładności.
Dostępne są następujące opcje: 

* Użycie wartości z poprzedniego rzeczywistego punktu danych. Ta wartość jest używana domyślnie.
* Przy użyciu określonej wartości.

### <a name="action-link-template"></a>Szablon linku akcji: 

Szablony linków akcji służą do wstępnego definiowania adresów URL http, które składają się z symboli zastępczych,,, `%datafeed` `%metric` `%timestamp` `%detect_config` i `%tagset` . Możesz użyć szablonu, aby przekierować z anomalii lub zdarzenia do określonego adresu URL w celu przechodzenia do szczegółów.

:::image type="content" source="../media/action-link-template.png" alt-text="Strumieniowe źródło danych" lightbox="../media/action-link-template.png":::

Po wypełnieniu linku Akcja kliknij pozycję **Przejdź do linku akcja** na liście Akcja i kliknij prawym przyciskiem myszy drzewo zdarzeń. Zastąp symbole zastępcze w szablonie linku akcji odpowiednimi wartościami anomalii lub incydentu.

| Symbol zastępczy | Przykłady | Komentarz |
| ---------- | -------- | ------- |
| `%datafeed` | - | Identyfikator strumieniowego źródła danych |
| `%metric` | - | Identyfikator metryki |
| `%detect_config` | - | Wykryj Identyfikator konfiguracji |
| `%timestamp` | - | Znacznik czasu anomalii lub czasu zakończenia zdarzenia trwałego |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Wartości wymiarów anomalii lub najwyższej anomalii zdarzenia.   <br> `filterVal`Służy do filtrowania pasujących wartości w nawiasach kwadratowych.   |

Pokazują

* Jeśli szablon łącza akcji ma wartość `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * Link akcji `https://action-link/metric/1234?detectConfigId=2345` spowoduje przejście do anomalii lub incydentów w obszarze Metryka `1234` i Wykryj konfigurację `2345` .

* Jeśli szablon łącza akcji ma wartość `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * Łącze akcji będzie mieć związek `https://action-link?Dim1=Val1&Dim2=Val2` z nieanomalią `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * Łącze akcji może być `https://action-link?Dim2=Val2` nietypowe `{ "Dim1": "", "Dim2": "Val2" } ` , ponieważ `[Dim1=***&]` jest pomijane dla pustego ciągu wartości wymiaru. 

* Jeśli szablon łącza akcji ma wartość `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * Łącze akcji może wystąpić `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` , gdy anomalia jest `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * Łącze akcji może wystąpić, `https://action-link?filter=Name/Dim2 eq 'Val2'` gdy anomalia jest `{ "Dim1": "", "Dim2": "Val2" }` `[Name/Dim1 eq '***' and ]` pomijana dla pustego ciągu wartości wymiaru. 
   
### <a name="data-feed-not-available-alert-settings"></a>Ustawienia alertu "strumieniowe źródło danych nie jest dostępne"

Strumieniowe źródło danych jest uznawane za niedostępną, jeśli nie pozyskuje danych ze źródła w okresie prolongaty określonym od momentu rozpoczęcia pozyskiwania strumieniowego źródła danych. W takim przypadku jest wyzwalany alert.

Aby skonfigurować alert, należy najpierw [utworzyć punkt zaczepienia](alerts.md#create-a-hook) . Alerty będą wysyłane za pomocą skonfigurowanego punktu zaczepienia.

* **Okres prolongaty**: ustawienie okresu prolongaty służy do określania, kiedy ma być wysyłany alert, jeśli nie zostaną pozyskane żadne punkty danych. Punkt odniesienia to czas pierwszego pozyskiwania. Jeśli pozyskiwanie nie powiedzie się, Doradca metryk będzie podejmować próby w regularnych odstępach czasu określonych przez stopień szczegółowości. Jeśli okres prolongaty zostanie zakończony niepowodzeniem, zostanie wysłany alert.

* **Odmowa: gdy**ta opcja jest ustawiona na zero, każdy znacznik czasu z *niedostępnym* wyzwala alert. Gdy określone jest ustawienie inne niż zero, ciągłe znaczniki czasu po pierwszej sygnaturze czasowej z *niedostępnymi* nie są wyzwalane zgodnie z określonym ustawieniem.

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)
- [Dostosowywanie wykrywania anomalii przy użyciu opinii](anomaly-feedback.md)
- [Diagnozuj zdarzenie](diagnose-incident.md).
