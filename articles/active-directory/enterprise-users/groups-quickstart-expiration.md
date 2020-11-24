---
title: Przewodnik Szybki Start dotyczący zasad wygasania grup — Azure AD | Microsoft Docs
description: Wygaśnięcie dla grup Microsoft 365 — Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0b9e4ccd595e90ab941cb061a016f8c4d4f3d0
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503028"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Szybki Start: Ustawianie Microsoft 365ych grup do wygaśnięcia w Azure Active Directory

W tym przewodniku szybki start ustawisz zasady wygasania dla grup Microsoft 365. Umożliwienie użytkownikom tworzenia własnych grup może prowadzić do powstania wielu nieużywanych grup. Jednym ze sposobów radzenia sobie z nieużywanymi grupami jest skonfigurowanie wygasania tych grup, co pozwala uniknąć konieczności ich ręcznego usuwania.

Zasady wygasania są proste:

- Grupy z działaniami użytkownika są automatycznie odnawiane jako zbliżające się czas wygaśnięcia
- Właściciele grupy są powiadamiani o konieczności odnowienia wygasającej grupy
- Grupa, która nie zostanie odnowiona, zostanie usunięta
- Usunięta Grupa Microsoft 365 może zostać przywrócona w ciągu 30 dni przez właściciela grupy lub przez administratora usługi Azure AD

> [!NOTE]
> Grupy teraz używają usługi Azure AD Intelligence do automatycznego odnawiania w zależności od tego, czy zostały one ostatnio użyte. Ta decyzja o odnowieniu jest oparta na aktywności użytkownika w grupach w ramach usług Microsoft 365, takich jak Outlook, SharePoint, Teams, Yammer i inne.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Wymaganie wstępne

 Rola najniższych uprawnień wymagana do skonfigurowania wygaśnięcia grupy jest administratorem użytkownika w organizacji.

## <a name="turn-on-user-creation-for-groups"></a>Włączanie tworzenia grup przez użytkowników

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników.

2. Wybierz pozycję **Grupy**, a następnie wybierz pozycję **Ogólne**.
  
   ![Strona ustawień grupy samoobsługowej](./media/groups-quickstart-expiration/self-service-settings.png)

3. Ustawienie  **Użytkownicy mogą tworzyć grupy Microsoft 365** na **tak**.

4. Wybierz pozycję **Zapisz**, aby zapisać ustawienia grup, gdy wszytko będzie gotowe.

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję **Azure Active Directory**  >  **grupy**  >  **wygaśnięcia** , aby otworzyć ustawienia wygasania.
  
   ![Strona ustawień wygasania dla grupy](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ustaw interwał wygasania. Wybierz wstępnie zdefiniowaną wartość lub wprowadź niestandardową wartość większą niż 31 dni. 

3. Podaj adres e-mail, na który mają być wysyłane powiadomienia dotyczące wygaśnięcia, jeśli grupa nie ma właściciela.

4. **W tym** przewodniku szybki start ustaw opcję **Włącz wygasanie dla tych grup Microsoft 365** .

5. Wybierz przycisk **Zapisz**, aby zapisać ustawienia wygasania, gdy wszystko będzie gotowe.

Gotowe. W tym przewodniku szybki start pomyślnie ustawiono zasady wygasania dla wybranych grup Microsoft 365.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="to-remove-the-expiration-policy"></a>Aby usunąć zasady wygasania

1. Upewnij się, że zalogowano się do [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym organizacji usługi Azure AD.
2. Wybierz **Azure Active Directory** pozycję  >  **Groups**  >  **wygaśnięcie** grup Azure Active Directory.
3. Ustaw opcję **Włącz wygasanie dla tych grup Microsoft 365** na **none**.

### <a name="to-turn-off-user-creation-for-groups"></a>Aby wyłączyć tworzenie grup przez użytkowników

1. Wybierz **Azure Active Directory** pozycję  >  **grupy** Azure Active Directory  >  **Ogólne**. 
2. Ustawienie **Użytkownicy mogą tworzyć grupy Microsoft 365 w portalach platformy Azure** do **nie**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wygaśnięcia, w tym instrukcji programu PowerShell i ograniczeń technicznych, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Zasady wygasania programu PowerShell](groups-lifecycle.md)
