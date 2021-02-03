---
title: Kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB.
description: W tym artykule opisano, jak działa automatyczne tworzenie kopii zapasowej, przywracanie danych na żądanie. Wyjaśniono również różnicę między ciągłymi i okresowymi trybami kopii zapasowych.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525436"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są wykonywane bez wpływu na wydajność ani dostępność operacji bazy danych. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu. Automatyczne kopie zapasowe są przydatne w scenariuszach, w których przypadkowo usunięto lub Zaktualizowano konto, bazę danych lub kontener usługi Azure Cosmos, a następnie wymagane jest odzyskanie danych. Istnieją dwa tryby tworzenia kopii zapasowej:

* **Okresowy tryb tworzenia kopii zapasowej** — ten tryb jest domyślnym trybem tworzenia kopii zapasowych dla wszystkich istniejących kont. W tym trybie kopia zapasowa jest wykonywana w regularnych odstępach czasu, a dane są przywracane przez utworzenie żądania z zespołem pomocy technicznej. W tym trybie można skonfigurować interwał i przechowywanie kopii zapasowych dla Twojego konta. Maksymalny okres przechowywania jest dłuższy niż miesiąc. Minimalny interwał tworzenia kopii zapasowej może wynosić godzinę.  Aby dowiedzieć się więcej, zobacz artykuł [tryb okresowej kopii zapasowej](configure-periodic-backup-restore.md) .

* **Tryb ciągłej kopii zapasowej** (obecnie w publicznej wersji zapoznawczej) — ten tryb jest wybierany podczas tworzenia konta Azure Cosmos DB. Ten tryb pozwala wykonać przywracanie w dowolnym momencie w ciągu ostatnich 30 dni. Aby dowiedzieć się więcej, zobacz artykuł [wprowadzenie do trybu ciągłej kopii zapasowej](continuous-backup-restore-introduction.md), Konfigurowanie ciągłej kopii zapasowej za pomocą [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)i [Menedżer zasobów](continuous-backup-restore-template.md) .

  > [!NOTE]
  > W przypadku skonfigurowania nowego konta przy użyciu ciągłej kopii zapasowej można wykonać przywracanie samoobsługowe za pomocą Azure Portal, PowerShell lub interfejsu wiersza polecenia. Jeśli Twoje konto jest skonfigurowane w trybie ciągłym, nie można przełączyć go z powrotem w tryb okresowy. Obecnie istniejące konta z trybem okresowej kopii zapasowej nie mogą zostać zmienione do trybu ciągłego.  

## <a name="next-steps"></a>Następne kroki

Następnie można dowiedzieć się, jak skonfigurować tryby okresowego i ciągłego tworzenia kopii zapasowych oraz zarządzać nimi w ramach konta:

* [Skonfiguruj okresowe zasady tworzenia kopii zapasowych i zarządzaj nimi](configure-periodic-backup-restore.md) .
* Co to jest tryb [ciągłej kopii zapasowej](continuous-backup-restore-introduction.md) ?
* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
