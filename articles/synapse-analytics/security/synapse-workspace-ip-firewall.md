---
title: Konfigurowanie reguł zapory adresów IP
description: Artykuł, który uczy się skonfigurować reguły zapory IP w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: a7e159c94bf1b9f3e8049fd657abb562f1c85671
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87503926"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Reguły zapory IP usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule opisano reguły zapory IP i naucz się, jak je skonfigurować w usłudze Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP

Reguły zapory adresów IP udzielają lub nie zezwalają na dostęp do obszaru roboczego Synapse na podstawie źródłowego adresu IP każdego żądania. Reguły zapory IP można skonfigurować dla obszaru roboczego. Reguły zapory adresów IP skonfigurowane na poziomie obszaru roboczego mają zastosowanie do wszystkich publicznych punktów końcowych obszaru roboczego (pule SQL, SQL na żądanie i programowanie).

## <a name="create-and-manage-ip-firewall-rules"></a>Tworzenie reguł zapory adresów IP i zarządzanie nimi

Istnieją dwa sposoby dodawania reguł zapory IP do obszaru roboczego Synapse. Aby dodać zaporę IP do obszaru roboczego, wybierz pozycję **zabezpieczenia i obsługa sieci** i zaznacz pole wyboru **Zezwalaj na połączenia ze wszystkich adresów IP** podczas tworzenia obszaru roboczego.

![Azure Portal konfigurację adresu IP obszaru roboczego Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure Portal konfigurację adresu IP obszaru roboczego Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Możesz również dodać reguły zapory adresów IP do obszaru roboczego Synapse po utworzeniu obszaru roboczego. Wybierz pozycję **zapory** w obszarze **zabezpieczenia** z Azure Portal. Aby dodać nową regułę zapory adresów IP, nadaj jej nazwę, początkowy adres IP i końcowy adres IP. Po zakończeniu wybierz pozycję **Zapisz**.

![Konfiguracja adresu IP obszaru roboczego usługi Azure Synapse w Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Nawiązywanie połączenia z usługą Synapse z własnej sieci

Możesz nawiązać połączenie z obszarem roboczym usługi Synapse za pomocą programu Synapse Studio. Możesz również użyć SQL Server Management Studio (SSMS), aby nawiązać połączenie z zasobami SQL (pulami SQL i SQL na żądanie) w obszarze roboczym.

Upewnij się, że Zapora w sieci i komputer lokalny zezwalają na komunikację wychodzącą na portach TCP 80, 443 i 1443 dla Synapse Studio.

Ponadto należy zezwolić na komunikację wychodzącą na porcie UDP 53 dla Synapse Studio. Aby nawiązać połączenie przy użyciu narzędzi, takich jak program SSMS i Power BI, musisz zezwolić na komunikację wychodzącą na porcie TCP 1433.

Jeśli używasz domyślnego ustawienia zasad połączenia przekierowania, może być konieczne zezwolenie na komunikację wychodzącą na dodatkowych portach. Więcej informacji o zasadach połączeń można znaleźć [tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy).

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego usługi Azure Synapse](../quickstart-create-workspace.md)

Tworzenie obszaru roboczego usługi Azure Synapse z [zarządzanym obszarem roboczym Virtual Network](./synapse-workspace-managed-vnet.md)