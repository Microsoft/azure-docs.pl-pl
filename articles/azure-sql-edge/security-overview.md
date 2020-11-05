---
title: Zabezpieczanie usługi Azure SQL Edge
description: Informacje o zabezpieczeniach w usłudze Azure SQL Edge
keywords: SQL Edge, zabezpieczenia
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: cb673efb3e5d14e72e945bcf8c23d57495823720
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394955"
---
# <a name="securing-azure-sql-edge"></a>Zabezpieczanie usługi Azure SQL Edge

Wraz ze wzrostem liczby rozwiązań IoT i brzegowych między branżami można zwiększyć liczbę urządzeń i dane wygenerowane z tych urządzeń. Zwiększona ilość danych i liczba punktów końcowych urządzenia stanowią znaczący wyzwanie w zakresie zabezpieczeń danych i urządzeń. 

Usługa Azure SQL Edge oferuje wiele funkcji i możliwości, które znacznie ułatwiają Zabezpieczanie danych IoT w ramach SQL Server baz danych. Usługa Azure SQL Edge jest tworzona przy użyciu tego samego aparatu SQL, który zapewnia Microsoft SQL Server i Azure SQL, udostępniając te same funkcje zabezpieczeń, co ułatwia poszerzanie tych samych zasad zabezpieczeń i praktyk z chmury do krawędzi.

Podobnie jak Microsoft SQL Server i Azure SQL, zabezpieczanie wdrożeń usługi Azure SQL Edge może być widoczne jako seria kroków obejmujących cztery obszary: platformy, uwierzytelniania, obiektów (w tym danych) i aplikacji, które uzyskują dostęp do systemu. 

## <a name="platform-and-system-security"></a>Zabezpieczenia platformy i systemu

Platforma usługi Azure SQL Edge obejmuje fizyczny Host platformy Docker, system operacyjny na hoście oraz systemy sieciowe łączące urządzenie fizyczne z aplikacjami i klientami. 

Implementacja zabezpieczeń platformy rozpoczyna się od utrzymywania nieautoryzowanych użytkowników w sieci. Niektóre najlepsze rozwiązania obejmują, ale nie są ograniczone do:
- Implementowanie reguł zapory w celu zapewnienia zasad zabezpieczeń organizacji. 
- Upewnij się, że system operacyjny na urządzeniu fizycznym ma wszystkie najnowsze aktualizacje zabezpieczeń. 
- Określanie i ograniczanie portów hosta, które są używane na potrzeby usługi Azure SQL Edge
- Upewnienie się, że odpowiednia kontrola dostępu jest stosowana do wszystkich woluminów danych, które obsługują dane usługi Azure SQL Edge. 

