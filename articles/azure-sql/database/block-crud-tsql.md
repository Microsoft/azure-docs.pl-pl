---
title: Blokuj polecenia T-SQL w celu tworzenia lub modyfikowania zasobów usługi Azure SQL
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: W tym artykule szczegółowo opisano funkcję umożliwiającą administratorom platformy Azure Blokowanie poleceń języka T-SQL w celu tworzenia lub modyfikowania zasobów usługi Azure SQL
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556138"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Co to jest funkcja CRUD w języku T-SQL?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Ta funkcja umożliwia administratorom platformy Azure blokowanie tworzenia lub modyfikowania zasobów usługi Azure SQL przy użyciu języka T-SQL. Jest to wymuszane na poziomie subskrypcji, aby blokować polecenia T-SQL wpływające na zasoby SQL w dowolnej usłudze Azure SQL Database lub wystąpieniu zarządzanym.

## <a name="overview"></a>Omówienie

Aby zablokować Tworzenie lub modyfikowanie zasobów przy użyciu języka T-SQL i wymuszać zarządzanie zasobami za pomocą szablonu Azure Resource Manager (szablon ARM) dla danej subskrypcji, można użyć funkcji w wersji zapoznawczej poziomu subskrypcji w Azure Portal. Jest to szczególnie przydatne w przypadku korzystania z [zasad platformy Azure](/azure/governance/policy/overview) w celu wymuszenia standardów organizacyjnych za pomocą szablonów ARM. Ponieważ język T-SQL nie jest zgodny z zasadami platformy Azure, można zastosować blok operacji tworzenia lub modyfikowania w języku T-SQL. Blokowana składnia zawiera instrukcje CRUD (Create, Update, Delete) dla baz danych w usłudze Azure SQL, w tym `CREATE DATABASE` `ALTER DATABASE` `DROP DATABASE` instrukcji i. 

Operacje CRUD T-SQL można zablokować za pomocą Azure Portal, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Uprawnienia

Aby można było zarejestrować lub usunąć tę funkcję, użytkownik platformy Azure musi być członkiem roli właściciela lub współautora subskrypcji.

## <a name="examples"></a>Przykłady

W poniższej sekcji opisano, jak można zarejestrować lub wyrejestrować funkcję w wersji zapoznawczej przy użyciu dostawcy zasobów Microsoft. SQL w Azure Portal: 

### <a name="register-block-t-sql-crud"></a>Zarejestruj blok T-SQL CRUD

1. Przejdź do subskrypcji na Azure Portal.
2. Wybierz pozycję na karcie **funkcje wersji zapoznawczej** . 
3. Wybierz pozycję **Blokuj T-SQL CRUD**.
4. Po wybraniu opcji **Zablokuj CRUD T-SQL** zostanie otwarte nowe okno, a następnie wybierz pozycję **zarejestruj**, aby zarejestrować ten blok przy użyciu dostawcy zasobów Microsoft. SQL.

![Wybierz pozycję "Blokuj T-SQL CRUD" na liście funkcji w wersji zapoznawczej](./media/block-tsql-crud/block-tsql-crud.png)

![Po zaznaczeniu pola wyboru "Blokuj T-SQL CRUD" Wybierz pozycję Zarejestruj](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Zarejestruj ponownie dostawcę zasobów Microsoft. SQL 
Po zarejestrowaniu bloku T-SQL CRUD z dostawcą zasobów Microsoft. SQL należy ponownie zarejestrować dostawcę zasobów Microsoft. SQL, aby zmiany zaczęły obowiązywać. Aby ponownie zarejestrować dostawcę zasobów Microsoft. SQL:

1. Przejdź do subskrypcji na Azure Portal.
2. Wybierz pozycję na karcie **dostawcy zasobów** .
3. Wyszukaj i wybierz pozycję Dostawca zasobów **Microsoft. SQL** .
4. Wybierz pozycję **zarejestruj ponownie**. 

> [!NOTE]
> Krok ponownej rejestracji jest obowiązkowy dla bloku T-SQL, który ma zostać zastosowany do subskrypcji. 

![Zarejestruj ponownie dostawcę zasobów Microsoft. SQL](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Usuwanie bloku T-SQL CRUD
Aby usunąć blok dla operacji tworzenia lub modyfikowania instrukcji T-SQL z subskrypcji, najpierw Wyrejestruj wcześniej zarejestrowany blok T-SQL. Następnie ponownie zarejestruj dostawcę zasobów Microsoft. SQL, jak pokazano powyżej, aby usunąć blok T-SQL, który zacznie obowiązywać. 


## <a name="next-steps"></a>Następne kroki

- [Przegląd możliwości zabezpieczeń Azure SQL Database](security-overview.md)
- [Najlepsze rozwiązania w zakresie zabezpieczeń Azure SQL Database](security-best-practice.md)
