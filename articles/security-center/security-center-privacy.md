---
title: Zarządzanie danymi użytkowników w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak zarządzać danymi użytkowników w Azure Security Center. Zarządzanie danymi użytkownika obejmuje możliwość uzyskiwania dostępu do danych, ich usuwania lub eksportowania.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595654"
---
# <a name="manage-user-data-in-azure-security-center"></a>Zarządzanie danymi użytkownika w usłudze Azure Security Center
Ten artykuł zawiera informacje o sposobach zarządzania danymi użytkownika w programie Azure Security Center. Zarządzanie danymi użytkownika obejmuje możliwość uzyskiwania dostępu do danych, ich usuwania lub eksportowania.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Użytkownik Security Center przypisany do roli czytelnik, właściciel, współautor lub administrator konta może uzyskać dostęp do danych klienta w ramach tego narzędzia. Aby dowiedzieć się więcej o roli administratora konta, zobacz [wbudowane role dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md) , aby dowiedzieć się więcej na temat ról czytelnik, właściciel i współautor. Zobacz [administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i identyfikowanie danych osobowych
Użytkownik Security Center może przeglądać swoje dane osobowe za pomocą Azure Portal. Security Center przechowuje wyłącznie szczegóły dotyczące kontaktu z zabezpieczeniami, takie jak adresy e-mail i numery telefonów. Aby uzyskać więcej informacji, zobacz [Podaj szczegóły kontaktu zabezpieczeń w Azure Security Center](security-center-provide-security-contact-details.md).

W Azure Portal użytkownik może wyświetlić dozwolone konfiguracje IP przy użyciu funkcji dostępu just in Time do maszyny wirtualnej Security Center. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu just-in-Time](security-center-just-in-time.md).

W Azure Portal użytkownik może wyświetlać alerty zabezpieczeń dostarczone przez Security Center, w tym adresy IP i szczegóły osoby atakującej. Aby uzyskać więcej informacji, zobacz [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klasyfikowanie danych osobowych
Nie ma potrzeby klasyfikowania danych osobowych znalezionych w funkcji kontaktu z zabezpieczeniami Security Center. Zapisane dane to adres e-mail (lub wiele adresów e-mail) i numer telefonu. [Dane kontaktowe](security-center-provide-security-contact-details.md) są weryfikowane przez Security Center.

Nie trzeba klasyfikować adresów IP i numerów portów zapisanych przez funkcję [just in time](security-center-just-in-time.md) Security Center.

Tylko użytkownik przypisany do roli administratora może klasyfikować dane osobowe, [wyświetlając alerty](security-center-managing-and-responding-alerts.md) w Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpieczanie i kontrola dostępu do danych osobowych
Użytkownik Security Center przypisany do roli czytelnik, właściciel, współautor lub administrator konta może uzyskać dostęp do [danych kontaktowych zabezpieczeń](security-center-provide-security-contact-details.md).

Użytkownik Security Center przypisany do roli czytelnik, właściciel, współautor lub administrator konta może uzyskać dostęp do swoich zasad [just-in-Time](security-center-just-in-time.md) .

Użytkownik Security Center przypisany do roli czytelnik, właściciel, współautor lub administrator konta może wyświetlać [alerty](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizowanie danych osobowych
Security Center użytkownik przypisany do roli właściciela, współautora lub administrator konta może aktualizować [dane kontaktu zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem Azure Portal.

Użytkownik Security Center przypisany do roli właściciela, współautora lub administrator konta może zaktualizować swoje [zasady just-in-Time](security-center-just-in-time.md).

Administrator konta nie może edytować zdarzeń alertów. [Zdarzenie alertu](security-center-managing-and-responding-alerts.md) jest uznawane za dane zabezpieczeń i jest tylko do odczytu.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Security Center użytkownik przypisany do roli właściciela, współautora lub administrator konta może usunąć [dane kontaktu zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem Azure Portal.

Security Center użytkownik przypisany do roli właściciela, współautora lub administrator konta może usunąć [zasady just in Time](security-center-just-in-time.md) za pośrednictwem Azure Portal.

Użytkownik Security Center nie może usunąć zdarzeń alertów. Ze względów bezpieczeństwa [zdarzenie alertu](security-center-managing-and-responding-alerts.md) jest uznawane za dane tylko do odczytu.

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Użytkownik Security Center przypisany do roli czytelnik, właściciel, współautor lub administrator konta może eksportować [dane kontaktowe dotyczące zabezpieczeń](security-center-provide-security-contact-details.md) , wykonując następujące działania:

- Kopiowanie z Azure Portal
- Wykonywanie wywołania interfejsu API REST platformy Azure, pobieranie protokołu HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Security Center użytkownik przypisany do roli administratora konta może eksportować [zasady just in Time](security-center-just-in-time.md) zawierające adresy IP według:

- Kopiowanie z Azure Portal
- Wykonywanie wywołania interfejsu API REST platformy Azure, pobieranie protokołu HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Administrator konta może wyeksportować szczegóły alertu:

- Kopiowanie z Azure Portal
- Wykonywanie wywołania interfejsu API REST platformy Azure, pobieranie protokołu HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie alertów zabezpieczeń (Get Collection)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Ograniczanie użycia danych osobowych do profilowania lub marketingu bez zgody
Użytkownik Security Center może zrezygnować z usunięcia swoich [danych kontaktowych o zabezpieczeniach](security-center-provide-security-contact-details.md).

[Dane just in Time](security-center-just-in-time.md) są uznawane za dane nierozpoznawalne i przechowywane przez okres 30 dni.

[Dane alertu](security-center-managing-and-responding-alerts.md) są uznawane za dane zabezpieczeń i są przechowywane przez okres dwóch lat.

## <a name="auditing-and-reporting"></a>Inspekcja i raportowanie
Dzienniki inspekcji dotyczące kontaktów zabezpieczeń, just-in-Time i aktualizacji alertów są przechowywane w [dziennikach aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).