Aby uzyskać więcej informacji na temat protokołów sieciowych i punktów końcowych usługi Azure SQL Edge, odwołuje się do nich, [protokoły sieciowe i punkty końcowe TDS](//previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja 

### <a name="authentication"></a>Authentication  
Uwierzytelnianie to proces potwierdzania tożsamości użytkownika. Usługa Azure SQL Edge obecnie obsługuje tylko `SQL Authentication` mechanizm.

- *Uwierzytelnianie SQL* :

    Uwierzytelnianie SQL dotyczy uwierzytelnienia użytkownika podczas nawiązywania połączenia z usługą Azure SQL Edge przy użyciu nazwy użytkownika i hasła. Podczas wdrażania programu SQL Edge należy określić hasło logowania do programu SQL **sa** . Następnie administrator serwera może utworzyć dodatkowe identyfikatory logowania i użytkowników SQL, które umożliwią użytkownikom łączenie się przy użyciu nazwy użytkownika i hasła.

    Aby uzyskać więcej informacji na temat tworzenia i zarządzania nazwami logowania i użytkownikami w usłudze SQL Edge, zapoznaj się z tematem [Tworzenie nazwy logowania](/sql/relational-databases/security/authentication-access/create-a-login) i [użytkownika CREATE DATABASE](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autoryzacja   

Autoryzacja odnosi się do uprawnień przypisanych do użytkownika w bazie danych usługi Azure SQL Edge i określa, co użytkownik może zrobić. Uprawnienia są kontrolowane przez dodawanie kont użytkowników do [ról bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles) i przypisywanie uprawnień na poziomie bazy danych do tych ról lub udzielanie użytkownikowi określonych [uprawnień na poziomie obiektów](/sql/relational-databases/security/permissions-database-engine). Aby uzyskać więcej informacji, zobacz [logowania i użytkownicy](../azure-sql/database/logins-create-manage.md).

Najlepszym rozwiązaniem jest utworzenie ról niestandardowych w razie konieczności. Dodaj użytkowników do roli o najniższych uprawnieniach wymaganych do wykonania funkcji zadań. Nie należy przypisywać uprawnień bezpośrednio do użytkowników. Konto administratora serwera jest członkiem wbudowanej roli db_owner, która ma rozległe uprawnienia i powinno być udzielane tylko kilku użytkownikom z zadaniami administracyjnymi. W przypadku aplikacji użyj funkcji [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) , aby określić kontekst wykonywania wywołanego modułu, lub Użyj [ról aplikacji](/sql/relational-databases/security/authentication-access/application-roles) z ograniczonymi uprawnieniami. Dzięki temu aplikacja, która łączy się z bazą danych, ma najniższe uprawnienia wymagane przez aplikację. Poniższe najlepsze rozwiązania wspierają także Rozdzielenie obowiązków.

## <a name="database-object-security"></a>Zabezpieczenia obiektu bazy danych

Podmioty zabezpieczeń są osobami, grupami i procesami, którym udzielono dostępu do programu SQL Edge. "Zabezpieczane" to serwer, baza danych i obiekty zawarte w bazie danych. Każdy z nich ma zestaw uprawnień, które można skonfigurować, aby zmniejszyć obszar powierzchni. Poniższa tabela zawiera informacje dotyczące podmiotów zabezpieczeń i zabezpieczania.

|Aby uzyskać informacje na temat|Zobacz|  
|---------------------------|---------|  
|Użytkownicy serwera i bazy danych, role i procesy|[Aparat bazy danych podmiotów zabezpieczeń](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Zabezpieczenia obiektów serwera i bazy danych|[Obiektów zabezpieczanych](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Szyfrowanie i certyfikaty  
 
Szyfrowanie nie rozwiązuje problemów z kontrolą dostępu. Jednak zwiększa to bezpieczeństwo przez ograniczenie utraty danych, nawet w rzadkich przypadkach, gdy formanty dostępu są pomijane. Na przykład jeśli komputer hosta bazy danych jest niepoprawnie skonfigurowany i złośliwy użytkownik uzyska dane poufne, takie jak numery kart kredytowych, skradzione informacje mogą być bezużyteczne, jeśli są szyfrowane. Poniższa tabela zawiera więcej informacji na temat szyfrowania w usłudze Azure SQL Edge.  
  
|Aby uzyskać informacje na temat|Zobacz|  
|---------------------------|---------|  
|Implementowanie bezpiecznych połączeń|[Szyfrowanie połączeń](/sql/linux/sql-server-linux-encrypted-connections)|  
|Funkcje szyfrowania|[Funkcje kryptograficzne &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Szyfrowanie danych magazynowanych|[Niewidoczne szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Zawsze szyfrowane|[Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> Ograniczenia zabezpieczeń opisane dla [SQL Server on Linux](/sql/linux/sql-server-linux-security-overview) dotyczą również usługi Azure SQL Edge. 


> [!NOTE]
> Usługa Azure SQL Edge nie obejmuje narzędzia MSSQL-conf. Wszystkie konfiguracje, w tym konfiguracja związana z szyfrowaniem, muszą zostać wykonane za pomocą [pliku MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) lub [zmiennych środowiskowych](configure.md#configure-by-using-environment-variables). 


Podobnie jak w przypadku usługi Azure SQL i Microsoft SQL Server, usługa Azure SQL Edge zapewnia ten sam mechanizm do tworzenia i używania certyfikatów w celu zwiększenia bezpieczeństwa obiektów i połączeń. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatu (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Zabezpieczenia aplikacji

### <a name="client-programs"></a>Programy klienckie

Najlepsze rozwiązania w zakresie zabezpieczeń usługi Azure SQL Edge obejmują pisanie bezpiecznych aplikacji klienckich. Aby uzyskać więcej informacji na temat zabezpieczania aplikacji klienckich w warstwie sieciowej, zobacz [Konfiguracja sieci klienta](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>Widoki i funkcje wykazu zabezpieczeń  
Informacje o zabezpieczeniach są udostępniane w kilku widokach i funkcjach, które są zoptymalizowane pod kątem wydajności i narzędzi. Poniższa tabela zawiera informacje o widokach i funkcjach zabezpieczeń w usłudze Azure SQL Edge.  
  
|Funkcje i widoki|Linki|  
|---------------------------|---------|  
|Widoki wykazu zabezpieczeń, które zwracają informacje dotyczące uprawnień na poziomie bazy danych i serwera, podmiotów zabezpieczeń, ról i tak dalej. Ponadto istnieją widoki wykazu, które zawierają informacje na temat kluczy szyfrowania, certyfikatów i poświadczeń.|[Widoki wykazu zabezpieczeń &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Funkcje zabezpieczeń, które zwracają informacje o bieżącym użytkowniku, uprawnieniach i schematach.|[Funkcje zabezpieczeń &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Dynamiczne widoki zarządzania zabezpieczeniami.|[Dynamiczne widoki zarządzania i funkcje związane z zabezpieczeniami &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Inspekcja 

Usługa Azure SQL Edge zapewnia te same mechanizmy inspekcji co SQL Server. Aby uzyskać więcej informacji, zobacz [SQL Server Audit (aparat bazy danych)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie z funkcjami zabezpieczeń](/sql/linux/sql-server-linux-security-get-started)
- [Uruchamianie usługi Azure SQL Edge jako użytkownika niebędącego głównym](configure.md#run-azure-sql-edge-as-non-root-user)
- [Azure Security Center IoT](../defender-for-iot/overview.md)