---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS w Azure CDN
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259975"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS w Azure CDN

Należy spełnić określone wymagania dotyczące certyfikatu po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) dla domeny niestandardowej usługi Azure Content Delivery Network (CDN). **Standard Azure CDN z profilu Microsoft** wymaga certyfikatu z jednego z zatwierdzonych urzędów certyfikacji (CA) znajdujących się na poniższej liście. Jeśli certyfikat z niezatwierdzonego urzędu certyfikacji lub certyfikat z podpisem własnym jest używany, żądanie zostanie odrzucone. **Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** akceptują wszystkie ważne certyfikaty z dowolnego prawidłowego urzędu certyfikacji.

> [!NOTE]
> Opcja używania własnego certyfikatu do włączania funkcji HTTPS domeny niestandardowej *nie* jest dostępna dla **Azure CDN Standard z profilów Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

