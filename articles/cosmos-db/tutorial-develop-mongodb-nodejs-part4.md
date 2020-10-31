---
title: Tworzenie aplikacji kątowej z interfejsem API Azure Cosmos DB dla MongoDB (part1)
description: Część 4 z serii samouczków o tworzeniu aplikacji bazy danych MongoDB przy użyciu usługi Angular i języka Node dla usługi Azure Cosmos DB przy użyciu dokładnie tych samych interfejsów API, które były używane dla bazy danych MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 4ca7286676c441b2fa96883e0c187497f59d6222
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082631"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Tworzenie aplikacji Angular przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB — tworzenie konta usługi Cosmos
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ten wieloczęściowy samouczek pokazuje, jak utworzyć nową aplikację napisaną w środowisku Node.js za pomocą programu Express i platformy Angular, a następnie podłączyć ją do [konta usługi Cosmos skonfigurowanego przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md).

Część 4 samouczka jest oparta na [Części 3](tutorial-develop-mongodb-nodejs-part3.md) i obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie grupy zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Tworzenie konta usługi Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="video-walkthrough"></a>Przewodnik wideo

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tej części samouczka upewnij się, że zostały wykonane czynności opisane w [części 3](tutorial-develop-mongodb-nodejs-part3.md) samouczka. 

W tej sekcji samouczka możesz użyć usługi Azure Cloud Shell (w przeglądarce internetowej) lub zainstalowanego lokalnie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórz konto Azure Cosmos DB za pomocą [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) polecenia.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Jako elementu `<cosmosdb-name>` użyj własnej unikatowej nazwy konta usługi Azure Cosmos DB. Nazwa musi być unikatowa w obrębie wszystkich nazw kont usługi Azure Cosmos DB na platformie Azure.
* Ustawienie `--kind MongoDB` umożliwia usłudze Azure Cosmos DB korzystanie z połączeń klienckich bazy danych MongoDB.

Wykonanie polecenia może potrwać minutę lub dwie. Po zakończeniu w oknie terminala zostaną wyświetlone informacje o nowej bazie danych. 

Po utworzeniu konta usługi Azure Cosmos DB:
1. Otwórz nowe okno przeglądarki i przejdź do [https://portal.azure.com](https://portal.azure.com)
1. Kliknij logo Azure Cosmos DB :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png"::: na pasku po lewej stronie, a zobaczysz wszystkie Cosmos baz danych.
1. Kliknij właśnie utworzone konto usługi Azure Cosmos DB, wybierz kartę **Omówienie** i przewiń w dół, aby na mapie wyświetlić lokalizację bazy danych. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png" alt-text="Zrzut ekranu przedstawia przegląd konta usługi Azure Cosmos D B.":::

4. Przewiń w dół w lewym obszarze nawigacji i kliknij kartę **Replikuj dane globalnie** . Spowoduje to wyświetlenie mapy, na której można zobaczyć różne potencjalne docelowe obszary replikacji. Można na przykład kliknąć region Australia Południowo-Wschodnia lub Australia Wschodnia i replikować dane do klientów w Australii. Więcej informacji o replikacji globalnej można znaleźć w temacie [How to distribute data globally with Azure Cosmos DB (Jak dystrybuować dane globalnie przy użyciu usługi Azure Cosmos DB)](distribute-data-globally.md). Na razie zajmijmy się jednym wystąpieniem, które w razie potrzeby możemy zreplikować, ponieważ już znamy sposób.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png" alt-text="Zrzut ekranu przedstawia przegląd konta usługi Azure Cosmos D B.":::

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Utworzono grupę zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Utworzono konto usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz przejść do następnej części samouczka, aby połączyć usługę Azure Cosmos DB z aplikacją przy użyciu wtyczki Mongoose.

> [!div class="nextstepaction"]
> [Łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose](tutorial-develop-mongodb-nodejs-part5.md)