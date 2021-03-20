---
title: Rozwiązanie VMware firmy Azure według CloudSimple — publiczny adres IP
description: Dowiedz się więcej o publicznych adresach IP i ich korzyściach na platformie Azure VMware przez CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77024980"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Publiczny adres IP CloudSimple — Omówienie

Publiczny adres IP umożliwia zasobom internetowym komunikowanie się z zasobami w chmurze prywatnej w prywatnym adresie IP. Prywatny adres IP to maszyna wirtualna lub programowy moduł równoważenia obciążenia w usłudze vCenter w chmurze prywatnej. Publiczny adres IP umożliwia udostępnianie usług uruchomionych w chmurze prywatnej w Internecie.

Publiczny adres IP jest przydzielany do prywatnego adresu IP, dopóki nie zostanie przypisany. Publiczny adres IP można przypisać tylko do jednego prywatnego adresu IP.

Zasób skojarzony z publicznym adresem IP zawsze używa publicznego adresu IP na potrzeby dostępu do Internetu. Domyślnie na publicznym adresie IP jest dozwolony tylko wychodzący dostęp do Internetu.  Odmowa ruchu przychodzącego na publicznym adresie IP.  Aby zezwolić na ruch przychodzący, Utwórz regułę zapory dla publicznego adresu IP na określonym porcie.

## <a name="benefits"></a>Korzyści

Użycie publicznego adresu IP do komunikacji przychodzącej zapewnia następujące informacje:

* Ochrona przed atakami typu "odmowa usługi" (DDoS). Ta ochrona jest automatycznie włączona dla publicznego adresu IP.
* Zawsze włączone monitorowanie ruchu i łagodzenie w czasie rzeczywistym typowych ataków na poziomie sieci. Te zabezpieczenia są tymi samymi zabezpieczeniami, które są używane przez firmę Microsoft Usługi online.
* Cała Skala sieci globalnej platformy Azure. Za pomocą tej sieci można dystrybuować i ograniczać ruch związany z atakami między regionami.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przydzielić publiczny adres IP](public-ips.md)