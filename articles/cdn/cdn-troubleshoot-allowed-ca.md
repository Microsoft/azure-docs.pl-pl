---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS
titleSuffix: Azure Content Delivery Network
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573402"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS

Wymagania dotyczące certyfikatów są wymagane po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) dla domeny niestandardowej Azure CDN (Content Delivery Network). 

* **Standard Azure CDN z profilu Microsoft** wymaga certyfikatu z jednego z zatwierdzonych urzędów certyfikacji (CA) znajdujących się na poniższej liście. Jeśli certyfikat z niezatwierdzonego urzędu certyfikacji lub certyfikat z podpisem własnym jest używany, żądanie zostanie odrzucone. 

* **Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** akceptują wszystkie ważne certyfikaty z dowolnego prawidłowego urzędu certyfikacji. Profile Verizon nie obsługują certyfikatów z podpisem własnym.

> [!NOTE]
> Opcja używania własnego certyfikatu do włączania funkcji HTTPS domeny niestandardowej *nie* jest dostępna dla **Azure CDN Standard z profilów Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

