---
title: Zarządzanie dostępem gościa za pomocą przeglądów dostępu — Azure AD
description: Zarządzanie użytkownikami-gość jako członkami grupy lub przypisanymi do aplikacji przy użyciu Azure Active Directory przeglądy dostępu
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76aeb63feb006e6962efeb3687363f18acb59904
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746698"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD


Dzięki usłudze Azure Active Directory (Azure AD) można łatwo włączyć współpracę w granicach organizacyjnych za pomocą [funkcji B2B usługi Azure AD](../external-identities/what-is-b2b.md). Użytkownicy-Goście z innych dzierżawców mogą być [zapraszani przez administratorów](../external-identities/add-users-administrator.md) lub [innych użytkowników](../external-identities/what-is-b2b.md). Ta możliwość dotyczy również tożsamości społecznościowych, takich jak konta Microsoft.

Można również łatwo upewnić się, że użytkownicy-Goście mają odpowiedni dostęp. Możesz polecić Gościom lub producentowi decyzji wziąć udział w przeglądzie dostępu i ponownie zatwierdzić (lub zaświadczyć) dostęp gościa. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu przeglądu dostępu można wprowadzić zmiany i usunąć dostęp dla Gości, którzy już nie potrzebują.

> [!NOTE]
> Ten dokument koncentruje się na sprawdzaniu dostępu użytkowników-Gości. Jeśli chcesz przejrzeć dostęp wszystkich użytkowników, a nie tylko do Gości, zobacz [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu](manage-user-access-with-access-reviews.md). Jeśli chcesz przejrzeć członkostwo użytkowników w rolach administracyjnych, takich jak Administrator globalny, zobacz [Rozpocznij przegląd dostępu w Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — wersja Premium P2

Aby uzyskać więcej informacji, [wymagania dotyczące licencji](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Tworzenie i przeprowadzanie przeglądu dostępu dla Gości

Najpierw należy przypisać jedną z następujących ról:
- Administrator globalny
- Administrator użytkowników
- Przeglądania M365 lub właściciel grupy zabezpieczeń usługi AAD grupy do zweryfikowania

Następnie przejdź do [strony ładu zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) , aby upewnić się, że przeglądy dostępu są gotowe dla Twojej organizacji.

Usługa Azure AD umożliwia przeglądanie wielu scenariuszy użytkowników-Gości.

Możesz przejrzeć:

 - Grupa w usłudze Azure AD, która ma co najmniej jednego gościa jako członków.
 - Aplikacja połączona z usługą Azure AD, do której jest przypisany co najmniej jeden użytkownik-Gość. 

Następnie można zdecydować, czy poproszony każdy Gość ma sprawdzić własny dostęp, czy też polecić jednemu lub większej liczbie użytkowników przeglądanie dostępu do poszczególnych Gości.

 Te scenariusze zostały omówione w poniższych sekcjach.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Poproszenie gościów o przeglądanie własnych członkostw w grupie

Za pomocą przeglądów dostępu można upewnić się, że użytkownicy, którzy zostali zaproszeni i dodawani do grupy, nadal potrzebują dostępu. Można łatwo zadawać Gościom możliwość przejrzenia ich własnego członkostwa w tej grupie.

1. Aby utworzyć przegląd dostępu dla grupy, wybierz pozycję przegląd, aby uwzględnić tylko członków-Gości Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md).

2. Poproszenie każdego gościa o przeglądanie własnych członkostw. Domyślnie każdy gość, który zaakceptował zaproszenie, otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do przeglądu dostępu. Usługa Azure AD zawiera instrukcje dla Gości, jak [przeglądać dostęp do grup lub aplikacji](perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu do grup lub aplikacji](complete-access-review.md).

4. Oprócz tych użytkowników, którzy odmówili konieczności ciągłego dostępu, można również usunąć użytkowników, którzy nie odpowiadają. Nieodpowiadający użytkownicy nie mogą już otrzymywać wiadomości e-mail.

5. Jeśli grupa nie jest używana do zarządzania dostępem, można również usunąć użytkowników, którzy nie zostali wybrani do wzięcia udziału w przeglądzie, ponieważ nie zaakceptowali zaproszenia. Nieakceptowanie może wskazywać, że adres e-mail zaproszonego użytkownika ma literówki. Jeśli grupa jest używana jako lista dystrybucyjna, prawdopodobnie niektórzy użytkownicy-Goście nie zostali wybrani do uczestnictwa, ponieważ są obiektami kontaktowymi.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Poproszenie sponsora o przejrzenie członkostwa gościa w grupie

Możesz polecić sponsora, na przykład właściciela grupy, aby przejrzeć potrzeby dalszego członkostwa w grupie.

1. Aby utworzyć przegląd dostępu dla grupy, wybierz pozycję przegląd, aby uwzględnić tylko członków-Gości. Następnie określ co najmniej jednego recenzenta. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie każdy z nich otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przejrzenie dostępu do grup lub aplikacji](perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu do grup lub aplikacji](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Poproszenie gościa o sprawdzenie własnego dostępu do aplikacji

Za pomocą przeglądów dostępu można upewnić się, że użytkownicy, którzy zostali zaproszeni do określonej aplikacji, nadal potrzebują dostępu. Można z łatwością podawać Gościom możliwość przejrzenia ich potrzeb w celu uzyskania dostępu.

1. Aby utworzyć przegląd dostępu dla aplikacji, wybierz pozycję przegląd, aby uwzględnić tylko gościa, a użytkownicy przeglądali własny dostęp. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md).

