---
title: Szybki Start — Zarządzanie numerami telefonów przy użyciu usług Azure Communications Services
description: Dowiedz się, jak zarządzać numerami telefonów przy użyciu usług Azure Communications Services
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: e28b8581342824032921ad75e0297b3604fc0bb4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629390"
---
# <a name="quickstart-manage-phone-numbers"></a>Szybki Start: Zarządzanie numerami telefonów

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu Azure Portal lub biblioteki klienta numerów telefonów usług komunikacyjnych, aby zarządzać numerami telefonów.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Często zadawane pytania i problemy:

- Telefon zakupów jest obsługiwany tylko w Stanach Zjednoczonych. Aby kupić numery telefonów, upewnij się, że:
  - Skojarzony adres rozliczeniowy subskrypcji platformy Azure znajduje się w Stany Zjednoczone. W tej chwili nie można przenieść zasobu do innej subskrypcji.
  - Zasób usług komunikacyjnych jest inicjowany w lokalizacji danych Stany Zjednoczone. W tej chwili nie można przenieść zasobu do innej lokalizacji danych.

- Po wydaniu numeru telefonu numer telefonu nie zostanie wykupiony lub nie można go wykupić do końca cyklu rozliczeniowego.

- Po usunięciu zasobu usług komunikacyjnych numery telefonów skojarzone z tym zasobem będą automatycznie wydawane w tym samym czasie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat wykonywania tych instrukcji:

> [!div class="checklist"]
> * Kup numer telefonu
> * Zarządzanie numerem telefonu
> * Zwolnij numer telefonu

> [!div class="nextstepaction"]
> [Wyślij wiadomość SMS](../telephony-sms/send.md) 
>  [Wprowadzenie do wywoływania](../voice-video-calling/getting-started-with-calling.md)
