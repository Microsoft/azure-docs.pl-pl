---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70020170"
---
Teraz możesz dodawać dane do nowego kontenera przy użyciu Eksplorator danych.

1. W **Eksplorator danych** rozwiń bazę danych **zadania** , rozwiń kontener **elementy** . Wybierz pozycję **elementy**, a następnie wybierz pozycję **nowy element**.

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Teraz Dodaj dokument do kontenera z następującą strukturą.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Po dodaniu pliku JSON do karty **dokumenty** wybierz pozycję **Zapisz**.

    ![Kopiuj w danych JSON i wybierz pozycję Zapisz w Eksplorator danych w Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Utwórz i zapisz jeszcze jeden dokument, w którym wstawisz unikatową wartość dla właściwości `id` i zmienisz inne właściwości wedle uznania. Nowe dokumenty mogą mieć dowolną strukturę, ponieważ usługa Azure Cosmos DB nie wymusza żadnego schematu danych.