---
title: Pobieranie listy grup w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Pobieranie właściwości grupy zbiorczo w centrum administracyjnym platformy Azure w usłudze Azure Active Directory.
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
ms.openlocfilehash: 59983678c1b14d6aa87a7b500605e3abeb6a9b85
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533546"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Pobieranie zbiorcze listy grup w usłudze Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobrać listę wszystkich grup w organizacji do pliku wartości oddzielonych przecinkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Aby pobrać listę grup

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta administratora w organizacji.
1. W usłudze Azure AD wybierz pozycję **Grupy** > **pobierania grup**.
1. Na stronie **Pobieranie grup** wybierz pozycję **Start,** aby otrzymać plik CSV z listą grup.

   ![Polecenie Grupy pobierania znajduje się na stronie Wszystkie grupy](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej.**

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usług pobierania zbiorczego

Każde działanie zbiorcze w celu pobrania listy grup może trwać do jednej godziny. Dzięki temu można pobrać listę co najmniej 300 000 grup.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobieranie członków grupy](groups-bulk-download-members.md)
