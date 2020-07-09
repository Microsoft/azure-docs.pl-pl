---
title: Striim — Szybki Start
description: Szybko Rozpocznij pracę z Striimami i Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 945bcd03bc3bf13517836e7a5624bd5142782183
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208556"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Przewodnik dotyczący instalacji oferty portalu Azure SQL DW w witrynie Striim

W tym przewodniku szybki start założono, że masz już istniejące wystąpienie SQL Data Warehouse.

Wyszukaj Striim w portalu Azure Marketplace i wybierz opcję Striim integracji danych do SQL Data Warehouse (przemieszczane) 

![Zainstaluj Striim][install]

Skonfiguruj maszynę wirtualną Striim z określonymi właściwościami, zwracając uwagę na nazwę klastra Striim, hasło i hasło administratora.

![Konfigurowanie Striim][configure]

Po wdrożeniu kliknij pozycję \<VM Name> masternode w Azure Portal, kliknij pozycję Połącz i skopiuj nazwę logowania przy użyciu lokalnego konta maszyny wirtualnej 

![Połącz Striim z SQL Data Warehouse][connect]

Pobierz plik sqljdbc42. jar z programu <https://www.microsoft.com/en-us/download/details.aspx?id=54671> na komputer lokalny. 

Otwórz okno wiersza polecenia i zmień katalogi na miejsce pobrania JDBC jar. Przechodzenie do pliku JAR na maszynę wirtualną Striim, pobieranie adresu i hasła z Azure Portal

![Kopiuj plik jar do maszyny wirtualnej][copy-jar]

Otwórz inne okno wiersza polecenia lub użyj narzędzia SSH do nawiązać połączenia SSH z klastrem Striim

![Protokół SSH do klastra][ssh]

Wykonaj następujące polecenia, aby przenieść plik JAR JDBC do katalogu lib Striim, a następnie uruchom i Zatrzymaj serwer.

   1. sudo Su
   2. /tmp CD
   3. MV sqljdbc42. jar/opt/striim/lib
   4. systemctl Zatrzymaj striim — węzeł
   5. systemctl Stop striim-DBMS
   6. systemctl Start striim-DBMS
   7. systemctl Start striim — węzeł

![Uruchom klaster Striim][start-striim]

Teraz otwórz ulubioną przeglądarkę i przejdź do \<DNS Name> : 9080

![Przejdź do ekranu logowania][navigate]

Zaloguj się przy użyciu nazwy użytkownika i hasła, które zostały skonfigurowane w Azure Portal, a następnie wybierz preferowanego kreatora, aby rozpocząć, lub przejdź do strony aplikacje, aby rozpocząć korzystanie z interfejsu użytkownika przeciągania i upuszczania.

![Zaloguj się przy użyciu poświadczeń serwera][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
