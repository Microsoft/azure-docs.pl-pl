---
title: Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB
description: Podczas tworzenia w językach i środowiska uruchomieniowego, które nie korzystają z magazynu certyfikatów systemu Windows, należy wyeksportować certyfikaty TLS/SSL i zarządzać nimi. Ten wpis zawiera instrukcje krok po kroku.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java
ms.openlocfilehash: e1321c0d5b1f83ffcfd3f46384dfb3af792c9b8b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373100"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB do użycia w językach Java, Python i Node.js

[**Pobieranie emulatora**](https://aka.ms/cosmosdb-emulator)

Emulator Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów deweloperskich, łącznie z wykorzystaniem połączeń TLS. Ten wpis pokazuje, jak wyeksportować certyfikaty protokołu TLS/SSL do użycia w językach i środowiskach uruchomieniowych, które nie są integrowane z magazynem certyfikatów systemu Windows, takim jak Java, który używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) i języka Python, który używa [otoki gniazda](https://docs.python.org/2/library/ssl.html) i Node.js, które używają [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Więcej informacji na temat emulatora zawiera artykuł [Use the Azure Cosmos DB Emulator for development and testing](./local-emulator.md) (Korzystanie z emulatora usługi Azure Cosmos DB na potrzeby programowania i testowania).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Wymiana certyfikatów
> * Eksportowanie certyfikatu TLS/SSL
> * Poznanie sposobu używania certyfikatu w środowiskach Java, Python i Node.js

## <a name="certification-rotation"></a>Wymiana certyfikatów

Certyfikaty w lokalnym emulatorze usługi Azure Cosmos DB są generowane podczas pierwszego uruchomienia emulatora. Istnieją dwa certyfikaty. Jeden używany do nawiązywania połączenia z lokalnym emulatorem i jeden do zarządzania wpisami tajnymi w emulatorze. Certyfikat, który chcesz wyeksportować, to certyfikat połączenia z przyjazną nazwą „DocumentDBEmulatorCertificate”.

Oba certyfikaty można wygenerować ponownie, klikając pozycję **Reset Data** (Resetuj dane) w emulatorze usługi Azure Cosmos DB uruchomionym w obszarze powiadomień systemu Windows. Jeśli certyfikaty zostaną ponownie wygenerowane i zainstalowane w magazynie certyfikatów języka Java lub użyte w innym miejscu, konieczne będzie ich zaktualizowanie, ponieważ w przeciwnym razie aplikacja nie będzie już nawiązywała połączenia z lokalnym emulatorem.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Resetowanie danych lokalnego emulatora usługi Azure Cosmos DB":::

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Jak wyeksportować Azure Cosmos DB certyfikat TLS/SSL

1. Uruchom menedżera certyfikatów systemu Windows, uruchamiając plik certlm.msc, przejdź do folderu Osobiste->Certyfikaty i otwórz certyfikat o przyjaznej nazwie **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 1":::

2. Kliknij pozycję **Szczegóły**, a następnie przycisk **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 2":::

3. Kliknij pozycję **Kopiuj do pliku...**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 3":::

4. Kliknij przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 4":::

5. Kliknij pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 5":::

6. Kliknij pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 6":::

7. Nadaj certyfikatowi nazwę. W tym przypadku wpisz nazwę **documentdbemulatorcert**, a następnie kliknij przycisk **Dalej**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 7":::

8. Kliknij przycisk **Zakończ**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Eksportowanie lokalnego emulatora usługi Azure Cosmos DB — krok 8":::

## <a name="how-to-use-the-certificate-in-java"></a>Sposób użycia certyfikatu w środowisku Java

Podczas uruchamiania aplikacji Java lub aplikacji MongoDB, które używają klienta Java, łatwiej jest zainstalować certyfikat do domyślnego magazynu certyfikatów Java niż w przypadku przekazywania `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` flag. Na przykład uwzględniona aplikacja demonstracyjna Java ( `https://localhost:8081/_explorer/index.html` ) zależy od domyślnego magazynu certyfikatów.

Postępuj zgodnie z instrukcjami w artykule [Dodawanie certyfikatu do magazynu certyfikatów Java CA](https://docs.microsoft.com/azure/java-add-certificate-ca-store), aby zaimportować certyfikat X.509 do domyślnego magazynu certyfikatów Java. Pamiętaj, że podczas uruchamiania narzędzia keytool będziesz pracować w katalogu %JAVA_HOME%.

Alternatywnie możesz utworzyć i uruchomić skrypt "bash", który automatycznie wykonuje to:
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

Po zainstalowaniu certyfikatu TLS/SSL "CosmosDBEmulatorCertificate" aplikacja powinna mieć możliwość nawiązywania połączenia i używania lokalnego emulatora Azure Cosmos DB. Jeśli nadal występują problemy, możesz wykonać czynności opisane w artykule [Debugowanie połączeń SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Jest bardzo prawdopodobne, że certyfikat nie został zainstalowany w magazynie %JAVA_HOME%/jre/lib/security/cacerts. Na przykład jeśli masz wiele zainstalowanych wersji języka Java, aplikacja może używać innego magazynu cacerts niż ten, który został zaktualizowany.

## <a name="how-to-use-the-certificate-in-python"></a>Sposób użycia certyfikatu w środowisku Python

Domyślnie [zestaw SDK języka Python (wersja 2.0.0 lub nowsza)](sql-api-sdk-python.md) dla interfejsu API SQL nie będzie próbował używać certyfikatu TLS/SSL podczas nawiązywania połączenia z emulatorem lokalnym. Jeśli jednak chcesz użyć walidacji TLS, możesz skorzystać z przykładów w dokumentacji [otok gniazda języka Python](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Sposób użycia certyfikatu w środowisku Node.js

Domyślnie [zestaw SDKNode.js (wersja 1.10.1 lub nowsza)](sql-api-sdk-node.md) dla interfejsu API SQL nie będzie próbował używać certyfikatu TLS/SSL podczas nawiązywania połączenia z emulatorem lokalnym. Jeśli jednak chcesz użyć walidacji TLS, możesz skorzystać z przykładów w [dokumentacjiNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Wymieniono certyfikaty
> * Wyeksportowano certyfikat TLS/SSL
> * Przedstawiono sposób używania certyfikatu w językach Java, Python i Node.js

Teraz możesz przejść do sekcji pojęć, aby uzyskać więcej informacji na temat usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB](../cosmos-db/consistency-levels.md)
