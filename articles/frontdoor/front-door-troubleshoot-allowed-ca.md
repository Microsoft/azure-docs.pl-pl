---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS na platformie Azure front-drzwi
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie 0custom z przodu platformy Azure, musisz użyć dozwolonego urzędu certyfikacji, aby go utworzyć.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269809"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS na platformie Azure front-drzwi

W przypadku domeny niestandardowej drzwi platformy Azure, po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), należy użyć dozwolonego urzędu certyfikacji w celu utworzenia certyfikatu TLS/SSL. W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
