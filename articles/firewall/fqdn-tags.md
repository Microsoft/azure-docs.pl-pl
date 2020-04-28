---
title: Omówienie tagów FQDN dla zapory platformy Azure
description: Tag FQDN reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzonych z dobrze znanymi usługami firmy Microsoft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74169051"
---
# <a name="fqdn-tags-overview"></a>Omówienie tagów FQDN

Tag FQDN reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzonych z dobrze znanymi usługami firmy Microsoft. Możesz użyć znacznika FQDN w regułach aplikacji, aby zezwolić na wymagany ruch sieciowy wychodzący przez zaporę.

Na przykład aby ręcznie zezwolić na Windows Update ruchu sieciowego przez zaporę, należy utworzyć wiele reguł aplikacji na podstawie dokumentacji firmy Microsoft. Za pomocą tagów FQDN można utworzyć regułę aplikacji, dołączyć tag **aktualizacje systemu Windows** i teraz ruch sieciowy do punktów końcowych firmy Microsoft Windows Update może przepływać przez zaporę.

Nie można tworzyć własnych tagów FQDN ani nie można określać, które nazwy FQDN są zawarte w tagu. Firma Microsoft zarządza nazwami FQDN, które obejmują tag FQDN, i aktualizuje tag w miarę zmiany nazw FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

W poniższej tabeli przedstawiono bieżące Tagi FQDN, których można użyć. Firma Microsoft zachowuje te Tagi i można oczekiwać, że dodatkowe Tagi będą dodawane okresowo.

## <a name="current-fqdn-tags"></a>Bieżące Tagi FQDN

|Tag FQDN  |Opis  |
|---------|---------|
|Windows Update     |Zezwalaj na dostęp wychodzący do Microsoft Update zgodnie z opisem w artykule [jak skonfigurować zaporę pod kątem aktualizacji oprogramowania](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostyka systemu Windows|Zezwalaj na dostęp wychodzący do wszystkich [punktów końcowych diagnostyki systemu Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Usługa Microsoft Active Protection (MAPS)|Zezwalaj na dostęp wychodzący do [map](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Zezwala na dostęp wychodzący do ruchu platformy ASE. Ten tag nie obejmuje magazynów specyficznych dla klienta i punktów końcowych SQL utworzonych przez środowisko ASE. Powinny one być włączane za pośrednictwem [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub dodawane ręcznie.<br><br>Aby uzyskać więcej informacji na temat integrowania zapory platformy Azure z środowiskiem ASE, zobacz [blokowanie App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Zezwala na dostęp wychodzący do usług Azure Backup.|
|Azure HDInsight|Zezwala na dostęp wychodzący dla ruchu platformy usługi HDInsight. Ten tag nie obejmuje magazynów specyficznych dla klienta ani ruchu SQL z usługi HDInsight. Włącz je za pomocą [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub Dodaj je ręcznie.|

> [!NOTE]
> W przypadku wybrania znacznika FQDN w regule aplikacji pole Protokół: Port musi być ustawione na **https**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć zaporę platformy Azure, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md).
