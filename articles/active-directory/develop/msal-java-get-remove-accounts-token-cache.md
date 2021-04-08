---
title: Pobierz & usunąć konta z pamięci podręcznej tokenów (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak wyświetlać i usuwać konta z pamięci podręcznej tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: fc039e06c8c9d75608b60c2f48e86bc5503e5aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96344865"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Pobieranie i usuwanie kont z pamięci podręcznej tokenów przy użyciu programu MSAL for Java

MSAL for Java domyślnie udostępnia pamięć podręczną tokenów w pamięci. Pamięć podręczna tokenów w pamięci jest okresem trwania wystąpienia aplikacji.

## <a name="see-which-accounts-are-in-the-cache"></a>Zobacz, które konta znajdują się w pamięci podręcznej

Możesz sprawdzić, jakie konta znajdują się w pamięci podręcznej, wywołując `PublicClientApplication.getAccounts()` , jak pokazano w następującym przykładzie:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Usuwanie kont z pamięci podręcznej

Aby usunąć konto z pamięci podręcznej, Znajdź konto, które należy usunąć, a następnie Wywołaj metodę, `PublicClientApplication.removeAccount()` jak pokazano w poniższym przykładzie:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Więcej tutaj

Jeśli używasz programu MSAL for Java, Dowiedz się więcej o [serializacji niestandardowego buforu tokenów w MSAL for Java](msal-java-token-cache-serialization.md).
