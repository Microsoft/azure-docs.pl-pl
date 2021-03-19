---
title: Samouczek dotyczący zbiorczego zapraszania użytkowników współpracy B2B — Azure AD
description: Z tego samouczka dowiesz się, jak wysyłać zaproszenia zbiorcze do zewnętrznych użytkowników współpracy w usłudze Azure AD B2B za pomocą programu PowerShell i pliku CSV.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01deae46c442fc95c6aead0f11de929f47163c3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586561"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Samouczek: zbiorcze zapraszanie użytkowników współpracy w usłudze Azure AD B2B

Jeśli używasz funkcji współpracy B2B w usłudze Azure Active Directory (Azure AD) do pracy z partnerami zewnętrznymi, możesz równocześnie zapraszać wielu użytkowników-gości do swojej organizacji. W ramach tego samouczka nauczysz się używać Azure Portal do wysyłania zaproszeń zbiorczych do użytkowników zewnętrznych. W szczególności należy wykonać następujące czynności:

> [!div class="checklist"]
> * Użyj **zbiorczej zapraszanych użytkowników** do przygotowania pliku wartości rozdzielanych przecinkami (CSV) za pomocą preferencji informacji o użytkowniku i zaproszenia
> * Przekazywanie pliku CSV do usługi Azure AD
> * Sprawdzenie, czy użytkownicy zostali dodani do katalogu

Jeśli nie masz Azure Active Directory, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="understand-the-csv-template"></a>Zrozumienie szablonu CSV

Pobierz i wypełnij szablon CSV przekazywanie zbiorcze, aby ułatwić pomyślne Zapraszanie użytkowników-Gości usługi Azure AD. Pobrany szablon CSV może wyglądać podobnie do tego przykładu:

![Arkusz kalkulacyjny do przekazywania i wywoływać wywołajeń objaśniających przeznaczenie i wartości dla każdego wiersza i kolumny](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>Struktura szablonu CSV

Wiersze pobranego szablonu CSV są następujące:

- **Numer wersji**: pierwszy wiersz zawierający numer wersji musi być uwzględniony w pliku CSV przekazywania.
- **Nagłówki kolumn**: format nagłówków kolumn jest &lt;  &gt; &lt; *wymagany lub pusty* &gt; . Na przykład `Email address to invite [inviteeEmail] Required`. Niektóre starsze wersje szablonu mogą mieć niewielkie wahania.
- **Wiersz przykładów**: uwzględniono w szablonie wiersz przykładów wartości dla każdej kolumny. Musisz usunąć wiersz przykładów i zastąpić go własnymi wpisami.

### <a name="additional-guidance"></a>Dodatkowe wskazówki

- Pierwsze dwa wiersze szablonu przekazywania nie mogą być usuwane ani modyfikowane lub nie można przetworzyć przekazywania.
- Wymagane kolumny są wyświetlane jako pierwsze.
- Nie zalecamy dodawania nowych kolumn do szablonu. Wszelkie dodatkowe dodawane kolumny są ignorowane i nie są przetwarzane.
- Zalecamy pobranie najnowszej wersji szablonu CSV tak często, jak to możliwe.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz co najmniej dwóch testowych kont e-mail, na które będzie można wysłać zaproszenia. Konta muszą być spoza Twojej organizacji. Możesz użyć dowolnego typu konta, w tym kont społecznościowych z adresem w domenie gmail.com lub outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Zapraszanie użytkowników-Gości w trybie zbiorczym

1. Zaloguj się do Azure Portal przy użyciu konta, które jest administratorem globalnym w organizacji.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzaj** wybierz pozycję **Wszyscy użytkownicy**.
4. Wybierz zbiorcze zaproszenia **operacji** zbiorczych  >  .

    ![Przycisk Zaproś zbiorczy](media/tutorial-bulk-invite/bulk-invite-button.png)

4. Na stronie **Zapraszanie użytkowników w trybie zbiorczym** wybierz pozycję **Pobierz** , aby pobrać prawidłowy szablon. CSV z właściwościami zaproszenia.

     ![Pobierz plik CSV](media/tutorial-bulk-invite/download-button.png)

1. Otwórz szablon. csv i Dodaj wiersz dla każdego użytkownika-gościa. Wymagane wartości to:

   * **Adres e-mail do zaproszenia** — użytkownik, który otrzyma zaproszenie

   * **Adres URL przekierowania** — adres URL, do którego zostanie przekazany zaproszony użytkownik po zaakceptowaniu zaproszenia. Aby przekazać użytkownika do strony Moje aplikacje, należy zmienić tę wartość na https://myapps.microsoft.com lub https://myapplications.microsoft.com .

    ![Przykładowy plik CSV z wprowadzonymi użytkownikami gościa](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Nie używaj przecinków w **dostosowywanym komunikacie zaproszenia** , ponieważ uniemożliwi to pomyślne przeanalizowanie komunikatu.

6. Zapisz plik.
7. Na stronie **Zapraszanie użytkowników zaproszonych** w obszarze **Przekaż plik CSV** przejdź do pliku. Po wybraniu pliku zostanie uruchomiony Walidacja pliku CSV. 
8. Gdy zawartość pliku zostanie sprawdzona, zostanie wyświetlony **plik przekazany pomyślnie**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
9. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która dodaje zaproszenia. 
10. Aby wyświetlić stan zadania, wybierz **pozycję kliknij tutaj, aby wyświetlić stan każdej operacji**. Można też wybrać pozycję **wyniki operacji zbiorczych** w sekcji **działanie** . Aby uzyskać szczegółowe informacje na temat każdego elementu wiersza w ramach operacji zbiorczej, wybierz wartości w kolumnach **# Success**, **# Failure** lub **Total Requests** . Jeśli wystąpią błędy, zostaną wyświetlone przyczyny niepowodzenia.

    ![Przykład wyników operacji zbiorczej](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Po zakończeniu zadania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

## <a name="verify-guest-users-in-the-directory"></a>Weryfikowanie użytkowników-Gości w katalogu

Sprawdź, czy dodani użytkownicy-Goście znajdują się w katalogu w Azure Portal lub przy użyciu programu PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Wyświetlanie użytkowników-Gości w Azure Portal

1. Zaloguj się do Azure Portal przy użyciu konta, które jest administratorem użytkownika w organizacji.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
4. W obszarze **Pokaż** wybierz pozycję **tylko użytkownicy-Goście** i sprawdź, czy dodano użytkowników.

### <a name="view-guest-users-with-powershell"></a>Wyświetlanie użytkowników-Gości przy użyciu programu PowerShell

Uruchom następujące polecenie:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Zobaczysz użytkowników, którzy zaprosił, z główną nazwą użytkownika (UPN) w formacie *emailaddress*#EXT # \@ *Domain*. Na przykład *lstokes_fabrikam. com # EXT # \@ contoso.onmicrosoft.com*, gdzie contoso.onmicrosoft.com jest organizacją, z której wysłano zaproszenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz usunąć konta użytkowników testowych z katalogu w Azure Portal na stronie użytkownicy, zaznaczając pole wyboru obok pola Gość użytkownika, a następnie wybierając pozycję **Usuń**. 

Możesz też uruchomić następujące polecenie programu PowerShell, aby usunąć konto użytkownika:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Na przykład: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano zaproszenia zbiorcze do użytkowników-gości spoza organizacji. Następnie dowiesz się, jak działa proces realizacji zaproszenia.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat procesu realizacji zaproszenia do współpracy B2B w usłudze Azure AD](redemption-experience.md)
