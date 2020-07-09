---
title: Zdarzenie rozpoczęcia zadania Azure Batch
description: Informacje referencyjne dotyczące zdarzenia uruchamiania zadania wsadowego. To zdarzenie jest emitowane po zaplanowaniu uruchomienia zadania w węźle obliczeniowym przez harmonogram.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: ed7b3014279fc87fbaae6f1c270f056e95073aaf
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965097"
---
# <a name="task-start-event"></a>Zdarzenie rozpoczęcia zadania

 To zdarzenie jest emitowane po zaplanowaniu uruchomienia zadania w węźle obliczeniowym przez harmonogram. Należy pamiętać, że jeśli zadanie zostanie ponowione lub zostanie ponownie poddana w kolejce, to zdarzenie zostanie wyemitowane dla tego samego zadania, ale liczba ponownych prób i wersja zadania systemowego zostaną odpowiednio zaktualizowane.


 Poniższy przykład przedstawia treść zdarzenia rozpoczęcia zadania.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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
        "retryCount": 0
    }
}
```

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`jobId`|String|Identyfikator zadania zawierającego zadanie.|
|`id`|String|Identyfikator zadania.|
|`taskType`|String|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie Menedżera zadań lub "użytkownika" wskazujące, że nie jest to zadanie Menedżera zadań.|
|`systemTaskVersion`|Int32|Jest to wewnętrzny licznik ponownych prób dla zadania. Wewnętrznie usługa Batch może ponowić zadanie, aby uwzględnić przejściowe problemy. Te problemy mogą obejmować wewnętrzne błędy planowania lub próby odzyskania z węzłów obliczeniowych w nieprawidłowym stanie.|
|[`nodeInfo`](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Typ złożony|Określa, że zadanie jest zadaniem z wieloma wystąpieniami wymagającymi wielu węzłów obliczeniowych.  Aby uzyskać szczegółowe informacje, zobacz [multiInstanceSettings](/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[`executionInfo`](#executionInfo)|Typ złożony|Zawiera informacje o wykonywaniu zadania.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`poolId`|String|Identyfikator puli, w której uruchomiono zadanie.|
|`nodeId`|String|IDENTYFIKATOR węzła, w którym uruchomiono zadanie.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceSettings

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`numberOfInstances`|int|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a><a name="constraints"></a>powiązanych

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maksymalna liczba prób wykonania zadania. Usługa Batch ponawia próbę wykonania zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy zauważyć, że ta wartość kontroluje liczbę ponownych prób. Usługa Batch spróbuje wykonać zadanie jeden raz, a następnie ponowić próbę wykonania tego limitu. Jeśli na przykład maksymalna liczba ponownych prób wynosi 3, zadanie wsadowe próbuje wykonać zadania do 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie wykonuje żadnych ponownych prób wykonywania zadań.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi-1, usługa Batch ponawia zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>executionInfo

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`retryCount`|Int32|Liczba ponownych prób wykonania zadania przez usługę Batch. Zadanie jest ponawiane, jeśli zostanie zakończone z niezerowym kodem zakończenia, do określonego MaxTaskRetryCount|
