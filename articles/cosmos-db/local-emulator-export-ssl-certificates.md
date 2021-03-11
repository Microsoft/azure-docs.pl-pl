---
title: Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB
description: Dowiedz się, jak wyeksportować certyfikat emulatora Azure Cosmos DB do użycia z aplikacjami Java, Python i Node.js. Certyfikaty powinny być eksportowane i używane w środowiskach języka i środowiska uruchomieniowego, które nie korzystają z magazynu certyfikatów systemu Windows.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554007"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Eksportowanie Azure Cosmos DB certyfikatów emulatora do użycia z aplikacjami Java, Python i Node.js
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Emulator Azure Cosmos DB obsługuje tylko bezpieczną komunikację za poorednictwem połączeń TLS.

Certyfikaty w lokalnym emulatorze Azure Cosmos DB są generowane podczas pierwszego uruchomienia emulatora. Istnieją dwa certyfikaty. Jeden z nich jest używany do nawiązywania połączenia z lokalnym emulatorem, a drugi jest używany do zarządzania domyślnym szyfrowaniem danych emulatora w emulatorze. Certyfikat, który chcesz wyeksportować, to certyfikat połączenia z przyjazną nazwą „DocumentDBEmulatorCertificate”.

W przypadku używania emulatora do tworzenia aplikacji w różnych językach, takich jak Java, Python lub Node.js, należy wyeksportować certyfikat emulatora i zaimportować go do wymaganego magazynu certyfikatów.

