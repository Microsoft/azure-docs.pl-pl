---
title: Pobierz listę grup w portalu Azure Active Directory | Microsoft Docs
description: Pobierz zbiorczo właściwości grupy w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00fdd94e8a8cd4b8769260cd595dfee5ff898039
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732622"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Pobierz zbiorczo listę grup w Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobrać listę wszystkich grup w organizacji do pliku wartości rozdzielanych przecinkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Aby pobrać listę grup

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora w organizacji.
1. W usłudze Azure AD wybierz pozycję **grupy**  >  **Pobierz grupy**.
1. Na stronie **pobieranie grup** wybierz pozycję **Rozpocznij** , aby otrzymać plik CSV z listą Twoich grup.

   ![Polecenie Pobierz grupy znajduje się na stronie wszystkie grupy](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze do pobrania listy grup można uruchomić przez maksymalnie godzinę. Pozwala to na pobranie listy co najmniej 300 000 grup.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
