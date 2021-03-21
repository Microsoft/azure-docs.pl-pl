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
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102603326"
---
Wielokanałowość protokołu SMB dla udziałów plików platformy Azure ma obecnie następujące ograniczenia:
- Może być używany tylko z lokalnymi nadmiarowymi kontami FileStorage.
- Obsługiwane tylko w przypadku klientów z systemem Windows. 
- Maksymalna liczba kanałów wynosi cztery.
- Funkcja SMB Direct nie jest obsługiwana.
- Prywatne punkty końcowe dla kont magazynu nie są obsługiwane.
- W przypadku kont magazynu z [uwierzytelnianiem na podstawie](../articles/storage/files/storage-files-active-directory-overview.md) Active Directory Domain Services lokalnego (AD DS) lub na platformie Azure AD DS z włączoną obsługą Azure Files klienci SMB nie będą mogli korzystać z Eksploratora plików systemu Windows w celu skonfigurowania uprawnień NTFS do katalogów i plików.
    - Aby skonfigurować uprawnienia, użyj narzędzia [icacls](/windows-server/administration/windows-commands/icacls) systemu Windows lub polecenia [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) .

