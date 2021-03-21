---
title: Łączenie i zapytanie — pojedynczy serwer MySQL
description: Linki do przewodników szybki start przedstawiające sposób nawiązywania połączeń z usługą Azure My SQL Database Single Server i uruchamiania zapytań.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546436"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Przegląd połączeń i zapytań dotyczących usługi Azure Database for MySQL — pojedynczy serwer

Poniższy dokument zawiera linki do przykładów przedstawiających sposób nawiązywania połączeń z usługą Azure Database for MySQL pojedynczego serwera i wykonywania w niej zapytań. Ten przewodnik zawiera również rekomendacje i biblioteki protokołu TLS, których można użyć do nawiązania połączenia z serwerem w obsługiwanych językach poniżej.

## <a name="quickstarts"></a>Przewodniki Szybki start

| Szybki start | Opis |
|---|---|
|[Workbench MySQL](connect-workbench.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z bazą danych przy użyciu klienta MySQL Workbench. Następnie można użyć instrukcji MySQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|W tym artykule pokazano, jak uruchomić **mysql.exe** w programie [Azure Cloud Shell](../cloud-shell/overview.md) , aby nawiązać połączenie z serwerem, a następnie uruchomić instrukcje w celu wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych.|
|[Baza danych MySQL z programem Visual Studio](https://www.mysql.com/why-mysql/windows/visualstudio)|Do łączenia się z serwerem MySQL można użyć programu MySQL for Visual Studio. Program MySQL for Visual Studio integruje się bezpośrednio z Eksplorator serwera, ułatwiając konfigurowanie nowych połączeń i pracę z obiektami bazy danych.|
|[PHP](connect-php.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia programu w celu nawiązania połączenia z bazą danych przy użyciu języka PHP i używania instrukcji MySQL do wykonywania zapytań dotyczących danych.|
|[Java](connect-java.md)|W tym przewodniku szybki start pokazano, jak nawiązać połączenie z bazą danych przy użyciu języka Java, a następnie użyć instrukcji MySQL do wykonywania zapytań dotyczących danych.|
|[Node.js](connect-nodejs.md)|W tym przewodniku szybki start pokazano, jak za pomocą Node.js utworzyć program do łączenia się z bazą danych i używać instrukcji MySQL do wykonywania zapytań dotyczących danych.|
|[.NET (C#)](connect-csharp.md)|Ten przewodnik Szybki Start przedstawia sposób use.NET (C#) w celu utworzenia programu w języku C# do łączenia się z bazą danych i używania instrukcji MySQL do wykonywania zapytań dotyczących danych.|
|[Przejdź](connect-go.md)|W tym przewodniku szybki start pokazano, jak używać języka go do łączenia się z bazą danych. Ponadto przedstawiono instrukcje języka Transact-SQL umożliwiające wykonywanie zapytań i modyfikowanie danych.|
|[Python](connect-python.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z bazą danych przy użyciu języka Python i używanie instrukcji MySQL do wykonywania zapytań dotyczących danych. |
|[Ruby](connect-ruby.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia programu w celu nawiązania połączenia z bazą danych przy użyciu języka Ruby oraz wykonywania zapytań dotyczących danych za pomocą instrukcji MySQL.|
|[C++](connect-cpp.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia programu w celu nawiązania połączenia z bazą danych i korzystania z danych zapytania przy użyciu języka C++ +.|

## <a name="tls-considerations-for-database-connectivity"></a>Zagadnienia dotyczące łączności z bazą danych TLS

Transport Layer Security (TLS) jest używany przez wszystkie sterowniki dostarczone lub obsługiwane przez firmę Microsoft w celu łączenia się z bazami danych w programie Azure Database for MySQL. Nie jest wymagana żadna specjalna konfiguracja, ale należy wymusić użycie protokołu TLS 1,2 dla nowo utworzonych serwerów. Zaleca się, aby w przypadku używania protokołów TLS 1,0 i 1,1 zaktualizować wersję protokołu TLS dla serwerów. Zobacz [jak skonfigurować protokół TLS](howto-tls-configurations.md)

## <a name="libraries"></a>Biblioteki

Azure Database for MySQL korzysta z najbardziej popularnej wersji Community Database programu MySQL. W związku z tym jest to zgodne z szeroką gamą języków programowania i sterowników. Celem jest wsparcie trzech najnowszych wersji sterowników MySQL oraz podejmowanie wysiłków z członkami społeczności open source, aby stale ulepszać funkcje i użyteczność sterowników MySQL.

Sprawdź, jakie [sterowniki](concepts-compatibility.md) są zgodne z Azure Database for MySQL jednym serwerem.

## <a name="next-steps"></a>Następne kroki

- [Migrowanie danych przy użyciu zrzutów i przywracania](concepts-migrate-dump-restore.md)
- [Migrowanie danych przy użyciu funkcji importowania i eksportowania](concepts-migrate-import-export.md)