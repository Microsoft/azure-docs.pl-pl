---
title: Nawiązywanie połączenia z Azure Cosmos DB przy użyciu kompasu
description: Dowiedz się, jak używać kompasu MongoDB do przechowywania danych i zarządzania nimi w Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 74822211ae834bc4705552bd3291299cf14b54d0
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465732"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Użyj MongoDB kompas do nawiązywania połączenia z interfejsem API usługi Azure Cosmos DB dla MongoDB

W tym samouczku pokazano, jak używać [kompasu MongoDB](https://www.mongodb.com/products/compass) podczas przechowywania i/lub zarządzania danymi w Cosmos DB. Dla tego przewodnika korzystamy z interfejsu API Azure Cosmos DB dla MongoDB. Dla osób z nieznajomym, kompas jest graficznym interfejsem użytkownika dla MongoDB. Jest on często używany do wizualizacji danych, uruchamiania zapytań ad hoc oraz zarządzania danymi.

Cosmos DB to globalnie dystrybuowana wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby nawiązać połączenie z kontem usługi Cosmos DB przy użyciu kompasu MongoDB, musisz:

* Pobierz i zainstaluj [kompas](https://www.mongodb.com/download-center/compass?jmp=hero)
* Cosmos DB informacje o [parametrach połączenia](connect-mongodb-account.md)

> [!NOTE]
> Obecnie program Azure Cosmos DB API for MongoDB Server w wersji 3,2 jest obsługiwany z najnowszą wersją MongoDB kompasu. MongoDB kompas nie jest jeszcze obsługiwany w wersji serwera 3,6. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Nawiązywanie połączenia z interfejsem API Cosmos DB MongoDB

Aby połączyć konto Cosmos DB z kompasem, możesz wykonać poniższe czynności:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos, które skonfigurowano za pomocą interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu instrukcji znajdujących się [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający Blok parametrów połączenia](./media/mongodb-compass/mongodb-compass-connection.png)

2. Kliknij przycisk informujący **o skopiowaniu do schowka** obok **podstawowego/pomocniczego ciągu połączenia** w Cosmos DB. Kliknięcie tego przycisku spowoduje skopiowanie całego ciągu połączenia do Schowka.

    ![Zrzut ekranu przedstawiający przycisk Kopiuj do schowka](./media/mongodb-compass/mongodb-connection-copy.png)

3. Otwórz kompas na pulpicie/komputerze i kliknij przycisk **Połącz** , a następnie **Połącz z...**.

4. Kompas automatycznie wykryje parametry połączenia w schowku i wyświetli monit o podanie, czy chcesz użyć tego do nawiązania połączenia. Kliknij przycisk **tak** , tak jak pokazano na poniższym zrzucie ekranu.

    ![Zrzut ekranu przedstawiający monit o podanie poziomu kompasu do nawiązania połączenia](./media/mongodb-compass/mongodb-compass-detect.png)

5. Po kliknięciu przycisku **tak** w powyższym kroku zostaną automatycznie wypełnione szczegóły z parametrów połączenia. Usuń wartość automatycznie wypełnianą w polu **Nazwa zestawu replik** , aby upewnić się, że pole pozostanie puste.

    ![Zrzut ekranu przedstawiający monit o podanie poziomu kompasu do nawiązania połączenia](./media/mongodb-compass/mongodb-compass-replica.png)

6. Kliknij pozycję **Połącz** w dolnej części strony. Twoje konto Cosmos DB i bazy danych powinny teraz być widoczne w MongoDB kompasu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
