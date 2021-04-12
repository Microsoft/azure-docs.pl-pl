---
title: Połączenie szyfrowane przy użyciu protokołu TLS/SSL w Azure Database for PostgreSQL-elastycznym serwerze
description: Instrukcje i informacje dotyczące sposobu nawiązywania połączenia przy użyciu protokołu TLS/SSL w Azure Database for PostgreSQL-elastycznym serwerze.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: cd9a74e9a05dad68a5d05fd8b9c92ed566c21766
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227436"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Zaszyfrowane połączenie przy użyciu Transport Layer Security na serwerze Azure Database for PostgreSQL-elastycznym

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Azure Database for PostgreSQL — elastyczny serwer obsługuje łączenie aplikacji klienckich z usługą PostgreSQL przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako Secure Sockets Layer (SSL). TLS jest standardowym protokołem, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności.

Azure Database for PostgreSQL — serwer elastyczny obsługuje zaszyfrowane połączenia przy użyciu protokołu Transport Layer Security (TLS 1.2 +), a wszystkie połączenia przychodzące z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone. W przypadku wszystkich elastycznych serwerów wymuszania połączeń TLS jest włączone i nie można wyłączyć protokołu TLS/SSL w celu nawiązania połączenia z serwerem elastycznym.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplikacje wymagające weryfikacji certyfikatu dla połączeń TLS/SSL
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji w celu bezpiecznego nawiązywania połączenia. Serwer elastyczny Azure Database for PostgreSQL używa *globalnego głównego urzędu certyfikacji DigiCert*. Pobierz ten certyfikat wymagany do komunikacji za pośrednictwem protokołu SSL z [globalnego głównego urzędu certyfikacji DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) i Zapisz plik certyfikatu w preferowanej lokalizacji. Na przykład w tym samouczku używamy `c:\ssl` .


### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL
Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera.

Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze.

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem za pomocą interfejsu wiersza polecenia PSQL. Użyj `sslmode=verify-full` Ustawienia parametrów połączenia, aby wymusić weryfikację certyfikatu TLS/SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do `sslrootcert` parametru.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Upewnij się, że wartość jest `sslrootcert` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Upewnij się, że aplikacja lub platforma obsługuje połączenia TLS

Niektóre struktury aplikacji korzystające z PostgreSQL dla usług baz danych nie domyślnie włączają TLS podczas instalacji. Serwer PostgreSQL wymusza połączenia protokołu TLS, ale jeśli aplikacja nie jest skonfigurowana dla protokołu TLS, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia TLS.

## <a name="next-steps"></a>Następne kroki
- [Utwórz sieć wirtualną serwera Azure Database for PostgreSQL elastyczną i zarządzaj nią przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Dowiedz się więcej o [sieci w Azure Database for PostgreSQL-elastycznym serwerze](./concepts-networking.md)
- Dowiedz się więcej o [regułach zapory serwera elastycznych Azure Database for PostgreSQL](./concepts-networking.md#public-access-allowed-ip-addresses)
