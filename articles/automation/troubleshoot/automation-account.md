---
title: Rozwiązywanie problemów z kontami Azure Automation
description: W tym artykule opisano sposób rozwiązywania problemów z kontem platformy Azure.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896583"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Rozwiązywanie problemów z kontami Azure Automation

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z konta Azure Automation. Ogólne informacje o kontach usługi Automation można znaleźć w temacie [Azure Automation Authentication Account Overview](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenariusz: nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

### <a name="issue"></a>Problem

Podczas pracy z funkcjami zarządzania, na przykład Update Management, na koncie usługi Automation wystąpi następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Aby zarejestrować dostawcę zasobów usługi Automation, wykonaj następujące kroki w Azure Portal:

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. Przejdź do pozycji **subskrypcje** i wybierz swoją subskrypcję.   

3. W obszarze **Ustawienia** wybierz pozycję **dostawcy zasobów**.

4. Z listy dostawców zasobów Sprawdź, czy dostawca zasobów **Microsoft. Automation** jest zarejestrowany.

5. Jeśli dostawca nie znajduje się na liście, zarejestruj go zgodnie z opisem w artykule [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="next-steps"></a>Następne kroki

Jeśli ten artykuł nie rozwiąże problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) . To oficjalne konto Microsoft Azure do łączenia społeczności platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
