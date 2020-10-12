---
title: Uzyskiwanie tokenów dostępu do usług
description: Opisuje sposób tworzenia tokenów na potrzeby uzyskiwania dostępu do interfejsów API REST
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076161"
---
# <a name="get-service-access-tokens"></a>Uzyskiwanie tokenów dostępu do usług

Dostęp do interfejsów API REST ARR jest udzielany tylko autoryzowanym użytkownikom. Aby potwierdzić autoryzację, musisz wysłać *token dostępu* wraz z żądaniami Rest. Tokeny te są wystawiane przez *usługę bezpiecznego tokenu* (STS) w programie Exchange dla klucza konta. Tokeny mają **okres istnienia 24 godzin** , więc mogą być wystawiane dla użytkowników bez nadawania im pełnego dostępu do usługi.

W tym artykule opisano sposób tworzenia takiego tokenu dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto ARR](create-an-account.md), jeśli jeszcze go nie masz.

## <a name="token-service-rest-api"></a>Interfejs API REST usługi tokenów

Aby można było utworzyć tokeny dostępu, *Usługa Secure Tokens* oferuje pojedynczy interfejs API REST. Adres URL usługi STS programu z identyfikatorem: \/ /STS.mixedreality.Azure.com.

### <a name="get-token-request"></a>Żądanie "Get token"

| URI | Metoda |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Header | Wartość |
|--------|:------|
| Autoryzacja | "Bearer **accountId**:**accountKey**" |

Zastąp *accountId* i *accountKey* odpowiednimi danymi.

### <a name="get-token-response"></a>Odpowiedź "Get token"

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | AccessToken: ciąg | Powodzenie |

| Header | Przeznaczenie |
|--------|:------|
| MS-CV | Ta wartość może służyć do śledzenia wywołania w ramach usługi |

## <a name="getting-a-token-using-powershell"></a>Pobieranie tokenu przy użyciu programu PowerShell

Poniższy kod programu PowerShell pokazuje, jak wysłać wymagane żądanie REST do usługi STS. Następnie program drukuje token do wiersza polecenia programu PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skrypt po prostu drukuje token do danych wyjściowych, skąd można go skopiować & wkleić. W przypadku rzeczywistego projektu należy zautomatyzować ten proces.

## <a name="next-steps"></a>Następne kroki

* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
* [Interfejsy API frontonu platformy Azure](../how-tos/frontend-apis.md)
* [Interfejs API REST zarządzania sesją](../how-tos/session-rest-api.md)
