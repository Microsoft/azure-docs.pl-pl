---
title: Integracja z zasadami platformy Azure
description: Azure Policy to usługa platformy Azure służąca do tworzenia, przypisywania i zarządzania zasadami, które wymuszają stosowanie reguł do zasobów.
ms.topic: article
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: a160de1277afea026a16f470c8f76cdc2ec1733f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184269"
---
# <a name="integration-with-azure-policy"></a>Integracja z usługą Azure Policy

Azure Policy to usługa platformy Azure służąca do tworzenia, przypisywania i zarządzania zasadami, które wymuszają reguły dotyczące zasobów, aby zapewnić, że te zasoby pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. Azure Policy szacuje zasoby pod kątem braku zgodności z przypisaną zasadami. 

Azure Batch ma dwa wbudowane rozszerzenia ułatwiające zarządzanie zgodnością zasad. 

|**Nazwa**...|   **Opis**|**Efekt (s)**|  **Wersja**|    **Element źródłowy**
|----------------|----------|----------|----------------|---------------|
|Należy włączyć dzienniki diagnostyczne na kontach wsadowych|   Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci|AuditIfNotExists, wyłączone|  2.0.0|  GitHub|
|Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych| Inspekcja konfiguracji reguł alertów metryk na koncie wsadowym, aby włączyć wymaganą metrykę|   AuditIfNotExists, wyłączone| 1.0.0|  GitHub|

Definicje zasad opisują warunki, które muszą zostać spełnione. Warunek porównuje właściwość zasobu z wymaganą wartością. Pola właściwości zasobu są dostępne przy użyciu wstępnie zdefiniowanych aliasów. Aliasy właściwości są używane do uzyskiwania dostępu do określonych właściwości dla typu zasobu. Aliasy umożliwiają ograniczenie wartości lub warunków dozwolonych dla właściwości zasobu. Każdy alias mapuje do ścieżek w różnych wersjach interfejsu API dla danego typu zasobu. Podczas obliczania zasad aparat zasad Pobiera ścieżkę właściwości dla tej wersji interfejsu API.

Zasoby wymagane przez zadanie wsadowe obejmują: konto, węzeł obliczeniowy, pulę, zadanie i zadanie. Aby uzyskać dostęp do określonych właściwości dla tych zasobów, należy użyć aliasów właściwości. Dowiedz się więcej o [aliasach](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Aby upewnić się, że znasz bieżące aliasy i zapoznaj się z zasobami i zasadami, użyj rozszerzenia Azure Policy Visual Studio Code. Można ją zainstalować na wszystkich platformach obsługiwanych przez Visual Studio Code. Ta obsługa obejmuje systemy Windows, Linux i macOS. Zobacz [wskazówki dotyczące instalacji](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



