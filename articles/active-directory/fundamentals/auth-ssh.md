---
title: Uwierzytelnianie SSH za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące osiągania integracji SSH z Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 315fe35a79ade39de9f541504fc2fe52754614de
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749352"
---
# <a name="ssh"></a>Protokół SSH  

Secure Shell (SSH) to protokół sieciowy, który zapewnia szyfrowanie usług sieciowych w bezpieczny sposób za pośrednictwem niezabezpieczonych sieci. SSH zapewnia również logowanie za pomocą wiersza polecenia, wykonuje polecenia zdalne i bezpiecznie przesyła pliki. Jest często używany w systemach UNIX, takich jak Linux®. Protokół SSH zastępuje protokół Telnet, który nie zapewnia szyfrowania w niezabezpieczonych sieciach. 

Azure Active Directory (Azure AD) udostępnia rozszerzenie maszyny wirtualnej dla systemów ® Linux działających na platformie Azure. 

## <a name="use-when"></a>Zastosowania 

* Praca z maszynami ® z systemem Linux, które wymagają zdalnego logowania

* Wykonywanie poleceń zdalnych w systemach opartych ® Linux

* Bezpieczne przesyłanie plików w niezabezpieczonych sieciach

![diagram usługi Azure AD z protokołem SSH](./media/authentication-patterns/ssh-auth.png)

SSH z usługą Azure AD

## <a name="components-of-system"></a>Składniki systemu 

* **Użytkownik:** uruchamia klienta SSH w celu skonfigurowania połączenia z maszynami ® wirtualnych z systemem Linux i udostępnia poświadczenia do uwierzytelniania.

* **Przeglądarka internetowa:** składnik, z który użytkownik wchodzi w interakcję. Komunikuje się ona z dostawcą tożsamości (Azure AD) w celu bezpiecznego uwierzytelniania i autoryzacji użytkownika.

* **Klient SSH:** kieruje procesem konfiguracji połączenia.

* **Azure AD:** uwierzytelnia tożsamość użytkownika przy użyciu przepływu urządzenia i wydaje token do maszyn wirtualnych z systemem Linux.

* **Maszyna wirtualna z systemem Linux:** akceptuje token i zapewnia pomyślne połączenie.

## <a name="implement-ssh-with-azure-ad"></a>Implementowanie SSH za pomocą usługi Azure AD 

* [Logowanie do maszyny wirtualnej z systemem Linux® przy użyciu Azure Active Directory poświadczeń — Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [Przepływ kodu urządzenia OAuth 2.0 — platforma tożsamości firmy Microsoft ](../develop/v2-oauth2-device-code.md)

* [Integracja z Azure Active Directory (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

