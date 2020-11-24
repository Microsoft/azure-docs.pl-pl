---
title: Co to są przeglądy dostępu? — Azure Active Directory | Microsoft Docs
description: Za pomocą Azure Active Directory przeglądy dostępu można kontrolować członkostwo w grupach i dostęp do aplikacji, aby zaspokoić inicjatywy dotyczące zarządzania, ryzyka i zgodności w organizacji.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperfq1
ms.openlocfilehash: 423a879889402d5d3df70a7fadae6451c8418238
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95743161"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co to są przeglądy dostępu w usłudze Azure AD?

Przeglądy dostępu Azure Active Directory (Azure AD) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.

Oto film wideo, który zawiera krótkie omówienie przeglądów dostępu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Dlaczego przeglądy dostępu są ważne?

Usługa Azure AD umożliwia współpracę z użytkownikami spoza organizacji i użytkownikami zewnętrznymi. Użytkownicy mogą dołączać do grup, zapraszać Gości, łączyć się z aplikacjami w chmurze i zdalnie korzystać z urządzeń służbowych lub osobistych. Wygoda korzystania z samoobsługowego systemu doprowadziła do zapewnienia lepszych możliwości zarządzania dostępem.

- Jak są dołączeni nowi pracownicy, w jaki sposób upewnić się, że mają oni dostęp do nich, czego potrzebują?
- Jak ludzie przemieszczają zespoły lub opuszczają firmę, jak upewnić się, że ich stary dostęp jest usuwany?
- Nadmierne prawa dostępu mogą prowadzić do złamania zabezpieczeń.
- Nadmierne prawo dostępu może również prowadzić do wyników inspekcji, ponieważ wskazują one brak kontroli dostępu.
- Musisz aktywnie współpracować z właścicielami zasobów, aby zapewnić ich regularne sprawdzenie, kto ma dostęp do swoich zasobów.

## <a name="when-should-you-use-access-reviews"></a>Kiedy należy używać przeglądów dostępu?

