---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS na platformie Azure front-drzwi
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie niestandardowej drzwi platformy Azure, musisz użyć dozwolonego urzędu certyfikacji, aby go utworzyć.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613683"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS na platformie Azure front-drzwi

Po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) dla domeny niestandardowej drzwi platformy Azure. Do utworzenia certyfikatu TLS/SSL wymagany jest dozwolony urząd certyfikacji (CA). W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
