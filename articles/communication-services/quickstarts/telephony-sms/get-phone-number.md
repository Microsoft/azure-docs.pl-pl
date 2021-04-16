---
title: Szybki start — zarządzanie numerami telefonów przy użyciu Azure Communication Services
description: Dowiedz się, jak zarządzać numerami telefonów przy użyciu Azure Communication Services
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498201"
---
# <a name="quickstart-manage-phone-numbers"></a>Szybki start: zarządzanie numerami telefonów

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

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

Typowe pytania i problemy:

- Telefon do zakupu jest obsługiwany tylko w Usa. Aby kupić numery telefonów, upewnij się, że:
  - Skojarzony adres rozliczeniowy subskrypcji platformy Azure znajduje się w Stany Zjednoczone. Obecnie nie można przenieść zasobu do innej subskrypcji.
  - Zasób Communication Services jest aprowowany w Stany Zjednoczone lokalizacji danych. Obecnie nie można przenieść zasobu do innej lokalizacji danych.

- Po zwolnieniu numeru telefonu numer telefonu nie zostanie zwolniony ani nie będzie można go ponownie wykupić do końca cyklu rozliczeniowego.

- Po usunięciu zasobu Communication Services numery telefonów skojarzone z tym zasobem zostaną automatycznie wydane w tym samym czasie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano, jak:

> [!div class="checklist"]
> * Kupowanie numeru telefonu
> * Zarządzanie numerem telefonu
> * Zwolnij numer telefonu

> [!div class="nextstepaction"]
> [Wysyłanie wiadomości SMS](../telephony-sms/send.md) 
>  [Wprowadzenie do wywoływania](../voice-video-calling/getting-started-with-calling.md)
