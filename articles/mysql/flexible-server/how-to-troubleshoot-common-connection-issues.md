---
title: Rozwiązywanie problemów z połączeniami — Azure Database for MySQL — elastyczny serwer
description: Dowiedz się, jak rozwiązywać problemy z połączeniem, aby Azure Database for MySQL elastyczny serwer.
keywords: połączenie MySQL, parametry połączenia, problemy z łącznością, błąd trwały, błąd połączenia
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: bdd52ba77fd9a65ce27985ff3c86a93fc887ddf9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109991"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Rozwiązywanie problemów z połączeniem do Azure Database for MySQL-elastyczny serwer

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Problemy z połączeniem mogą być spowodowane różnymi elementami, w tym:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe informacje logowania
* Osiągnięto maksymalny limit dla niektórych Azure Database for MySQL elastycznych zasobów serwera

W tym artykule omówiono sposób rozwiązywania niektórych typowych błędów i kroków rozwiązywania tych błędów.

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

Jeśli aplikacja trwale nie może nawiązać połączenia z Azure Database for MySQL elastycznym serwerze, zazwyczaj wskazuje problem z jedną z następujących czynności:

* Szyfrowane połączenie przy użyciu protokołu TLS/SSL: elastyczny serwer obsługuje tylko połączenia szyfrowane przy użyciu Transport Layer Security (TLS 1,2) i wszystkie **połączenia przychodzące z protokołem tls 1,0 i tls 1,1 zostaną odrzucone**. Nie można wyłączyć ani zmienić wersji protokołu TLS. Dowiedz się więcej o [łączności szyfrowanej przy użyciu Transport Layer Security (TLS 1,2) w Azure Database for MySQL-elastycznym serwerze](./how-to-connect-tls-ssl.md).
- Elastyczny serwer w *dostępie prywatnym (Integracja* z siecią wirtualną): Upewnij się, że łączysz się z tej samej sieci wirtualnej co serwer elastyczny. Zapoznaj się z tematem [Sieć wirtualna w Azure Database for MySQL elastyczny serwer]<!--(./concepts-networking-virtual-network.md)-->
- Elastyczny serwer z *dostępem publicznym (dozwolone adresy IP)* upewnij się, że Zapora jest skonfigurowana tak, aby zezwalać na połączenia z Twojego klienta. Zapoznaj się z tematem [Tworzenie i zarządzanie elastycznymi regułami zapory serwera przy użyciu Azure Portal](./how-to-manage-firewall-portal.md).
* Konfiguracja zapory klienta: Zapora na kliencie musi zezwalać na połączenia z serwerem bazy danych. Adresy IP i porty serwera, które nie mogą być dozwolone, a także nazwy aplikacji, takie jak MySQL w niektórych zaporach.
* Błąd użytkownika: być może masz błędne typy parametrów połączenia, takie jak nazwa serwera w parametrach połączenia.

### <a name="resolve-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

* Zapoznaj się z [szyfrowanym połączeniem przy użyciu Transport Layer Security (TLS 1,2) w Azure Database for MySQL-elastycznym serwerze](./how-to-connect-tls-ssl.md) -->, aby dowiedzieć się więcej o połączeniach szyfrowanych.
* Jeśli używasz **dostępu publicznego (dozwolone adresy IP)**, skonfiguruj [reguły zapory](./how-to-manage-firewall-portal.md) w taki sposób, aby zezwalały na adres IP klienta. Wyłącznie do celów testowania tymczasowego, należy skonfigurować regułę zapory przy użyciu adresu 0.0.0.0 jako początkowy adres IP i przy użyciu wartości 255.255.255.255 jako końcowego adresu IP. Spowoduje to otwarcie serwera do wszystkich adresów IP. Jeśli spowoduje to rozwiązanie problemu z łącznością, Usuń tę regułę i Utwórz regułę zapory dla odpowiednio ograniczonego adresu IP lub zakresu adresów.
* Na wszystkich zaporach między klientem a Internetem upewnij się, że port 3306 jest otwarty dla połączeń wychodzących.
* Sprawdź parametry połączenia i inne ustawienia połączenia. Zapoznaj się ze wstępnie zdefiniowanymi parametrami połączenia na stronie **Parametry połączenia** dostępne dla Twojego serwera w Azure Portal dla typowych języków.

## <a name="next-steps"></a>Następne kroki
- [Program MySQL Workbench umożliwia łączenie i wykonywanie zapytań dotyczących danych w Azure Database for MySQL elastycznym serwerze](./connect-workbench.md).
- [Używaj języka PHP do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL elastycznym serwerze](./connect-php.md).
- [Używanie języka Python do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL elastycznym serwerze](./connect-python.md).
