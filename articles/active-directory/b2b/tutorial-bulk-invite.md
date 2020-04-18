---
title: Samouczek dotyczący zbiorczego zapraszania użytkowników współpracy B2B — Usługa Azure AD
description: Z tego samouczka dowiesz się, jak wysyłać zaproszenia zbiorcze do zewnętrznych użytkowników współpracy w usłudze Azure AD B2B za pomocą programu PowerShell i pliku CSV.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/13/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ef9172ca5d0961bb6de1949a61199ce1d6c1bff
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603415"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Samouczek: zbiorcze zapraszanie użytkowników współpracy w usłudze Azure AD B2B

Jeśli używasz funkcji współpracy B2B w usłudze Azure Active Directory (Azure AD) do pracy z partnerami zewnętrznymi, możesz równocześnie zapraszać wielu użytkowników-gości do swojej organizacji. W tym samouczku dowiesz się, jak używać witryny Azure portal do wysyłania zbiorczych zaproszeń do użytkowników zewnętrznych. W szczególności należy wykonać następujące czynności:

> [!div class="checklist"]
> * Używanie **zbiorczego zapraszania użytkowników** do przygotowania pliku wartości rozdzielanego przecinkami (csv) z informacjami o użytkowniku i preferencjami zaproszeń
> * Przekazywanie pliku csv do usługi Azure AD
> * Sprawdzenie, czy użytkownicy zostali dodani do katalogu

Jeśli nie masz usługi Azure Active Directory, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz co najmniej dwóch testowych kont e-mail, na które będzie można wysłać zaproszenia. Konta muszą być spoza Twojej organizacji. Możesz użyć dowolnego typu konta, w tym kont społecznościowych z adresem w domenie gmail.com lub outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Zbiorcze zapraszanie użytkowników-gości

1. Zaloguj się do witryny Azure portal przy za pomocą konta, które jest administratorem użytkownika w organizacji.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzaj**wybierz pozycję **Użytkownicy** > **zaproś zbiorczo**.
4. Na stronie **Użytkownicy zaproszenia zbiorczego** wybierz pozycję **Pobierz,** aby uzyskać prawidłowy plik csv z właściwościami zaproszenia.

    ![Przycisk pobierania zaproszenia zbiorczego](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Otwórz plik csv i dodaj wiersz dla każdego użytkownika-gościa. Wymagane wartości to:

   * **Adres e-mail do zaproszenia** - użytkownik, który otrzyma zaproszenie

   * **Adres URL przekierowania** - adres URL, do którego zaproszony użytkownik jest przekazywane po zaakceptowaniu zaproszenia

    ![Przykład pliku CSV z wprowadzonymi użytkownikami-gośćmi](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Nie używaj przecinków w **wiadomości z zaproszeniem dostosowane,** ponieważ uniemożliwią one pomyślną analizę wiadomości.

6. Zapisz plik.
7. Na stronie **Użytkownicy zaproszenia zbiorczego** w obszarze **Prześlij plik csv**przejdź do pliku. Po wybraniu pliku rozpocznie się sprawdzanie poprawności pliku csv. 
8. Po sprawdzeniu poprawności zawartości pliku zostanie **wyświetlony folder Plik przekazany pomyślnie**. Jeśli występują błędy, należy je naprawić, zanim będzie można przesłać zadanie.
9. Gdy plik przechodzi weryfikacji, wybierz **prześlij,** aby rozpocząć operację zbiorczą platformy Azure, która dodaje zaproszenia. 
10. Aby wyświetlić stan zadania, wybierz pozycję **Kliknij tutaj, aby wyświetlić stan każdej operacji**. Można też wybrać **zbiorcze wyniki operacji** w sekcji **Działanie.** Aby uzyskać szczegółowe informacje o każdym elemencie zamówienia w ramach operacji zbiorczej, wybierz wartości w kolumnach **# Sukces**, **# Niepowodzenie**lub Całkowita **liczba żądań.** Jeśli wystąpiły błędy, zostaną wyświetlone przyczyny awarii.

    ![Przykład wyników operacji zbiorczych](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Po zakończeniu zadania zostanie wyświetlone powiadomienie, że operacja zbiorcza powiodła się.

## <a name="verify-guest-users-in-the-directory"></a>Weryfikowanie użytkowników-gości w katalogu

Sprawdź, czy dodana użytkownicy-goście istnieją w katalogu w witrynie Azure portal lub przy użyciu programu PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Wyświetlanie użytkowników-gości w witrynie Azure portal

1. Zaloguj się do witryny Azure portal przy za pomocą konta, które jest administratorem użytkownika w organizacji.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
4. W obszarze **Pokaż**wybierz pozycję **Tylko użytkownicy-goście i** sprawdź, czy dodana użytkownicy są na liście.

### <a name="view-guest-users-with-powershell"></a>Wyświetlanie użytkowników-gości za pomocą programu PowerShell

Uruchom następujące polecenie:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Użytkownicy, których zaproszono, powinni być wymienieni z główną nazwą użytkownika (UPN) w formacie *adresu e-mail*\@*#EXT# domeny*. Na przykład *\@lstokes_fabrikam.com#EXT# contoso.onmicrosoft.com*, gdzie contoso.onmicrosoft.com jest organizacja, z której wysłano zaproszenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można usunąć konta użytkowników testowych w katalogu w witrynie Azure portal na stronie Użytkownicy, zaznaczając pole wyboru obok użytkownika-gościa, a następnie wybierając polecenie **Usuń**. 

Możesz też uruchomić następujące polecenie programu PowerShell, aby usunąć konto użytkownika:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Na przykład: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano zaproszenia zbiorcze do użytkowników-gości spoza organizacji. Następnie dowiesz się, jak działa proces realizacji zaproszenia.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat procesu realizacji zaproszenia do współpracy B2B w usłudze Azure AD](redemption-experience.md)
