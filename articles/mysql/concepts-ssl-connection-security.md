---
title: Łączność SSL/TLS — Azure Database for MySQL
description: Informacje dotyczące konfigurowania Azure Database for MySQL i skojarzonych aplikacji w celu prawidłowego używania połączeń SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 2421f8a9396b47d04db35a7cad843f6baa6f6177
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416107"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Łączność SSL/TLS w Azure Database for MySQL

Azure Database for MySQL obsługuje łączenie serwera bazy danych z aplikacjami klienckimi przy użyciu protokołu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="ssl-default-settings"></a>Ustawienia domyślne protokołu SSL

Domyślnie usługa bazy danych powinna być skonfigurowana w taki sposób, aby wymagała połączeń SSL podczas nawiązywania połączenia z usługą MySQL.  Zalecamy uniknięcie wyłączania opcji SSL, jeśli jest to możliwe.

Podczas aprowizacji nowego serwera Azure Database for MySQL za pośrednictwem Azure Portal i interfejsu wiersza polecenia wymuszanie połączeń SSL jest domyślnie włączone. 

W Azure Portal przedstawiono parametry połączenia dla różnych języków programowania. Te parametry połączenia obejmują wymagania SSL wymagane do nawiązania połączenia z bazą danych. W Azure Portal wybierz serwer. W polu Nagłówek **ustawień** wybierz **Parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenie SSL podczas tworzenia aplikacji, zapoznaj się z [tematem Konfigurowanie protokołu SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Wymuszanie protokołu TLS w Azure Database for MySQL

Azure Database for MySQL obsługuje szyfrowanie dla klientów nawiązujących połączenie z serwerem bazy danych przy użyciu usługi Transport Layer Security (TLS). TLS jest standardowym protokołem, który zapewnia bezpieczne połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności.

### <a name="tls-settings"></a>Ustawienia protokołu TLS

Azure Database for MySQL umożliwia wymuszanie wersji protokołu TLS dla połączeń klientów. Aby wymusić wersję protokołu TLS, użyj ustawienia opcji **minimalnej wersji protokołu TLS** . Dla tego ustawienia opcji można używać następujących wartości:

|  Minimalne ustawienie protokołu TLS             | Obsługiwana wersja protokołu TLS klienta                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (domyślnie) | Nie jest wymagany protokół TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 i nowsze           |
| TLS1_1                           | TLS 1,1, TLS 1,2 i nowsze                   |
| TLS1_2                           | TLS w wersji 1,2 lub nowszej                     |


Na przykład ustawienie wartości minimalna wersja ustawienia protokołu TLS na TLS 1,0 oznacza, że serwer będzie zezwalał na połączenia od klientów przy użyciu protokołu TLS 1,0, 1,1 i 1.2 +. Alternatywnie ustawienie tego ustawienia na 1,2 oznacza, że zezwalasz tylko na połączenia od klientów korzystających z protokołu TLS 1.2 + i wszystkie połączenia z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone.

> [!Note] 
> Azure Database for MySQL wartość domyślna protokołu TLS jest wyłączona dla wszystkich nowych serwerów.
>
> Obecnie wersje protokołu TLS obsługiwane przez Azure Database for MySQL to TLS 1,0, 1,1 i 1,2. Po wymuszeniu dla określonej minimalnej wersji protokołu TLS nie można jej zmienić na wyłączony.

Aby dowiedzieć się, jak ustawić ustawienie protokołu TLS dla Azure Database for MySQL, zobacz [jak skonfigurować ustawienie protokołu TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md)
- Dowiedz się, jak [skonfigurować protokół SSL](howto-configure-ssl.md)
- Dowiedz się, jak [skonfigurować protokół TLS](howto-tls-configurations.md)
