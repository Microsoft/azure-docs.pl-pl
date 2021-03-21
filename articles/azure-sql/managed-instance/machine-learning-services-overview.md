---
title: Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL
description: Ten artykuł zawiera omówienie lub Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL.
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
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599565"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL

Machine Learning Services to funkcja wystąpienia zarządzanego usługi Azure SQL, która zapewnia Uczenie maszynowe w bazie danych, obsługujące skrypty Python i R. Ta funkcja obejmuje pakiety Microsoft Python i języka R dla wysokiej wydajności analizy predykcyjnej i uczenia maszynowego. Dane relacyjne mogą być używane w skryptach za pomocą procedur składowanych, skryptu T-SQL zawierającego instrukcje języka Python lub języka R lub kodu Python lub R zawierającego język T-SQL.

## <a name="what-is-machine-learning-services"></a>Co to jest Machine Learning Services?

Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL umożliwia wykonywanie skryptów Python i języka R w bazie danych. Za jego pomocą można przygotować i oczyścić dane, przeprowadzić Inżynieria funkcji oraz uczenie, oszacować i wdrożyć modele uczenia maszynowego w ramach bazy danych. Funkcja uruchamia skrypty, w których znajdują się dane, i eliminuje transfer danych przez sieć na inny serwer.

Użyj Machine Learning Services z obsługą języka R/Python w wystąpieniu zarządzanym Azure SQL, aby:

- **Uruchamiaj skrypty języka r i Python w celu przygotowania danych i ogólnego przeznaczenia danych** — możesz teraz przenieść skrypty języka R/Python do wystąpienia zarządzanego usługi Azure SQL, w którym znajdują się dane, zamiast przenosić dane na inny serwer w celu uruchamiania skryptów języka r i Python. Można wyeliminować konieczność przenoszenia danych i związanych z nimi problemów związanych z opóźnieniami, bezpieczeństwem i zgodnością.

- **Uczenie modeli uczenia maszynowego w bazie danych** — można nauczyć modele przy użyciu dowolnych algorytmów Open Source. Możesz łatwo skalować swoje szkolenia do całego zestawu danych, a nie polegać na przykładowych zestawach danych, które są pobierane z bazy.

- **Wdrażaj modele i skrypty w środowisku produkcyjnym w procedurach składowanych** — skrypty i modele przeszkolone mogą być inicjowane po prostu przez osadzenie ich w procedurach składowanych T-SQL. Aplikacje łączące się z wystąpieniem zarządzanym usługi Azure SQL mogą korzystać z prognoz i analiz w tych modelach przez wywołanie procedury składowanej. Można również użyć natywnej funkcji przewidywania T-SQL do operacjonalizować modeli szybkiego oceniania w wysoce współbieżnych scenariuszach w czasie rzeczywistym.

Podstawowe dystrybucje języków Python i R są zawarte w Machine Learning Services. Możesz zainstalować i używać pakietów i struktur typu open source, takich jak PyTorch, TensorFlow i scikit — Dowiedz się, oprócz pakietów firmy Microsoft [biblioteki revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) i [Microsoftml](/sql/machine-learning/python/ref-py-microsoftml) dla języka Python oraz [kolekcję funkcji revoscaler](/sql/machine-learning/r/ref-r-revoscaler), [Microsoftml](/sql/machine-learning/r/ref-r-microsoftml), [OLAP](/sql/machine-learning/r/ref-r-olapr)i [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) w języku R.

## <a name="how-to-enable-machine-learning-services"></a>Jak włączyć Machine Learning Services

Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL można włączyć, włączając rozszerzalność za pomocą następujących poleceń SQL (wystąpienie zarządzane SQL zostanie uruchomione ponownie i będzie niedostępne przez kilka sekund):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Aby uzyskać szczegółowe informacje na temat tego, jak to polecenie ma wpływ na zasoby wystąpienia zarządzanego SQL, zobacz temat [Zarządzanie zasobami](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Włączanie Machine Learning Services w grupie trybu failover

W [grupie trybu failover](failover-group-add-instance-tutorial.md)systemowe bazy danych nie są replikowane do wystąpienia pomocniczego (zobacz [ograniczenia grup trybu failover](../database/auto-failover-group-overview.md#limitations-of-failover-groups) , aby uzyskać więcej informacji).

Jeśli używane wystąpienie zarządzane jest częścią grupy trybu failover, wykonaj następujące czynności:

- Uruchom `sp_configure` polecenia i `RECONFIGURE` w każdym wystąpieniu grupy trybu failover, aby włączyć Machine Learning Services.

- Zainstaluj biblioteki języka R/Python w bazie danych użytkownika, a nie w bazie danych Master.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z kluczowymi różnicami w Machine Learning Services SQL Server](machine-learning-services-differences.md).
- Aby dowiedzieć się, jak używać języka Python w Machine Learning Services, zobacz [Uruchamianie skryptów Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Aby dowiedzieć się, jak używać języka R w Machine Learning Services, zobacz [Uruchamianie skryptów języka r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Aby uzyskać więcej informacji na temat uczenia maszynowego na innych platformach SQL, zobacz [dokumentację usługi SQL Machine Learning](/sql/machine-learning/index).
