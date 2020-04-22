---
title: Tworzenie sieci FQDN dla maszyny Wirtualnej w witrynie Azure portal
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla maszyny wirtualnej opartej na Menedżerze zasobów w witrynie Azure portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51b9ecf8e300af6b1e3f11d8431de7a282ab342
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759354"
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

