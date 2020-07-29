---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8817f5c7ab53c5b0ebb0e8adf8c6f0f7b8a2ff42
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372330"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem [lokalnych Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) i [Azure Active Directory Domain Services (AD DS platformy Azure)](../articles/active-directory-domain-services/overview.md). Ten artykuł koncentruje się na tym, jak udziały plików platformy Azure mogą używać usług domenowych lokalnie lub na platformie Azure, aby obsługiwać dostęp oparty na tożsamościach do udziałów plików platformy Azure za pośrednictwem protokołu SMB. Włączenie dostępu opartego na tożsamościach dla udziałów plików platformy Azure umożliwia zastępowanie istniejących serwerów plików z udziałami plików platformy Azure bez zastępowania istniejącej usługi katalogowej, co zapewnia bezproblemowe dostęp użytkowników do udziałów. 

Azure Files wymusza autoryzację dostępu użytkowników zarówno do udziału, jak i do poziomów plików i katalogów. Przypisanie uprawnień na poziomie udziału można wykonać w odniesieniu do użytkowników lub grup Azure Active Directory (Azure AD) zarządzanych za pośrednictwem modelu [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md) . W przypadku kontroli RBAC poświadczenia używane do uzyskiwania dostępu do plików powinny być dostępne lub synchronizowane z usługą Azure AD. Role wbudowane platformy Azure można przypisać do użytkowników lub grup w usłudze Azure AD, takich jak dane plików magazynu SMB, do udziału plików platformy Azure.

Na poziomie katalogu/pliku Azure Files obsługuje zachowanie, dziedziczenie i wymuszanie [list DACL systemu Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) podobnie jak w przypadku wszystkich serwerów plików systemu Windows. Możesz zachować DACL systemu Windows podczas kopiowania danych za pośrednictwem protokołu SMB między istniejącym udziałem plików i udziałami plików platformy Azure. Niezależnie od tego, czy zamierzasz wymusić autoryzację, czy nie, możesz użyć udziałów plików platformy Azure do tworzenia kopii zapasowych list ACL wraz z danymi. 
