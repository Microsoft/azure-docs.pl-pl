---
title: Dostęp warunkowy wymaga warunków użytkowania — Azure Active Directory
description: W tym przewodniku szybki start dowiesz się, jak można wymagać, aby warunki użytkowania zostały zaakceptowane, zanim dostęp do wybranych aplikacji w chmurze zostanie udzielony przez Azure Active Directory dostęp warunkowy.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab484e8caaffaf57f19f1fcd1e65f4b8e723f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077901"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Szybki Start: Wymagaj akceptacji warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze

Przed uzyskaniem dostępu do niektórych aplikacji w chmurze w Twoim środowisku warto uzyskać zgodę od użytkowników w formie zaakceptowania warunków użytkowania (warunków użytkowania). Dostęp warunkowy Azure Active Directory (Azure AD) zapewnia następujące informacje:

- Prosta metoda konfigurowania warunków użytkowania
- Opcja wymaga zaakceptowania warunków użytkowania za pomocą zasad dostępu warunkowego  

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasad dostępu warunkowego usługi Azure AD](./overview.md) , które wymagają zaakceptowania warunków użytkowania dla wybranej aplikacji w chmurze w danym środowisku.

:::image type="content" source="./media/require-tou/5555.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Widoczne jest okienko definiujące zasady o nazwie Wymagaj T O U dla Isabella." border="false":::

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz w tym przewodniku Szybki Start, musisz wykonać następujące czynności:

