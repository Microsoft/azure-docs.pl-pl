---
title: Omówienie usługi Virtual Machines
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się więcej na temat maszyn wirtualnych CloudSimple i ich korzyści. Można zarządzać maszynami wirtualnymi VMware z poziomu Azure Portal.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141980"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple maszyn wirtualnych — Omówienie

CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware z poziomu Azure Portal.  Klaster lub Pula zasobów z klastra vSphere jest zarządzana za pośrednictwem platformy Azure, mapując ją na subskrypcję.

Aby można było utworzyć maszynę wirtualną z systemem CloudSimple na platformie Azure, w chmurze prywatnej vCenter musi istnieć szablon maszyny wirtualnej.  Szablon służy do dostosowywania systemu operacyjnego i aplikacji.  Szablon maszyny wirtualnej może być zaostrzony w celu spełnienia zasad zabezpieczeń przedsiębiorstwa.  Przy użyciu szablonu można tworzyć maszyny wirtualne, a następnie korzystać z nich z Azure Portal przy użyciu modelu samoobsługowego.

## <a name="benefits"></a>Korzyści

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
