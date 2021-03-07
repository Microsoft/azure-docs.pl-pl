---
title: Dozwolone urzędy certyfikacji dla usług Azure Front-Standard/Premium (wersja zapoznawcza)
description: Ten artykuł zawiera listę wszystkich urzędów certyfikacji dozwolonych podczas tworzenia własnego certyfikatu.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434870"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Dozwolone urzędy certyfikacji dla usług Azure Front-Standard/Premium (wersja zapoznawcza)

Po włączeniu funkcji HTTPS przy użyciu własnego certyfikatu dla niestandardowej domeny Standard/Premium platformy Azure. Do utworzenia certyfikatu TLS/SSL wymagany jest dozwolony urząd certyfikacji (CA). W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
