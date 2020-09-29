---
title: Łączność SSL/TLS — Azure Database for MariaDB
description: Informacje dotyczące konfigurowania Azure Database for MariaDB i skojarzonych aplikacji w celu prawidłowego używania połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: b23783080e976f70ba8c5e02f67dcee36bbc9c34
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444954"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>Łączność SSL/TLS w Azure Database for MariaDB
Azure Database for MariaDB obsługuje łączenie serwera bazy danych z aplikacjami klienckimi przy użyciu protokołu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

> [!IMPORTANT] 
> Certyfikat główny SSL jest ustawiany na wygasa od 26 października 2020 (10/26/2020). Zaktualizuj swoją aplikację, aby używała [nowego certyfikatu](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Aby dowiedzieć się więcej, zobacz [aktualizacje dotyczące planowanych certyfikatów](concepts-certificate-rotation.md)

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych powinna być skonfigurowana w taki sposób, aby wymagała połączeń SSL podczas nawiązywania połączenia z usługą MariaDB.  Zalecamy uniknięcie wyłączania opcji SSL, jeśli jest to możliwe.

Podczas aprowizacji nowego serwera Azure Database for MariaDB za pośrednictwem Azure Portal i interfejsu wiersza polecenia wymuszanie połączeń SSL jest domyślnie włączone.

W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji w celu bezpiecznego nawiązywania połączenia. Obecnie klienci mogą **używać** wstępnie zdefiniowanego certyfikatu do nawiązywania połączenia z serwerem Azure Database for MariaDB, który znajduje się w lokalizacji https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Podobnie następujące linki wskazują certyfikaty dla serwerów w chmurach suwerennych: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Chiny](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)i [Azure (Niemcy](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)).

W Azure Portal przedstawiono parametry połączenia dla różnych języków programowania. Te parametry połączenia obejmują wymagania SSL wymagane do nawiązania połączenia z bazą danych. W Azure Portal wybierz serwer. W polu Nagłówek **ustawień** wybierz **Parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenie SSL podczas tworzenia aplikacji, zapoznaj się z [tematem Konfigurowanie protokołu SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Wymuszanie protokołu TLS w Azure Database for MariaDB

Azure Database for MariaDB obsługuje szyfrowanie dla klientów nawiązujących połączenie z serwerem bazy danych przy użyciu usługi Transport Layer Security (TLS). TLS jest standardowym protokołem, który zapewnia bezpieczne połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności.

### <a name="tls-settings"></a>Ustawienia protokołu TLS

Azure Database for MariaDB umożliwia wymuszanie wersji protokołu TLS dla połączeń klientów. Aby wymusić wersję protokołu TLS, użyj ustawienia opcji **minimalnej wersji protokołu TLS** . Dla tego ustawienia opcji można używać następujących wartości:

|  Minimalne ustawienie protokołu TLS             | Obsługiwana wersja protokołu TLS klienta                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (domyślnie) | Nie jest wymagany protokół TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 i nowsze         |
| TLS1_1                           | TLS 1,1, TLS 1,2 i nowsze              |
| TLS1_2                           | TLS w wersji 1,2 lub nowszej                  |


Na przykład ustawienie wartości minimalna wersja ustawienia protokołu TLS na TLS 1,0 oznacza, że serwer będzie zezwalał na połączenia od klientów przy użyciu protokołu TLS 1,0, 1,1 i 1.2 +. Alternatywnie ustawienie tego ustawienia na 1,2 oznacza, że zezwalasz tylko na połączenia od klientów korzystających z protokołu TLS 1.2 + i wszystkie połączenia z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone.

> [!Note] 
> Domyślnie Azure Database for MariaDB nie wymusza minimalnej wersji protokołu TLS (ustawienie `TLSEnforcementDisabled` ).
>
> Po wymuszeniu minimalnej wersji protokołu TLS nie można później wyłączyć wymuszania wersji minimalnej.

Aby dowiedzieć się, jak ustawić ustawienie protokołu TLS dla Azure Database for MariaDB, zobacz [jak skonfigurować ustawienie protokołu TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mariadb"></a>Obsługa szyfrowania przez Azure Database for MariaDB

W ramach komunikacji SSL/TLS mechanizmy szyfrowania są weryfikowane i obsługują tylko ubrania szyfrowania, które mogą komunikować się z bazą danych serer. Sprawdzanie poprawności pakietu szyfrowania jest kontrolowane w [warstwie bramy](concepts-connectivity-architecture.md#connectivity-architecture) i nie jest jawnie w samym węźle. Jeśli mechanizmy szyfrowania nie pasują do jednego z wymienionych poniżej pakietów, przychodzące połączenia klienta zostaną odrzucone.

### <a name="cipher-suite-supported"></a>Obsługiwany mechanizm szyfrowania

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [regułach zapory serwera](concepts-firewall-rules.md)
- Dowiedz się, jak [skonfigurować protokół SSL](howto-configure-ssl.md)
- Dowiedz się, jak [skonfigurować protokół TLS](howto-tls-configurations.md)
