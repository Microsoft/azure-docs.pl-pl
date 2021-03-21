---
title: Kluczowe różnice dotyczące Machine Learning Services
description: W tym artykule opisano kluczowe różnice między Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL i SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599548"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Kluczowe różnice między usługami Machine Learning Services w usłudze Azure SQL Managed Instance i programie SQL Server

W tym artykule opisano kilka najważniejszych różnic między funkcjami [Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL](machine-learning-services-overview.md) i [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Obsługa języków

Machine Learning Services zarówno w wystąpieniu zarządzanym SQL, jak i SQL Server obsługują [strukturę rozszerzalności](/sql/machine-learning/concepts/extensibility-framework)Python i R. Kluczowe różnice w wystąpieniu zarządzanym SQL:

- Obsługiwane są tylko języki Python i R. Nie można dodać języków zewnętrznych, takich jak Java.

- Początkowe wersje języków Python i R są różne:

  | Platforma                   | Wersja środowiska uruchomieniowego języka Python           | Wersje środowiska uruchomieniowego języka R                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Wystąpienie zarządzane Azure SQL | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 i 3.7.2 (CU22 i nowsze) | 3.3.3 i 3.5.2 (CU22 i nowsze)     |
  | SQL Server 2016            | Niedostępne                    | 3.2.2 i 3.5.2 (SP2 CU14 i nowszych) |

## <a name="python-and-r-packages"></a>Pakiety Python i R

Wystąpienie zarządzane SQL nie obsługuje pakietów, które są zależne od zewnętrznych środowisk uruchomieniowych (takich jak Java) lub potrzebują dostępu do interfejsów API systemu operacyjnego w celu instalacji lub użycia.

Aby uzyskać więcej informacji na temat zarządzania pakietami Python i R, zobacz:

- [Uzyskiwanie informacje o pakiecie Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Uzyskiwanie informacji o pakiecie R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Nadzór nad zasobami

W wystąpieniu zarządzanym SQL nie można ograniczyć zasobów R za poorednictwem [prezesa zasobów](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true), a zewnętrzne pule zasobów nie są obsługiwane.

Domyślnie zasoby języka R są ustawione na maksymalnie 20% dostępnych zasobów wystąpienia zarządzanego SQL, gdy rozszerzalność jest włączona. Aby zmienić ten domyślny procent, Utwórz bilet pomocy technicznej platformy Azure pod adresem [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

Rozszerzalność jest włączona z następującymi poleceniami SQL (wystąpienie zarządzane SQL zostanie ponownie uruchomione i będzie niedostępne przez kilka sekund):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Aby wyłączyć rozszerzalność i przywrócić 100% pamięci i zasobów procesora CPU do SQL Server, użyj następujących poleceń:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

Całkowita liczba zasobów dostępnych dla wystąpienia zarządzanego SQL zależy od wybranej warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure SQL Database modele zakupu](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Błąd niewystarczającej pamięci

Użycie pamięci zależy od ilości używanej w skryptach języka R i liczby wykonywanych zapytań równoległych. Jeśli jest za mało dostępnej pamięci dla języka R, zostanie wyświetlony komunikat o błędzie. Typowe komunikaty o błędach to:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Jeśli zostanie wyświetlony jeden z tych błędów, możesz rozwiązać ten problem, przenosząc bazę danych do wyższej warstwy usług.

## <a name="sql-managed-instance-pools"></a>Pule wystąpień zarządzanych przez program SQL

Machine Learning Services nie jest obecnie obsługiwana w [pulach wystąpienia zarządzanego usługi Azure SQL (wersja zapoznawcza)](instance-pools-overview.md).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z omówieniem [Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL](machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka Python w Machine Learning Services, zobacz [Uruchamianie skryptów Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Aby dowiedzieć się, jak używać języka R w Machine Learning Services, zobacz [Uruchamianie skryptów języka r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
