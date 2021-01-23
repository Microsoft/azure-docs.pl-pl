---
title: Opis pojęć dotyczących ról Azure Active Directory
description: Dowiedz się, jak zrozumieć Azure Active Directory ról wbudowanych i niestandardowych z zakresem zasobów w Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b46262e34f57b3ff9aeb6bd4ea671ec611b99df
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740350"
---
# <a name="understand-roles-in-azure-active-directory"></a>Informacje o rolach w Azure Active Directory

Istnieją role wbudowane w 60 Azure Active Directory (Azure AD), które są rolami ze stałym zestawem uprawnień roli. Aby uzupełnić wbudowane role, usługa Azure AD obsługuje również role niestandardowe. Użyj ról niestandardowych, aby wybrać odpowiednie uprawnienia do roli. Można na przykład utworzyć jeden do zarządzania konkretnymi zasobami usługi Azure AD, takimi jak aplikacje lub jednostki usługi.

W tym artykule wyjaśniono, czym są role usługi Azure AD i jak można ich używać.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Jak role usługi Azure AD różnią się od innych ról Microsoft 365

Istnieje wiele różnych usług w Microsoft 365, takich jak Azure AD i Intune. Niektóre z tych usług mają własne systemy kontroli dostępu oparte na rolach; opracowany

- Azure AD
- Exchange
- Intune
- Security Center
- Centrum zgodności
- Microsoft Cloud App Security
- Handel

Inne usługi, takie jak zespoły, SharePoint i Managed Desktop, nie mają oddzielnych systemów kontroli dostępu opartej na rolach. Korzystają one z ról usługi Azure AD na potrzeby dostępu administracyjnego. Platforma Azure ma własny system kontroli dostępu oparty na rolach dla zasobów platformy Azure, takich jak maszyny wirtualne, i ten system nie jest taki sam jak w przypadku ról usługi Azure AD.

