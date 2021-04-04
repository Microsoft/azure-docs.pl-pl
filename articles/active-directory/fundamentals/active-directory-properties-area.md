---
title: Dodaj informacje o ochronie prywatności organizacji — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące sposobu dodawania informacji o ochronie prywatności organizacji do obszaru właściwości Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996762"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Dodaj informacje o ochronie prywatności w organizacji za pomocą Azure Active Directory
W tym artykule wyjaśniono, jak Administrator dzierżawy może dodać do dzierżawy usługi Azure Active Directory (Azure AD) informacje związane z prywatnością, korzystając z Azure Portal.

Zdecydowanie zalecamy dodanie zarówno globalnego kontaktu z prywatnością, jak i zasad zachowania poufności informacji organizacji, dzięki czemu pracownicy wewnętrzni i zewnętrzni Goście mogą przeglądać swoje zasady. Ze względu na to, że zasady zachowania poufności informacji są tworzone i dostosowane do poszczególnych firm, zdecydowanie zalecamy skontaktowanie się z prawnikem w celu uzyskania pomocy.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Dodawanie informacji o prywatności w usłudze Azure AD
Informacje o ochronie prywatności organizacji są dodawane w obszarze **Właściwości** usługi Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Aby uzyskać dostęp do obszaru właściwości i dodać informacje o prywatności

1. Zaloguj się do Azure Portal jako Administrator dzierżawy.

2. Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Właściwości**.

    Zostanie wyświetlony obszar **Właściwości** .

    ![Obszar właściwości usługi Azure AD podświetlanie obszaru informacji o prywatności](media/active-directory-properties-area/properties-area.png)

3. Dodaj informacje o prywatności dla swoich pracowników:

    - **Kontakt techniczny.** Wpisz adres e-mail osoby, aby skontaktować się z pomocą techniczną w organizacji.
    
    - **Globalny kontakt z prywatnością.** Wpisz adres e-mail osoby, aby skontaktować się z informacjami na temat prywatności danych osobowych. Ta osoba jest również osobą, która kontaktuje się z firmą Microsoft w przypadku naruszenia danych. Jeśli w tym miejscu nie ma osoby, firma Microsoft kontaktuje się z administratorami globalnymi.

    - **Adres URL zasad zachowania poufności informacji.** Wpisz link do dokumentu organizacji, który opisuje, jak Twoja organizacja obsługuje zarówno prywatność danych wewnętrznych, jak i zewnętrznych.

        >[!Important]
        >Jeśli nie dołączysz własnych zasad zachowania poufności informacji lub kontaktu z prywatnością, zobaczysz tekst w polu **uprawnienia przeglądu** , które informuje, że **< _Nazwa organizacji_> nie podano linków do ich terminów do przejrzenia**. Na przykład użytkownik-Gość zobaczy ten komunikat, gdy otrzyma zaproszenie do uzyskania dostępu do organizacji za pomocą współpracy B2B.

        ![Pole uprawnień przeglądu współpracy B2B z komunikatem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki
- [Realizacja zaproszenia do współpracy B2B w usłudze Azure Active Directory](../external-identities/redemption-experience.md)
- [Dodawanie lub zmiana informacji o profilu dla użytkownika w Azure Active Directory](active-directory-users-profile-azure-portal.md)