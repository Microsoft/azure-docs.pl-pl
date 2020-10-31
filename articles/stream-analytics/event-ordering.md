---
title: Konfigurowanie zasad określania kolejności zdarzeń dla Azure Stream Analytics
description: W tym artykule opisano sposób konfigurowania nawet określania kolejności ustawień w Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 80567a211f08d6322c80b6645f8b70ec7df64b59
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130667"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurowanie zasad określania kolejności zdarzeń dla Azure Stream Analytics

W tym artykule opisano sposób konfigurowania i używania późnego przybycia i zasad dotyczących zdarzeń poza kolejnością w programie Azure Stream Analytics. Te zasady są stosowane tylko wtedy, gdy używasz klauzuli [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) w zapytaniu i są one stosowane tylko w przypadku źródeł danych wejściowych w chmurze.

## <a name="event-time-and-arrival-time"></a>Godzina i czas przybycia zdarzenia

Zadanie Stream Analytics może przetwarzać zdarzenia na podstawie *czasu zdarzenia* lub *czasu przybycia* . **Czas zdarzenia/aplikacji** to sygnatura czasowa występująca w ładunku zdarzenia (po wygenerowaniu zdarzenia). **Czas przybycia** to sygnatura czasowa odebrania zdarzenia w źródle danych wejściowych (Event Hubs/IoT Hub/BLOB Storage). 

Domyślnie Stream Analytics przetwarza zdarzenia według *czasu przybycia* , ale można wybrać przetwarzanie zdarzeń według *czasu zdarzenia* , używając klauzuli [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) w zapytaniu. Opóźnione przyjęcie i zasady poza kolejnością mają zastosowanie tylko w przypadku przetwarzania zdarzeń według czasu zdarzenia. Podczas konfigurowania tych ustawień należy wziąć pod uwagę wymagania dotyczące opóźnienia i poprawności dla danego scenariusza. 

## <a name="what-is-late-arrival-policy"></a>Co to są zasady spóźnionego przybycia?

Czasami zdarzenia są odbierane z różnych powodów. Na przykład zdarzenie, które dociera do 40 sekund, będzie miało czas zdarzenia = 00:10:00 i czas przybycia = 00:10:40. W przypadku ustawienia zasad późnego przybycia na 15 sekund wszystkie zdarzenia, które docierają później niż 15 sekund, zostaną usunięte (nie przetworzone przez Stream Analytics) lub zostaną dostosowane do czasu zdarzenia. W powyższym przykładzie, gdy zdarzenie dotarło do 40 sekund (więcej niż zestaw zasad), jego czas zdarzenia zostanie dostosowany do maksymalnej liczby zasad najpóźnego przybycia 00:10:25 (wartość zasad najpóźnego przybycia). Domyślne zasady późnego przybycia to 5 sekund.

## <a name="what-is-out-of-order-policy"></a>Co to są zasady niezwiązane z kolejnością? 

Zdarzenie może się pojawić również poza kolejnością. Po dopasowaniu czasu zdarzenia na podstawie zasad późnego przybycia można również automatycznie porzucić lub dostosować zdarzenia, które są nieaktualne. Ustawienie tych zasad na 8 sekund spowoduje, że wszystkie zdarzenia, które dotarły poza kolejnością, ale w 8-sekundowym oknie są zmieniane według czasu zdarzenia. Zdarzenia, które docierają później, zostaną usunięte lub dopasowane do maksymalnej wartości zasad poza kolejnością. Domyślne zasady poza kolejnością to 0 sekund. 

## <a name="adjust-or-drop-events"></a>Dostosuj lub upuść zdarzenia

Jeśli zdarzenia dotarły do siebie z opóźnieniem lub z kolejnością na podstawie skonfigurowanych zasad, można je usunąć (nie przetwarzane przez Stream Analytics) lub zmienić czas zdarzenia.

Zapoznaj się z przykładem tych zasad w działaniu.
<br> **Zasady późnego przybycia:** 15 sekund
<br> **Zasady poza kolejnością:** 8 sekund

