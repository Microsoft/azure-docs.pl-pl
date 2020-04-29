---
title: TLS-Azure Database for PostgreSQL — pojedynczy serwer
description: Instrukcje i informacje dotyczące sposobu konfigurowania łączności TLS dla Azure Database for PostgreSQL-pojedynczego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141741"
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

![Zabezpieczenia połączeń — Wyłącz wymuszanie protokołu TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Ustawienie można potwierdzić, wyświetlając stronę **Przegląd** , aby wyświetlić wskaźnik **stanu wymuszania protokołu SSL** .

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można włączyć lub wyłączyć `Enabled` parametr `Disabled` **wymuszania protokołu SSL** lub wartości.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Upewnij się, że aplikacja lub platforma obsługuje połączenia TLS

Niektóre struktury aplikacji korzystające z PostgreSQL dla usług baz danych nie domyślnie włączają TLS podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia protokołu TLS, ale aplikacja nie jest skonfigurowana dla protokołu TLS, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności TLS

W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji (. cer), aby bezpiecznie nawiązać połączenie. Certyfikat do połączenia z serwerem Azure Database for PostgreSQL znajduje się w lokalizacji https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Pobierz plik certyfikatu i Zapisz go w preferowanej lokalizacji.

### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem PostgreSQL za pomocą narzędzia wiersza polecenia PSQL. Użyj ustawienia `sslmode=verify-full` parametrów połączenia, aby wymusić weryfikację certyfikatu TLS/SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do `sslrootcert` parametru.

Następujące polecenie jest przykładem parametrów połączenia PSQL:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Upewnij się, że wartość jest `sslrootcert` zgodna z ścieżką pliku dla zapisywanych certyfikatów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z różnymi opcjami łączności aplikacji w [bibliotekach połączeń dla Azure Database for PostgreSQL](concepts-connection-libraries.md).
