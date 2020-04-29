---
title: TLS-Citus) — Azure Database for PostgreSQL
description: Instrukcje i informacje dotyczące konfigurowania Azure Database for PostgreSQL-Citus i skojarzonych aplikacji do prawidłowego korzystania z połączeń TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422335"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurowanie protokołu TLS w Azure Database for PostgreSQL-Citus
Połączenia aplikacji klienckich z węzłem koordynatora Citus () wymagają Transport Layer Security (TLS), wcześniej znanego jako SSL (SSL). Wymuszanie połączeń TLS między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją.

## <a name="enforcing-tls-connections"></a>Wymuszanie połączeń TLS
W przypadku wszystkich serwerów Azure Database for PostgreSQL, które są obsługiwane za pomocą Azure Portal, wymuszanie połączeń TLS jest domyślnie włączone. 

Analogicznie, parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "parametry połączenia" w ramach serwera w Azure Portal zawierają wymagane parametry dla wspólnych języków do łączenia się z serwerem bazy danych przy użyciu protokołu TLS. Parametr TLS zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Upewnij się, że aplikacja lub platforma obsługuje połączenia TLS
Niektóre struktury aplikacji korzystające z PostgreSQL dla usług baz danych nie domyślnie włączają TLS podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia protokołu TLS, ale aplikacja nie jest skonfigurowana dla protokołu TLS, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności TLS
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji (. cer), aby bezpiecznie nawiązać połączenie. Certyfikat do połączenia z Azure Database for PostgreSQL-Citus) znajduje się w lokalizacji https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Pobierz plik certyfikatu i Zapisz go w preferowanej lokalizacji.

### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL
W poniższym przykładzie pokazano, jak nawiązać połączenie z węzłem koordynatora Citus (PSQL) za pomocą narzędzia wiersza polecenia. Użyj ustawienia `sslmode=verify-full` parametrów połączenia, aby wymusić weryfikację certyfikatu TLS. Przekaż ścieżkę pliku certyfikatu lokalnego do `sslrootcert` parametru.

Poniżej znajduje się przykład parametrów połączenia PSQL:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Upewnij się, że wartość jest `sslrootcert` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

## <a name="next-steps"></a>Następne kroki
Zwiększ bezpieczeństwo, korzystając z [reguł zapory w Azure Database for PostgreSQL-Citus](concepts-hyperscale-firewall-rules.md).
