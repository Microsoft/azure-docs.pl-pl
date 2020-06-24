---
title: Kluczowe różnice dotyczące Machine Learning Services (wersja zapoznawcza)
description: W tym temacie opisano kluczowe różnice między Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL i SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: da97938736e7a3719da9d280e60e6a636b86e0e5
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254752"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Kluczowe różnice między Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL i SQL Server

Funkcja [Machine Learning Services w wystąpieniu zarządzanym Azure SQL (wersja zapoznawcza)](machine-learning-services-overview.md) jest niemal identyczna z [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej przedstawiono niektóre kluczowe różnice.

> [!IMPORTANT]
> Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL jest obecnie w publicznej wersji zapoznawczej. Aby się zarejestrować, zobacz artykuł [Rejestrowanie się w celu uzyskania podglądu](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W ramach wersji zapoznawczej usługa ma następujące ograniczenia:

- Połączenia sprzężenia zwrotnego nie działają (zobacz [połączenie sprzężenia zwrotnego z SQL Server ze skryptu Python lub R](/sql/machine-learning/connect/loopback-connection)).
- Zewnętrzne pule zasobów nie są obsługiwane.
- Obsługiwane są tylko Języki Python i R. Nie można dodać języków zewnętrznych, takich jak Java.
- Scenariusze wykorzystujące [interfejs przekazywania komunikatów](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) (MPI) nie są obsługiwane.

W przypadku aktualizacji celu poziomu usługi (SLO) zaktualizuj cel SLO i Zgłoś bilet pomocy technicznej w celu ponownego włączenia dedykowanych limitów zasobów dla języka R/Python.

## <a name="language-support"></a>Obsługa języków

Machine Learning Services w wystąpieniu zarządzanym SQL i SQL Server obsługują zarówno [strukturę rozszerzalności](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)Python, jak i języka R. Kluczowe różnice są następujące:

- Początkowe wersje środowiska Python i języka R różnią się między Machine Learning Services w wystąpieniu zarządzanym SQL i SQL Server:

  |                      | Python | R     |
  |----------------------|--------|-------|
  | Wystąpienie zarządzane SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Nie ma potrzeby konfigurowania `external scripts enabled` za pośrednictwem programu `sp_configure` . Po [zarejestrowaniu](machine-learning-services-overview.md#signup) się w wersji zapoznawczej Uczenie maszynowe jest włączone dla wystąpienia zarządzanego Azure SQL.

## <a name="packages"></a>Pakiety

Zarządzanie pakietami Python i R działa inaczej niż w przypadku wystąpienia zarządzanego SQL i SQL Server. Te różnice są następujące:

- Pakiety nie mogą wykonywać wychodzących wywołań sieciowych. To ograniczenie jest podobne do [domyślnych reguł zapory dla Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) w SQL Server, ale nie można go zmienić w wystąpieniu zarządzanym SQL.
- Pakiety, które są zależne od zewnętrznych środowisk uruchomieniowych (na przykład Java) lub potrzebują dostępu do interfejsów API systemu operacyjnego w celu instalacji lub użycia, nie są obsługiwane.

Aby uzyskać więcej informacji na temat zarządzania pakietami Python i R, zobacz:

- [Uzyskiwanie informacje o pakiecie Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Uzyskiwanie informacji o pakiecie R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe ograniczanie zasobów R przy użyciu [regulatora zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) i pul zasobów zewnętrznych.

W publicznej wersji zapoznawczej zasoby R są ustawione na maksymalnie 20% zasobów wystąpienia zarządzanego SQL i zależą od wybranej warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure SQL Database modele zakupu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Błąd niewystarczającej pamięci

Jeśli jest za mało dostępnej pamięci dla języka R, zostanie wyświetlony komunikat o błędzie. Typowe komunikaty o błędach to:

- Nie można nawiązać komunikacji ze środowiskiem uruchomieniowym dla skryptu "R" dla identyfikatora żądania: * * * * * * *. Sprawdź wymagania środowiska uruchomieniowego "R"
- Wystąpił błąd skryptu "R" podczas wykonywania elementu "sp_execute_external_script" z wartością HRESULT 0x80004004. ... Wystąpił zewnętrzny błąd skryptu: ".. nie można przydzielić pamięci (0 MB) w funkcji C "R_AllocStringBuffer" "
- Wystąpił błąd zewnętrzny skryptu: błąd: nie można przydzielić wektora rozmiaru.

Użycie pamięci zależy od tego, ile jest używane w skryptach języka R i liczby wykonywanych zapytań równoległych. Jeśli otrzymasz powyższe błędy, możesz skalować bazę danych do wyższej warstwy usług, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z omówieniem [Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL](machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka Python w Machine Learning Services, zobacz [Uruchamianie skryptów Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Aby dowiedzieć się, jak używać języka R w Machine Learning Services, zobacz [Uruchamianie skryptów języka r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