Język .NET i środowisko uruchomieniowe korzystają z magazynu certyfikatów systemu Windows w celu bezpiecznego nawiązywania połączenia z lokalnym emulatorem Azure Cosmos DB, gdy aplikacja jest uruchomiona na hoście systemu operacyjnego Windows. Inne języki korzystają z własnych metod używania certyfikatów i zarządzania nimi. Na przykład Java używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Język Python używa [otok gniazda](https://docs.python.org/2/library/ssl.html), a Node.js używa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

W tym artykule przedstawiono sposób eksportowania certyfikatów protokołu TLS/SSL do użycia w różnych językach i środowiskach środowiska uruchomieniowego, które nie są integrowane z magazynem certyfikatów systemu Windows. Więcej informacji na temat emulatora zawiera artykuł [Use the Azure Cosmos DB Emulator for development and testing](./local-emulator.md) (Korzystanie z emulatora usługi Azure Cosmos DB na potrzeby programowania i testowania).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Eksportowanie Azure Cosmos DB protokołu TLS/SSL

Należy wyeksportować certyfikat emulatora, aby pomyślnie korzystać z punktu końcowego emulatora z języków i środowisk uruchomieniowych, które nie są integrowane z magazynem certyfikatów systemu Windows. Certyfikat można wyeksportować za pomocą Menedżera certyfikatów systemu Windows. Wykonaj następujące instrukcje krok po kroku, aby wyeksportować certyfikat "DocumentDBEmulatorCertificate" jako plik X. 509 z kodowaniem BASE-64 (CER):

1. Uruchom menedżera certyfikatów systemu Windows, uruchamiając plik certlm.msc, przejdź do folderu Osobiste->Certyfikaty i otwórz certyfikat o przyjaznej nazwie **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 1":::

1. Kliknij pozycję **Szczegóły**, a następnie przycisk **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 2":::

1. Kliknij pozycję **Kopiuj do pliku...**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 3":::

1. Kliknij przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 4":::

1. Kliknij pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 5":::

1. Kliknij pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 6":::

1. Nadaj certyfikatowi nazwę. W tym przypadku wpisz nazwę **documentdbemulatorcert**, a następnie kliknij przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 7":::

1. Kliknij przycisk **Finish** (Zakończ).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 8":::

## <a name="use-the-certificate-with-java-apps"></a>Używanie certyfikatu z aplikacjami Java

W przypadku uruchamiania aplikacji Java lub aplikacji MongoDB, które korzystają z klienta opartego na języku Java, łatwiej jest zainstalować certyfikat w domyślnym magazynie certyfikatów Java niż w przypadku przekazywania `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` flag. Na przykład uwzględniona aplikacja demonstracyjna Java ( `https://localhost:8081/_explorer/index.html` ) zależy od domyślnego magazynu certyfikatów.

Postępuj zgodnie z instrukcjami w temacie [Dodawanie certyfikatu do magazynu certyfikatów Java](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) , aby zaimportować certyfikat X. 509 do domyślnego magazynu certyfikatów języka Java. Pamiętaj, że będziesz pracować w katalogu *% JAVA_HOME%* podczas uruchamiania narzędzia. Po zaimportowaniu certyfikatu do magazynu certyfikatów klienci programu SQL i interfejsu API Azure Cosmos DB dla MongoDB będą mogli nawiązywać połączenia z emulatorem Azure Cosmos DB.

Alternatywnie można uruchomić następujący skrypt bash w celu zaimportowania certyfikatu:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Po zainstalowaniu certyfikatu TLS/SSL "CosmosDBEmulatorCertificate" aplikacja powinna mieć możliwość nawiązywania połączenia i używania lokalnego emulatora Azure Cosmos DB. Jeśli masz jakieś problemy, możesz skorzystać z artykułu [debugowanie połączeń SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . W większości przypadków certyfikat może nie być zainstalowany w magazynie *% JAVA_HOME%/JRE/lib/Security/cacerts* . Jeśli na przykład masz wiele zainstalowanych wersji języka Java, aplikacja może korzystać z innego magazynu cacerts niż ten, który został zaktualizowany.

## <a name="use-the-certificate-with-python-apps"></a>Używanie certyfikatu z aplikacjami języka Python

W przypadku nawiązywania połączenia z emulatorem z aplikacji w języku Python weryfikacja protokołu TLS jest wyłączona. Domyślnie [zestaw SDK języka Python (wersja 2.0.0 lub nowsza)](sql-api-sdk-python.md) dla interfejsu API SQL nie będzie próbował używać certyfikatu TLS/SSL podczas nawiązywania połączenia z emulatorem lokalnym. Jeśli jednak chcesz użyć walidacji TLS, możesz skorzystać z przykładów w dokumentacji [otok gniazda języka Python](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Sposób użycia certyfikatu w środowisku Node.js

W przypadku nawiązywania połączenia z emulatorem z Node.js zestawów SDK weryfikacja protokołu TLS jest wyłączona. Domyślnie [ zestaw SDKNode.js (wersja 1.10.1 lub nowsza)](sql-api-sdk-node.md) dla interfejsu API SQL nie będzie próbował używać certyfikatu TLS/SSL podczas nawiązywania połączenia z emulatorem lokalnym. Jeśli jednak chcesz użyć walidacji TLS, możesz skorzystać z przykładów w [ dokumentacjiNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Obróć certyfikaty emulatora

Można wymusić ponowne wygenerowanie certyfikatów emulatora, wybierając pozycję **Zresetuj dane** z emulatora Azure Cosmos DB działającego na pasku zadań systemu Windows. Należy zauważyć, że ta akcja spowoduje również wymazanie wszystkich danych przechowywanych lokalnie przez emulator.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Resetowanie danych lokalnego emulatora usługi Azure Cosmos DB":::

Jeśli certyfikat został zainstalowany do magazynu certyfikatów Java lub był używany w innym miejscu, należy go ponownie zaimportować przy użyciu bieżących certyfikatów. Aplikacja nie może połączyć się z emulatorem lokalnym, dopóki nie zaktualizujesz certyfikatów.

## <a name="next-steps"></a>Następne kroki

* [Używanie parametrów wiersza polecenia i poleceń programu PowerShell do sterowania emulatorem](emulator-command-line-parameters.md)
* [Problemy z debugowaniem przy użyciu emulatora](troubleshoot-local-emulator.md)
