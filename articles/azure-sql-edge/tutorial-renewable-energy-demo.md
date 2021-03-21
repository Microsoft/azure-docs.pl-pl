---
title: Wdrażanie usługi Azure SQL Edge na turbinach w farmie wiatrów firmy Contoso
description: W tym samouczku użyjesz usługi Azure SQL Edge do wykrywania wznawiania na turbinach w farmie wiatrów firmy Contoso.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723477"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Korzystanie z usługi Azure SQL Edge do tworzenia inteligentniejszych zasobów odnawialnych

Ten pokaz usługi Azure SQL Edge jest oparty na technologii odnawialnej firmy Contoso, w której znajduje się zespół z wiatru 

Ten pokaz przeprowadzi Cię przez proces rozwiązywania zgłoszonego alertu z powodu wykrycia przez program wiatru na urządzeniu. Nastąpi przeszkolenie modelu i wdrożenie go w usłudze SQL DB Edge, która poprawi wykryte wyjście wiatru i ostatecznie optymalizuje moc wyjściową.

Usługa Azure SQL Edge — wideo z pokazem energii dla systemu w witrynie Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Konfigurowanie pokazu na komputerze lokalnym
Program Git będzie używany do kopiowania wszystkich plików z wersji demonstracyjnej na komputer lokalny. 

1. Zainstaluj usługę Git w [tym miejscu](https://git-scm.com/download).
2. Otwórz wiersz polecenia i przejdź do folderu, w którym ma zostać pobrane repozytorium. 
3. Wydaj polecenie https://github.com/microsoft/sql-server-samples.git .
4. Przejdź do pozycji **"Demonstracja demona SQL-Server-samples\samples\demos\azure-SQL-Edge-demos\Wind"** w lokalizacji, w której jest Sklonowane repozytorium.
5. Postępuj zgodnie z instrukcjami w README.md, aby skonfigurować środowisko demonstracyjne i wykonać demonstrację.