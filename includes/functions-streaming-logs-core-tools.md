---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020369"
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
