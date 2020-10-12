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
ms.openlocfilehash: 6267c2118b7bac9befb42102d1118faac639bf49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87312558"
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

Aby usunąć konto z pamięci podręcznej, Znajdź konto, które należy usunąć, a następnie Wywołaj metodę, `PublicClientApplicatoin.removeAccount()` jak pokazano w poniższym przykładzie:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli używasz programu MSAL for Java, Dowiedz się więcej o [serializacji niestandardowego buforu tokenów w MSAL for Java](msal-java-token-cache-serialization.md).
