---
title: Omówienie usługi Virtual Machines
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się więcej na temat maszyn wirtualnych CloudSimple i ich korzyści.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024912"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple maszyn wirtualnych — Omówienie

CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware z poziomu Azure Portal.  Klaster lub Pula zasobów z klastra vSphere jest zarządzana za pośrednictwem platformy Azure, mapując ją na subskrypcję.

Aby można było utworzyć maszynę wirtualną z systemem CloudSimple na platformie Azure, w chmurze prywatnej vCenter musi istnieć szablon maszyny wirtualnej.  Szablon służy do dostosowywania systemu operacyjnego i aplikacji.  Szablon maszyny wirtualnej może być zaostrzony w celu spełnienia zasad zabezpieczeń przedsiębiorstwa.  Przy użyciu szablonu można tworzyć maszyny wirtualne, a następnie korzystać z nich z Azure Portal przy użyciu modelu samoobsługowego.

## <a name="benefits"></a>Zalety

CloudSimple maszyny wirtualne z Azure Portal udostępniają mechanizm samoobsługowy do tworzenia maszyn wirtualnych VMware i zarządzania nimi.

* Tworzenie maszyny wirtualnej CloudSimple w chmurze prywatnej vCenter
* Zarządzanie właściwościami maszyny wirtualnej
  * Dodawanie/usuwanie dysków
  * Dodawanie/usuwanie kart sieciowych
* Operacje na mocy maszyny wirtualnej CloudSimple
  * Włącz i Wyłącz zasilanie
  * Resetowanie maszyny wirtualnej
* Usuwanie maszyny wirtualnej

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [korzystać z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się, jak [mapować swoją subskrypcję platformy Azure](azure-subscription-mapping.md)
