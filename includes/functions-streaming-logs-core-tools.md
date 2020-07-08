---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
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
