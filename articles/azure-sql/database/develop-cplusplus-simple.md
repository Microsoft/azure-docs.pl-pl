---
title: Nawiązywanie połączenia z SQL Database przy użyciu języka C i C++
description: Użyj przykładowego kodu w tym przewodniku Szybki Start, aby skompilować nowoczesne aplikacje przy użyciu języka C++ i obsługiwane przez zaawansowaną relacyjną bazę danych w chmurze z Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: e891c5797c9ce93e6cab7a07d2f68de1a9157249
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674767"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Nawiązywanie połączenia z SQL Database przy użyciu języka C i C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten wpis jest przeznaczony dla deweloperów C i C++ próbujących nawiązać połączenie z Azure SQL Database. Jest on podzielony na sekcje, dzięki czemu możesz przejść do sekcji, która najlepiej przechwytuje zainteresowania.

## <a name="prerequisites-for-the-cc-tutorial"></a>Wymagania wstępne dotyczące samouczka C/C++

Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Program Visual Studio](https://www.visualstudio.com/downloads/). Aby skompilować i uruchomić ten przykład, należy zainstalować składniki języka C++.
* [Programowanie dla systemu Linux w programie Visual Studio](/cpp/linux/?view=vs-2019). W przypadku tworzenia aplikacji w systemie Linux należy również zainstalować rozszerzenie programu Visual Studio Linux.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database i SQL Server na maszynach wirtualnych

Azure SQL Database jest oparta na Microsoft SQL Server i została zaprojektowana w celu zapewnienia wysokiej dostępności, wydajnej i skalowalnej usługi. Korzystanie z usługi Azure SQL w ramach własnej bazy danych działającego lokalnie ma wiele zalet. Za pomocą usługi Azure SQL nie trzeba instalować, konfigurować i obsługiwać bazy danych ani zarządzać nią, ale tylko zawartością i strukturą bazy danych. Typowe rzeczy, z którymi obawiamy się z bazami danych, takie jak odporność na uszkodzenia i nadmiarowość, są wbudowane.

Platforma Azure ma obecnie dwie opcje hostingu obciążeń programu SQL Server: Azure SQL Database, baza danych jako usługa i program SQL Server na Virtual Machines (VM). Nie będziemy szczegółowo dowiedzieć się więcej o różnicach między tymi dwoma, z wyjątkiem tego, że Azure SQL Database jest najlepszym trafieniem dla nowych aplikacji opartych na chmurze, aby skorzystać z oszczędności kosztów i optymalizacji wydajności, które zapewniają usługi w chmurze. Jeśli rozważasz Migrowanie lub rozszerzanie aplikacji lokalnych do chmury, program SQL Server na maszynie wirtualnej platformy Azure może jeszcze bardziej usprawnić działanie. Aby zachować łatwość działania w tym artykule, Utwórzmy Azure SQL Database.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologie dostępu do danych: ODBC i OLE DB

Łączenie się z Azure SQL Databaseami nie różni się i obecnie istnieją dwa sposoby łączenia się z bazami danych: ODBC (Open Database Connectivity) i OLE DB (łączenie obiektów i osadzanie bazy danych). W ostatnich latach firma Microsoft połączyła się z [ODBC na potrzeby natywnego dostępu do danych relacyjnych](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access). ODBC jest stosunkowo prosta, a także znacznie szybszy niż OLE DB. Jedynym zastrzeżeniem jest to, że ODBC używa starego interfejsu API w stylu C.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Krok 1. Tworzenie Azure SQL Database

Zobacz [stronę wprowadzenie](single-database-create-quickstart.md) , aby dowiedzieć się, jak utworzyć przykładową bazę danych.  Możesz też wykonać następujące [krótkie wideo z dwoma minutami](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) , aby utworzyć Azure SQL Database przy użyciu Azure Portal.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Krok 2. Uzyskiwanie parametrów połączenia

Po zainicjowaniu obsługi Azure SQL Database należy wykonać następujące czynności, aby określić informacje o połączeniu i dodać adres IP klienta na potrzeby dostępu do zapory.

W [Azure Portal](https://portal.azure.com/)przejdź do Azure SQL Database parametrów połączenia ODBC przy użyciu polecenia **Pokaż parametry połączenia bazy danych** wymienione jako część sekcji Przegląd dla bazy danych:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Skopiuj zawartość **ODBC (w tym Node.js) [uwierzytelnianie SQL]** . Ten ciąg jest używany później do łączenia się z naszym interpreterem wiersza polecenia języka C++ ODBC. Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwer i inne parametry połączenia z bazą danych.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Krok 3. Dodawanie adresu IP do zapory

Przejdź do sekcji Zapora serwera i Dodaj [adres IP klienta do zapory, wykonując następujące kroki](firewall-configure.md) , aby upewnić się, że możemy nawiązać połączenie:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

W tym momencie skonfigurowano Azure SQL Database i wszystko jest gotowe do nawiązania połączenia z kodem języka C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Krok 4. Łączenie z aplikacji systemu Windows C/C++

Możesz łatwo połączyć się z [Azure SQL Database przy użyciu interfejsu ODBC w systemie Windows przy użyciu tego przykładu](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) , który kompiluje się z programem Visual Studio. Przykład implementuje interpreter wiersza polecenia ODBC, którego można użyć do nawiązania połączenia z naszym Azure SQL Database. Ten przykład pobiera plik nazwy źródła bazy danych (DSN) jako argument wiersza polecenia lub pełne parametry połączenia, które zostały wcześniej skopiowane z Azure Portal. Wywołaj stronę właściwości dla tego projektu i wklej parametry połączenia jako argument polecenia, jak pokazano poniżej:

![Propsfile DSN](./media/develop-cplusplus-simple/props.png)

Upewnij się, że podajesz odpowiednie szczegóły uwierzytelniania dla bazy danych w ramach tego ciągu połączenia z bazą danych.

Uruchom aplikację, aby ją skompilować. Powinno zostać wyświetlone następujące okno z walidacją pomyślnego nawiązania połączenia. Możesz nawet uruchamiać podstawowe polecenia SQL, takie jak **Tworzenie tabeli** , aby sprawdzić poprawność łączności z bazą danych:

![Polecenia SQL](./media/develop-cplusplus-simple/sqlcommands.png)

Alternatywnie można utworzyć plik DSN przy użyciu kreatora, który jest uruchamiany, gdy nie podano żadnych argumentów polecenia. Zalecamy również wypróbowanie tej opcji. Tego pliku DSN można użyć do automatyzacji i ochrony ustawień uwierzytelniania:

![Utwórz plik DSN](./media/develop-cplusplus-simple/datasource.png)

Gratulacje! Pomyślnie nawiązano połączenie z usługą Azure SQL przy użyciu języka C++ i ODBC w systemie Windows. Można kontynuować odczytywanie w taki sam sposób dla platformy Linux.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Krok 5. Łączenie z aplikacji systemu Linux C/C++

Jeśli nie zostały jeszcze wysłuchane wiadomości, program Visual Studio umożliwia teraz również opracowywanie aplikacji dla systemu Linux w języku C++. Informacje o tym nowym scenariuszu można znaleźć w blogu [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . Aby można było skompilować system Linux, potrzebna jest maszyna zdalna, w której działa system Linux dystrybucji. Jeśli nie masz takiego dostępu, możesz je skonfigurować szybko przy użyciu [maszyn wirtualnych systemu Linux Azure](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym samouczku Przypuśćmy, że masz konfigurację dystrybucji systemu Linux Ubuntu 16,04. Kroki opisane w tym miejscu należy również stosować do Ubuntu 15,10, Red Hat 6 i Red Hat 7.

Poniższe kroki instalują biblioteki wymagane przez SQL i ODBC dla dystrybucji:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Uruchom program Visual Studio. W obszarze Narzędzia — Opcje > — > wielu platform — > Menedżer połączeń, Dodaj połączenie do pola systemu Linux:

![Opcje narzędzi](./media/develop-cplusplus-simple/tools.png)

Po ustanowieniu połączenia za pośrednictwem protokołu SSH Utwórz pusty szablon projektu (Linux):

![Nowy szablon projektu](./media/develop-cplusplus-simple/template.png)

Następnie można dodać [nowy plik źródłowy C i zastąpić go tą zawartością](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Korzystając z interfejsów API ODBC SQLAllocHandle, SQLSetConnectAttr i SQLDriverConnect, powinno być możliwe zainicjowanie i nawiązanie połączenia z bazą danych.
Podobnie jak w przypadku przykładu ODBC dla systemu Windows, należy zastąpić Wywołanie SQLDriverConnect danymi z parametrów połączenia bazy danych skopiowanych z Azure Portal wcześniej.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

Ostatnim zadaniem do wykonania przed kompilacją jest dodanie **ODBC** jako zależności biblioteki:

![Dodawanie ODBC jako biblioteki wejściowej](./media/develop-cplusplus-simple/lib.png)

Aby uruchomić aplikację, wywołaj konsolę systemu Linux z menu **Debuguj** :

![Konsola systemu Linux](./media/develop-cplusplus-simple/linuxconsole.png)

Jeśli połączenie zakończyło się pomyślnie, w konsoli systemu Linux powinna zostać wyświetlona nazwa bieżącej bazy danych:

![Dane wyjściowe okna konsoli systemu Linux](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Gratulacje! Samouczek został pomyślnie ukończony i teraz można połączyć się z Azure SQL Database z poziomu języka C++ na platformach z systemami Windows i Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Pobierz kompletne rozwiązanie dla samouczka C/C++

Można znaleźć rozwiązanie getstarted, które zawiera wszystkie przykłady w tym artykule w witrynie GitHub:

* [Przykład ODBC w systemie Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), pobieranie PRZYKŁADu ODBC dla systemu Windows c++ do łączenia z usługą Azure SQL
* [Przykład ODBC c++](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)w systemie Linux, pobieranie PRZYKŁADowego ODBC z systemem Linux c++ w celu nawiązania połączenia z usługą Azure SQL

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [omówieniem opracowywania SQL Database](develop-overview.md)
* Więcej informacji na temat [dokumentacji interfejsu API ODBC](/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](saas-tenancy-app-design-patterns.md)
* Poznaj wszystkie [możliwości usługi SQL Database](https://azure.microsoft.com/services/sql-database/)