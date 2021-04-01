---
title: Uzyskiwanie tokenów dostępu do usług
description: Opisuje sposób tworzenia tokenów na potrzeby uzyskiwania dostępu do interfejsów API REST
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97830704"
---
# <a name="get-service-access-tokens"></a>Uzyskiwanie tokenów dostępu do usług

Dostęp do interfejsów API REST ARR jest udzielany tylko autoryzowanym użytkownikom. Aby potwierdzić autoryzację, musisz wysłać *token dostępu* wraz z żądaniami Rest. Tokeny te są wystawiane przez *usługę bezpiecznego tokenu* (STS) w programie Exchange dla klucza konta. Tokeny mają **okres istnienia 24 godzin** , więc mogą być wystawiane dla użytkowników bez nadawania im pełnego dostępu do usługi.

W tym artykule opisano sposób tworzenia takiego tokenu dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto ARR](create-an-account.md), jeśli jeszcze go nie masz.

## <a name="token-service-rest-api"></a>Interfejs API REST usługi tokenów

Aby można było utworzyć tokeny dostępu, *Usługa Secure Tokens* oferuje pojedynczy interfejs API REST. Adres URL usługi STS zależy od domeny konta konta renderowania zdalnego. Ma postać https://sts . [ domena konta], np. `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>Żądanie "Get token"

| URI | Metoda |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Nagłówek | Wartość |
|--------|:------|
| Autoryzacja | "Bearer **accountId**:**accountKey**" |

Zastąp *accountId* i *accountKey* odpowiednimi danymi.

### <a name="get-token-response"></a>Odpowiedź "Get token"

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | AccessToken: ciąg | Powodzenie |

| Nagłówek | Przeznaczenie |
|--------|:------|
| MS-CV | Ta wartość może służyć do śledzenia wywołania w ramach usługi |

## <a name="getting-a-token-using-powershell"></a>Pobieranie tokenu przy użyciu programu PowerShell

Poniższy kod programu PowerShell pokazuje, jak wysłać wymagane żądanie REST do usługi STS. Następnie program drukuje token do wiersza polecenia programu PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skrypt po prostu drukuje token do danych wyjściowych, skąd można go skopiować & wkleić. W przypadku rzeczywistego projektu należy zautomatyzować ten proces.

## <a name="next-steps"></a>Następne kroki

* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
* [Interfejsy API frontonu platformy Azure](../how-tos/frontend-apis.md)
* [Interfejs API REST zarządzania sesją](../how-tos/session-rest-api.md)
