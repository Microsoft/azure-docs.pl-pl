---
title: Rozwiązywanie problemów z kontem usługi Automation
description: Dowiedz się, jak rozwiązywać problemy z kontem platformy Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679386"
---
# <a name="troubleshoot-the-automation-account"></a>Rozwiązywanie problemów z kontem usługi Automation

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z konta usługi Automation. W poniższych sekcjach wyróżniono określone komunikaty o błędach i możliwe rozwiązania dla każdego z nich. Aby uzyskać ogólne informacje o kontach usługi Automation, zobacz [Tworzenie konta na platformie Azure](../automation-quickstart-create-account.md).

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

2. Przejdź do **subskrypcji** i wybierz subskrypcję na stronie Subskrypcje.   

3. W obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.

4. Z listy dostawców zasobów Sprawdź, czy dostawca zasobów **Microsoft. Automation** jest zarejestrowany.

5. Jeśli dostawca nie znajduje się na liście, zarejestruj go zgodnie z opisem w artykule [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz Twojego problemu powyżej lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.