2. Zwróć się do każdego gościa, aby przeanalizować własny dostęp do aplikacji. Domyślnie każdy gość, który zaakceptował zaproszenie, otrzymuje wiadomość e-mail z usługi Azure AD. Ta wiadomość e-mail zawiera link do przeglądu dostępu w panelu dostępu w organizacji. Usługa Azure AD zawiera instrukcje dla Gości, jak [przeglądać dostęp do grup lub aplikacji](perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu do grup lub aplikacji](complete-access-review.md).

4. Oprócz użytkowników, którzy odmówili konieczności ciągłego dostępu, można również usunąć użytkowników-Gości, którzy nie odpowiadają. Nieodpowiadający użytkownicy nie mogą już otrzymywać wiadomości e-mail. Można również usunąć użytkowników-Gości, którzy nie zostali wybrani do uczestnictwa, szczególnie jeśli nie zostały ostatnio zaproszeni. Ci użytkownicy nie zaakceptowali zaproszenia i nie mają dostępu do aplikacji. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Poproszenie sponsora o przejrzenie dostępu gościa do aplikacji

Możesz polecić sponsora, na przykład właściciela aplikacji, aby zapoznać się z potrzebami gościa w celu uzyskania ciągłego dostępu do aplikacji.

1. Aby utworzyć przegląd dostępu dla aplikacji, wybierz pozycję przegląd, aby uwzględnić tylko Gości. Następnie określ co najmniej jednego użytkownika jako recenzentów. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie każdy z nich otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przejrzenie dostępu do grup lub aplikacji](perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu do grup lub aplikacji](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Poproszenie gościa o przejrzenie potrzeb dostępu, ogólnie rzecz biorąc

W niektórych organizacjach Goście mogą nie wiedzieć o członkostwie w grupach.

> [!NOTE]
> Wcześniejsze wersje Azure Portal nie umożliwiały dostępu administracyjnego użytkownikom typu gość. W niektórych przypadkach administrator w katalogu mógł zmienić wartość UserType gościa przy użyciu programu PowerShell. Jeśli ta zmiana nastąpiła wcześniej w katalogu, poprzednia kwerenda może nie obejmować wszystkich użytkowników-Gości, którzy w przeszłości mieli prawa dostępu administracyjnego. W takim przypadku należy zmienić użytkownika-gościa lub ręcznie dołączyć gościa do członkostwa w grupie.

1. Utwórz grupę zabezpieczeń w usłudze Azure AD z Gośćmi jako członkami, Jeśli odpowiednia grupa jeszcze nie istnieje. Można na przykład utworzyć grupę z ręcznie utrzymywanym członkostwem Gości. Można też utworzyć grupę dynamiczną o nazwie "Goście of contoso" dla użytkowników w dzierżawie contoso, którzy mają wartość atrybutu UserType dla gościa.  W celu zapewnienia wydajności upewnij się, że grupa ma głównie Gości — nie wybieraj grupy, która ma użytkowników będących członkami, ponieważ nie trzeba recenzować użytkowników należących do członków.  Należy również pamiętać, że użytkownik-Gość, który jest członkiem grupy, może zobaczyć innych członków tej grupy.

2. Aby utworzyć przegląd dostępu dla tej grupy, wybierz recenzentów jako samych członków. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md).

3. Poproszenie każdego gościa o przeglądanie własnych członkostw. Domyślnie każdy gość, który zaakceptował zaproszenie, otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do przeglądu dostępu w panelu dostępu w organizacji. Usługa Azure AD zawiera instrukcje dla Gości, jak [przeglądać dostęp do grup lub aplikacji](perform-access-review.md).  Osoby te, które nie zaakceptowali zaproszenia, będą wyświetlane w wynikach przeglądu jako "nie powiadomiono".

4. Po udzieleniu przez recenzentów danych wejściowych Zatrzymaj przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu do grup lub aplikacji](complete-access-review.md).

5. Usuń dostęp gościa dla Gości, którzy mieli odmowę, nie dokończył przeglądu lub nie zaakceptował wcześniej zaproszenia. Jeśli niektórzy Goście są kontaktami, którzy zostali wybrani do wzięcia udziału w recenzji lub nie zaakceptowali wcześniej zaproszenia, możesz wyłączyć swoje konta za pomocą Azure Portal lub programu PowerShell. Jeśli gość nie potrzebuje już dostępu i nie jest osobą kontaktową, możesz usunąć swój obiekt użytkownika z katalogu za pomocą Azure Portal lub PowerShell, aby usunąć obiekt użytkownika-gościa.

## <a name="next-steps"></a>Następne kroki

[Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)