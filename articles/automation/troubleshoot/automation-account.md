---
title: Rozwiązywanie problemów z kontem usługi Automation
description: Dowiedz się, jak rozwiązywać problemy z kontem platformy Azure i rozwiązywać je.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679386"
---
# <a name="troubleshoot-the-automation-account"></a>Rozwiązywanie problemów z kontem automatyzacji

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z konta automatyzacji. W poniższych sekcjach wyróżniono określone komunikaty o błędach i możliwe rozwiązania dla każdego z nich. Aby uzyskać ogólne informacje dotyczące kont automatyzacji, zobacz [Tworzenie konta platformy Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenariusz: Nie można zarejestrować dostawcy zasobów automatyzacji dla subskrypcji

### <a name="issue"></a>Problem

Podczas pracy z rozwiązaniami do zarządzania na koncie automatyzacji pojawia się następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów automatyzacji nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Aby zarejestrować dostawcę zasobów automatyzacji, wykonaj następujące kroki w witrynie Azure portal:

1. W przeglądarce przejdź do [witryny Azure portal](https://portal.azure.com).

2. Przejdź do **pozycji Subskrypcje** i wybierz subskrypcję ze strony Subskrypcje.   

3. W obszarze **Ustawienia**wybierz pozycję **Dostawcy zasobów**.

4. Z listy dostawców zasobów sprawdź, czy dostawca zasobów **microsoft.automation** jest zarejestrowany.

5. Jeśli dostawcy nie ma na liście, zarejestruj go zgodnie z opisem w [obszarze Rozwiązywanie błędów rejestracji dostawcy zasobów](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.