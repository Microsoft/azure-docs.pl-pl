---
title: Uaktualnij agenta Azure Backup
description: Te informacje wyjaśniają, dlaczego należy uaktualnić agenta Azure Backup i skąd należy pobrać uaktualnienie.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294103"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnianie agenta MARS

Wersje agenta Microsoft Azure Recovery Services (MARS) poniżej 2.0.9083.0 mają zależność od usługi Azure Access Control. Agent MARS jest również nazywany agentem Azure Backup.

W 2018 firma Microsoft [zaprzestarzała usługę Access Control platformy Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19 marca 2018 wszystkie wersje agenta MARS poniżej 2.0.9083.0 będą powodować błędy kopii zapasowych. Aby uniknąć błędów kopii zapasowych, należy [uaktualnić agenta Mars do najnowszej wersji](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Aby zidentyfikować serwery wymagające uaktualnienia agenta MARS, wykonaj kroki opisane w temacie [Uaktualnianie agenta Microsoft Azure Recovery Services (MARS)](../articles/backup/upgrade-mars-agent.md).

Agent MARS służy do tworzenia kopii zapasowych plików i folderów oraz danych stanu systemu na platformie Azure. Program System Center DPM i Azure Backup Server używają agenta MARS do tworzenia kopii zapasowych danych na platformie Azure.
