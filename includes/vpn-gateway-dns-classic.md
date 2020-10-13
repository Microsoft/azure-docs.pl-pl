---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875573"
---
Ustawienia DNS nie są wymaganą częścią tej konfiguracji, ale system DNS jest niezbędny, jeśli chcesz rozpoznawanie nazw między maszynami wirtualnymi. Określenie wartości nie powoduje utworzenia nowego serwera DNS. Określony adres IP serwera DNS powinien być adresem serwera będącego w stanie rozpoznawać nazwy zasobów, z którymi nawiązywane jest połączenie.

Po utworzeniu sieci wirtualnej możesz dodać adres IP serwera DNS, aby umożliwić obsługę rozpoznawania nazw. Otwórz ustawienia sieci wirtualnej, wybierz pozycję Serwery DNS i Dodaj adres IP serwera DNS, który ma być używany do rozpoznawania nazw.

1. Zlokalizuj sieć wirtualną w portalu.
2. Na stronie sieci wirtualnej w sekcji **Ustawienia** wybierz pozycję **serwery DNS**.
3. Dodaj serwer DNS.
4. Aby zapisać ustawienia, wybierz pozycję **Zapisz** w górnej części strony.