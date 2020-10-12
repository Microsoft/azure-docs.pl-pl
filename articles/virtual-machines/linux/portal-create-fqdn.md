---
title: Tworzenie nazwy FQDN dla maszyny wirtualnej w Azure Portal
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla maszyny wirtualnej z systemem Linux w Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331475"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Linux

Podczas tworzenia maszyny wirtualnej w [Azure Portal](https://portal.azure.com)zostanie automatycznie utworzony publiczny zasób IP dla maszyny wirtualnej. Ten adres IP jest używany do zdalnego uzyskiwania dostępu do maszyny wirtualnej. Mimo że portal nie tworzy w [pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można dodać jedną po utworzeniu maszyny wirtualnej. W tym artykule przedstawiono procedurę tworzenia nazwy DNS lub FQDN.

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że maszyna wirtualna została już utworzona. W razie konieczności można [utworzyć maszynę wirtualną w portalu](quick-create-portal.md) lub [za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md). Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teraz można połączyć się zdalnie z maszyną wirtualną przy użyciu tej nazwy DNS, na przykład z `ssh azureuser@mydns.westus.cloudapp.azure.com` .

## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma publiczny adres IP i nazwę DNS, można wdrażać typowe struktury aplikacji lub usługi, takie jak Nginx, MongoDB, Docker itp.

Więcej informacji na temat korzystania z [Menedżer zasobów](../../azure-resource-manager/management/overview.md) można znaleźć w tematach dotyczących tworzenia wdrożeń platformy Azure.

