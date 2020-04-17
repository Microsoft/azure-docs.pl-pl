---
title: Tworzenie sieci FQDN dla maszyny Wirtualnej systemu Windows w portalu platformy Azure
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla maszyny wirtualnej opartej na Menedżerze zasobów w witrynie Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7bfa510068d71afb7701ab8964f06053d38ac70
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458900"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w portalu Azure dla maszyny Wirtualnej systemu Windows

Podczas tworzenia maszyny wirtualnej (VM) w [witrynie Azure portal,](https://portal.azure.com)publiczny zasób IP dla maszyny wirtualnej jest tworzony automatycznie. Ten adres IP służy do zdalnego dostępu do maszyny Wirtualnej. Chociaż portal nie tworzy [w pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można ją utworzyć po utworzeniu maszyny wirtualnej. W tym artykule przedstawiono kroki tworzenia nazwy DNS lub nazwy FQDN.

## <a name="create-a-fqdn"></a>Tworzenie fqdn
W tym artykule przyjęto założenie, że utworzono już maszynę wirtualną. W razie potrzeby można [utworzyć maszynę wirtualną w portalu](quick-create-portal.md) lub za pomocą programu Azure [PowerShell](quick-create-powershell.md). Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teraz można łączyć się zdalnie z maszyną wirtualną przy użyciu tej nazwy DNS, takiej jak protokół RDP (Remote Desktop Protocol).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma publiczną nazwę IP i DNS, można wdrożyć typowe struktury aplikacji lub usługi, takie jak usługi IIS, SQL lub SharePoint.

Możesz również dowiedzieć się więcej na temat [korzystania z Usługi Resource Manager,](../../azure-resource-manager/management/overview.md) aby uzyskać wskazówki dotyczące tworzenia wdrożeń platformy Azure.

