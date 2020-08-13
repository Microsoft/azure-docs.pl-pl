---
title: Rozwiązywanie problemów z zasadami dostępu do magazynu kluczy Azure
description: Rozwiązywanie problemów z zasadami dostępu do magazynu kluczy Azure
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: f775a0e1dcb82afc9a458ce4ee608a52ec06dc4c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135916"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Rozwiązywanie problemów z zasadami dostępu do magazynu kluczy Azure

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Jak można sprawdzić, jak i kiedy są dostępne magazyny kluczy?
Po utworzeniu co najmniej jednego magazynu kluczy prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do Twoich magazynów kluczy oraz przez kogo. Można to zrobić przez włączenie rejestrowania dla Azure Key Vault, aby zapoznać się z przewodnikiem krok po kroku, aby włączyć rejestrowanie. [Przeczytaj więcej](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Jak można monitorować dostępność magazynu, okresy opóźnienia usługi lub inne metryki wydajności dla magazynu kluczy?
Po rozpoczęciu skalowania usługi liczba żądań wysyłanych do magazynu kluczy wzrośnie. Jest to możliwe, aby zwiększyć opóźnienie żądań i w skrajnych przypadkach, co spowoduje ograniczenie żądań, co wpłynie na wydajność usługi. Można monitorować metryki wydajności magazynu kluczy i otrzymywać alerty dla określonych progów, aby zapoznać się z przewodnikiem krok po kroku w celu skonfigurowania monitorowania, [Dowiedz się więcej](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Jak mogę przypisać kontrolę dostępu dla obiektu magazynu kluczy? 
Dostępność funkcji kontroli dostępu na klucz tajny/klucza/certyfikatu będzie powiadamiana w tym miejscu. [Przeczytaj więcej](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Jak zapewnić uwierzytelnianie magazynu kluczy przy użyciu zasad kontroli dostępu?
Najprostszym sposobem na uwierzytelnianie aplikacji opartej na chmurze w celu Key Vault jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz używanie Azure Key Vault tożsamości zarządzanej App Service]( https://docs.microsoft.com/azure/key-vault/general/managed-identity) .
Jeśli tworzysz aplikację Premium, przeprowadzając lokalne programowanie lub w inny sposób nie można użyć tożsamości zarządzanej, możesz zamiast tego zarejestrować jednostkę usługi ręcznie i zapewnić dostęp do magazynu kluczy przy użyciu zasad kontroli dostępu, aby dowiedzieć się [więcej](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Jak nadawać grupie usługi AD dostęp do magazynu kluczy?
Nadaj grupie usługi AD uprawnienia do Twojego magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure AZ Key AzKeyVaultAccessPolicy Set-Policy lub Azure PowerShell Set-polecenia cmdlet. Aby zapoznać się z przykładami, zapoznaj się z tematem [udzielenie aplikacji, grupie usługi Azure AD lub dostępu użytkowników do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

Aplikacja wymaga również co najmniej jednej roli zarządzania tożsamościami i dostępem (IAM) przypisanej do magazynu kluczy. W przeciwnym razie nie będzie można zalogować się i nie powiedzie się z powodu niewystarczających praw dostępu do subskrypcji. Grupy usługi Azure AD z tożsamościami zarządzanymi mogą wymagać do 8hr tokenu odświeżania i zaczynają obowiązywać.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Jak można ponownie wdrożyć Key Vault przy użyciu szablonu ARM bez usuwania istniejących zasad dostępu?
Obecnie Key Vault ARM redopleyment usunie wszelkie zasady dostępu w Key Vault i zastąpi je zasadami dostępu w szablonie ARM. Nie ma opcji przyrostowej dla zasad dostępu Key Vault. Aby zachować zasady dostępu w Key Vault należy odczytać istniejące zasady dostępu w Key Vault i wypełnić szablon ARM przy użyciu tych zasad, aby uniknąć awarii dostępu.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Zalecane kroki rozwiązywania problemów dla następujących typów błędów
* HTTP 401: żądanie nieuwierzytelnione — [kroki rozwiązywania problemów](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: niewystarczające uprawnienia — [kroki rozwiązywania problemów](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: zbyt wiele żądań — [kroki rozwiązywania problemów](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Sprawdź, czy masz uprawnienie do usuwania dostępu do magazynu kluczy: [Zasady dostępu do usługi Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Jeśli masz w kodzie problem z uwierzytelnianiem w magazynie kluczy, użyj [zestawu SDK uwierzytelniania](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Jakie są najlepsze rozwiązania, które należy zaimplementować podczas uzyskiwania ograniczenia w magazynie kluczy?
Postępuj zgodnie z najlepszymi rozwiązaniami opisanymi [tutaj](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rozwiązywać problemy z błędami uwierzytelniania magazynu kluczy. [Przewodnik rozwiązywania problemów Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
