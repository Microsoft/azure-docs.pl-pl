---
title: Uzyskiwanie tokenów dostępu do usług
description: W tym artykule opisano sposób tworzenia tokenów dostępu do interfejsów API REST ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687079"
---
# <a name="get-service-access-tokens"></a>Uzyskiwanie tokenów dostępu do usług

Dostęp do interfejsów API REST ARR jest przyznawany tylko autoryzowanym użytkownikom. Aby udowodnić autoryzację, należy wysłać *token dostępu* wraz z żądaniami REST. Tokeny te są wystawiane przez *usługę bezpiecznego tokenu* (STS) w zamian za klucz konta. Tokeny mają **okres istnienia 24 godzin** i w związku z tym mogą być wydawane użytkownikom bez udzielania im pełnego dostępu do usługi.

W tym artykule opisano sposób tworzenia takiego tokenu dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto ARR](create-an-account.md), jeśli jeszcze go nie masz.

## <a name="token-service-rest-api"></a>Interfejs API REST usługi tokenu

Aby utworzyć tokeny dostępu, *usługa bezpiecznego tokenu* udostępnia jeden interfejs API REST. Adres URL usługi ARR STS to\/https: /sts.mixedreality.azure.com.

### <a name="get-token-request"></a>Żądanie "Pobierz token"

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /konta/**accountId**/token | GET |

| Nagłówek | Wartość |
|--------|:------|
| Autoryzacja | "Konto na **okazicielaId:****accountKey"** |

Zastąp *kontoId* i *accountKey* odpowiednimi danymi.

### <a name="get-token-response"></a>Odpowiedź "Pobierz token"

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | AccessToken: ciąg | Powodzenie |

| Nagłówek | Przeznaczenie |
|--------|:------|
| MS-CV | Ta wartość może służyć do śledzenia wywołania w usłudze |

## <a name="getting-a-token-using-powershell"></a>Uzyskiwanie tokenu przy użyciu programu PowerShell

Poniższy kod programu PowerShell pokazuje, jak wysłać niezbędne żądanie REST do STS. Następnie drukuje token do monitu programu PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skrypt po prostu drukuje token do danych wyjściowych, z którego można skopiować & wkleić go. W przypadku prawdziwego projektu należy zautomatyzować ten proces.

## <a name="next-steps"></a>Następne kroki

* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
* [Interfejsy API interfejsu frontendu platformy Azure](../how-tos/frontend-apis.md)
* [Interfejs API REST zarządzania sesjami](../how-tos/session-rest-api.md)
