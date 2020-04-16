---
title: Konfigurowanie reguł zapory IP w usłudze Azure Synapse Analytics
description: Artykuł, który uczy konfigurowania reguł zapory IP w usłudze Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424111"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Reguły zapory IP usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule opisano reguły zapory IP i nauczysz, jak je skonfigurować w usłudze Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Reguły zapory IP

Reguły zapory IP udzielają lub odmawiają dostępu do obszaru roboczego Synapse na podstawie źródłowego adresu IP każdego żądania. Reguły zapory IP można skonfigurować dla obszaru roboczego. Reguły zapory IP skonfigurowane na poziomie obszaru roboczego mają zastosowanie do wszystkich publicznych punktów końcowych obszaru roboczego (pule SQL, SQL na żądanie i programistyczne).

## <a name="create-and-manage-ip-firewall-rules"></a>Tworzenie reguł zapory IP i zarządzanie nimi

Istnieją dwa sposoby dodawania reguł zapory IP do obszaru roboczego Synapse. Aby dodać zaporę IP do obszaru roboczego, wybierz pozycję **Zabezpieczenia + sieć** i zaznacz pole wyboru **Zezwalaj na połączenia ze wszystkich adresów IP** podczas tworzenia obszaru roboczego.

![Konfiguracja IP obszaru roboczego usługi Azure portal Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Konfiguracja IP obszaru roboczego usługi Azure portal Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Po utworzeniu obszaru roboczego można również dodać reguły zapory IP do obszaru roboczego Synapse. Wybierz zapory w obszarze **Zabezpieczenia** z **witryny** Azure portal. Aby dodać nową regułę zapory IP, nadaj jej nazwę, Początkowe IP i Początkowy adres IP. Po zakończeniu wybierz pozycję **Zapisz**.

![Konfiguracja IP obszaru roboczego usługi Azure Synapse w witrynie Azure portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Łączenie się z synapse z własnej sieci

Za pomocą synapse studio można połączyć się z obszarem roboczym Synapse. Za pomocą programu SQL Server Management Studio (SSMS) można również połączyć się z zasobami SQL (pule SQL i SQL na żądanie) w obszarze roboczym.

Upewnij się, że zapora sieciowa i komputer lokalny umożliwia komunikację wychodzącą na portach TCP 80, 443 i 1443 dla Synapse Studio.

Ponadto musisz zezwolić na komunikację wychodzącą na porcie UDP 53 dla Synapse Studio. Aby połączyć się przy użyciu narzędzi, takich jak SSMS i Power BI, należy zezwolić na komunikację wychodzącą na porcie TCP 1433.

Jeśli używasz domyślnego ustawienia zasad połączenia przekierowania, może być konieczne zezwolenie na komunikację wychodzącą na dodatkowych portach. Więcej informacji na temat zasad połączeń można znaleźć tutaj.

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego synapsa platformy Azure](../quickstart-create-workspace.md)

Tworzenie obszaru roboczego Usługi Azure Synapse za pomocą [sieci wirtualnej zarządzanego obszaru roboczego](./synapse-workspace-managed-vnet.md)