Gdy mówimy oddzielny system kontroli dostępu oparty na rolach. oznacza to, że istnieje inny magazyn danych, w którym są przechowywane definicje ról i przypisania ról. Podobnie istnieją różne punkty decyzyjne dotyczące zasad, w których następuje sprawdzanie dostępu. Aby uzyskać więcej informacji, zobacz [role usługi Microsoft 365 Services w usłudze Azure AD](m365-workload-docs.md) oraz [role administratora klasycznej subskrypcji, role platformy Azure i role usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Dlaczego niektóre role usługi Azure AD są przeznaczone dla innych usług

Microsoft 365 ma wiele systemów kontroli dostępu opartych na rolach, które zostały opracowane niezależnie w czasie, z których każdy korzysta z własnego portalu usług. Aby zapewnić sobie możliwość zarządzania tożsamościami w Microsoft 365 z poziomu portalu usługi Azure AD, dodaliśmy pewne wbudowane role specyficzne dla usługi, z których każdy ma dostęp administracyjny do usługi Microsoft 365. Przykładem tego dodania jest rola Administrator programu Exchange w usłudze Azure AD. Ta rola jest równoważna z [grupą roli zarządzania organizacją](/exchange/organization-management-exchange-2013-help) w systemie kontroli dostępu opartej na rolach programu Exchange i może zarządzać wszystkimi aspektami programu Exchange. Podobnie dodaliśmy rolę administratora usługi Intune, administratora zespołów, administratora programu SharePoint i tak dalej. Role specyficzne dla usług to jedna kategoria wbudowanych ról usługi Azure AD w poniższej sekcji.

## <a name="categories-of-azure-ad-roles"></a>Kategorie ról usługi Azure AD

Wbudowane role usługi Azure AD różnią się w zależności od tego, gdzie mogą być używane, które należą do następujących trzech szerokich kategorii.

- **Role specyficzne dla usługi Azure AD**: te role przyznają uprawnienia do zarządzania zasobami tylko w usłudze Azure AD. Na przykład administrator użytkowników, administrator aplikacji, Administrator grup wszyscy przyznaje uprawnienia do zarządzania zasobami, które znajdują się na żywo w usłudze Azure AD.
- **Role specyficzne dla usługi**: dla głównych usług Microsoft 365 (spoza usługi Azure AD) skompilowano role specyficzne dla usługi, które przyznają uprawnienia do zarządzania wszystkimi funkcjami w ramach usługi.  Na przykład role Administrator programu Exchange, administrator usługi Intune, administrator programu SharePoint i zespoły mogą zarządzać funkcjami przy użyciu odpowiednich usług. Administrator programu Exchange może zarządzać skrzynkami pocztowymi, administrator usługi Intune może zarządzać zasadami dotyczącymi urządzeń, administrator programu SharePoint może zarządzać kolekcjami witryn, a administrator zespołów może zarządzać jakością wywołań i tak dalej.
- **Role międzyusługowe**: istnieją pewne role obejmujące usługi. Mamy dwie role globalne — globalnego administratora i globalnego czytnika. Wszystkie usługi Microsoft 365 mają te dwie role. Istnieją także pewne role związane z zabezpieczeniami, takie jak administrator zabezpieczeń i czytelnik zabezpieczeń, które zapewniają dostęp do wielu usług zabezpieczeń w ramach Microsoft 365. Na przykład przy użyciu ról administratora zabezpieczeń w usłudze Azure AD można zarządzać Microsoft 365 Security Center, zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender i Microsoft Cloud App Security. Analogicznie, w roli Administrator zgodności można zarządzać ustawieniami dotyczącymi zgodności w Microsoft 365 Centrum zgodności, Exchange i tak dalej.

![Trzy kategorie wbudowanych ról usługi Azure AD](./media/concept-understand-roles/role-overlap-diagram.png)

Poniższa tabela jest oferowana jako pomoc w zrozumieniu tych kategorii ról. Kategorie są nazywane arbitralnie i nie są przeznaczone do oznaczania innych możliwości wykraczających poza [udokumentowane uprawnienia roli](permissions-reference.md).

Kategoria | Rola
---- | ----
Role specyficzne dla usługi Azure AD | Administrator aplikacji<br>Deweloper aplikacji<br>Administrator uwierzytelniania<br>Administrator zestawu kluczy B2C IEF<br>Administrator zasad B2C IEF<br>Administrator aplikacji w chmurze<br>Administrator urządzenia w chmurze<br>Administrator dostępu warunkowego<br>Administratorzy urządzeń<br>Czytelnicy katalogów<br>Konta synchronizacji katalogów<br>Autorzy katalogów<br>Administrator przepływu użytkownika z IDENTYFIKATORem zewnętrznym<br>Administrator atrybutów przepływu użytkownika zewnętrznego ID<br>Administrator zewnętrznego dostawcy tożsamości<br>Administrator grup<br>Zapraszający gościa<br>Administrator pomocy technicznej<br>Administrator tożsamości hybrydowej<br>Administrator licencji<br>Obsługa pomoc partnera<br>Obsługa SVR partnera<br>Administrator haseł<br>Administrator uprzywilejowanego uwierzytelniania<br>Administrator ról uprzywilejowanych<br>Czytelnik raportów<br>Administrator konta użytkownika
Role międzyusługowe | Administrator firmy<br>Administrator zgodności<br>Administrator danych zgodności<br>Czytelnik globalny<br>Administrator zabezpieczeń<br>Operator zabezpieczeń<br>Czytelnik zabezpieczeń<br>Administrator pomocy technicznej usługi
Role specyficzne dla usługi | Administrator usługi Azure DevOps<br>Azure Information Protection administrator<br>Administrator rozliczeń<br>Administrator usługi CRM<br>Osoba zatwierdzająca dostęp do skrytki klienta<br>Administrator usługi Desktop Analytics<br>Administrator usługi Exchange<br>Administrator usługi Insights<br>Lider biznesowy usługi Insights<br>Administrator usługi Intune<br>Usługi kaizala administrator<br>Administrator usługi Lync<br>Czytnik prywatności centrum wiadomości<br>Czytelnik centrum wiadomości<br>Nowoczesny użytkownik handlowy<br>Administrator sieci<br>Administrator aplikacji pakietu Office<br>Administrator usługi Power BI<br>Administrator platformy w elektrowni<br>Administrator drukarki<br>Technika drukarki<br>Administrator wyszukiwania<br>Edytor wyszukiwania<br>Administrator usługi SharePoint<br>Administratorzy zespołu ds. komunikacji<br>Inżynierowie pomocy technicznej dla zespołów<br>Zespoły ds. pomocy technicznej<br>Administrator urządzeń zespołów<br>Administrator usługi Teams

## <a name="next-steps"></a>Następne kroki

- [Omówienie kontroli dostępu opartej na rolach w usłudze Azure AD](custom-overview.md)
- Tworzenie przypisań ról przy użyciu [Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](custom-create.md)
- [Wyświetlanie przypisań roli](custom-view-assignments.md)
