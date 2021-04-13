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
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307443"
---
# <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych
Rozpocznij pracę z usługami Azure Communications Services przy użyciu tożsamości zarządzanych. Tożsamość usług komunikacyjnych i zestawy SDK programu SMS obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym przewodniku szybki start pokazano, jak autoryzować dostęp do tożsamości i zestawów SDK programu SMS ze środowiska platformy Azure, które obsługuje zarządzane tożsamości. Opisano w nim również sposób testowania kodu w środowisku programistycznym.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free)
- Aktywny zasób usług Azure Communications Services, zobacz [Tworzenie zasobu usług komunikacyjnych](./create-communication-resource.md) , jeśli go nie masz.
- Do wysłania wiadomości SMS będzie potrzebny [numer telefonu](./telephony-sms/get-phone-number.md).
- Tożsamość zarządzana przez Instalatora dla środowiska deweloperskiego, zobacz [Autoryzuj dostęp z tożsamością zarządzaną](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o bibliotece tożsamości platformy Azure dla platformy .NET](/dotnet/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../quickstarts/telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../concepts/telephony-sms/concepts.md)
