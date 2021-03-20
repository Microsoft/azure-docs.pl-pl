---
title: Przegląd zasad przechowywania danych — Azure SQL Edge
description: Informacje na temat zasad przechowywania danych w usłudze Azure SQL Edge
keywords: SQL Edge, przechowywanie danych
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90976337"
---
# <a name="data-retention-overview"></a>Omówienie przechowywania danych

Zbieranie i przechowywanie danych z połączonych urządzeń IoT jest ważne, aby zwiększyć możliwości operacyjne i szczegółowe informacje biznesowe. Jednak dane pochodzące z tych urządzeń stają się ważne dla organizacji, aby starannie planować ilość danych, które mają być zachowane, oraz na jakim poziomie szczegółowości. Zachowanie wszystkich danych na całym poziomie szczegółowości jest pożądane, ale nie zawsze jest praktyczne. Ponadto ilość danych, które mogą być zachowywane, jest ograniczona ilością miejsca dostępnego na urządzeniach IoT lub brzegowych. 

Administratorzy baz danych usługi Azure SQL Edge mogą definiować zasady przechowywania danych w bazie danych programu SQL Edge i jej źródłowych tabelach. Po zdefiniowaniu zasad przechowywania danych zostanie uruchomione zadanie systemu w tle w celu przeczyszczenia wszelkich przestarzałych (starych) danych z tabel użytkowników. 

> [!Note]
> Dane po przeczyszczeniu z tabeli nie są możliwe do odzyskania. Jedynym możliwym sposobem odzyskania przeczyszczonych danych jest przywrócenie bazy danych ze starszej kopii zapasowej.

Przewodniki Szybki start:

- [Włączanie i wyłączanie zasad przechowywania danych](data-retention-enable-disable.md)
- [Zarządzanie danymi historycznymi przy użyciu zasad przechowywania](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Jak działa przechowywanie danych

Aby skonfigurować przechowywanie danych, można użyć instrukcji języka DDL. Aby uzyskać więcej informacji, [włączać i wyłączać zasady przechowywania danych](data-retention-enable-disable.md). W przypadku automatycznego usuwania przestarzałych rekordów należy najpierw włączyć przechowywanie danych zarówno dla bazy danych, jak i tabel, które mają zostać usunięte w tej bazie danych. 

Po skonfigurowaniu przechowywania danych dla tabeli zadanie w tle jest uruchamiane w celu zidentyfikowania przestarzałych rekordów w tabeli i usunięcia tych rekordów. Jeśli z jakiegoś powodu, automatyczne czyszczenie zadań nie jest uruchomione lub nie można przeprowadzić operacji usuwania, w tych tabelach można wykonać operację ręcznego czyszczenia. Aby uzyskać więcej informacji na temat automatycznego i ręcznego czyszczenia, zapoznaj się z [automatycznym i ręcznym oczyszczaniem](data-retention-cleanup.md).

## <a name="limitations-and-restrictions"></a>Ograniczenia i ograniczenia

- Przechowywanie danych, jeśli ta funkcja jest włączona, jest automatycznie wyłączana, gdy baza danych zostanie przywrócona z pełnej kopii zapasowej lub ponownie dołączona. 
- Nie można włączyć utrwalania danych dla tabeli historii danych czasowych
- Nie można zmienić colomn filtru przechowywania danych. Aby zmienić kolumnę, wyłącz przechowywanie danych w tabeli.  

## <a name="next-steps"></a>Następne kroki

- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md).
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md).
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)
