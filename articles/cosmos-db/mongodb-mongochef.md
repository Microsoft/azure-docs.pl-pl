---
title: Łączenie się z interfejsem API Azure Cosmos DB w usłudze MongoDB przy użyciu programu Studio 3T
description: Dowiedz się, jak nawiązać połączenie z interfejsem API Azure Cosmos DB MongoDB przy użyciu programu Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096519"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Nawiązywanie połączenia z kontem usługi Azure Cosmos za pomocą programu Studio 3T
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Aby nawiązać połączenie z interfejsem API Azure Cosmos DB MongoDB za pomocą programu Studio 3T, należy:

* Pobierz i zainstaluj program [Studio 3T](https://studio3t.com/).
* Zawierają informacje o [parametrach połączenia](connect-mongodb-account.md) konta usługi Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Utwórz połączenie w programie Studio 3T

Aby dodać konto usługi Azure Cosmos do Menedżera połączeń programu Studio 3T, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla interfejsu API Azure Cosmos DB dla konta MongoDB, korzystając z instrukcji w temacie [Connect a MongoDB Application to Azure Cosmos DB](connect-mongodb-account.md) article.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Zrzut ekranu strony parametrów połączenia":::

2. Kliknij przycisk **Połącz** , aby otworzyć Menedżera połączeń, a następnie kliknij przycisk **nowe połączenie** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
3. W oknie **nowe połączenie** na karcie **serwer** wprowadź nazwę hosta (FQDN) konta usługi Azure Cosmos i port.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
4. W oknie **nowe połączenie** na karcie **uwierzytelnianie** wybierz pozycję uwierzytelnianie **podstawowe (MONGODB-CR lub SCARM-SHA-1)** , a następnie wprowadź nazwę użytkownika i hasło.  Zaakceptuj domyślną bazę danych uwierzytelniania (Administrator) lub podaj własną wartość.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
5. W oknie **nowe połączenie** na karcie **SSL** zaznacz pole wyboru **Użyj protokołu SSL do połączenia** i przycisk radiowy **Zaakceptuj certyfikaty SSL z podpisem własnym serwera** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
6. Kliknij przycisk **Testuj połączenie** , aby sprawdzić poprawność informacji o połączeniu, kliknij przycisk **OK** , aby powrócić do okna nowe połączenie, a następnie kliknij przycisk **Zapisz** .

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Zrzut ekranu strony parametrów połączenia":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Tworzenie bazy danych, kolekcji i dokumentów przy użyciu programu Studio 3T
Aby utworzyć bazę danych, kolekcję i dokumenty przy użyciu programu Studio 3T, wykonaj następujące czynności:

1. W **Menedżerze połączeń** zaznacz połączenie i kliknij pozycję **Połącz** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
2. Kliknij prawym przyciskiem myszy hosta i wybierz polecenie **Dodaj bazę danych** .  Podaj nazwę bazy danych i kliknij przycisk **OK** .

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
3. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Dodaj kolekcję** .  Podaj nazwę kolekcji, a następnie kliknij przycisk **Utwórz** .

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Zrzut ekranu strony parametrów połączenia":::
4. Kliknij element menu **kolekcji** , a następnie kliknij pozycję **Dodaj dokument** .

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Zrzut ekranu strony parametrów połączenia" i zwróć pola nadrzędne i stanowe.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Zrzut ekranu strony parametrów połączenia":::

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
