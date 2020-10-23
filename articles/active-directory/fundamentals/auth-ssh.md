---
title: Uwierzytelnianie SSH za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury dotyczącej integracji SSH z Azure Active Directory
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
ms.openlocfilehash: eb766150339820f9356fe94311cd1ff33dda5480
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462916"
---
# <a name="ssh"></a>Protokół SSH  

Secure Shell (SSH) to protokół sieciowy, który zapewnia bezpieczne szyfrowanie usług sieciowych za pośrednictwem niezabezpieczonej sieci. Protokół SSH udostępnia również w wierszu polecenia logowanie, wykonywanie poleceń zdalnych i bezpieczne przesyłanie plików. Jest on często używany w systemach opartych na systemie UNIX, takich jak Linux®. SSH zastępuje protokół Telnet, który nie zapewnia szyfrowania w niezabezpieczonej sieci. 

Azure Active Directory (Azure AD) zapewnia rozszerzenie maszyny wirtualnej (VM) dla systemów opartych na systemie Linux® działających na platformie Azure. 

## <a name="use-when"></a>Zastosowania 

* Praca z maszynami wirtualnymi z systemem Linux®, które wymagają logowania zdalnego

* Wykonywanie poleceń zdalnych w systemach opartych na® Linux

* Bezpieczne transferowanie plików w niezabezpieczonej sieci

![Diagram usługi Azure AD z protokołem SSH](./media/authentication-patterns/ssh-auth.png)

SSH z usługą Azure AD

## <a name="components-of-system"></a>Składniki systemu 

* **Użytkownik**: uruchamia klienta SSH w celu skonfigurowania połączenia z maszynami wirtualnymi z systemem Linux® i udostępnia poświadczenia do uwierzytelniania.

* **Przeglądarka sieci Web**: składnik, z którym korzysta użytkownik. Komunikuje się z dostawcą tożsamości (Azure AD) w celu bezpiecznego uwierzytelniania i autoryzowania użytkownika.

* **Klient SSH**: steruje procesem konfigurowania połączenia.

* **Azure AD**: uwierzytelnia tożsamość użytkownika przy użyciu przepływu urządzenia i tokenu problemów z maszynami wirtualnymi z systemem Linux.

* **Maszyna wirtualna z systemem Linux**: akceptuje token i zapewnia pomyślne połączenie.

## <a name="implement-ssh-with-azure-ad"></a>Implementowanie protokołu SSH w usłudze Azure AD 

* [Logowanie do maszyny wirtualnej z systemem Linux® przy użyciu poświadczeń Azure Active Directory — platforma Azure Virtual Machines ](https://docs.microsoft.com/azure/virtual-machines/linux/login-using-aad) 

* [Przepływ kodu urządzenia OAuth 2,0 — platforma tożsamości firmy Microsoft ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)

* [Integracja z usługą Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 
