---
title: Lista członkostwa w grupie pobierania zbiorczego — Portal Azure Active Directory | Microsoft Docs
description: Dodaj użytkowników zbiorczo w centrum administracyjnym platformy Azure.
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
ms.openlocfilehash: ec712d2f58c3028aa11d0b12132669648351bfb1
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376905"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Zbiorcze pobieranie elementów członkowskich grupy w Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobierać elementy członkowskie grupy w organizacji do pliku z wartościami rozdzielanymi przecinkami (CSV).

## <a name="to-bulk-download-group-membership"></a>Do pobierania zbiorczego członkostwa w grupie

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo pobierać członków grup, których są właścicielami.
1. W usłudze Azure AD wybierz kolejno pozycje **grupy**  >  **wszystkie grupy**.
1. Otwórz grupę, której członkostwo chcesz pobrać, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Pobierz członków** , aby pobrać plik CSV zawierający listę elementów członkowskich grupy.

   ![Polecenie Pobierz elementy członkowskie znajduje się na stronie profilu dla grupy](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

[![Sprawdź stan na stronie wyniki operacji zbiorczych.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze służące do pobierania listy członków grupy może być uruchamiane przez maksymalnie jedną godzinę. Dzięki temu można pobrać listę co najmniej 500 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Zbiorcze usuwanie członków grupy](groups-bulk-download-members.md)
