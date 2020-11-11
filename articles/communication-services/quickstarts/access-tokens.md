---
title: Szybki Start — tworzenie tokenów dostępu i zarządzanie nimi
titleSuffix: An Azure Communication Services quickstart
description: Dowiedz się, jak zarządzać tożsamościami i tokenami dostępu przy użyciu biblioteki klienta administracji usługi Azure Communication Services.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: b67d0808643797d88628b626403c1b9d97cf1cad
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506248"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Szybki Start: tworzenie tokenów dostępu i zarządzanie nimi

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu biblioteki klienta Administracja usług komunikacyjnych. Umożliwia tworzenie tożsamości i zarządzanie tokenami dostępu. Tożsamość reprezentuje jednostkę aplikacji w usłudze komunikacyjnej platformy Azure (na przykład użytkownik lub urządzenie). Tokeny dostępu umożliwiają rozmowę i wywoływanie bibliotek klienckich bezpośrednio w usłudze Azure Communications Services. Zalecamy generowanie tokenów dostępu w usłudze po stronie serwera. Tokeny dostępu są następnie używane do inicjowania bibliotek klienckich usług komunikacyjnych na urządzeniach klienckich.

Wszystkie ceny widoczne w obrazach w tym samouczku służą wyłącznie do celów demonstracyjnych.

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

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued a access token with 'voip' scope for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502:
<token signature here>

Successfully deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
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
 