---
title: Serializacja niestandardowa pamięci podręcznej tokenów (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zdeserializować pamięć podręczną tokenów dla MSAL dla języka Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87846194"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serializacja niestandardowego buforu tokenów w MSAL dla języka Python

W MSAL Python, pamięć podręczna tokenów w pamięci, która będzie trwała w czasie trwania sesji aplikacji, jest udostępniana domyślnie podczas tworzenia wystąpienia elementu [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serializacja pamięci podręcznej tokenów, tak aby różne sesje aplikacji mogły uzyskać do niej dostęp, nie jest ona dostępna. Dzieje się tak, ponieważ MSAL Python może być używany w typach aplikacji, które nie mają dostępu do systemu plików — takich jak aplikacje sieci Web. Aby mieć trwałą pamięć podręczną tokenów w aplikacji MSAL w języku Python, musisz zapewnić serializowaną niestandardową pamięć podręczną tokenów.

Strategie serializowania pamięci podręcznej tokenów różnią się w zależności od tego, czy piszesz publiczną aplikację kliencką (Desktop), czy poufną aplikację kliencką (aplikację sieci Web, internetowy interfejs API lub aplikację demona).

## <a name="token-cache-for-a-public-client-application"></a>Pamięć podręczna tokenów dla publicznej aplikacji klienckiej

Publiczne aplikacje klienckie działają na urządzeniu użytkownika i zarządzają tokenami dla pojedynczego użytkownika. W takim przypadku można serializować całą pamięć podręczną do pliku. Pamiętaj, aby zapewnić blokowanie plików, jeśli aplikacja lub inna aplikacja mogą uzyskać dostęp do pamięci podręcznej współbieżnie. Aby zapoznać się z prostym przykładem, jak serializować pamięć podręczną tokenów do pliku bez blokowania, zobacz przykład w dokumentacji dotyczącej klasy [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) .

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Pamięć podręczna tokenów dla aplikacji sieci Web (poufne aplikacje klienckie)

W przypadku aplikacji sieci Web lub interfejsów API sieci Web można użyć sesji lub pamięci podręcznej Redis lub bazy danych do przechowywania pamięci podręcznej tokenów. Powinna istnieć jedna pamięć podręczna tokenów dla każdego użytkownika (na konto), dlatego należy serializować pamięć podręczną tokenów na konto.

## <a name="next-steps"></a>Następne kroki

Zobacz [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) , aby zapoznać się z przykładem użycia pamięci podręcznej tokenów dla aplikacji sieci Web systemu Windows lub Linux lub interfejsu API sieci Web. Przykład dotyczy aplikacji sieci Web, która wywołuje interfejs API Microsoft Graph.
