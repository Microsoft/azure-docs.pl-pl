---
title: Korzystanie z tożsamości zarządzanych w usługach komunikacyjnych
titleSuffix: An Azure Communication Services quickstart
description: Zarządzane tożsamości umożliwiają autoryzowanie dostępu do usług Azure Communications Services z aplikacji uruchamianych na maszynach wirtualnych platformy Azure, aplikacjach funkcji i innych zasobach.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ffda88da451e25b79112a7adf85026158bd27acc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492357"
---
# <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych
Rozpocznij pracę z usługami Azure Communications Services przy użyciu tożsamości zarządzanych. Biblioteki usług komunikacyjnych i klienckie programu SMS obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym przewodniku szybki start przedstawiono sposób autoryzacji dostępu do tożsamości i bibliotek klienckich programu SMS ze środowiska platformy Azure, które obsługuje zarządzane tożsamości. Opisano w nim również sposób testowania kodu w środowisku programistycznym.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o bibliotece tożsamości platformy Azure dla platformy .NET](/dotnet/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../quickstarts/telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../concepts/telephony-sms/concepts.md)
