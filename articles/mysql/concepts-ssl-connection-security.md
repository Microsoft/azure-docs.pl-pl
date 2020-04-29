---
title: Połączenie SSL — Azure Database for MySQL
description: Informacje dotyczące konfigurowania Azure Database for MySQL i skojarzonych aplikacji w celu prawidłowego używania połączeń SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6a12ef851823ab5eff2b11905d05be1950c82ef0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474275"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Połączenie SSL w Azure Database for MySQL

Azure Database for MySQL obsługuje łączenie serwera bazy danych z aplikacjami klienckimi przy użyciu protokołu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="ssl-default-settings"></a>Ustawienia domyślne protokołu SSL

Domyślnie usługa bazy danych powinna być skonfigurowana w taki sposób, aby wymagała połączeń SSL podczas nawiązywania połączenia z usługą MySQL.  Zalecamy uniknięcie wyłączania opcji SSL, jeśli jest to możliwe.

Podczas aprowizacji nowego serwera Azure Database for MySQL za pośrednictwem Azure Portal i interfejsu wiersza polecenia wymuszanie połączeń SSL jest domyślnie włączone. 

W Azure Portal przedstawiono parametry połączenia dla różnych języków programowania. Te parametry połączenia obejmują wymagania SSL wymagane do nawiązania połączenia z bazą danych. W Azure Portal wybierz serwer. W polu Nagłówek **ustawień** wybierz **Parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenie SSL podczas tworzenia aplikacji, zapoznaj się z [tematem Konfigurowanie protokołu SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Następne kroki

[Biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md)
