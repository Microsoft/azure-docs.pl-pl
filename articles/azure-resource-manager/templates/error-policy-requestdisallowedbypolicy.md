---
title: RequestDisallowedByPolicy error (Błąd RequestDisallowedByPolicy)
description: Opisuje przyczynę błędu RequestDisallowedByPolicy podczas wdrażania zasobów przy użyciu Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75477670"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Błąd RequestDisallowedByPolicy z zasadami zasobów platformy Azure

W tym artykule opisano przyczynę błędu RequestDisallowedByPolicy i przedstawiono w nim również rozwiązanie tego błędu.

## <a name="symptom"></a>Objaw

Podczas wdrażania może zostać wyświetlony błąd **RequestDisallowedByPolicy** , który uniemożliwia tworzenie zasobów. Poniższy przykład pokazuje błąd:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby pobrać szczegóły dotyczące zasad, które zablokowały wdrożenie, należy użyć jednej z następujących metod:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W programie PowerShell podaj identyfikator zasad jako parametr, `Id` Aby pobrać szczegóły dotyczące zasad, które zablokowały wdrożenie.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure Podaj nazwę definicji zasad:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Rozwiązanie

Aby zapewnić bezpieczeństwo lub zgodność, Administratorzy subskrypcji mogą przypisywać zasady ograniczające sposób wdrażania zasobów. Na przykład subskrypcja może mieć zasady, które uniemożliwiają tworzenie publicznych adresów IP, sieciowych grup zabezpieczeń, tras User-Defined lub tabel tras. Komunikat o błędzie w sekcji **objawy** zawiera nazwę zasad.
Aby rozwiązać ten problem, przejrzyj zasady zasobów i określ sposób wdrażania zasobów, które są zgodne z tymi zasadami.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Co to jest usługa Azure Policy?](../../governance/policy/overview.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)
