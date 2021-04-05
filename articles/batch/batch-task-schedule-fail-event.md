---
title: Zdarzenie niepowodzenia harmonogramu zadań Azure Batch
description: Odwołanie do zdarzenia niepowodzenia harmonogramu zadań wsadowych. To zdarzenie jest emitowane, gdy nie można zaplanować zadania i próba zostanie ponowiona później.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852192"
---
# <a name="task-schedule-fail-event"></a>Zdarzenie niepowodzenia harmonogramu zadań

 To zdarzenie jest emitowane, gdy nie można zaplanować zadania i próba zostanie ponowiona później. Jest to tymczasowy błąd w czasie planowania zadań ze względu na ograniczenie zasobów, na przykład brak wystarczającej liczby gniazd dostępnych w węzłach, aby uruchomić zadanie z `requiredSlots` określonym.

 Poniższy przykład przedstawia treść zdarzenia błędu harmonogramu zadań.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
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
|[`schedulingError`](#schedulingError)|Typ złożony|Zawiera informacje o błędzie planowania zadania.|

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


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`category`|Ciąg|Kategoria błędu.|
|`code`|Ciąg|Identyfikator błędu planowania zadań. Kody są niezmienne i mają być używane programowo.|
|`message`|Ciąg|Komunikat opisujący błąd planowania zadań, przeznaczony do wyświetlania w interfejsie użytkownika.|
