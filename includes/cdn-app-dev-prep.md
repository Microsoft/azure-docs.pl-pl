---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993360"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed zapisaniem kodu zarządzania CDN należy wykonać pewne przygotowania, aby umożliwić współpracującie kodu z Azure Resource Manager. Aby to zrobić, należy wykonać następujące czynności:

* Utwórz grupę zasobów zawierającą profil usługi CDN utworzony w tym samouczku
* Konfigurowanie Azure Active Directory w celu zapewnienia uwierzytelniania aplikacji
* Zastosuj uprawnienia do grupy zasobów, aby tylko autoryzowani użytkownicy z dzierżawy usługi Azure AD mogli korzystać z profilu CDN

### <a name="creating-the-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób**.
3. Wyszukaj **grupę zasobów** i w okienku Grupa zasobów kliknij pozycję **Utwórz**.

    ![Tworzenie nowej grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nazwij grupę zasobów *CdnConsoleTutorial*.  Wybierz swoją subskrypcję i wybierz najbliższą lokalizację.  Jeśli chcesz, możesz kliknąć pole wyboru **Przypnij do pulpitu nawigacyjnego** , aby przypiąć grupę zasobów do pulpitu nawigacyjnego w portalu.  Przypinanie ułatwia znalezienie go w przyszłości.  Po dokonaniu wyboru kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający okno dialogowe Grupa zasobów.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Po utworzeniu grupy zasobów, jeśli nie została ona przypięta do pulpitu nawigacyjnego, możesz ją znaleźć, klikając przycisk **Przeglądaj**, a następnie **grupy zasobów**.  Aby go otworzyć, kliknij grupę zasobów.  Zanotuj swój **Identyfikator subskrypcji**. Potrzebujemy go później.

    ![Zrzut ekranu przedstawiający sekcję samouczka konsola C D N.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Tworzenie aplikacji usługi Azure AD i stosowanie uprawnień
Istnieją dwa podejścia do uwierzytelniania aplikacji przy użyciu Azure Active Directory: poszczególnych użytkowników lub nazwy głównej usługi. Nazwa główna usługi jest podobna do konta usługi w systemie Windows.  Zamiast udzielania określonych uprawnień użytkownika do współużytkowania z profilami CDN, uprawnienia są przydzielane do jednostki usługi.  Nazwy główne usług są zwykle używane dla zautomatyzowanych, nieinteraktywnych procesów.  Mimo że ten samouczek polega na napisaniu interaktywnej aplikacji konsolowej, należy skoncentrować się na podejściu głównej usługi.

Tworzenie jednostki usługi składa się z kilku kroków, w tym do tworzenia aplikacji Azure Active Directory.  Aby go utworzyć, [postępuj zgodnie z tym samouczkiem](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Pamiętaj, aby wykonać wszystkie kroki w [połączonym samouczku](../articles/active-directory/develop/howto-create-service-principal-portal.md).  *Ważne* jest, aby wypełnić je dokładnie zgodnie z opisem.  Pamiętaj, aby zanotować swój **Identyfikator dzierżawy**, **nazwę domeny dzierżawy** (zwykle domenę *. onmicrosoft.com* , chyba że określono domenę niestandardową), **Identyfikator klienta** i **klucz uwierzytelniania klienta**, ponieważ te informacje będą potrzebne później.  Należy zachować ostrożność w celu ochrony **identyfikatora klienta** i **klucza uwierzytelniania klienta**, ponieważ mogą one być używane przez każdego użytkownika do wykonywania operacji jako nazwy głównej usługi.
>
> Po wyświetleniu kroku o nazwie Konfiguruj wielodostępną aplikację wybierz pozycję **nie**.
>
> Po [przypisaniu aplikacji do roli](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)Użyj utworzonej wcześniej grupy zasobów,  *CdnConsoleTutorial*, ale zamiast roli **czytelnik** , przypisz rolę **współautor profilu usługi CDN** .  Po przypisaniu aplikacji rola **współautor profilu usługi CDN** w grupie zasobów Wróć do tego samouczka. 
>
>

Po utworzeniu nazwy głównej usługi i przypisaniu roli **współautor profilu sieci CDN** blok **Użytkownicy** dla grupy zasobów powinien wyglądać podobnie do poniższej ilustracji.

![Blok użytkownicy](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Uwierzytelnianie użytkowników interakcyjnych
Jeśli zamiast nazwy głównej usługi nie masz interaktywnego uwierzytelniania poszczególnych użytkowników, proces jest podobny do tego dla jednostki usługi.  W rzeczywistości należy postępować zgodnie z tą samą procedurą, ale wprowadzić kilka drobnych zmian.

> [!IMPORTANT]
> Poniższe kroki należy wykonać tylko w przypadku wybrania opcji uwierzytelniania poszczególnych użytkowników zamiast nazwy głównej usługi.
>
>

1. Podczas tworzenia aplikacji, a nie **aplikacji sieci Web**, wybierz **aplikację natywną**.

    ![Aplikacja natywna](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na następnej stronie zostanie wyświetlony monit o podanie **identyfikatora URI przekierowania**.  Identyfikator URI nie zostanie zweryfikowany, ale Zapamiętaj, co wprowadzono. Będzie potrzebna później.
3. Nie ma potrzeby tworzenia **klucza uwierzytelniania klienta**.
4. Zamiast przypisywać jednostkę usługi do roli **współautor profilu CDN** , przypiszemy poszczególnych użytkowników lub grupy.  W tym przykładzie można zobaczyć, że przypisano  *użytkownika demonstracyjnego usługi CDN* do roli **współautor profilu CDN** .  

    ![Dostęp do poszczególnych użytkowników](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
