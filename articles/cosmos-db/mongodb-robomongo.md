---
title: Użyj Robo 3T, aby nawiązać połączenie z Azure Cosmos DB
description: Dowiedz się, jak nawiązać połączenie z usługą Azure Cosmos DB przy użyciu interfejsu API Robo 3T i Azure Cosmos DB dla MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096414"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Używanie programu Robo 3T za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Aby nawiązać połączenie z kontem usługi Cosmos przy użyciu usługi Robo 3T, musisz:

* Pobierz i zainstaluj program [Robo 3T](https://robomongo.org/)
* Cosmos DB informacje o [parametrach połączenia](connect-mongodb-account.md)

> [!NOTE]
> Obecnie Robo 3T v 1.2 i Lower są obsługiwane z interfejsem API Cosmos DB dla MongoDB.

## <a name="connect-using-robo-3t"></a>Łączenie za pomocą programu Robo 3T

Aby dodać konto Cosmos do Menedżera połączeń usługi Robo 3T, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos, które skonfigurowano za pomocą interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu instrukcji znajdujących się [tutaj](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Zrzut ekranu przedstawiający Blok parametrów połączenia":::
2. Uruchom aplikację *Korzystanie narzędzia robomongo* .

3. Kliknij przycisk połączenia w obszarze **plik** , aby zarządzać połączeniami. Następnie kliknij pozycję **Utwórz** w oknie **połączenia MongoDB** , co spowoduje otwarcie okna **Ustawienia połączenia** .

4. W oknie **Ustawienia połączenia** wybierz nazwę. Następnie Znajdź **hosta** i **port** z informacji o połączeniu w kroku 1 i wprowadź je odpowiednio do **adresu** i **portu**.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Zrzut ekranu przedstawiający korzystanie narzędzia robomongo zarządzanie połączeniami":::
5. Na karcie **uwierzytelnianie** kliknij pozycję **wykonaj uwierzytelnianie**. Następnie wprowadź swoją bazę danych (domyślnie: *admin*), **nazwę użytkownika** i **hasło**.
**Nazwę użytkownika** i **hasło** można znaleźć w informacjach o połączeniu w kroku 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Zrzut ekranu karty uwierzytelnianie korzystanie narzędzia robomongo":::
6. Na karcie **SSL** zaznacz opcję **Użyj protokołu SSL**, a następnie zmień **metodę uwierzytelniania** na certyfikat z podpisem **własnym**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Zrzut ekranu karty korzystanie narzędzia robomongo SSL":::
7. Na koniec kliknij przycisk **Testuj** , aby sprawdzić, czy możesz nawiązać połączenie, a następnie **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
