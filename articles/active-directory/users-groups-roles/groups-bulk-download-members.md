---
title: Zbiorcza lista członkostw w grupie pobierania — portal Usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Zbiorcze dodawanie użytkowników w centrum administracyjnym platformy Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533692"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Pobieranie zbiorcze członków grupy w usłudze Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobrać członków grupy w organizacji do pliku wartości oddzielonych przecinkami (CSV).

## <a name="to-bulk-download-group-membership"></a>Aby zbiorczo pobrać członkostwo w grupie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta administratora użytkownika w organizacji. Właściciele grup mogą również pobierać zbiorczo członków grup, których są właścicielami.
1. W usłudze Azure AD wybierz pozycję **Grupy** > **Wszystkie grupy**.
1. Otwórz grupę, której członkostwo chcesz pobrać, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz **pozycję Pobierz członków,** aby pobrać plik CSV z listą członków grupy.

   ![Polecenie Pobierz członków znajduje się na stronie profilu grupy](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usług pobierania zbiorczego

Każde działanie zbiorcze w celu pobrania listy członków grupy może trwać maksymalnie jedną godzinę. Dzięki temu można pobrać listę co najmniej 500 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Zbiorcze usuwanie członków grupy](groups-bulk-download-members.md)
