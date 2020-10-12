---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68881317"
---
#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

Użyj `logstream` opcji, aby rozpocząć otrzymywanie dzienników przesyłania strumieniowego określonej aplikacji funkcji działającej na platformie Azure, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

Możesz również wyświetlić [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) aplikacji funkcji w nowym oknie przeglądarki, dołączając `--browser` opcję, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
