---
title: Tworzenie nazwy FQDN dla maszyny wirtualnej w Azure Portal
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny (FQDN) dla maszyny wirtualnej w Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b010070b7a45c24037c6de4648574c01b017d759
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107401"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Linux

Podczas tworzenia maszyny wirtualnej w [Azure Portal](https://portal.azure.com)zostanie automatycznie utworzony publiczny zasób IP dla maszyny wirtualnej. Ten adres IP jest używany do zdalnego uzyskiwania dostępu do maszyny wirtualnej. Mimo że portal nie tworzy w [pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można dodać jedną po utworzeniu maszyny wirtualnej. W tym artykule przedstawiono procedurę tworzenia nazwy DNS lub FQDN. 

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że maszyna wirtualna została już utworzona. W razie konieczności można utworzyć maszynę wirtualną z systemem [Linux](./linux/quick-create-portal.md) lub [Windows](./windows/quick-create-portal.md) w portalu. Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:


1. Wybierz maszynę wirtualną w portalu. 
1. W menu po lewej stronie wybierz pozycję **Właściwości** .
1. W obszarze **publiczna nazwa ADDRESS\DNS IP etykieta**, wybierz adres IP.
2. W obszarze **etykieta nazwy DNS** wprowadź prefiks, którego chcesz użyć.
3. Wybierz pozycję **Zapisz** w górnej części strony.
4. Wybierz pozycję **Przegląd** w menu po lewej stronie, aby wrócić do bloku przegląd maszyny wirtualnej.
5. Sprawdź, czy **nazwa DNS** jest wyświetlana poprawnie. 

## <a name="next-steps"></a>Następne kroki

Usługi DNS można także zarządzać za pomocą [stref Azure DNS](../dns/dns-getstarted-portal.md).

