---
title: Tworzenie sieci FQDN dla maszyny Wirtualnej z systemem Linux w witrynie Azure portal
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla maszyny wirtualnej opartej na Menedżerze zasobów w witrynie Azure portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: caf7c1c9c0704de1408a322f581019eb74443365
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461501"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w portalu Azure dla maszyny Wirtualnej z systemem Linux

Podczas tworzenia maszyny wirtualnej (VM) w [witrynie Azure portal,](https://portal.azure.com)publiczny zasób IP dla maszyny wirtualnej jest tworzony automatycznie. Ten adres IP służy do zdalnego dostępu do maszyny Wirtualnej. Chociaż portal nie tworzy [w pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można dodać jedną po utworzeniu maszyny Wirtualnej. W tym artykule przedstawiono kroki tworzenia nazwy DNS lub nazwy FQDN.

## <a name="create-a-fqdn"></a>Tworzenie fqdn
W tym artykule przyjęto założenie, że utworzono już maszynę wirtualną. W razie potrzeby można [utworzyć maszynę wirtualną w portalu](quick-create-portal.md) lub za pomocą interfejsu [wiersza polecenia platformy Azure.](quick-create-cli.md) Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teraz można połączyć się zdalnie z maszyną `ssh azureuser@mydns.westus.cloudapp.azure.com`wirtualną przy użyciu tej nazwy DNS, takiej jak .

## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma publiczną nazwę IP i DNS, można wdrożyć typowe struktury aplikacji lub usługi, takie jak nginx, MongoDB, Docker itp.

Możesz również dowiedzieć się więcej na temat [korzystania z Usługi Resource Manager,](../../azure-resource-manager/management/overview.md) aby uzyskać wskazówki dotyczące tworzenia wdrożeń platformy Azure.

