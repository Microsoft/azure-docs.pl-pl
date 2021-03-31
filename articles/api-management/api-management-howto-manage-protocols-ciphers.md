---
title: Zarządzanie protokołami i szyframi na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak zarządzać protokołami (TLS) i szyframi (DES) na platformie Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 043a3d0b63dfc74f587b58b3c2ac42f1a084cc4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250315"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Zarządzanie protokołami i szyfrowaniem w usłudze Azure API Management

Usługa Azure API Management obsługuje wiele wersji protokołu TLS zarówno dla strony klienta, jak i zaplecza, jak również szyfrowania 3DES.

W tym przewodniku przedstawiono sposób zarządzania protokołami i szyfrowania konfiguracji wystąpienia usługi Azure API Management.

![Zarządzanie protokołami i szyframi w APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Wystąpienie API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak zarządzać protokołami TLS i algorytmem szyfrowania 3DES

1. Przejdź do **wystąpienia API Management** w Azure Portal.
2. Wybierz pozycję **Ustawienia protokołu** z menu.  
3. Włącza lub wyłącza wymagane protokoły lub szyfry.
4. Kliknij pozycję **Zapisz**. Zmiany zostaną zastosowane w ciągu godziny.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o protokole [TLS (Transport Layer Security)](/dotnet/framework/network-programming/tls).
* Poznaj więcej [filmów wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) dotyczących API Management.
