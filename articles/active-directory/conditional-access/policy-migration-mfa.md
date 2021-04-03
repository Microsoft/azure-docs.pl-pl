---
title: Migrowanie zasad dostępu warunkowego z uwierzytelnianiem wieloskładnikowym — Azure Active Directory
description: W tym artykule przedstawiono sposób migracji zasad klasycznych, które wymagają uwierzytelniania wieloskładnikowego w Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87027397"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrowanie zasad klasycznych w Azure Portal

W tym artykule przedstawiono sposób migrowania zasad klasycznych, które wymagają **uwierzytelniania wieloskładnikowego** w przypadku aplikacji w chmurze. Chociaż nie jest to wymaganie wstępne, przed rozpoczęciem migrowania klasycznych zasad zaleca się odczytanie [zasad migracji klasycznych w Azure Portal](policy-migration.md) .

![Klasyczne Szczegóły zasad wymagające uwierzytelniania wieloskładnikowego dla aplikacji usługi Salesforce](./media/policy-migration/33.png)

Proces migracji obejmuje następujące kroki:

1. Aby uzyskać ustawienia konfiguracji, [Otwórz zasady klasyczne](#open-a-classic-policy) .
1. Utwórz nowe zasady dostępu warunkowego usługi Azure AD, aby zastąpić zasady klasyczne. 
1. Wyłącz zasady klasyczne.

## <a name="open-a-classic-policy"></a>Otwieranie zasad klasycznych

1. W [Azure Portal](https://portal.azure.com)przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Wybierz pozycję **zasady klasyczne**.

   ![Widok zasad klasycznych](./media/policy-migration-mfa/12.png)

1. Na liście zasad klasycznych wybierz zasady, które chcesz zmigrować. Udokumentowanie ustawień konfiguracji, aby można było je ponownie utworzyć przy użyciu nowych zasad dostępu warunkowego.

## <a name="create-a-new-conditional-access-policy"></a>Tworzenie nowych zasad dostępu warunkowego

1. W [Azure Portal](https://portal.azure.com)przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Aby utworzyć nowe zasady dostępu warunkowego, wybierz pozycję **nowe zasady**.
1. Na **nowej** stronie, w polu tekstowym **Nazwa** wpisz nazwę zasady.
1. W sekcji **przypisania** kliknij pozycję **Użytkownicy i grupy**.
   1. Jeśli masz wszystkich użytkowników wybranych w ramach zasad klasycznych, kliknij pozycję **Wszyscy użytkownicy**. 
   1. Jeśli w zasadach klasycznych zostały wybrane grupy, kliknij pozycję **Wybierz użytkowników i grupy**, a następnie wybierz wymaganych użytkowników i grupy.
   1. Jeśli masz wykluczone grupy, kliknij kartę **Wyklucz** , a następnie wybierz wymaganych użytkowników i grupy. 
   1. Wybierz pozycję **gotowe**
1. W sekcji **przypisanie** kliknij pozycję **aplikacje w chmurze lub akcje**.
1. Na stronie **aplikacje lub akcje w chmurze** wykonaj następujące czynności:
   1. Kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz aplikację w chmurze, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie **aplikacje w chmurze** kliknij pozycję **gotowe**.
1. Jeśli wybrano **wymaganie uwierzytelniania wieloskładnikowego** :
   1. W sekcji **kontrole dostępu** kliknij pozycję **Udziel**.
   1. Na stronie **Grant** kliknij pozycję **Udziel dostępu**, a następnie kliknij pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij pozycję **Wybierz**.
1. Kliknij **pozycję Włącz,** aby włączyć zasady, a następnie wybierz pozycję **Zapisz**.

   ![Tworzenie zasad dostępu warunkowego](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Wyłącz zasady klasyczne

Aby wyłączyć zasady klasyczne, kliknij przycisk **Wyłącz** w widoku **szczegółów** .

![Wyłącz zasady klasyczne](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat klasycznej migracji zasad, zobacz [Migrowanie zasad klasycznych w Azure Portal](policy-migration.md).
- [Użyj trybu tylko do raportowania dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)