| Nr zdarzenia | Czas zdarzenia | Czas przybycia | System.Timestamp | Wyjaśnienie |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Zdarzenie dotarło do najpóźnego i zewnętrznego poziomu tolerancji. W związku z tym czas zdarzenia jest dostosowywany do maksymalnej tolerancji późnego przybycia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Zdarzenie zostało odebrane z opóźnieniem, ale w ramach poziomu tolerancji. Czas zdarzenia nie zostanie dostosowany.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Zdarzenie odebrane na czas. Nie jest wymagana żadna korekta.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Zdarzenie zostało odebrane poza kolejnością, ale w ramach tolerancji wynoszącej 8 sekund. W związku z tym czas zdarzenia nie zostanie dostosowany. Na potrzeby analiz to zdarzenie będzie traktowane jako poprzednie zdarzenie nr 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Zdarzenie zostało odebrane poza kolejnością i tolerancja zewnętrzna wynosząca 8 sekund. W związku z tym czas zdarzenia jest dostosowywany do maksymalnej wartości z przekroczeniem kolejności. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Czy te ustawienia mogą opóźniać dane wyjściowe mojego zadania? 

Tak. Domyślnie zasady poza kolejnością mają ustawioną wartość zero (00 minut i 00 sekund). Jeśli zmienisz wartość domyślną, pierwsze wyjście zadania zostanie opóźnione o tę wartość (lub wyższą). 

Jeśli jedna z partycji danych wejściowych nie odbiera zdarzeń, należy oczekiwać, że dane wyjściowe zostaną opóźnione przez wartość zasad późnego przybycia. Aby dowiedzieć się, dlaczego zapoznaj się z sekcją błędu InputPartition poniżej. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Widzę komunikaty LateInputEvents w moim dzienniku aktywności

Te komunikaty są wyświetlane, aby poinformować użytkownika o tym, że zdarzenia zostały odebrane z opóźnieniem i zostały usunięte lub dopasowane zgodnie z konfiguracją. Te komunikaty można zignorować, jeśli zasady późnego przybycia zostały odpowiednio skonfigurowane. 

Przykładem tego komunikatu jest: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Widzę InputPartitionNotProgressing w moim dzienniku aktywności

Źródło danych wejściowych (centrum zdarzeń/IoT Hub) ma największej liczby partycji. Azure Stream Analytics generuje dane wyjściowe dla sygnatury czasowej T1 tylko wtedy, gdy wszystkie połączone partycje są co najmniej w czasie T1. Załóżmy na przykład, że zapytanie odczytuje z partycji centrum zdarzeń, która ma dwie partycje. Jedna z partycji, P1, ma zdarzenia do czasu T1. Druga partycja, P2, ma zdarzenia do czasu T1 + x. Dane wyjściowe są następnie generowane do czasu T1. Ale jeśli istnieje jawna partycja z klauzulą PartitionId, obie partycje postępują niezależnie. 

Gdy wiele partycji z tego samego strumienia wejściowego jest połączonych, tolerancja późnego przybycia jest maksymalną ilością czasu, jaką każda partycja czeka na nowe dane. Jeśli w centrum zdarzeń istnieje jedna partycja lub jeśli IoT Hub nie odbiera danych wejściowych, oś czasu dla tej partycji nie postępuje, dopóki osiągnie próg tolerancji późnego przybycia. To opóźnia dane wyjściowe przez próg tolerancji późnego przybycia. W takich przypadkach może zostać wyświetlony następujący komunikat:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ten komunikat informujący o tym, że co najmniej jedna partycja w danych wejściowych jest pusta i będzie opóźniać dane wyjściowe przez próg późnego przybycia. W celu przezwyciężenia zalecane jest:  
1. Upewnij się, że wszystkie partycje centrum zdarzeń/IoT Hub odbierają dane wejściowe. 
2. W zapytaniu Użyj klauzuli Partition by PartitionID. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Dlaczego widzę opóźnienie 5 sekund nawet wtedy, gdy zasady późnego przybycia mają wartość 0?
Dzieje się tak, gdy istnieje partycja wejściowa, która nigdy nie otrzymała żadnych danych wejściowych. Aby sprawdzić to zachowanie, można zweryfikować metryki danych wejściowych według partycji. 

Gdy partycja nie ma żadnych danych przekraczających skonfigurowany próg późnego przybycia, usługa Stream Analytics zaawansowana sygnatura czasowa aplikacji, zgodnie z opisem w sekcji Uwagi dotyczące porządkowania zdarzeń. Wymaga to szacowanego czasu odbioru. Jeśli partycja nigdy nie zawierała żadnych danych, program Stream Analytics szacuje czas przybycia jako *czas lokalny — 5 sekund* . Ze względu na to, że te partycje, które nigdy nie miały żadnych danych, nie mogą pokazać opóźnienia w ciągu 5 sekund.  

## <a name="next-steps"></a>Następne kroki
* [Zagadnienia dotyczące obsługi czasu](stream-analytics-time-handling.md)
* [Metryki dostępne w Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)