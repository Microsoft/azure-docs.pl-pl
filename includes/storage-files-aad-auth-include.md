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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536589"
---
[Usługa Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem bloku komunikatów serwera (SMB) za pośrednictwem [lokalnych Usług domenowych Active Directory (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (wersja zapoznawcza) i [usług domenowych Active Directory platformy Azure (Usługi Azure AD DS).](../articles/active-directory-domain-services/overview.md) W tym artykule skupiono się na tym, jak udziały plików platformy Azure mogą korzystać z usług domeny lokalnie lub na platformie Azure w celu obsługi dostępu opartego na tożsamości do udziałów plików platformy Azure za pomocą SMB. Włączenie dostępu opartego na tożsamości dla udziałów plików platformy Azure umożliwia zastąpienie istniejących serwerów plików udziałami plików platformy Azure bez zastępowania istniejącej usługi katalogowej, przy zachowaniu bezproblemowego dostępu użytkowników do udziałów. 

Usługa Azure Files wymusza autoryzację dostępu użytkownika do poziomu udziału i katalogu/pliku. Przypisanie uprawnień na poziomie udziału można wykonać na użytkowników lub grupach usługi Azure Active Directory (Azure AD) zarządzanych za pomocą modelu [kontroli dostępu opartego na rolach (RBAC).](../articles/role-based-access-control/overview.md) W przypadku funkcji RBAC poświadczenia używane do dostępu do plików powinny być dostępne lub synchronizowane z usługą Azure AD. Wbudowane role RBAC, takie jak czytnik udostępniania SMB danych magazynu, można przypisać użytkownikom lub grupom w usłudze Azure AD, aby udzielić dostępu do odczytu do udziału plików platformy Azure.

Na poziomie katalogu/pliku usługa Azure Files obsługuje zachowywanie, dziedziczenie i wymuszanie [dacl dacl systemu Windows,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) podobnie jak wszystkie serwery plików systemu Windows. Można zachować dacl systemu Windows podczas kopiowania danych za pomocą protokołu SMB między istniejącym udziałem plików a udziałami plików platformy Azure. Niezależnie od tego, czy planujesz wymusić autoryzację, czy nie, możesz użyć udziałów plików platformy Azure do utworzenia kopii zapasowej list ACL wraz z danymi. 
