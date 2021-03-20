---
title: Zdarzenie niepowodzenia zadania Azure Batch
description: Odwołanie do zdarzenia niepowodzenia zadania usługi Batch. To zdarzenie będzie emitowane jako uzupełnienie zdarzenia ukończenia zadania i może służyć do wykrywania, kiedy zadanie zakończyło się niepowodzeniem.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e13692b45ff5a049d0b724525ad6565d2b894a3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91850816"
---
# <a name="task-fail-event"></a>Zdarzenie niepowodzenia zadania

 To zdarzenie jest emitowane, gdy zadanie kończy się niepowodzeniem z powodu błędu. Obecnie wszystkie kody zakończenia inne niż zero są uznawane za niepowodzenia. To zdarzenie będzie emitowane *jako uzupełnienie* zdarzenia ukończenia zadania i może służyć do wykrywania, kiedy zadanie zakończyło się niepowodzeniem.


 Poniższy przykład przedstawia treść zdarzenia niepowodzenia zadania.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`jobId`|Ciąg|Identyfikator zadania zawierającego zadanie.|
|`id`|Ciąg|Identyfikator zadania.|
|`taskType`|Ciąg|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie Menedżera zadań lub "użytkownika" wskazujące, że nie jest to zadanie Menedżera zadań. To zdarzenie nie jest emitowane w przypadku zadań związanych z przygotowaniem zadania, zadań wydania zadania lub uruchamiania zadań.|
|`systemTaskVersion`|Int32|Jest to wewnętrzny licznik ponownych prób dla zadania. Wewnętrznie usługa Batch może ponowić zadanie, aby uwzględnić przejściowe problemy. Te problemy mogą obejmować wewnętrzne błędy planowania lub próby odzyskania z węzłów obliczeniowych w nieprawidłowym stanie.|
|`requiredSlots`|Int32|Wymagane miejsca do uruchomienia zadania.|
|[`nodeInfo`](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Typ złożony|Określa, że zadanie jest zadaniem z wieloma wystąpieniami wymagającymi wielu węzłów obliczeniowych.  [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)Aby uzyskać szczegółowe informacje, zobacz.|
|[`constraints`](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[`executionInfo`](#executionInfo)|Typ złożony|Zawiera informacje o wykonywaniu zadania.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`poolId`|Ciąg|Identyfikator puli, w której uruchomiono zadanie.|
|`nodeId`|Ciąg|IDENTYFIKATOR węzła, w którym uruchomiono zadanie.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a><a name="constraints"></a> powiązanych

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maksymalna liczba prób wykonania zadania. Usługa Batch ponawia próbę wykonania zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy zauważyć, że ta wartość kontroluje liczbę ponownych prób. Usługa Batch spróbuje wykonać zadanie jeden raz, a następnie ponowić próbę wykonania tego limitu. Jeśli na przykład maksymalna liczba ponownych prób wynosi 3, zadanie wsadowe próbuje wykonać zadania do 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie wykonuje żadnych ponownych prób wykonywania zadań.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi-1, usługa Batch ponawia zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|


###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`startTime`|DateTime|Godzina, o której uruchomiono zadanie. "Uruchomiona" odpowiada stanie **uruchomienia** , więc jeśli zadanie Określa pliki zasobów lub pakiety aplikacji, czas rozpoczęcia odzwierciedla czas, w którym zadanie rozpoczęło pobieranie lub wdrażanie.  Jeśli zadanie zostało ponownie uruchomione lub ponowione, jest to Ostatnia godzina, o której uruchomiono zadanie.|
|`endTime`|DateTime|Godzina ukończenia zadania.|
|`exitCode`|Int32|Kod zakończenia zadania.|
|`retryCount`|Int32|Liczba ponownych prób wykonania zadania przez usługę Batch. Zadanie zostanie ponowione, jeśli zostanie zakończone z niezerowym kodem zakończenia, do określonego MaxTaskRetryCount.|
|`requeueCount`|Int32|Liczba uruchomień zadania w kolejce przez usługę Batch w wyniku żądania użytkownika.<br /><br /> Gdy użytkownik usuwa węzły z puli (zmieniając rozmiar lub zmniejszając pulę) lub gdy zadanie jest wyłączone, użytkownik może określić, że uruchomione zadania w węzłach będą ponownie umieszczane w kolejce do wykonania. Ta liczba śledzi, ile razy zadanie zostało ponownie poddane kolejce z tych przyczyn.|
