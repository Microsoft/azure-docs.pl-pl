---
title: Rozwiązywanie problemów z zasadami dostępu do magazynu kluczy Azure
description: Rozwiązywanie problemów z zasadami dostępu do magazynu kluczy Azure
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791411"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Rozwiązywanie problemów z zasadami dostępu do magazynu kluczy Azure

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Nie mogę wyświetlić lub pobrać wpisów tajnych/kluczy/certyfikatu. Widzę komunikat "coś poszło źle..." Porn.
Jeśli masz problem z listą/pobraniem/utworzeniem lub uzyskaniem dostępu do klucza tajnego, upewnij się, że masz zdefiniowane zasady dostępu, aby wykonać tę operację: [Key Vault zasad dostępu](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Jak można sprawdzić, jak i kiedy są dostępne magazyny kluczy?

Po utworzeniu co najmniej jednego magazynu kluczy prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do Twoich magazynów kluczy oraz przez kogo. Monitorowanie można przeprowadzić przez włączenie rejestrowania dla Azure Key Vault, aby zapoznać się z przewodnikiem krok po kroku, aby włączyć rejestrowanie. [Przeczytaj więcej](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Jak można monitorować dostępność magazynu, okresy opóźnienia usługi lub inne metryki wydajności dla magazynu kluczy?

Po rozpoczęciu skalowania usługi liczba żądań wysyłanych do magazynu kluczy zostanie wykorzystana. Takie zapotrzebowanie ma możliwość zwiększenia opóźnienia żądań i w skrajnych przypadkach, powodując ograniczenie żądań, co wpłynie na wydajność usługi. Można monitorować metryki wydajności magazynu kluczy i otrzymywać alerty dla określonych progów, aby zapoznać się z przewodnikiem krok po kroku w celu skonfigurowania monitorowania, [Dowiedz się więcej](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Nie mogę zmodyfikować zasad dostępu, jak można je włączyć?
Użytkownik musi mieć wystarczającą liczbę uprawnień usługi AAD, aby zmodyfikować zasady dostępu. W takim przypadku użytkownik musi mieć wyższą rolę współautor.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>Widzę komunikat o błędzie "nieznany zasad". Co to oznacza?
Istnieją dwie różne możliwości wyświetlania zasad dostępu w nieznanej sekcji:
* Może istnieć wcześniejszy użytkownik, który miał dostęp, i z jakiegoś powodu, że użytkownik nie istnieje.
* Jeśli zasady dostępu są dodawane za pośrednictwem programu PowerShell, a zasady dostępu są dodawane dla identyfikatora obiektu aplikacji zamiast nazwy głównej usługi.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Jak mogę przypisać kontrolę dostępu dla obiektu magazynu kluczy? 

Dostępność funkcji kontroli dostępu na klucz tajny/klucza/certyfikatu będzie powiadamiana w tym miejscu. [Przeczytaj więcej](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Jak zapewnić uwierzytelnianie magazynu kluczy przy użyciu zasad kontroli dostępu?

Najprostszym sposobem na uwierzytelnianie aplikacji opartej na chmurze w celu Key Vault jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz Uwierzytelnianie w Azure Key Vault](authentication.md) .
Jeśli tworzysz aplikację Premium, przenosząc lokalne programowanie lub w inny sposób nie można użyć tożsamości zarządzanej, możesz zamiast tego zarejestrować jednostkę usługi ręcznie i zapewnić dostęp do magazynu kluczy przy użyciu zasad kontroli dostępu. Zobacz [przypisywanie zasad kontroli dostępu](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Jak nadawać grupie usługi AD dostęp do magazynu kluczy?

Nadaj grupie usługi AD uprawnienia do Twojego magazynu kluczy za pomocą polecenia interfejsu CLI platformy Azure `az keyvault set-policy` lub polecenia cmdlet Azure PowerShell Set-AzKeyVaultAccessPolicy. Zobacz [przypisywanie zasad dostępu — interfejs wiersza polecenia](assign-access-policy-cli.md) i [przypisywanie zasad dostępu — PowerShell](assign-access-policy-powershell.md).

Aplikacja wymaga również co najmniej jednej roli zarządzania tożsamościami i dostępem (IAM) przypisanej do magazynu kluczy. W przeciwnym razie nie będzie można zalogować się i nie powiedzie się z powodu niewystarczających praw dostępu do subskrypcji. Grupy usługi Azure AD z zarządzanymi tożsamościami mogą odświeżać tokeny do ośmiu godzin i zaczynają obowiązywać.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Jak można ponownie wdrożyć Key Vault przy użyciu szablonu ARM bez usuwania istniejących zasad dostępu?

Obecnie Key Vault ponowne wdrażanie spowoduje usunięcie wszystkich zasad dostępu w Key Vault i zamieninie ich przy użyciu zasad dostępu w szablonie ARM. Nie ma opcji przyrostowej dla zasad dostępu Key Vault. Aby zachować zasady dostępu w Key Vault, należy przeczytać istniejące zasady dostępu w Key Vault i wypełnić szablon ARM przy użyciu tych zasad, aby uniknąć awarii dostępu.

Kolejną opcją, która może pomóc w tym scenariuszu, jest użycie RBAC i ról platformy Azure jako alternatywy dla zasad dostępu. Dzięki kontroli RBAC platformy Azure można ponownie wdrożyć Magazyn kluczy bez konieczności ponownego określania zasad. Więcej informacji można znaleźć [tutaj](./rbac-guide.md).

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Zalecane kroki rozwiązywania problemów dla następujących typów błędów

* HTTP 401: Nieuwierzytelnione żądanie — [procedura rozwiązywania problemów](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Niewystarczające uprawnienia — [procedura rozwiązywania problemów](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Zbyt wiele żądań — [procedura rozwiązywania problemów](rest-error-codes.md#http-429-too-many-requests)
* Sprawdź, czy masz uprawnienie dostępu do usuwania magazynu kluczy: zobacz [przypisywanie zasad dostępu — interfejs wiersza polecenia](assign-access-policy-cli.md), [przypisywanie zasad dostępu — PowerShell](assign-access-policy-powershell.md)lub [przypisywanie zasad dostępu — Portal](assign-access-policy-portal.md).
* Jeśli masz w kodzie problem z uwierzytelnianiem w magazynie kluczy, użyj [zestawu SDK uwierzytelniania](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Jakie są najlepsze rozwiązania, które należy zaimplementować podczas uzyskiwania ograniczenia w magazynie kluczy?
Postępuj zgodnie z najlepszymi rozwiązaniami opisanymi [tutaj](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rozwiązywać problemy z błędami uwierzytelniania magazynu kluczy: [Key Vault Przewodnik rozwiązywania problemów](rest-error-codes.md).