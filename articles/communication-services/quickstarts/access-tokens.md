---
title: Szybki Start — tworzenie tokenów dostępu i zarządzanie nimi
titleSuffix: An Azure Communication Services quickstart
description: Dowiedz się, jak zarządzać tożsamościami i tokenami dostępu przy użyciu biblioteki klienta administracji usługi Azure Communication Services.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074128"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Szybki Start: tworzenie tokenów dostępu i zarządzanie nimi

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu biblioteki klienta administrowania usługami komunikacyjnymi do udostępniania tokenów dostępu i zarządzania nimi. Tokeny dostępu umożliwiają rozmowę i wywoływanie bibliotek klienckich bezpośrednio w usłudze Azure Communications Services. Te tokeny są generowane przez zaimplementowaną usługę inicjowania obsługi tokenów po stronie serwera. Są one następnie używane do inicjowania bibliotek klienckich usług komunikacyjnych na urządzeniach klienckich.

Należy pamiętać, że wszystkie ceny widoczne w obrazach w tym samouczku są przeznaczone tylko do celów.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

W danych wyjściowych aplikacji opisano każdą akcję, która została ukończona:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zarządzanie tożsamościami
> * Wystawianie tokenów dostępu
> * Korzystanie z biblioteki klienta administrowania usługami komunikacyjnymi


> [!div class="nextstepaction"]
> [Dodawanie połączenia głosowego do aplikacji](./voice-video-calling/getting-started-with-calling.md)

Możesz również chcieć:

 - [Informacje o uwierzytelnianiu](../concepts/authentication.md)
 - [Dodawanie czatu do aplikacji](./chat/get-started.md)
 - [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
