---
title: Wysoka dostępność — Azure Database for MySQL
description: Ten temat zawiera informacje o wysokiej dostępności podczas korzystania z Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 50bf1502589ea0932fd45367c039e6e37135d761
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086062"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Pojęcia dotyczące wysokiej dostępności w Azure Database for MySQL
Usługa Azure Database for MySQL zapewnia gwarantowany wysoki poziom dostępności. Umowa dotycząca poziomu usług (SLA 99,99) na finanse W przypadku korzystania z tej usługi nie ma praktycznie czasu na żadną aplikację.

## <a name="high-availability"></a>Wysoka dostępność
Model wysokiej dostępności (HA) jest oparty na wbudowanych mechanizmach awaryjnych w przypadku wystąpienia przerwy na poziomie węzła. Przyczyną może być zakłócenie na poziomie węzła ze względu na awarię sprzętu lub w odpowiedzi na wdrożenie usługi.

Przez cały czas zmiany wprowadzone do serwera bazy danych Azure Database for MySQL są wykonywane w kontekście transakcji. Zmiany są rejestrowane synchronicznie w usłudze Azure Storage po zatwierdzeniu transakcji. Jeśli wystąpi zakłócenie na poziomie węzła, serwer bazy danych automatycznie utworzy nowy węzeł i dołączy magazyn danych do nowego węzła. Wszystkie aktywne połączenia są porzucane i wszystkie transakcje numerów porządkowych określających nie są zatwierdzane.

## <a name="application-retry-logic-is-essential"></a>Logika ponawiania aplikacji jest istotna
Ważne jest, aby aplikacje bazy danych MySQL zostały skompilowane w celu wykrywania i ponawiania próby porzucenia połączeń i transakcji zakończonych niepowodzeniem. Po ponownym uruchomieniu aplikacji połączenie aplikacji jest w sposób niewidoczny do odkierowany do nowo utworzonego wystąpienia, które przejmuje wystąpienie zakończone niepowodzeniem.

Wewnętrznie na platformie Azure Brama jest używana do przekierowywania połączeń do nowego wystąpienia. Po przerwie cały proces działający w trybie failover zazwyczaj trwa dziesiątki sekund. Ponieważ przekierowanie jest obsługiwane wewnętrznie przez bramę, parametry połączenia zewnętrznego pozostają takie same dla aplikacji klienckich.

## <a name="scaling-up-or-down"></a>Skalowanie w górę lub w dół
Podobnie jak w przypadku modelu HA, gdy Azure Database for MySQL jest skalowane w górę lub w dół, tworzone jest nowe wystąpienie serwera o określonym rozmiarze. Istniejący magazyn danych jest odłączony od oryginalnego wystąpienia i dołączony do nowego wystąpienia.

Podczas operacji skalowania następuje przerwanie połączeń z bazą danych. Aplikacje klienckie są rozłączone, a otwieranie niezatwierdzonych transakcji zostało anulowane. Gdy aplikacja kliencka ponowi próbę nawiązania połączenia lub przetworzy nowe połączenie, Brama kieruje połączenie do nowego wystąpienia.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [obsługi błędów łączności przejściowej](concepts-connectivity.md)
- Dowiedz się, jak [replikować dane za pomocą replik odczytu](howto-read-replicas-portal.md)
