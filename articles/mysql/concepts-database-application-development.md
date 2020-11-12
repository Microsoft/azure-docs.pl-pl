---
title: Programowanie aplikacji — Azure Database for MySQL
description: Wprowadza uwagi dotyczące projektowania, które deweloper powinien wykonać, gdy piszesz kod aplikacji w celu nawiązania połączenia z Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20c6841bb596c7f4a32ef689423515a8da59a945
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541559"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Omówienie tworzenia aplikacji dla Azure Database for MySQL 
W tym artykule omówiono zagadnienia związane z projektowaniem, które deweloper powinien wykonać, gdy piszesz kod aplikacji w celu nawiązania połączenia z Azure Database for MySQL. 

> [!TIP]
> Aby zapoznać się z samouczkiem dotyczącym tworzenia serwera, tworzenia zapory opartej na serwerze, wyświetlania właściwości serwera, tworzenia bazy danych oraz łączenia i wykonywania zapytań przy użyciu Workbench i mysql.exe, zobacz [projektowanie pierwszej Azure Database for MySQL bazy danych](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Język i platforma
Dostępne są przykłady kodu dla różnych języków programowania i platform programistycznych. Linki do przykładów kodu można znaleźć pod adresem: [biblioteki połączeń używane do łączenia](concepts-connection-libraries.md) się z Azure Database for MySQL

## <a name="tools"></a>Narzędzia
Azure Database for MySQL używa wersji społeczności MySQL zgodnej z typowymi narzędziami do zarządzania MySQL, takimi jak Workbench lub MySQL Utilities, takimi jak mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)i inne. Korzystając z Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsów API REST, można również korzystać z usługi bazy danych.

## <a name="resource-limitations"></a>Ograniczenia zasobów
Azure Database for MySQL zarządza zasobami dostępnymi dla serwera przy użyciu dwóch różnych mechanizmów: 
- Zarządzanie zasobami.
- Wymuszanie limitów.

## <a name="security"></a>Zabezpieczenia
Azure Database for MySQL zapewnia zasoby do ograniczania dostępu, ochrony danych, konfigurowania użytkowników i ról oraz monitorowania działań w bazie danych MySQL.

## <a name="authentication"></a>Authentication
Azure Database for MySQL obsługuje uwierzytelnianie serwera dla użytkowników i logowania.

## <a name="resiliency"></a>Odporność
Gdy wystąpi błąd przejściowy podczas łączenia z bazą danych MySQL, kod powinien ponowić próbę wywołania. Zaleca się, aby logika ponowienia używała logiki wstecznej, aby nie przeciążać bazy danych SQL wieloma klientami jednocześnie.

- Przykłady kodu: w przypadku przykładów kodu, które ilustrują logikę ponowień, zobacz przykłady wybranego języka w: [biblioteki połączeń używane do łączenia](concepts-connection-libraries.md) się z Azure Database for MySQL

## <a name="managing-connections"></a>Zarządzanie połączeniami
Połączenia bazy danych są zasobami ograniczonymi, dlatego zalecamy korzystanie z połączeń podczas uzyskiwania dostępu do bazy danych MySQL w celu uzyskania lepszej wydajności.
- Dostęp do bazy danych za pomocą puli połączeń lub połączeń trwałych.
- Uzyskaj dostęp do bazy danych przy użyciu okresu krótkiego połączenia. 
- Użycie logiki ponawiania w aplikacji w punkcie próby połączenia w celu przechwycenia błędów wynikających z jednoczesnych połączeń osiągnie maksymalną dozwoloną wartość. W logice ponawiania Ustaw krótkie opóźnienie, a następnie poczekaj na losowy czas przed dodatkowymi próbami połączenia.