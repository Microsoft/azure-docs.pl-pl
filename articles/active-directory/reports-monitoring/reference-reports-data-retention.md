---
title: Jak długo dane są raportowane przez usługę Azure AD Store? | Microsoft Docs
description: Dowiedz się, jak długo platforma Azure przechowuje różne typy danych raportowania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5deb84cdc2dee61474c9f2bd1f93311d89f8918
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308878"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Jak długo dane są raportowane przez usługę Azure AD Store?


Ten artykuł zawiera informacje dotyczące zasad przechowywania danych dla różnych raportów działań w programie Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Kiedy usługa Azure AD zacznie zbierać dane?

| Wersja usługi Azure AD | Początek kolekcji |
| :--              | :--   |
| Usługa Azure AD — wersja Premium P1 <br /> Usługa Azure AD — wersja Premium P2 | Gdy zarejestrujesz się w celu uzyskania subskrypcji |
| Usługa Azure AD — warstwa Bezpłatna| Przy pierwszym otwarciu [bloku Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) lub użyciu [interfejsów API raportowania](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Kiedy dane działania są dostępne w Azure Portal?

- **Natychmiast** — Jeśli już pracujesz z raportami w Azure Portal.
- **W ciągu 2 godzin** — Jeśli Raportowanie nie zostało włączone w Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak wkrótce można zobaczyć dane o działaniach po uzyskaniu licencji Premium?

Jeśli masz już dane dotyczące działań z bezpłatną licencją, zobaczysz ją natychmiast po uaktualnieniu. Jeśli nie masz żadnych danych, po przeprowadzeniu uaktualnienia do licencji Premium dane będą wyświetlane w raportach dopiero po upływie jednego lub dwóch dni.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Czy po uzyskaniu licencji usługi Azure AD Premium mogę zobaczyć dane z ostatniego miesiąca?

Jeśli ostatnio przełączono się do wersji Premium (w tym wersji próbnej), dane można zobaczyć na początku do 7 dni. Po zebraniu danych można zobaczyć dane z ostatnich 30 dni.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Kiedy usługa Azure AD zacznie zbierać dane sygnału zabezpieczeń?  

W przypadku sygnałów zabezpieczeń proces zbierania jest uruchamiany, gdy użytkownik zdecyduje się korzystać z **centrum usługi Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak długo usługa Azure AD przechowuje dane?

**Raporty dotyczące działań**    

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — wersja Premium P1 | Usługa Azure AD — wersja Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        | 30 dni             | 30 dni             |
| Logowania               | 7 dni        | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

Dane dotyczące inspekcji i logowania można zachować dłużej niż domyślny okres przechowywania opisany powyżej przez kierowanie go do konta usługi Azure Storage przy użyciu Azure Monitor. Aby uzyskać więcej informacji, zobacz [Archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sygnały zabezpieczeń**

| Raport         | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — wersja Premium P1 | Usługa Azure AD — wersja Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Narażeni użytkownicy  | 7 dni        | 30 dni             | 90 dni             |
| Ryzykowne logowania | 7 dni        | 30 dni             | 90 dni             |

---