---
title: SSL/TLS-Azure Database for PostgreSQL-pojedynczy serwer
description: Instrukcje i informacje dotyczące sposobu konfigurowania łączności TLS dla Azure Database for PostgreSQL-pojedynczego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: e54b8bc51817d2a56153dcc0c14f45e76b9ae88b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444942"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurowanie łączności TLS w Azure Database for PostgreSQL-pojedynczym serwerze

Azure Database for PostgreSQL preferuje łączenie aplikacji klienckich z usługą PostgreSQL przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako SSL (SSL). Wymuszanie połączeń TLS między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją.

Domyślnie usługa bazy danych PostgreSQL jest skonfigurowana tak, aby wymagane było połączenie TLS. Można wyłączyć wymaganie protokołu TLS, jeśli aplikacja kliencka nie obsługuje łączności TLS.

## <a name="enforcing-tls-connections"></a>Wymuszanie połączeń TLS

W przypadku wszystkich serwerów Azure Database for PostgreSQL, które są obsługiwane za pomocą Azure Portal i interfejsu wiersza polecenia, wymuszanie połączeń TLS jest domyślnie włączone. 

Analogicznie, parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "parametry połączenia" w ramach serwera w Azure Portal zawierają wymagane parametry dla wspólnych języków do łączenia się z serwerem bazy danych przy użyciu protokołu TLS. Parametr TLS zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

## <a name="configure-enforcement-of-tls"></a>Konfigurowanie wymuszania protokołu TLS

Opcjonalnie można wyłączyć wymuszanie łączności TLS. Zaleca się, aby zawsze włączyć ustawienie **Wymuszaj połączenie SSL** dla zwiększonych zabezpieczeń. Microsoft Azure

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Odwiedź serwer Azure Database for PostgreSQL i kliknij pozycję **zabezpieczenia połączeń**. Użyj przycisku przełącznika, aby włączyć lub wyłączyć ustawienie **Wymuszaj połączenie SSL** . Następnie kliknij przycisk **Zapisz**.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Zabezpieczenia połączeń — Wyłącz wymuszanie protokołu TLS/SSL":::

Ustawienie można potwierdzić, wyświetlając stronę **Przegląd** , aby wyświetlić wskaźnik **stanu wymuszania protokołu SSL** .

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można włączyć lub wyłączyć parametr **wymuszania protokołu SSL** `Enabled` lub `Disabled` wartości.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Upewnij się, że aplikacja lub platforma obsługuje połączenia TLS

Niektóre struktury aplikacji korzystające z PostgreSQL dla usług baz danych nie domyślnie włączają TLS podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia protokołu TLS, ale aplikacja nie jest skonfigurowana dla protokołu TLS, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności TLS

W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji w celu bezpiecznego nawiązywania połączenia. Certyfikat do połączenia z serwerem Azure Database for PostgreSQL znajduje się w lokalizacji https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Pobierz plik certyfikatu i Zapisz go w preferowanej lokalizacji. 

Zobacz następujące linki dla certyfikatów serwerów w chmurach suwerennych: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Chiny](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)i [Azure (Niemcy](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)).

### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem PostgreSQL za pomocą narzędzia wiersza polecenia PSQL. Użyj `sslmode=verify-full` Ustawienia parametrów połączenia, aby wymusić weryfikację certyfikatu TLS/SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do `sslrootcert` parametru.

Następujące polecenie jest przykładem parametrów połączenia PSQL:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Upewnij się, że wartość jest `sslrootcert` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Wymuszanie protokołu TLS w Azure Database for PostgreSQL pojedynczym serwerze

Azure Database for PostgreSQL — pojedynczy serwer obsługuje szyfrowanie dla klientów nawiązujących połączenie z serwerem bazy danych przy użyciu usługi Transport Layer Security (TLS). TLS jest standardowym protokołem, który zapewnia bezpieczne połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności.

### <a name="tls-settings"></a>Ustawienia protokołu TLS

Azure Database for PostgreSQL pojedynczy serwer umożliwia wymuszanie wersji protokołu TLS dla połączeń klientów. Aby wymusić wersję protokołu TLS, użyj ustawienia opcji **minimalnej wersji protokołu TLS** . Dla tego ustawienia opcji można używać następujących wartości:

|  Minimalne ustawienie protokołu TLS             | Obsługiwana wersja protokołu TLS klienta                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (domyślnie) | Nie jest wymagany protokół TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 i nowsze |
| TLS1_1                           | TLS 1,1, TLS 1,2 i nowsze          |
| TLS1_2                           | TLS w wersji 1,2 lub nowszej           |


Na przykład ustawienie minimalnej wersji protokołu TLS na TLS 1,0 oznacza, że serwer będzie zezwalał na połączenia od klientów przy użyciu protokołu TLS 1,0, 1,1 i 1.2 +. Alternatywnie ustawienie tego ustawienia na 1,2 oznacza, że zezwalasz tylko na połączenia od klientów korzystających z protokołu TLS 1.2 + i wszystkie połączenia z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone.

> [!Note] 
> Domyślnie Azure Database for PostgreSQL nie wymusza minimalnej wersji protokołu TLS (ustawienie `TLSEnforcementDisabled` ).
>
> Po wymuszeniu minimalnej wersji protokołu TLS nie można później wyłączyć wymuszania wersji minimalnej.

Aby dowiedzieć się, jak ustawić ustawienie protokołu TLS dla Azure Database for PostgreSQL pojedynczego serwera, zobacz [jak skonfigurować ustawienie protokołu TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Obsługa szyfrowania przez Azure Database for PostgreSQL pojedynczym serwerze

W ramach komunikacji SSL/TLS mechanizmy szyfrowania są weryfikowane i obsługują tylko ubrania szyfrowania, które mogą komunikować się z bazą danych serer. Sprawdzanie poprawności pakietu szyfrowania jest kontrolowane w [warstwie bramy](concepts-connectivity-architecture.md#connectivity-architecture) i nie jest jawnie w samym węźle. Jeśli mechanizmy szyfrowania nie pasują do jednego z wymienionych poniżej pakietów, przychodzące połączenia klienta zostaną odrzucone.

### <a name="cipher-suite-supported"></a>Obsługiwany mechanizm szyfrowania

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z różnymi opcjami łączności aplikacji w [bibliotekach połączeń dla Azure Database for PostgreSQL](concepts-connection-libraries.md).

- Dowiedz się, jak [skonfigurować protokół TLS](howto-tls-configurations.md)