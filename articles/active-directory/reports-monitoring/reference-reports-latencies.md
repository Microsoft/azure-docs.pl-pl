---
title: Opóźnienia raportowania Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o tym, ile czasu zajmuje raportowanie zdarzeń w Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0498ee1c57cfa661884fe3209d4e089b54996fae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89231065"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory reporting latencies (Opóźnienia raportowania w usłudze Azure Active Directory)

Opóźnienie to czas potrzebny na wyświetlenie danych raportowania Azure Active Directory (Azure AD) w [Azure Portal](https://portal.azure.com). W tym artykule wymieniono oczekiwane opóźnienie dla różnych typów raportów. 

## <a name="activity-reports"></a>Raporty dotyczące działań

Istnieją dwa typy raportów aktywności:

- [Logowania](concept-sign-ins.md) — zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników
- [Dzienniki inspekcji](concept-audit-logs.md) — zawiera informacje o aktywności systemu dotyczące użytkowników i grup, zarządzanych aplikacji i działań w katalogu

W poniższej tabeli wymieniono informacje o opóźnieniu dla raportów aktywności. 

> [!NOTE]
> **Opóźnienie (używany 95. percentyl)** odnosi się do czasu, przez który 95% dzienników zostanie zgłoszony i **opóźnienie (99 percentyl)** odnosi się do momentu, w którym będzie raportowany 99% dzienników. 
>

| Raport | Opóźnienie (używany 95. percentyl) |Opóźnienie (99 percentyl)|
| :-- | --- | --- |
| Dzienniki inspekcji | 2 min  | 5 min  |
| Logowania | 2 min  | 5 min |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak wkrótce można zobaczyć dane o działaniach po uzyskaniu licencji Premium?

Jeśli masz już dane dotyczące działań z bezpłatną licencją, zobaczysz ją natychmiast po uaktualnieniu. Jeśli nie masz żadnych danych, po przeprowadzeniu uaktualnienia do licencji Premium dane będą wyświetlane w raportach dopiero po upływie jednego lub dwóch dni.

## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

Istnieją dwa typy raportów dotyczących zabezpieczeń:

- [Ryzykowne logowania](../identity-protection/overview-identity-protection.md) — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika. 
- [Użytkownicy oflagowani do ryzyka](../identity-protection/overview-identity-protection.md) — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, które mogło zostać naruszone. 

W poniższej tabeli wymieniono informacje o opóźnieniu dla raportów zabezpieczeń.

| Raport | Minimum | Średnia | Maksimum |
| :-- | --- | --- | --- |
| Narażeni użytkownicy          | 5 min   | 15 minut  | 2 godziny  |
| Ryzykowne logowania         | 5 min   | 15 minut  | 2 godziny  |

## <a name="risk-detections"></a>Wykrycia ryzyka

Usługa Azure AD używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych działań, które są związane z kontami użytkowników. Każda wykryte podejrzane działania są przechowywane w rekordzie nazywanym **wykryciem ryzyka**.

W poniższej tabeli wymieniono informacje o opóźnieniu dotyczące wykrywania zagrożeń.

| Raport | Minimum | Średnia | Maksimum |
| :-- | --- | --- | --- |
| Logowania z anonimowych adresów IP |5 min |15 minut |2 godziny |
| Logowania z nieznanych lokalizacji |5 min |15 minut |2 godziny |
| Użytkownicy z ujawnionymi poświadczeniami |2 godziny |4 godziny |8 godzin |
| Niemożliwa podróż do nietypowych lokalizacji |5 min |1 godzina |8 godzin  |
| Logowania z zainfekowanych urządzeń |2 godziny |4 godziny |8 godzin  |
| Logowania z adresów IP związanych z podejrzanymi działaniami |2 godziny |4 godziny |8 godzin  |


## <a name="next-steps"></a>Następne kroki

* [Przegląd raportów usługi Azure AD](overview-reports.md)
* [Programistyczny dostęp do raportów usługi Azure AD](concept-reporting-api.md)
* [Azure Active Directory wykrywania ryzyka](../identity-protection/overview-identity-protection.md)