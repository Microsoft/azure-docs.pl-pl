---
title: Przewodnik Szybki Start dotyczący zasad wygasania grup — Azure AD | Microsoft Docs
description: Wygasanie grup usługi Office 365 — Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 616910eda33b3ddc49fa6233ccb3989c5e4214e2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582849"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Szybki start: konfigurowanie wygasania grup usługi Office 365 w usłudze Azure Active Directory

W tym przewodniku Szybki start ustawisz zasady wygasania grup usługi Office 365. Umożliwienie użytkownikom tworzenia własnych grup może prowadzić do powstania wielu nieużywanych grup. Jednym ze sposobów radzenia sobie z nieużywanymi grupami jest skonfigurowanie wygasania tych grup, co pozwala uniknąć konieczności ich ręcznego usuwania.

Zasady wygasania są proste:

- Grupy z działaniami użytkownika są automatycznie odnawiane jako zbliżające się czas wygaśnięcia
- Właściciele grupy są powiadamiani o konieczności odnowienia wygasającej grupy
- Grupa, która nie zostanie odnowiona, zostanie usunięta
- Usunięta grupa usługi Office 365 może zostać przywrócona w ciągu 30 dni przez właściciela grupy lub administratora usługi Azure AD

> [!NOTE]
> Grupy teraz używają usługi Azure AD Intelligence do automatycznego odnawiania w zależności od tego, czy zostały one ostatnio użyte. Ta decyzja o odnowieniu jest oparta na aktywności użytkownika w grupach w ramach usług Office 365, takich jak Outlook, SharePoint, Teams, Yammer i inne.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisite"></a>Wymagania wstępne

 Rola najniższych uprawnień wymagana do skonfigurowania wygaśnięcia grupy jest administratorem użytkownika w organizacji.

## <a name="turn-on-user-creation-for-groups"></a>Włączanie tworzenia grup przez użytkowników

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników.

2. Wybierz pozycję **Grupy**, a następnie wybierz pozycję **Ogólne**.
  
   ![Strona ustawień grupy samoobsługowej](./media/groups-quickstart-expiration/self-service-settings.png)

3. Zmień wartość ustawienia **Użytkownicy mogą tworzyć grupy usługi Office 365** na **Tak**.

4. Wybierz pozycję **Zapisz**, aby zapisać ustawienia grup, gdy wszytko będzie gotowe.

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję **Azure Active Directory** > **grupy** > **wygaśnięcia** , aby otworzyć ustawienia wygasania.
  
   ![Strona ustawień wygasania dla grupy](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ustaw interwał wygasania. Wybierz wstępnie zdefiniowaną wartość lub wprowadź niestandardową wartość większą niż 31 dni. 

3. Podaj adres e-mail, na który mają być wysyłane powiadomienia dotyczące wygaśnięcia, jeśli grupa nie ma właściciela.

4. Na potrzeby tego podręcznika Szybki start ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Wszystkie**.

5. Wybierz przycisk **Zapisz**, aby zapisać ustawienia wygasania, gdy wszystko będzie gotowe.

Gotowe. W tym przewodniku Szybki start pomyślnie ustawiono zasady wygasania dla wybranych grup usługi Office 365.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="to-remove-the-expiration-policy"></a>Aby usunąć zasady wygasania

1. Upewnij się, że zalogowano się do [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym organizacji usługi Azure AD.
2. Wybierz pozycję**wygaśnięcie****grup** >  **Azure Active Directory** > .
3. Ustaw opcję **Włącz wygaśnięcie dla tych grup usługi Office 365** na wartość **Brak**.

### <a name="to-turn-off-user-creation-for-groups"></a>Aby wyłączyć tworzenie grup przez użytkowników

1. Wybierz pozycję**grupy** >  **Azure Active Directory** > **Ogólne**. 
2. Ustaw opcję **Użytkownicy mogą tworzyć grupy usługi Office 365 w portalach platformy Azure** na wartość **Nie**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wygaśnięcia, w tym instrukcji programu PowerShell i ograniczeń technicznych, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Zasady wygasania programu PowerShell](groups-lifecycle.md)
