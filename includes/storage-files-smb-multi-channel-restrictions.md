---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052948"
---
Wielokanałowość protokołu SMB dla udziałów plików platformy Azure ma obecnie następujące ograniczenia:
- Może być używany tylko z lokalnymi nadmiarowymi kontami FileStorage.
- Obsługiwane tylko w przypadku klientów z systemem Windows. 
- Maksymalna liczba kanałów wynosi cztery.
- Funkcja SMB Direct nie jest obsługiwana.
- Prywatne punkty końcowe dla kont magazynu nie są obsługiwane.
- W przypadku kont magazynu z [uwierzytelnianiem na podstawie](../articles/storage/files/storage-files-active-directory-overview.md) Active Directory Domain Services lokalnego (AD DS) lub na platformie Azure AD DS z włączoną obsługą Azure Files klienci SMB nie będą mogli korzystać z Eksploratora plików systemu Windows w celu skonfigurowania uprawnień NTFS do katalogów i plików.
    - Aby skonfigurować uprawnienia, użyj narzędzia [icacls](/windows-server/administration/windows-commands/icacls) systemu Windows lub polecenia [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) .

