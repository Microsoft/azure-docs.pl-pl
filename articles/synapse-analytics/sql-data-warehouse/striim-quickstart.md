---
title: Striim — Szybki Start
description: Szybko Rozpocznij pracę z Striimami i Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349939"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Przewodnik dotyczący instalacji oferty portalu Azure SQL DW w witrynie Striim

W tym przewodniku szybki start założono, że masz już istniejące wystąpienie SQL Data Warehouse.

Wyszukaj Striim w portalu Azure Marketplace i wybierz opcję Striim integracji danych do SQL Data Warehouse (przemieszczane) 

![Zainstaluj Striim][install]

Skonfiguruj maszynę wirtualną Striim z określonymi właściwościami, zwracając uwagę na nazwę klastra Striim, hasło i hasło administratora.

![Konfigurowanie Striim][configure]

Po wdrożeniu kliknij pozycję \<Nazwa maszyny wirtualnej>-masternode w Azure Portal, kliknij pozycję Połącz i skopiuj nazwę logowania przy użyciu lokalnego konta maszyny wirtualnej 

![Połącz Striim z SQL Data Warehouse][connect]

Pobierz plik sqljdbc42. jar z <https://www.microsoft.com/en-us/download/details.aspx?id=54671> programu na komputer lokalny. 

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

Teraz otwórz ulubioną przeglądarkę i przejdź do \<nazwy DNS>:9080

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
