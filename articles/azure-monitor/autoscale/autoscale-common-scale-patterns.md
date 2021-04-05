---
title: Przegląd typowych wzorców autoskalowania
description: Zapoznaj się z typowymi wzorcami, aby automatycznie skalować zasoby na platformie Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100617730"
---
# <a name="overview-of-common-autoscale-patterns"></a>Przegląd typowych wzorców autoskalowania
W tym artykule opisano niektóre typowe wzorce umożliwiające skalowanie zasobów na platformie Azure.

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)i [usług API Management](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Umożliwia rozpoczęcie pracy

W tym artykule założono, że znasz automatyczne skalowanie. Możesz [zacząć tutaj, aby skalować zasób][1]. Poniżej przedstawiono niektóre typowe wzorce skalowania.

## <a name="scale-based-on-cpu"></a>Skalowanie w oparciu o procesor CPU

Masz aplikację sieci Web (rolę usługi/VMSS/cloud) i

- Skalowanie w poziomie i skalowanie w oparciu o procesor CPU.
- Ponadto należy upewnić się, że istnieje minimalna liczba wystąpień.
- Należy również upewnić się, że ustawisz maksymalny limit liczby wystąpień, do których można skalować.

![Skalowanie w oparciu o procesor CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skalowanie w różny sposób w dniach tygodnia a weekendy

Masz aplikację sieci Web (rolę usługi/VMSS/cloud) i

- Chcesz domyślnie 3 wystąpienia (w dniach roboczych)
- Nie oczekujesz ruchu w weekendy, dlatego chcesz skalować w dół do jednego wystąpienia w weekendy.

![Skalowanie w różny sposób w dniach tygodnia a weekendy][3]

## <a name="scale-differently-during-holidays"></a>Skalowanie w różny sposób podczas świąt

Masz aplikację sieci Web (rolę usługi/VMSS/cloud) i

- Chcesz skalować w górę/w dół na podstawie użycia procesora CPU domyślnie
- Jednak w sezonach świątecznych (lub określonych dniach ważnych dla Twojej firmy) chcesz przesłonić wartości domyślne i zwiększyć pojemność.

![Skalowanie w różny sposób w przypadku świąt][4]

## <a name="scale-based-on-custom-metric"></a>Skalowanie na podstawie metryki niestandardowej

Masz fronton internetowy i warstwę interfejsu API, która komunikuje się z zapleczem.

- Chcesz skalować warstwę interfejsu API na podstawie zdarzeń niestandardowych na frontonie (przykład: chcesz skalować proces wyewidencjonowywania na podstawie liczby elementów w koszyku)

![Skalowanie na podstawie metryki niestandardowej][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
