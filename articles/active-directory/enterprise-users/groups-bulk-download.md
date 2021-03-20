---
title: Pobierz listę grup w portalu Azure Active Directory | Microsoft Docs
description: Pobierz zbiorczo właściwości grupy w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98c61679fed04c0a696b60bb7ee53009a8a530e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547682"
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

[![Sprawdź stan na stronie wyniki operacji zbiorczych.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze do pobrania listy grup można uruchomić przez maksymalnie godzinę. Pozwala to na pobranie listy co najmniej 300 000 grup.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
