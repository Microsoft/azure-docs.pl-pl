---
title: Usuń Azure Active Directory Domain Services | Microsoft Docs
description: Dowiedz się, jak wyłączyć lub usunąć Azure Active Directory Domain Services zarządzaną domenę przy użyciu Azure Portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655460"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Usuwanie Azure Active Directory Domain Services domeny zarządzanej przy użyciu Azure Portal

Jeśli nie potrzebujesz już domeny zarządzanej, możesz usunąć wystąpienie Azure Active Directory Domain Services (AD DS platformy Azure). Nie ma możliwości wyłączenia lub tymczasowego wyłączenia domeny zarządzanej AD DS platformy Azure. Usunięcie domeny zarządzanej AD DS platformy Azure nie powoduje usunięcia lub w inny sposób niekorzystnego wpływu na dzierżawę usługi Azure AD. W tym artykule pokazano, jak usunąć domenę zarządzaną AD DS platformy Azure za pomocą Azure Portal.

> [!WARNING]
> **Usuwanie jest trwałe i nie można go cofnąć.**
> Po usunięciu domeny zarządzanej usługi Azure AD DS są wykonywane następujące czynności:
>   * Kontrolery domeny dla domeny zarządzanej są nieobsługiwane i usuwane z sieci wirtualnej.
>   * Dane w domenie zarządzanej są trwale usuwane. Te dane obejmują niestandardowe jednostki organizacyjne, obiekty zasad grupy, niestandardowe rekordy DNS, nazwy główne usług, kont gMSA itp.
>   * Maszyny połączone z domeną zarządzaną tracą swoją relację zaufania z domeną i muszą zostać odłączone od domeny.
>       * Nie można zalogować się na tych maszynach przy użyciu firmowych poświadczeń usługi AD. Zamiast tego należy użyć poświadczeń administratora lokalnego dla komputera.

## <a name="delete-the-managed-domain"></a>Usuń domenę zarządzaną

Aby usunąć domenę zarządzaną AD DS platformy Azure, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**.
1. Wybierz nazwę domeny zarządzanej AD DS platformy Azure, na przykład *aaddscontoso.com*.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**. Aby potwierdzić usunięcie, wpisz ponownie nazwę domeny zarządzanej domeny, a następnie wybierz pozycję **Usuń**.

Usunięcie domeny zarządzanej AD DS platformy Azure może potrwać od 15-20 minut lub dłużej.

## <a name="next-steps"></a>Następne kroki

Rozważ [udostępnienie opinii][feedback] na temat funkcji, które chcesz zobaczyć na platformie Azure AD DS.

Jeśli chcesz ponownie zacząć korzystać z usługi Azure AD DS, zobacz [Tworzenie i Konfigurowanie wystąpienia Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
