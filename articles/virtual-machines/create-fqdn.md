---
title: Tworzenie nazwy FQDN dla maszyny wirtualnej w Azure Portal
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny (FQDN) dla maszyny wirtualnej w Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132068"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Linux

Podczas tworzenia maszyny wirtualnej w [Azure Portal](https://portal.azure.com)zostanie automatycznie utworzony publiczny zasób IP dla maszyny wirtualnej. Ten adres IP jest używany do zdalnego uzyskiwania dostępu do maszyny wirtualnej. Mimo że portal nie tworzy w [pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można dodać jedną po utworzeniu maszyny wirtualnej. W tym artykule przedstawiono procedurę tworzenia nazwy DNS lub FQDN. 

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że maszyna wirtualna została już utworzona. W razie konieczności można utworzyć maszynę wirtualną z systemem [Linux](./linux/quick-create-portal.md) lub [Windows](./windows/quick-create-portal.md) w portalu. Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:


1. Wybierz maszynę wirtualną w portalu. 
1. W menu po lewej stronie wybierz pozycję **Konfiguracja** .
1. W obszarze **etykieta nazwy DNS** wprowadź prefiks, którego chcesz użyć.
1. Wybierz pozycję **Zapisz** w górnej części strony.
1. Wróć do bloku przegląd maszyny wirtualnej, wybierając pozycję **Przegląd** w menu po lewej stronie. 
1. Sprawdź, czy *nazwa DNS* jest wyświetlana poprawnie. 

## <a name="next-steps"></a>Następne kroki

Usługi DNS można także zarządzać za pomocą [stref Azure DNS](../dns/dns-getstarted-portal.md).

