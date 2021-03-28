---
title: Szybki Start — wysyłanie wiadomości SMS
titleSuffix: An Azure Communication Services quickstart
description: Dowiedz się, jak wysłać wiadomość SMS przy użyciu usług Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 9514f1616c0d70d7d195af2f052c604973709204
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644306"
---
# <a name="quickstart-send-an-sms-message"></a>Szybki Start: wysyłanie wiadomości SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> Wiadomości SMS mogą być wysyłane do numerów telefonów i odbierane z nich Stany Zjednoczone. Numery telefonów znajdujące się w innych lokalizacje geograficzne nie są jeszcze obsługiwane przez program SMS usług komunikacyjnych.
> Aby uzyskać więcej informacji, zobacz **[typy numerów telefonów](../../concepts/telephony-sms/plan-solution.md)**.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby rozwiązać problemy związane z dostarczaniem programu SMS, możesz [włączyć raportowanie dostarczania z Event Grid](./handle-sms-events.md) , aby przechwytywać szczegóły dostarczania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób wysyłania wiadomości SMS przy użyciu usług Azure Communication Services.

> [!div class="nextstepaction"]
> [Odbieraj zdarzenia SMS i Delivery Report](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Typy numerów telefonów](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o programie SMS](../../concepts/telephony-sms/concepts.md)
