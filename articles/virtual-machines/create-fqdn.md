---
title: Tworzenie nazwy FQDN dla maszyny wirtualnej w Azure Portal
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny (FQDN) dla maszyny wirtualnej w Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351912"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Linux

Podczas tworzenia maszyny wirtualnej w [Azure Portal](https://portal.azure.com)zostanie automatycznie utworzony publiczny zasób IP dla maszyny wirtualnej. Ten adres IP jest używany do zdalnego uzyskiwania dostępu do maszyny wirtualnej. Mimo że portal nie tworzy w [pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można dodać jedną po utworzeniu maszyny wirtualnej. W tym artykule przedstawiono procedurę tworzenia nazwy DNS lub FQDN. 

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że maszyna wirtualna została już utworzona. W razie konieczności można utworzyć maszynę wirtualną z systemem [Linux](./linux/quick-create-portal.md) lub [Windows](./windows/quick-create-portal.md) w portalu. Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:


1. Wybierz maszynę wirtualną w portalu. W obszarze **nazwa DNS** wybierz pozycję **Konfiguruj**.
2. Wprowadź nazwę DNS, a następnie wybierz pozycję **Zapisz** w górnej części strony.
3. Aby powrócić do bloku przegląd maszyny wirtualnej, Zamknij blok **Konfiguracja** , wybierając **symbol X** w prawym górnym rogu. 
4. Sprawdź, czy *nazwa DNS* jest teraz wyświetlana poprawnie.
   



## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma publiczny adres IP i nazwę DNS, można wdrażać typowe struktury aplikacji lub usługi, takie jak Nginx, MongoDB i Docker.

Więcej informacji na temat korzystania z [Menedżer zasobów](../azure-resource-manager/management/overview.md) można znaleźć w tematach dotyczących tworzenia wdrożeń platformy Azure.

