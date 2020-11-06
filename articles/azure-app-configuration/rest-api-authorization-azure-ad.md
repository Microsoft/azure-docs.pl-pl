---
title: Interfejs API REST usługi Azure App Configuration — Azure Active Directory autoryzacja
description: Używanie Azure Active Directory do autoryzacji względem konfiguracji aplikacji platformy Azure przy użyciu interfejsu API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424185"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory autoryzacja — dokumentacja interfejsu API REST

Gdy używane jest uwierzytelnianie Azure Active Directory (Azure AD), autoryzacja jest obsługiwana przez usługę Azure based Access Control (RBAC). Usługa Azure RBAC wymaga, aby użytkownicy mogli być przypisani do ról w celu udzielenia dostępu do zasobów. Każda rola zawiera zestaw akcji, które użytkownicy przypisani do roli będą mogli wykonywać.

## <a name="roles"></a>Role

Następujące role są wbudowanymi rolami, które są domyślnie dostępne w ramach subskrypcji platformy Azure:

- **Właściciel danych konfiguracji aplikacji platformy Azure** : Ta rola zapewnia pełny dostęp do wszystkich operacji.
- **Czytnik danych konfiguracji aplikacji platformy Azure** : Ta rola włącza operacje odczytu.

## <a name="actions"></a>Akcje

Role zawierają listę akcji, które użytkownicy przypisani do tej roli mogą wykonywać. Konfiguracja aplikacji platformy Azure obsługuje następujące akcje:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Ta akcja zezwala na dostęp do odczytu do klucza konfiguracji aplikacji — wartości takich jak/kV i/labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Ta akcja zezwala na dostęp do zapisu do zasobów klucza konfiguracji aplikacji.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Ta akcja umożliwia usunięcie zasobów klucza konfiguracji aplikacji. Uwaga: usunięcie zasobu zwraca klucz-wartość, który został usunięty.

## <a name="errors"></a>błędy

```http
HTTP/1.1 403 Forbidden
```

**Przyczyna:** Podmiot zabezpieczeń, który zgłosił żądanie, nie ma wymaganych uprawnień do wykonania żądanej operacji.
**Rozwiązanie:** Przypisz rolę wymaganą do wykonania żądanej operacji do podmiotu zabezpieczeń wysyłającego żądanie.

## <a name="managing-role-assignments"></a>Zarządzanie przypisaniami ról

Zarządzanie przypisaniami ról odbywa się przy użyciu procedur [RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) , które są standardowe dla wszystkich usług platformy Azure. Można to zrobić za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell, Azure Portal i innych. Oficjalnej dokumentacji dotyczącej sposobu tworzenia przypisań ról można znaleźć [tutaj](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
