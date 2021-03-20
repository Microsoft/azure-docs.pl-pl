---
title: Konfigurowanie replikacji do usługi Azure SQL Edge
description: Dowiedz się więcej o konfigurowaniu replikacji do usługi Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395227"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Konfigurowanie replikacji do usługi Azure SQL Edge 

Można skonfigurować wystąpienie usługi Azure SQL Edge jako subskrybent wypychany dla jednokierunkowej replikacji transakcyjnej lub replikacji migawek. To wystąpienie nie może działać jako Wydawca lub dystrybutor dla konfiguracji replikacji transakcyjnej. Należy pamiętać, że usługa Azure SQL Edge nie obsługuje replikacji scalającej, replikacji równorzędnej lub publikacji firmy Oracle.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje
  
- Wystąpienie usługi Azure SQL Edge musi być subskrybentem wypychanym dla wydawcy.
- Wydawca i dystrybutor może być:
   - Wystąpienie SQL Server uruchomione lokalnie lub wystąpienie SQL Server uruchomione na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [SQL Server na platformie Azure — omówienie Virtual Machines](../azure-sql/virtual-machines/index.yml). Wystąpienia SQL Server muszą używać wersji nowszej niż SQL Server 2016.
   - Wystąpienie wystąpienia zarządzanego usługi Azure SQL. Wystąpienie zarządzane SQL może hostować bazy danych wydawcy, dystrybutora i subskrybentów. Aby uzyskać więcej informacji, zobacz [replikacja z wystąpieniem zarządzanym SQL Database](/azure/sql-database/replication-with-sql-database-managed-instance/).

- Nie można umieścić bazy danych dystrybucji i agentów replikacji w wystąpieniu usługi Azure SQL Edge.  

> [!NOTE]
> Jeśli spróbujesz skonfigurować replikację przy użyciu nieobsługiwanej wersji, mogą pojawić się następujące dwa błędy: MSSQL_REPL20084 ("proces nie może połączyć się z subskrybentem.") i MSSQL_REPL40532 ("nie można otworzyć serwera \<name> żądanego przez nazwę logowania. Logowanie nie powiodło się.  

## <a name="remarks"></a>Uwagi

Poniższe wymagania i najlepsze rozwiązania są ważne, aby zrozumieć, jak skonfigurować replikację:

- Replikację można skonfigurować przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Można to również zrobić, uruchamiając na wydawcy instrukcje języka Transact-SQL, korzystając z SQL Server Management Studio lub [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
- Aby przeprowadzić replikację do wystąpienia usługi Azure SQL Edge, musisz użyć uwierzytelniania SQL Server, aby się zalogować.
- Zreplikowane tabele muszą mieć klucz podstawowy.
- Jedna publikacja na SQL Server może obsługiwać zarówno usługi Azure SQL Edge, jak i SQL Server (lokalne i SQL Server w ramach maszyn wirtualnych platformy Azure).  
- Zarządzanie replikacją, monitorowanie i rozwiązywanie problemów należy wykonać z wystąpienia SQL Server.  
- Obsługiwane są tylko subskrypcje wypychane do usługi Azure SQL Edge.  
- `@subscriber_type = 0`Obsługiwane tylko w procedurze składowanej `sp_addsubscription` dla usługi Azure SQL Edge.  
- Usługa Azure SQL Edge nie obsługuje dwukierunkowej, możliwej do aktualizacji ani replikacji równorzędnej.
- Usługa Azure SQL Edge obsługuje tylko podzestaw funkcji dostępnych w SQL Server lub wystąpieniu zarządzanym SQL. Próba replikacji bazy danych (lub obiektów w bazie danych), która zawiera co najmniej jedną nieobsługiwaną funkcję, nie powiedzie się. Na przykład, jeśli próbujesz zreplikować bazę danych zawierającą obiekty z danymi przestrzennymi, zostanie wyświetlony komunikat o błędzie. Aby uzyskać więcej informacji, zobacz [obsługiwane funkcje usługi Azure SQL Edge](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Zainicjuj dane referencyjne w wystąpieniu usługi Azure SQL Edge

Możliwe jest zainicjowanie wystąpienia z danymi referencyjnymi, które zmieniają się wraz z upływem czasu. Na przykład możesz chcieć zaktualizować modele uczenia maszynowego w wystąpieniu usługi Azure SQL Edge po przeszkoleniu ich na SQL Server wystąpieniu. Oto jak zainicjować wystąpienie w taki sposób:

1. Utwórz publikację z replikacją transakcyjną w bazie danych SQL Server.  
2. Na wystąpieniu SQL Server Użyj **Kreatora nowej subskrypcji** lub instrukcji języka Transact-SQL, aby utworzyć wypychanie do subskrypcji usługi Azure SQL Edge.  
3. Zreplikowaną bazę danych można zainicjować w usłudze Azure SQL Edge przy użyciu migawki wygenerowanej przez agenta migawek i dystrybuowanej i dostarczonej przez agenta dystrybucji. Alternatywnie można zainicjować za pomocą kopii zapasowej bazy danych od wydawcy. Należy pamiętać, że jeśli kopia zapasowa bazy danych zawiera obiekty lub funkcje nieobsługiwane przez usługę Azure SQL Edge, operacja przywracania kończy się niepowodzeniem.

## <a name="limitations"></a>Ograniczenia

Następujące opcje nie są obsługiwane w przypadku subskrypcji usługi Azure SQL Edge:

- Kopiuj skojarzenie grup plików  
- Kopiuj schematy partycjonowania tabel  
- Kopiuj schematy partycjonowania indeksów  
- Kopiowanie statystyk zdefiniowanych przez użytkownika  
- Kopiuj domyślne powiązania  
- Kopiuj powiązania reguły  
- Kopiuj indeksy pełnotekstowy  
- Kopiuj plik XSD XML  
- Kopiuj indeksy XML  
- Uprawnienia do kopiowania  
- Kopiuj indeksy przestrzenne  
- Kopiuj filtrowane indeksy  
- Kopiuj atrybut kompresji danych  
- Kopiuj atrybut kolumny rozrzedzonej  
- Kopiuj `hierarchyid` dane typu FILESTREAM lub przestrzenne
- Konwertuj `hierarchyid` na maksymalne typy danych  
- Konwertuj dane przestrzenne na wartości typu MAX  
- Kopiuj właściwości rozszerzone  
- Uprawnienia do kopiowania  

## <a name="examples"></a>Przykłady

Utwórz publikację i subskrypcję wypychaną. Aby uzyskać więcej informacji, zobacz:
  
- [Tworzenie publikacji](/sql/relational-databases/replication/publish/create-a-publication)
- [Utwórz subskrypcję wypychaną](/sql/relational-databases/replication/create-a-push-subscription/) przy użyciu nazwy i adresu IP serwera usługi Azure SQL Edge jako subskrybenta (na przykład **myEdgeinstance, 1433**) i nazwy bazy danych w wystąpieniu usługi Azure SQL Edge jako docelowej bazy danych (na przykład **AdventureWorks**).  

## <a name="next-steps"></a>Następne kroki  

- [Tworzenie publikacji](/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikacji](/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](/sql/relational-databases/replication/initialize-a-subscription)