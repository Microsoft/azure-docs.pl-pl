---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164872"
---
#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

Użyj `logstream` opcji, aby rozpocząć otrzymywanie dzienników przesyłania strumieniowego określonej aplikacji funkcji działającej na platformie Azure, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Wbudowane funkcje przesyłania strumieniowego dzienników nie są jeszcze włączone w podstawowych narzędziach dla aplikacji funkcji działających w systemie Linux w planie zużycia. W przypadku tych planów hostingu należy używać Live Metrics Stream do wyświetlania dzienników w czasie niemal rzeczywistym.

#### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

[Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) aplikacji funkcji można wyświetlić w nowym oknie przeglądarki, dołączając `--browser` opcję, jak w poniższym przykładzie:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
