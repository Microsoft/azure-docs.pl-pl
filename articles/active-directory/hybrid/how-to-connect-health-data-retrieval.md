---
title: Pobieranie danych instrukcji Azure AD Connect Health | Microsoft Docs
description: Na tej stronie opisano sposób pobierania danych z Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89463655"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Instrukcje Azure AD Connect Health pobierania danych

W tym dokumencie opisano sposób używania Azure AD Connect do pobierania danych z Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Pobierz wszystkie adresy e-mail dla użytkowników skonfigurowanych pod kątem alertów dotyczących kondycji.

Aby pobrać adresy e-mail dla wszystkich użytkowników skonfigurowanych w Azure AD Connect Health w celu otrzymywania alertów, wykonaj następujące czynności.

1.  Rozpocznij w bloku kondycja Azure Active Directory Connect a następnie wybierz pozycję **Synchronizuj usługi** na pasku nawigacyjnym po lewej stronie.
 ![Usługi synchronizacji](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Kliknij kafelek **alerty** .</br>
 ![Alerty](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Kliknij pozycję **Ustawienia powiadomień**.
 ![Powiadomienie](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  W bloku **Ustawienia powiadomienia** znajduje się lista adresów e-mail, które zostały włączone jako adresaci powiadomień o alertach kondycji.
 ![Wiadomości e-mail](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Pobieranie kont oflagowanych przy użyciu AD FS nieudanych prób wprowadzenia hasła

Aby pobrać konta oflagowane AD FS nieprawidłowymi próbami hasła, wykonaj następujące czynności.

1.  W bloku kondycja Azure Active Directory wybierz pozycję **Błędy synchronizacji**.
 ![Błędy synchronizacji](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  W bloku **Błędy synchronizacji** kliknij pozycję **Eksportuj**. Spowoduje to wyeksportowanie listy zarejestrowanych błędów synchronizacji.
 ![Eksportowanie](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Następne kroki
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalowanie agenta programu Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operacje w programie Azure AD Connect Health](how-to-connect-health-operations.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji Azure AD Connect Health](reference-connect-health-version-history.md)