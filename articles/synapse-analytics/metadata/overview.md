---
title: Model udostępnionej metadanych
description: Usługa Azure Synapse Analytics umożliwia korzystanie z różnych aparatów obliczeniowych obszarów roboczych w celu udostępniania baz danych i tabel między jej Apache Spark pulami (wersja zapoznawcza), bezserwerową pulą SQL (wersja zapoznawcza) i dedykowane pule SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 64c19f3331be8ffda433207da88ebf22c546ee4e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324664"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Udostępnione metadane usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia korzystanie z różnych aparatów obliczeniowych obszarów roboczych w celu udostępniania baz danych i tabel między jej Apache Spark pulami (wersja zapoznawcza) i bezserwerową pulą SQL (wersja zapoznawcza).

[!INCLUDE [preview](../includes/note-preview.md)]

Udostępnianie obsługuje tak zwane wzorce nowoczesnego magazynu danych i zapewnia obszarowi roboczemu aparaty SQL dostęp do baz danych i tabel utworzonych przy użyciu platformy Spark. Umożliwia także aparatom SQL tworzenie własnych obiektów, które nie są współużytkowane z innymi aparatami.

## <a name="support-the-modern-data-warehouse"></a>Obsługa nowoczesnego magazynu danych

Model udostępnionej metadanych obsługuje nowoczesny wzorzec magazynu danych w następujący sposób:

1. Dane z usługi Data Lake są przygotowywane i dobrze skonstruowane z platformą Spark przez przechowywanie przygotowanych danych (możliwych do partycjonowania) tabel Parquet, które znajdują się w niektórych bazach danych.

2. Utworzone bazy danych platformy Spark i wszystkie ich tabele stają się widoczne w dowolnym wystąpieniu puli Spark usługi Azure Synapse Workspace i mogą być używane z dowolnego zadania platformy Spark. Ta możliwość podlega [uprawnieniom](#security-model-at-a-glance) , ponieważ wszystkie pule platformy Spark w obszarze roboczym współdzielą ten sam magazyn metadanych katalogu.

3. Bazy danych utworzone przez platformę Spark i tabele z Parquetymi kopiami zapasowymi stają się widoczne w puli SQL bezserwerowej. [Bazy danych](database.md) są tworzone automatycznie w metadanych puli SQL bezserwerowej, a zarówno [tabela zewnętrzna, jak i zarządzana](table.md) utworzona przez zadanie Spark są udostępniane jako tabele zewnętrzne w metadanych puli SQL bezserwerowej w `dbo` schemacie odpowiedniej bazy danych. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synchronizacja obiektów odbywa się asynchronicznie. Obiekty będą z niewielkim opóźnieniem kilku sekund, dopóki nie pojawią się w kontekście SQL. Po ich wyświetleniu mogą być wysyłane zapytania, ale nie aktualizowane ani zmieniane przez aparaty SQL, które mają do nich dostęp.

## <a name="shared-metadata-objects"></a>Obiekty udostępnionych metadanych

Platforma Spark umożliwia tworzenie baz danych, tabel zewnętrznych, tabel zarządzanych i widoków. Ponieważ widoki platformy Spark wymagają aparatu Spark do przetwarzania zdefiniowanej instrukcji SQL platformy Spark i nie mogą być przetwarzane przez aparat SQL, do aparatu SQL obszaru roboczego są udostępniane tylko bazy danych i zawarte w nich tabele zewnętrzne i zarządzane, które używają formatu magazynu Parquet. Widoki platformy Spark są udostępniane tylko między wystąpieniami puli platformy Spark.

## <a name="security-model-at-a-glance"></a>Model zabezpieczeń w skrócie

Bazy danych i tabele platformy Spark wraz z synchronizowanymi reprezentacjami w aparacie SQL są zabezpieczone na podstawowym poziomie magazynu. Gdy w tabeli są wysyłane zapytania przez każdy z aparatów, których osoba przesyłająca zapytanie ma prawo do użycia, podmiot zabezpieczeń wysyłającego zapytania jest przesyłany do odpowiednich plików. Uprawnienia są sprawdzane na poziomie systemu plików.

Aby uzyskać więcej informacji, zobacz [udostępniona baza danych usługi Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Zmień konserwację

Jeśli obiekt metadanych został usunięty lub zmieniony z platformą Spark, zmiany są pobierane i propagowane do puli SQL bez serwera. Synchronizacja jest asynchroniczna, a zmiany są odzwierciedlane w aparacie SQL po krótkim opóźnieniu.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych bazach danych metadanych usługi Azure Synapse Analytics](database.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)

