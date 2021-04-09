---
title: Interfejs API REST usługi Azure App Configuration — Azure Active Directory autoryzacja
description: Używanie Azure Active Directory do autoryzacji względem konfiguracji aplikacji platformy Azure przy użyciu interfejsu API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092787"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory autoryzacja — dokumentacja interfejsu API REST

W przypadku korzystania z uwierzytelniania Azure Active Directory (Azure AD) Autoryzacja jest obsługiwana przez kontrolę dostępu opartą na rolach (RBAC). RBAC wymaga, aby użytkownicy mogli być przypisani do ról w celu udzielenia dostępu do zasobów. Każda rola zawiera zestaw akcji, które użytkownicy przypisani do roli mogą wykonywać.

## <a name="roles"></a>Role

Następujące role są domyślnie dostępne w ramach subskrypcji platformy Azure:

- **Właściciel danych konfiguracji aplikacji platformy Azure**: Ta rola zapewnia pełny dostęp do wszystkich operacji.
- **Czytnik danych konfiguracji aplikacji platformy Azure**: Ta rola włącza operacje odczytu.

## <a name="actions"></a>Akcje

Role zawierają listę akcji, które użytkownicy przypisani do tej roli mogą wykonywać. Konfiguracja aplikacji platformy Azure obsługuje następujące akcje:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Ta akcja zezwala na dostęp do odczytu do zasobów klucza konfiguracji aplikacji, takich jak/kV i/labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Ta akcja zezwala na dostęp do zapisu do zasobów klucza konfiguracji aplikacji.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Ta akcja umożliwia usunięcie zasobów klucza konfiguracji aplikacji. Należy pamiętać, że usunięcie zasobu zwraca klucz-wartość, który został usunięty.

## <a name="error"></a>Błąd

```http
HTTP/1.1 403 Forbidden
```

**Przyczyna:** Podmiot zabezpieczeń, który zgłosił żądanie, nie ma wymaganych uprawnień do wykonania żądanej operacji.
**Rozwiązanie:** Przypisz rolę wymaganą do wykonania żądanej operacji do podmiotu zabezpieczeń wysyłającego żądanie.

## <a name="managing-role-assignments"></a>Zarządzanie przypisaniami ról

Przypisaniami ról można zarządzać przy użyciu [procedur RBAC platformy Azure](../role-based-access-control/overview.md) , które są standardowe dla wszystkich usług platformy Azure. Można to zrobić za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell i Azure Portal. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).