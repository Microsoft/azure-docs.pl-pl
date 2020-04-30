---
title: Model udostępnionych metadanych usługi Azure Synapse Analytics
description: Usługa Azure Synapse Analytics umożliwia różnym aparatom obliczeniowym obszarów roboczych udostępnianie baz danych i tabel między jej pulami usługi Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pulami SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424118"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Udostępnione metadane usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia różnym aparatom obliczeniowym obszarów roboczych udostępnianie baz danych i tabel między jej pulami usługi Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pulami SQL.

[!INCLUDE [preview](../includes/note-preview.md)]



Udostępnianie obsługuje tak zwane wzorce nowoczesnego magazynu danych i zapewnia obszarowi roboczemu aparaty SQL dostęp do baz danych i tabel utworzonych przy użyciu platformy Spark. Umożliwia także aparatom SQL tworzenie własnych obiektów, które nie są współużytkowane z innymi aparatami.

## <a name="support-the-modern-data-warehouse"></a>Obsługa nowoczesnego magazynu danych

Model udostępnionej metadanych obsługuje nowoczesny wzorzec magazynu danych w następujący sposób:

1. Dane z usługi Data Lake są przygotowywane i dobrze skonstruowane z platformą Spark przez przechowywanie przygotowanych danych (możliwych do partycjonowania) tabel Parquet, które znajdują się w niektórych bazach danych.

2. Utworzone bazy danych platformy Spark i wszystkie ich tabele stają się widoczne w dowolnym wystąpieniu puli Spark usługi Azure Synapse Workspace i mogą być używane z dowolnego zadania platformy Spark. Ta możliwość podlega [uprawnieniom](#security-model-at-a-glance) , ponieważ wszystkie pule platformy Spark w obszarze roboczym współdzielą ten sam magazyn metadanych katalogu.

3. Bazy danych utworzone przez platformę Spark i tabele z Parquetymi kopiami zapasowymi stają się widoczne w aparacie roboczym SQL na żądanie. [Bazy danych](database.md) są tworzone automatycznie w metadanych na żądanie SQL, a [tabela zewnętrzna i zarządzana](table.md) utworzona przez zadanie platformy Spark są udostępniane jako tabele zewnętrzne w metadanych na żądanie SQL w `dbo` schemacie odpowiedniej bazy danych. <!--For more details, see [ADD LINK].-->

4. Jeśli w obszarze roboczym istnieją wystąpienia puli SQL, dla których włączono synchronizację metadanych <!--[ADD LINK]--> lub jeśli zostanie utworzone nowe wystąpienie puli SQL z włączoną synchronizacją metadanych, utworzone bazy danych i tabele z Parquetymi kopiami zapasowymi zostaną automatycznie zmapowane do bazy danych puli SQL, zgodnie z opisem w [udostępnionej bazie danych usługi Azure Synapse Analytics](database.md).

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synchronizacja obiektów odbywa się asynchronicznie. Obiekty będą z niewielkim opóźnieniem kilku sekund, dopóki nie pojawią się w kontekście SQL. Po ich wyświetleniu mogą być wysyłane zapytania, ale nie aktualizowane ani zmieniane przez aparaty SQL, które mają do nich dostęp.

## <a name="which-metadata-objects-are-shared"></a>Które obiekty metadanych są udostępniane

Platforma Spark umożliwia tworzenie baz danych, tabel zewnętrznych, tabel zarządzanych i widoków. Ponieważ widoki platformy Spark wymagają aparatu platformy Spark do przetwarzania zdefiniowanej instrukcji języka Spark SQL i nie mogą być przetwarzane przez aparat SQL, są udostępniane tylko bazy danych i zawarte w nich tabele zewnętrzne i zarządzane, które używają formatu magazynu Parquet. Widoki platformy Spark są udostępniane tylko między wystąpieniami puli platformy Spark.

## <a name="security-model-at-a-glance"></a>Model zabezpieczeń w skrócie

Bazy danych i tabele platformy Spark wraz z synchronizowanymi reprezentacjami w aparatach SQL są zabezpieczone na podstawowym poziomie magazynu. Gdy w tabeli są wysyłane zapytania przez każdy z aparatów, których osoba przesyłająca zapytanie ma prawo do użycia, podmiot zabezpieczeń wysyłającego zapytania jest przesyłany do odpowiednich plików. Uprawnienia są sprawdzane na poziomie systemu plików.

Aby uzyskać więcej informacji, zobacz [udostępniona baza danych usługi Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Zmień konserwację

Jeśli obiekt metadanych zostanie usunięty lub zmieniony przy użyciu platformy Spark, zmiany zostaną pobrane i rozpropagowane do aparatu SQL na żądanie oraz do pul SQL, które mają zsynchronizowane obiekty. Synchronizacja jest asynchroniczna, a zmiany są odzwierciedlane w aparatach SQL po krótkim opóźnieniu.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych bazach danych metadanych usługi Azure Synapse Analytics](database.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)

