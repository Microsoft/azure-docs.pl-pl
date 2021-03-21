---
title: Usuń Azure Active Directory Domain Services | Microsoft Docs
description: Dowiedz się, jak wyłączyć lub usunąć Azure Active Directory Domain Services zarządzaną domenę przy użyciu Azure Portal
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a5126abd6643eba7f63b2bf4ca984bb9892b2d7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619814"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Usuwanie Azure Active Directory Domain Services domeny zarządzanej przy użyciu Azure Portal

Jeśli nie potrzebujesz już domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS), możesz ją usunąć. Nie ma możliwości wyłączenia lub tymczasowego wyłączenia domeny zarządzanej AD DS platformy Azure. Usunięcie domeny zarządzanej nie powoduje usunięcia lub w inny sposób niekorzystnego wpływu na dzierżawę usługi Azure AD.

W tym artykule pokazano, jak za pomocą Azure Portal usunąć domenę zarządzaną.

> [!WARNING]
> **Usuwanie jest trwałe i nie można go cofnąć.**
> 
> Po usunięciu domeny zarządzanej są wykonywane następujące czynności:
>   * Kontrolery domeny dla domeny zarządzanej są nieobsługiwane i usuwane z sieci wirtualnej.
>   * Dane w domenie zarządzanej są trwale usuwane. Te dane obejmują niestandardowe jednostki organizacyjne, obiekty zasad grupy, niestandardowe rekordy DNS, nazwy główne usług, kont gMSA itp.
>   * Maszyny połączone z domeną zarządzaną tracą swoją relację zaufania z domeną i muszą zostać odłączone od domeny.
>       * Nie można zalogować się na tych maszynach przy użyciu firmowych poświadczeń usługi AD. Zamiast tego należy użyć poświadczeń administratora lokalnego dla komputera.

## <a name="delete-the-managed-domain"></a>Usuń domenę zarządzaną

Aby usunąć domenę zarządzaną, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**.
1. Wybierz nazwę domeny zarządzanej, na przykład *aaddscontoso.com*.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**. Aby potwierdzić usunięcie, wpisz ponownie nazwę domeny zarządzanej domeny, a następnie wybierz pozycję **Usuń**.

Usunięcie domeny zarządzanej może potrwać 15-20 minut lub dłużej.

## <a name="next-steps"></a>Następne kroki

Rozważ [udostępnienie opinii][feedback] na temat funkcji, które chcesz zobaczyć na platformie Azure AD DS.

Jeśli chcesz ponownie zacząć korzystać z usługi Azure AD DS, zobacz [Tworzenie i konfigurowanie Azure Active Directory Domain Services domeny zarządzanej][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160593%3fcategory_id%3d160593
[create-instance]: tutorial-create-instance.md