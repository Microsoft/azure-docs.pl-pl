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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995494"
---
Wielokanałowość protokołu SMB dla udziałów plików platformy Azure ma obecnie następujące ograniczenia:
- Może być używany tylko z lokalnymi nadmiarowymi kontami FileStorage.
- Obsługiwane tylko w przypadku klientów z systemem Windows. 
- Maksymalna liczba kanałów wynosi cztery.
- Funkcja SMB Direct nie jest obsługiwana.
- W przypadku kont magazynu z [uwierzytelnianiem na podstawie](../articles/storage/files/storage-files-active-directory-overview.md) Active Directory Domain Services lokalnego (AD DS) lub na platformie Azure AD DS z włączoną obsługą Azure Files klienci SMB nie będą mogli korzystać z Eksploratora plików systemu Windows w celu skonfigurowania uprawnień NTFS do katalogów i plików.
    - Aby skonfigurować uprawnienia, użyj narzędzia [icacls](/windows-server/administration/windows-commands/icacls) systemu Windows lub polecenia [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) .