- **Zbyt wielu użytkowników w uprzywilejowanych rolach:** Dobrym pomysłem jest sprawdzenie, ilu użytkowników ma dostęp administracyjny, ilu z nich są administratorzy globalni, a jeśli istnieją wszyscy zaproszeni goście lub partnerzy, którzy nie zostali usunięci po przypisaniu do wykonywania zadań administracyjnych. Możesz zatwierdzić użytkowników przypisania roli w [rolach usługi Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , takich jak Administratorzy globalni lub [role zasobów platformy Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , takie jak administrator dostępu użytkowników w środowisku [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Gdy Automatyzacja nie jest możliwa:** Możesz tworzyć reguły dynamicznego członkostwa w grupach zabezpieczeń lub grupach Microsoft 365, ale co zrobić, jeśli dane HR nie są w usłudze Azure AD lub jeśli użytkownicy nadal potrzebują dostępu po opuszczeniu grupy w celu nauczenia ich zastąpienia? Następnie można utworzyć przegląd tej grupy, aby upewnić się, kto nadal musi mieć dostęp.
- **Gdy grupa jest używana w nowym przeznaczeniu:** Jeśli masz grupę, która ma być synchronizowana z usługą Azure AD, lub jeśli planujesz włączenie usługi Salesforce dla wszystkich użytkowników w grupie zespołu sprzedaży, warto polecić właścicielowi grupy przeglądanie członkostwa w grupie przed grupą używaną w innej treści ryzyka.
- **Dostęp do danych o kluczowym znaczeniu dla firmy:** w przypadku niektórych zasobów może być konieczne zaproszenie osób poza nim, aby regularnie się wylogować i uzasadnić, dlaczego potrzebują dostępu do celów inspekcji.
- **Aby zachować listę wyjątków zasad:** W idealnym świecie wszyscy użytkownicy będą korzystać z zasad dostępu w celu zabezpieczenia dostępu do zasobów organizacji. Czasami jednak istnieją przypadki biznesowe, które wymagają wprowadzenia wyjątków. Jako administrator IT możesz zarządzać tym zadaniem, unikać nadzoru wyjątków zasad i zapewniać audytorom dowód, że te wyjątki są regularnie przeglądane.
- **Poproszenie właścicieli grupy o potwierdzenie, że nadal potrzebują Gości w swoich grupach:** Dostęp pracownika może być zautomatyzowany przy użyciu niektórych lokalnych tożsamości i zarządzania dostępem (IAM), ale nie zaproszonych Gości. Jeśli grupa daje gościom dostęp do zawartości poufnej, to jest odpowiedzialność właściciela grupy o potwierdzenie, że Goście nadal mają uzasadnione potrzeby biznesowe.
- **Okresowe przeglądy powtarzają się:** Można skonfigurować cykliczne przeglądy dostępu użytkowników przy użyciu skonfigurowanych częstotliwości, takich jak co tydzień, co miesiąc, co kwartał lub co rok, a Recenzenci będą powiadamiani na początku każdego przeglądu. Recenzenci mogą zatwierdzać lub odrzucać dostęp za pomocą przyjaznego interfejsu oraz z pomocą inteligentnych zaleceń.

>[!NOTE]
>Jeśli jesteś gotowy do wypróbowania przeglądów dostępu, zapoznaj się z [tematem tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)

## <a name="where-do-you-create-reviews"></a>Gdzie można tworzyć przeglądy?

W zależności od tego, co chcesz przejrzeć, możesz utworzyć przegląd dostępu w przeglądach dostępu usługi Azure AD, aplikacjach Azure AD Enterprise (w wersji zapoznawczej) lub w usłudze Azure AD PIM.

| Prawa dostępu użytkowników | Recenzenci mogą być | Przegląd utworzony w | Środowisko recenzenta |
| --- | --- | --- | --- |
| Członkowie grupy zabezpieczeń</br>Członkowie grupy pakietu Office | Określeni recenzenci</br>Właściciele grupy</br>Samoobsługowe przeglądanie | Przeglądy dostępu w usłudze Azure AD</br>Grupy usługi Azure AD | Panel dostępu |
| Przypisane do połączonej aplikacji | Określeni recenzenci</br>Samoobsługowe przeglądanie | Przeglądy dostępu w usłudze Azure AD</br>Aplikacje dla przedsiębiorstw usługi Azure AD (w wersji zapoznawczej) | Panel dostępu |
| Rola usługi Azure AD | Określeni recenzenci</br>Samoobsługowe przeglądanie | [Usługa Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Rola zasobów platformy Azure | Określeni recenzenci</br>Samoobsługowe przeglądanie | [Usługa Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Ile licencji musi mieć?

Katalog wymaga co najmniej tylu licencji na Azure AD — wersja Premium P2 jako liczby pracowników, którzy będą wykonywać następujące zadania:

-   Użytkownicy będący członkami, którzy są przypisani jako recenzenci
-   Użytkownicy będący członkami, którzy wykonują własne przeglądy
-   Użytkownicy będący członkami jako właściciele grup, którzy wykonują przegląd dostępu
-   Użytkownicy będący członkami jako właściciele aplikacji, którzy wykonują przegląd dostępu

Dla użytkowników-Gości wymagania dotyczące licencjonowania będą zależeć od używanego modelu licencjonowania. Jednak poniższe działania użytkowników-Gości są uważane za Azure AD — wersja Premium użycia P2:

-   Użytkownicy-Goście przypisani jako recenzenci
-   Użytkownicy-Goście, którzy wykonują własne przeglądy
-   Użytkownicy-Goście jako właściciele grup, którzy wykonują przegląd dostępu
-   Użytkownicy-Goście jako właściciele aplikacji, którzy wykonują przegląd dostępu


Licencje na Azure AD — wersja Premium P2 **nie** są wymagane dla użytkowników z rolami administratora globalnego lub administratora użytkowników, którzy konfigurują przeglądy dostępu, konfigurują ustawienia lub stosują decyzje z przeglądu.

Dostęp użytkownika gościa usługi Azure AD jest oparty na modelu rozliczania miesięcznych użytkowników (MAU), który zastępuje model rozliczeń 1:5. Aby uzyskać więcej informacji, zobacz [Cennik zewnętrzny usługi Azure AD](../external-identities/external-identities-pricing.md).

Aby uzyskać więcej informacji na temat licencji, zobacz [przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Przykładowe scenariusze licencji

Poniżej przedstawiono kilka przykładowych scenariuszy licencjonowania, które mogą pomóc w ustaleniu liczby posiadanych licencji.

| Scenariusz | Obliczenia | Liczba licencji |
| --- | --- | --- |
| Administrator tworzy przegląd dostępu grupy A o 75 użytkowników i 1 właściciela grupy i przypisuje właściciela grupy jako recenzenta. | 1 Licencja dla właściciela grupy jako recenzent | 1 |
| Administrator tworzy przegląd dostępu grupy B z 500 użytkowników i 3 grup właścicieli, a także przypisuje trzech właścicieli grup jako recenzentów. | 3 licencje dla każdego właściciela grupy jako recenzentów | 3 |
| Administrator tworzy przegląd dostępu grupy B z 500 użytkowników. Sprawia, że jest to samodzielna przegląd. | 500 licencji dla każdego użytkownika jako samoprzeglądający | 500 |
| Administrator tworzy przegląd dostępu grupy C z 50 użytkowników-członków i 25 użytkowników-Gości. Sprawia, że jest to samodzielna przegląd. | 50 licencji dla każdego użytkownika jako samoprzeglądający. * | 50 |
| Administrator tworzy przegląd dostępu grupy D z 6 użytkownikami-członkami i 108 użytkowników-Gości. Sprawia, że jest to samodzielna przegląd. | 6 licencji dla każdego użytkownika jako samoprzeglądający. Użytkownicy-Goście są rozliczani miesięcznie (MAU). Żadne dodatkowe licencje nie są wymagane. *  | - |

\* Cennik zewnętrzny usługi Azure AD (użytkownik-Gość) bazuje na comiesięcznych aktywnych użytkownikach (MAU), czyli liczbie unikatowych użytkowników z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego. Ten model zastępuje model rozliczeń dotyczący współczynnika 1:5, który zezwala maksymalnie pięciu użytkownikom-Gościom na każdą licencję Azure AD — wersja Premium w dzierżawie. Gdy dzierżawa jest połączona z subskrypcją i używasz funkcji tożsamości zewnętrznych do współpracy z użytkownikami-Gośćmi, będzie ona automatycznie rozliczana przy użyciu modelu rozliczania opartego na MAU. Aby uzyskać więcej informacji, zobacz model rozliczeń dla tożsamości zewnętrznych usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
