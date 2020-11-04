---
title: Zapisz procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript w Azure Cosmos DB
description: Dowiedz się, jak pisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335844"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Jak pisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Usługa Azure Cosmos DB umożliwia wykonywanie zapytań zoptymalizowanych przy użyciu płynnego interfejsu języka JavaScript bez konieczności znajomości języka SQL, co może służyć do pisania procedur składowanych i wyzwalaczy. Aby dowiedzieć się więcej na temat obsługi interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB, zobacz artykuł [Praca ze zintegrowanym interfejsem API zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md).

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Procedura składowana używająca interfejsu API zapytań języka JavaScript

Poniższy przykładowy kod pokazuje użycie interfejsu API zapytań języka JavaScript w kontekście procedury składowanej. Procedura składowana wstawia element Cosmos platformy Azure, który jest określony przez parametr wejściowy, i aktualizuje dokument metadanych przy użyciu `__.filter()` metody z MinSize, parametrem i totalSize na podstawie właściwości rozmiaru elementu wejściowego.

> [!NOTE]
> `__` (podwójne podkreślenie) jest aliasem funkcji `getContext().getCollection()` podczas korzystania z interfejsu API zapytań języka JavaScript.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższymi artykułami, aby dowiedzieć się więcej o procedurach składowanych, wyzwalaczach i funkcjach zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB:

* [Jak pracować z procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak rejestrować procedury składowane i używać ich w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Jak rejestrować [wyzwalacze wykonywane przed operacją](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) i [wyzwalacze wykonywane po operacji](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) i używać ich w usłudze Azure Cosmos DB

* [Jak rejestrować funkcje zdefiniowane przez użytkownika i używać ich w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Synthetic partition keys in Azure Cosmos DB (Syntetyczne klucze partycji w usłudze Azure Cosmos DB)](synthetic-partition-keys.md)
