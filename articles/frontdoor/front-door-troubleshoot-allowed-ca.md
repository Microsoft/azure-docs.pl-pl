---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS na platformie Azure front-drzwi
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
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
ms.openlocfilehash: 7bdef37561687b49b030d8237472c0d35f945c13
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399127"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS na platformie Azure front-drzwi

W przypadku domeny niestandardowej drzwi platformy Azure, po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), należy użyć dozwolonego urzędu certyfikacji w celu utworzenia certyfikatu TLS/SSL. W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
