---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94425568"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania mowy przez człowieka i przetłumaczenia jej na inne języki. W tym przewodniku szybki start dowiesz się, jak używać zestawu Speech SDK w aplikacjach i produktach do wykonywania translacji mowy o wysokiej jakości. Ten przewodnik Szybki Start tłumaczy mowę z mikrofonu na tekst w innym języku.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Ustaw język źródłowy i docelowy

To polecenie wywołuje interfejs wiersza polecenia mowy, aby przetłumaczyć mowę z mikrofonu z języka włoskiego na język francuski.

```shell
 spx translate --microphone --source it-IT --target fr
```