- **Dostęp do Azure AD — wersja Premium Edition** — dostęp warunkowy usługi Azure AD jest funkcją Azure AD — wersja Premium.
- **Konto testowe o nazwie Isabella Simonsen** — Jeśli nie wiesz, jak utworzyć konto testowe, zobacz [Dodawanie użytkowników opartych na chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest wyświetlenie doświadczenia związanego z logowaniem bez zasad dostępu warunkowego.

**Aby przetestować Logowanie:**

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Isabella Simonsen.
1. Wyloguj się.

## <a name="create-your-terms-of-use"></a>Tworzenie warunków użytkowania

Ta sekcja zawiera instrukcje dotyczące tworzenia przykładowej warunków użytkowania. Podczas tworzenia warunków użytkowania należy wybrać wartość **Wymuszaj przy użyciu szablonów zasad dostępu warunkowego**. Wybranie **zasad niestandardowych** spowoduje otwarcie okna dialogowego, w którym można utworzyć nowe zasady dostępu warunkowego zaraz po utworzeniu warunków użytkowania.

**Aby utworzyć warunki użytkowania:**

1. W programie Microsoft Word Utwórz nowy dokument.
1. Wpisz **Moje warunki użytkowania**, a następnie Zapisz dokument na komputerze jako **mytou.pdf**.
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. W Azure Portal na lewym pasku nawigacyjnym kliknij pozycję **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na stronie **Azure Active Directory** w sekcji **zabezpieczenia** kliknij pozycję **dostęp warunkowy**.

   ![Dostęp warunkowy](./media/require-tou/03.png)

1. W sekcji **Zarządzanie** kliknij pozycję **warunki użytkowania**.

   :::image type="content" source="./media/require-tou/04.png" alt-text="Zrzut ekranu przedstawiający sekcję Zarządzanie na stronie Azure Active Directory. Element Warunki użytkowania jest wyróżniony." border="false":::

1. W menu u góry kliknij pozycję **nowe warunki**.

   :::image type="content" source="./media/require-tou/05.png" alt-text="Zrzut ekranu przedstawiający menu na stronie Azure Active Directory. Zostanie wyróżnione nowe elementy warunków." border="false":::

1. Na stronie **nowe warunki użytkowania** :

   :::image type="content" source="./media/require-tou/112.png" alt-text="Zrzut ekranu przedstawiający stronę nowe warunki użytkowania, z nazwą, nazwą wyświetlaną, dokumentem, językiem, dostępem warunkowym i powiększaniem warunków." border="false":::

   1. W polu tekstowym **Nazwa** wpisz **My warunków użytkowania**.
   1. W polu tekstowym **Nazwa wyświetlana** wpisz **My warunków użytkowania**.
   1. Przekaż swoje warunki użytkowania pliku PDF.
   1. Jako **Język** wybierz pozycję **angielski**.
   1. **Aby użytkownicy mogli rozwijać warunki użytkowania**, wybierz pozycję **włączone**.
   1. Jako **wymuszanie przy użyciu szablonów zasad dostępu warunkowego**, wybierz pozycję **zasady niestandardowe**.
   1. Kliknij pozycję **Utwórz**.

## <a name="create-your-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

W tej sekcji przedstawiono sposób tworzenia wymaganych zasad dostępu warunkowego. Scenariusz w tym przewodniku szybki start używa:

- Azure Portal jako symbol zastępczy dla aplikacji w chmurze, która wymaga zaakceptowania warunków użytkowania. 
- Przykładowy użytkownik do testowania zasad dostępu warunkowego.  

W zasadach ustaw następujące ustawienia:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Isabella Simonsen |
| Aplikacje w chmurze | Zarządzanie Microsoft Azure |
| Udzielanie dostępu | Moje warunków użytkowania |

:::image type="content" source="./media/require-tou/1234.png" alt-text="Zrzut ekranu przedstawiający okienko Azure Portal definiujące zasady. Strzałki wskazują, że zasady udzielą dostępu do mojego T O i obejmują jednego użytkownika i aplikację." border="false":::

**Aby skonfigurować zasady dostępu warunkowego:**

1. Na **nowej** stronie w polu tekstowym **Nazwa** wpisz **Wymagaj warunków użytkowania dla Isabella**.

   ![Nazwa](./media/require-tou/71.png)

1. W sekcji **przypisanie** kliknij pozycję **Użytkownicy i grupy**.

   :::image type="content" source="./media/require-tou/06.png" alt-text="Zrzut ekranu sekcji przypisania okienka Azure Portal, który definiuje zasady. Element Users and Groups jest widoczny, ale nie wybrano żadnego z nich." border="false":::

1. Na stronie **Użytkownicy i grupy** :

   :::image type="content" source="./media/require-tou/24.png" alt-text="Zrzut ekranu przedstawiający kartę Dołącz na stronie użytkownicy i grupy. Wybierz pozycję Użytkownicy i grupy, ponieważ użytkownicy i grupy. SELECT jest wyróżniony." border="false":::

   1. Kliknij pozycję **Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Użytkownicy i grupy**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Isabella Simonsen**, a następnie kliknij pozycję **Wybierz**.
   1. Na stronie **Użytkownicy i grupy** kliknij przycisk **gotowe**.
1. Kliknij pozycję **aplikacje w chmurze**.

   :::image type="content" source="./media/require-tou/08.png" alt-text="Zrzut ekranu sekcji przypisania okienka Azure Portal, który definiuje zasady. Element aplikacje w chmurze jest widoczny, ale nie wybrano żadnego z nich." border="false":::

1. Na stronie **aplikacje w chmurze** :

   ![Wybierz aplikacje w chmurze](./media/require-tou/26.png)

   1. Kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Zarządzanie Microsoft Azure**, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie **aplikacje w chmurze** kliknij pozycję **gotowe**.
1. W sekcji **kontrole dostępu** kliknij pozycję **Udziel**.

   ![Kontrole dostępu](./media/require-tou/10.png)

1. Na stronie **Grant** :

   ![Uprawnienie](./media/require-tou/111.png)

   1. Kliknij opcję **Przyznaj dostęp**.
   1. Wybierz pozycję **Moje warunków użytkowania**.
   1. Kliknij pozycję **Wybierz**.
1. W sekcji **Włączanie zasad** **kliknij pozycję włączone.**

   ![Włącz zasady](./media/require-tou/18.png)

1. Kliknij pozycję **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowanie

Teraz, po skonfigurowaniu zasad dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. Pierwszym krokiem jest użycie narzędzia do działania warunkowego, co pozwala na symulowanie logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Aby zainicjować narzędzie do oceny zasad **What If** , ustaw następujące polecenie:

- **Isabella Simonsen** jako użytkownik
- **Zarządzanie Microsoft Azure** jako aplikacja w chmurze

Kliknięcie przycisku **What If** powoduje utworzenie raportu symulacji, który pokazuje:

- **Wymagaj warunków użytkowania dla Isabella** w ramach **zasad, które będą stosowane**
- **Moje warunków użytkowania** jako **Grant Controls**.

![Co to jest narzędzie zasad](./media/require-tou/79.png)

**Aby oszacować zasady dostępu warunkowego:**

1. Na stronie [zasady dostępu warunkowego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij pozycję **What If**.  

   ![What If](./media/require-tou/14.png)

1. Kliknij pozycję **Użytkownicy**, wybierz pozycję **Isabella Simonsen**, a następnie kliknij pozycję **Wybierz**.

   ![Użytkownik](./media/require-tou/15.png)

1. Aby wybrać aplikację w chmurze:

   :::image type="content" source="./media/require-tou/16.png" alt-text="Zrzut ekranu przedstawiający sekcję aplikacje w chmurze. Tekst wskazuje, że wybrano jedną aplikację." border="false":::

   1. Kliknij pozycję **aplikacje w chmurze**.
   1. Na **stronie aplikacje w chmurze** kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Zarządzanie Microsoft Azure**, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie aplikacje w chmurze kliknij pozycję **gotowe**.
1. Kliknij **What If**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji wiesz już, jak oszacować symulowaną logowanie. Oprócz symulacji należy również przetestować zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby przetestować zasady, spróbuj zalogować się do [Azure Portal](https://portal.azure.com) przy użyciu konta testowego **Isabella Simonsen** . Powinno zostać wyświetlone okno dialogowe, które wymaga zaakceptowania warunków użytkowania.

:::image type="content" source="./media/require-tou/57.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe z postanowieniami dotyczącymi ochrony zabezpieczeń tożsamości, z przyciskami Odrzuć i Zaakceptuj i przyciskiem z etykietą moje T O U." border="false":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń użytkownika testowego i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [usuwanie użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi Szybki dostęp.

    :::image type="content" source="./media/require-tou/33.png" alt-text="Zrzut ekranu przedstawiający zasady o nazwie Wymagaj M F A dla Azure Portal użytkowników. Menu skrótów jest widoczne z wyróżnioną pozycją Usuń." border="false":::

- Aby usunąć warunki użytkowania, zaznacz je, a następnie kliknij pozycję **Usuń warunki** na pasku narzędzi u góry.

    :::image type="content" source="./media/require-tou/29.png" alt-text="Zrzut ekranu przedstawiający część tabeli zawierający listę warunków użytkowania dokumentów. Mój dokument T O U jest widoczny. W menu zostanie wyróżnione polecenie Usuń warunki." border="false":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wymaganie uwierzytelniania MFA dla określonych aplikacji](../authentication/tutorial-enable-azure-mfa.md)