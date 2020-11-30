---
title: Kluczowe różnice dotyczące Machine Learning Services (wersja zapoznawcza)
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
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324537"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Kluczowe różnice między usługami Machine Learning Services w usłudze Azure SQL Managed Instance i programie SQL Server

Funkcja [Machine Learning Services w wystąpieniu zarządzanym Azure SQL (wersja zapoznawcza)](machine-learning-services-overview.md) jest niemal identyczna z [SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej przedstawiono niektóre kluczowe różnice.

> [!IMPORTANT]
> Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL jest obecnie w publicznej wersji zapoznawczej. Aby się zarejestrować, zobacz artykuł [Rejestrowanie się w celu uzyskania podglądu](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Jeśli korzystasz z wersji zapoznawczej, usługa ma następujące ograniczenia:

- Połączenia sprzężenia zwrotnego nie działają (zobacz [połączenie sprzężenia zwrotnego z SQL Server ze skryptu Python lub R](/sql/machine-learning/connect/loopback-connection)).
- Zewnętrzne pule zasobów nie są obsługiwane.
- Obsługiwane są tylko języki Python i R. Nie można dodać języków zewnętrznych, takich jak Java.
- Scenariusze wykorzystujące [interfejs przekazywania komunikatów](/message-passing-interface/microsoft-mpi) (MPI) nie są obsługiwane.

W przypadku aktualizacji celu poziomu usługi należy zaktualizować cel SLO i zgłosić bilet pomocy technicznej w celu ponownego włączenia dedykowanych limitów zasobów dla języka R/Python.

## <a name="language-support"></a>Obsługa języków

Machine Learning Services w wystąpieniu zarządzanym SQL i SQL Server obsługują zarówno [strukturę rozszerzalności](/sql/advanced-analytics/concepts/extensibility-framework)Python, jak i języka R. Kluczowe różnice są następujące:

- Początkowe wersje środowiska Python i języka R różnią się między Machine Learning Services w wystąpieniu zarządzanym SQL i SQL Server:

  | System               | Python | R     |
  |----------------------|--------|-------|
  | Wystąpienie zarządzane SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Nie ma potrzeby konfigurowania `external scripts enabled` za pośrednictwem programu `sp_configure` . Po [zarejestrowaniu](machine-learning-services-overview.md#signup) się w wersji zapoznawczej Uczenie maszynowe jest włączone dla wystąpienia zarządzanego Azure SQL.

## <a name="packages"></a>Pakiety

Zarządzanie pakietami Python i R działa inaczej niż w przypadku wystąpienia zarządzanego SQL i SQL Server. Te różnice są następujące:

- Pakiety, które są zależne od zewnętrznych środowisk uruchomieniowych (na przykład Java) lub potrzebują dostępu do interfejsów API systemu operacyjnego w celu instalacji lub użycia, nie są obsługiwane.
- Pakiety mogą wykonywać wychodzące wywołania sieciowe (zmienić z wcześniejszego poziomu w wersji zapoznawczej). Aby włączyć wychodzące wywołania sieciowe, można ustawić odpowiednie reguły zabezpieczeń dla ruchu wychodzącego na poziomie [grupy zabezpieczeń sieci](../../virtual-network/network-security-groups-overview.md) .

Aby uzyskać więcej informacji na temat zarządzania pakietami Python i R, zobacz:

- [Uzyskiwanie informacje o pakiecie Python](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Uzyskiwanie informacji o pakiecie R](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe ograniczanie zasobów R przy użyciu [regulatora zasobów](/sql/relational-databases/resource-governor/resource-governor) i pul zasobów zewnętrznych.

W publicznej wersji zapoznawczej zasoby języka R są ustawione na maksymalnie 20% zasobów usługi SQL Managed Instance i zależą od wybranej warstwy usługi. Aby uzyskać więcej informacji, zobacz [Azure SQL Database modele zakupu](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Błąd niewystarczającej pamięci

Ten komunikat o błędzie jest wyświetlany, jeśli ilość pamięci dostępna dla języka R jest niewystarczająca. Typowe komunikaty o błędach to:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Użycie pamięci zależy od ilości używanej w skryptach języka R i liczby wykonywanych zapytań równoległych. W przypadku wyświetlenia powyższych komunikatów o błędach możesz skalować bazę danych do wyższej warstwy usług, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z omówieniem [Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL](machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka Python w Machine Learning Services, zobacz [Uruchamianie skryptów Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Aby dowiedzieć się, jak używać języka R w Machine Learning Services, zobacz [Uruchamianie skryptów języka r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).