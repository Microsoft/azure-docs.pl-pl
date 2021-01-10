---
title: Konfiguracja adresu IP frontonu Application Gateway platformy Azure
description: W tym artykule opisano sposób konfigurowania adresu IP frontonu Application Gateway platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 732054ae2cd8f93d17694e9ea1bdd85680e9c314
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98058654"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway konfigurację adresu IP frontonu

Bramę aplikacji można skonfigurować tak, aby zawierała publiczny adres IP, prywatny adres IP lub oba te elementy. Publiczny adres IP jest wymagany w przypadku hostowania zaplecza, do którego klienci muszą uzyskać dostęp za pośrednictwem Internetu za pośrednictwem wirtualnego adresu IP (VIP) dostępnego z Internetu.

## <a name="public-and-private-ip-address-support"></a>Obsługa publicznych i prywatnych adresów IP

Application Gateway v2 obecnie nie obsługuje trybu prywatnego adresu IP. Obsługuje następujące kombinacje:

* Prywatny adres IP i publiczny adres IP
* Tylko publiczny adres IP

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące Application Gateway](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Publiczny adres IP nie jest wymagany dla wewnętrznego punktu końcowego, który nie jest narażony na Internet. Jest on znany jako punkt końcowy *wewnętrznego modułu równoważenia obciążenia* (ILB) lub prywatny adres IP frontonu. ILB bramy aplikacji jest przydatne w przypadku wewnętrznych aplikacji biznesowych, które nie są dostępne w Internecie. Jest on również przydatny w przypadku usług i warstw w aplikacji wielowarstwowej w ramach granicy zabezpieczeń, która nie jest dostępna w Internecie, ale wymaga dystrybucji obciążenia z działaniem okrężnym, lepkość sesji lub zakończenia protokołu TLS.

Obsługiwany jest tylko jeden publiczny adres IP lub jeden prywatny adres IP. Adres IP frontonu jest wybierany podczas tworzenia bramy aplikacji.

- W przypadku publicznego adresu IP można utworzyć nowy publiczny adres IP lub użyć istniejącego publicznego adresu IP w tej samej lokalizacji, w której znajduje się Brama aplikacji. Aby uzyskać więcej informacji, zobacz [statyczny i dynamiczny publiczny adres IP](./application-gateway-components.md#static-versus-dynamic-public-ip-address).

- Dla prywatnego adresu IP można określić prywatny adres IP z podsieci, w której jest tworzona Brama aplikacji. Jeśli nie określisz go, zostanie automatycznie wybrany dowolny adres IP z podsieci. Wybrany typ adresu IP (statyczny lub dynamiczny) nie może zostać później zmieniony. Aby uzyskać więcej informacji, zobacz [Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia](./application-gateway-ilb-arm.md).

Adres IP frontonu jest skojarzony z *odbiornikiem*, który sprawdza przychodzące żądania w adresie IP frontonu.

## <a name="next-steps"></a>Następne kroki

- [Informacje o konfiguracji odbiornika](configuration-listeners.md)