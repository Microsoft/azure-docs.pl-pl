---
title: Dostarczanie zawartości w Chinach przy użyciu Azure CDN | Microsoft Docs
description: Dowiedz się więcej o korzystaniu z usługi Azure Content Delivery Network (CDN) w celu dostarczania zawartości do użytkowników w Chinach.
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
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 599ec041837460c30b4655531b822eab5f0eafa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778918"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Dostarczanie zawartości w Chinach przy użyciu Azure CDN

Globalna usługa Azure Content Delivery Network (CDN) może obsłużyć zawartość dla użytkowników w Chinach z lokalizacjami punktów obecności (POP) w bliskiej Chinach lub dowolnym punkcie POP, który zapewnia najlepszą wydajność dla żądań pochodzących z Chin. Jeśli jednak Chiny to znaczący rynek dla klientów i potrzebują szybko wydajności, zamiast tego Rozważ użycie Azure CDN Chin.

Azure CDN Chińska różni się od Azure CDN globalnym w tym, że dostarcza zawartość z punktów obecności wewnątrz Chin przez partnera z wielu lokalnych dostawców. Ze względu na standardową zgodność i regulację należy zarejestrować oddzielną subskrypcję do korzystania z Azure CDN Chin, a witryny sieci Web muszą mieć licencję ICP. Portal i środowisko API do włączania dostarczania zawartości i zarządzania nią są identyczne między Azure CDN globalnymi i Azure CDN Chiny.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Porównanie Azure CDN globalnych i Azure CDN Chinach

Azure CDN globalne i Azure CDN Chińska mają następujące funkcje:

- Azure CDN globalny:

     - Portal https://portal.azure.com  

     - Wykonuje dostarczanie zawartości poza Chiny

     - Cztery warstwy cenowe: Microsoft Standard, Verizon Standard, Verizon Premium i Standard Akamai

     - [Dokumentacja](./index.yml)

- Azure CDN Chińska:

     - Portal https://portal.azure.cn

     - Wykonuje dostarczanie zawartości wewnątrz Chin

     - Dwie warstwy cenowe: standardowa i Premium

     - [Dokumentacja](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Azure CDN Chinach, zobacz:

- [Funkcje Content Delivery Network](https://www.azure.cn/en-us/home/features/cdn/)

- [Omówienie Content Delivery Network platformy Azure](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Korzystanie z usługi Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Dostępność usługi platformy Azure w Chinach](/azure/china/concepts-service-availability)