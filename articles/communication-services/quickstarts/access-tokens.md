---
title: Szybki Start — tworzenie tokenów dostępu i zarządzanie nimi
titleSuffix: An Azure Communication Services quickstart
description: Dowiedz się, jak zarządzać tożsamościami i tokenami dostępu przy użyciu zestawu SDK tożsamości usługi Azure Communication Services.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e356219d22ee558ce3de5a96d58f24b9e7902d8a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726621"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Szybki Start: tworzenie tokenów dostępu i zarządzanie nimi

Rozpocznij pracę z usługami Azure Communications Services przy użyciu zestawu SDK tożsamości usług komunikacyjnych. Umożliwia tworzenie tożsamości i zarządzanie tokenami dostępu. Tożsamość reprezentuje jednostkę aplikacji w usłudze komunikacyjnej platformy Azure (na przykład użytkownik lub urządzenie). Tokeny dostępu umożliwiają rozmowę i wywoływanie zestawów SDK bezpośrednio z usługami Azure Communications Services. Zalecamy generowanie tokenów dostępu w usłudze po stronie serwera. Tokeny dostępu są następnie używane do inicjowania zestawów SDK usług komunikacyjnych na urządzeniach klienckich.

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

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

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
> * Korzystanie z zestawu SDK tożsamości usług komunikacyjnych


> [!div class="nextstepaction"]
> [Dodawanie połączenia głosowego do aplikacji](./voice-video-calling/getting-started-with-calling.md)

Możesz również chcieć:

 - [Informacje o uwierzytelnianiu](../concepts/authentication.md)
 - [Dodawanie czatu do aplikacji](./chat/get-started.md)
 - [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
