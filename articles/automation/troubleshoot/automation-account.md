---
title: Rozwiązywanie problemów z Azure Automation konta
description: Dowiedz się, jak rozwiązywać problemy z kontem platformy Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864134"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Rozwiązywanie problemów z kontem Azure Automation

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z konta Azure Automation. Aby uzyskać ogólne informacje o kontach usługi Automation, zobacz [Tworzenie konta na platformie Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenariusz: nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

### <a name="issue"></a>Problem

Gdy pracujesz z rozwiązaniami do zarządzania na koncie usługi Automation, wystąpi następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Aby zarejestrować dostawcę zasobów usługi Automation, wykonaj następujące kroki w Azure Portal:

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. Przejdź do pozycji **subskrypcje**i wybierz swoją subskrypcję.   

3. W obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.

4. Z listy dostawców zasobów Sprawdź, czy dostawca zasobów **Microsoft. Automation** jest zarejestrowany.

5. Jeśli dostawca nie znajduje się na liście, zarejestruj go zgodnie z opisem w artykule [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Następne kroki

Jeśli ten artykuł nie rozwiąże problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz się [@AzureSupport](https://twitter.com/azuresupport)z. To oficjalne konto Microsoft Azure do łączenia społeczności platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.