---
title: Transport Layer Security (TLS)-skala (Citus) — Azure Database for PostgreSQL
description: Instrukcje i informacje dotyczące konfigurowania Azure Database for PostgreSQL-Citus i skojarzonych aplikacji do prawidłowego korzystania z połączeń TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87071461"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurowanie protokołu TLS w Azure Database for PostgreSQL-Citus
Węzeł koordynatora ze skalowaniem (Citus) wymaga, aby aplikacje klienckie łączyły się z usługą Transport Layer Security (TLS). Wymuszanie szyfrowania TLS między serwerem bazy danych a aplikacjami klienckim pozwala zachować poufne dane podczas przesyłania. Dodatkowe ustawienia weryfikacji opisane poniżej zapewniają również ochronę przed atakami typu man-in-the-middle.

## <a name="enforcing-tls-connections"></a>Wymuszanie połączeń TLS
Aplikacje używają "parametrów połączenia", aby identyfikować docelową bazę danych i ustawienia dla połączenia. Różni klienci wymagają różnych ustawień. Aby wyświetlić listę parametrów połączenia używanych przez typowych klientów, zapoznaj się z sekcją **Parametry połączenia** dla grupy serwerów w Azure Portal.

Parametry protokołu TLS `ssl` i `sslmode` różnią się w zależności od możliwości łącznika, na `ssl=true` przykład `sslmode=require` lub `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Upewnij się, że aplikacja lub platforma obsługuje połączenia TLS
Niektóre struktury aplikacji domyślnie nie włączają protokołu TLS dla połączeń PostgreSQL. Jednak bez bezpiecznego połączenia aplikacja nie może połączyć się z węzłem koordynatora ze skalowaniem (Citus). Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności TLS
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji (. cer), aby bezpiecznie nawiązać połączenie. Certyfikat do połączenia z Azure Database for PostgreSQL-Citus) znajduje się w lokalizacji https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Pobierz plik certyfikatu i Zapisz go w preferowanej lokalizacji.

> [!NOTE]
>
> Aby sprawdzić autentyczność certyfikatu, możesz zweryfikować odcisk palca SHA-256 przy użyciu narzędzia wiersza polecenia OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL
W poniższym przykładzie pokazano, jak nawiązać połączenie z węzłem koordynatora Citus (PSQL) za pomocą narzędzia wiersza polecenia. Użyj `sslmode=verify-full` Ustawienia parametrów połączenia, aby wymusić weryfikację certyfikatu TLS. Przekaż ścieżkę pliku certyfikatu lokalnego do `sslrootcert` parametru.

Poniżej znajduje się przykład parametrów połączenia PSQL:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Upewnij się, że wartość jest `sslrootcert` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

## <a name="next-steps"></a>Następne kroki
Zwiększ bezpieczeństwo, korzystając z [reguł zapory w Azure Database for PostgreSQL-Citus](concepts-hyperscale-firewall-rules.md).
