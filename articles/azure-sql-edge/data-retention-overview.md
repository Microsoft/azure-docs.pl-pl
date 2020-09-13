---
title: Przegląd zasad przechowywania danych — Azure SQL Edge (wersja zapoznawcza)
description: Informacje na temat zasad przechowywania danych w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: SQL Edge, przechowywanie danych
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 3649d4f77e5b57ab14accacd87fbaa867ba2742f
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550661"
---
# <a name="data-retention-policy-overview"></a>Przegląd zasad przechowywania danych

Zbieranie i przechowywanie danych z połączonych urządzeń IoT jest ważne, aby zwiększyć możliwości operacyjne i szczegółowe informacje biznesowe. Jednak dane pochodzące z tych urządzeń stają się ważne dla organizacji, aby starannie planować ilość danych, które mają być zachowane, oraz na jakim poziomie szczegółowości. Zachowanie wszystkich danych na całym poziomie szczegółowości jest pożądane, ale nie zawsze jest praktyczne. Ponadto ilość danych, które mogą być zachowywane, jest ograniczona ilością miejsca dostępnego na urządzeniach IoT lub brzegowych. 

Usługa Azure SQL Edge (wersja zapoznawcza) CTP 2.3 dodaje nową funkcję, która umożliwia administratorom baz danych Definiowanie zasad przechowywania danych w bazie danych programu SQL Edge i jej źródłowych tabelach. Po zdefiniowaniu zasad przechowywania danych zostanie uruchomione zadanie systemu w tle w celu przeczyszczenia wszelkich przestarzałych (starych) danych z tabel użytkowników. 

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

## <a name="next-steps"></a>Następne kroki

- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md).
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md).
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)
