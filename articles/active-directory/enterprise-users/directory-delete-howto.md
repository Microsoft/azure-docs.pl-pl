---
title: Usuwanie organizacji usługi Azure AD (dzierżawa) — Azure Active Directory | Microsoft Docs
description: Wyjaśnia, jak przygotować organizację usługi Azure AD (dzierżawę) do usunięcia, w tym organizacje samoobsługowe
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 05/21/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf8eeb2d1f19ca0848ae5c608ba9f0d8e9526e05
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377282"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Usuwanie dzierżawy w Azure Active Directory

Po usunięciu organizacji usługi Azure AD (dzierżawa) wszystkie zasoby zawarte w organizacji również zostaną usunięte. Przed usunięciem Przygotuj swoją organizację przez zminimalizowanie jej skojarzonych zasobów. Tylko administrator globalny Azure Active Directory (Azure AD) może usunąć organizację usługi Azure AD z portalu.

## <a name="prepare-the-organization"></a>Przygotuj organizację

Nie można usunąć organizacji w usłudze Azure AD, dopóki nie przejdzie kilka sprawdzeń. Te sprawdzenia zmniejszają ryzyko, że usunięcie organizacji usługi Azure AD ma negatywny wpływ na dostęp użytkownika, na przykład możliwość zalogowania się do Microsoft 365 lub uzyskania dostępu do zasobów na platformie Azure. Jeśli na przykład organizacja skojarzona z subskrypcją zostanie przypadkowo usunięta, użytkownicy nie będą mogli uzyskać dostępu do zasobów platformy Azure dla tej subskrypcji. Są sprawdzane następujące warunki:

* W organizacji usługi Azure AD (dzierżawy) nie mogą istnieć użytkownicy, z wyjątkiem jednego administratora globalnego, który ma zostać usunięty. Aby można było usunąć organizację, należy usunąć wszystkich innych użytkowników. Jeśli użytkownicy są synchronizowani ze środowiska lokalnego, najpierw należy wyłączyć synchronizację, a użytkownicy muszą zostać usunięci z organizacji w chmurze przy użyciu poleceń cmdlet Azure Portal lub Azure PowerShell.
* W organizacji nie mogą istnieć żadne aplikacje. Aby można było usunąć organizację, należy usunąć wszystkie aplikacje.
* Z organizacją nie mogą być połączeni dostawcy usługi uwierzytelniania wieloskładnikowego.
* Nie mogą istnieć żadne subskrypcje usług Microsoft Online Services, takich jak Microsoft Azure, Microsoft 365 lub Azure AD — wersja Premium skojarzone z organizacją. Jeśli na przykład została utworzona domyślna organizacja usługi Azure AD na platformie Azure, nie można usunąć tej organizacji, jeśli subskrypcja platformy Azure nadal korzysta z tej organizacji na potrzeby uwierzytelniania. Podobnie nie można usunąć organizacji, jeśli inny użytkownik skojarzył z nim subskrypcję.

## <a name="delete-the-organization"></a>Usuń organizację

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym w Twojej organizacji.

2. Wybierz pozycję **Azure Active Directory**.

3. Przejdź do organizacji, którą chcesz usunąć.
  
   ![Potwierdź organizację przed usunięciem](./media/directory-delete-howto/delete-directory-command.png)

4. Wybierz pozycję **Usuń dzierżawcę**.
  
   ![Wybierz polecenie, aby usunąć organizację](./media/directory-delete-howto/delete-directory-list.png)

5. Jeśli Twoja organizacja nie przekaże co najmniej jednego sprawdzenia, zostanie wyświetlony link do dodatkowych informacji na temat sposobu przekazywania. Po przejściu wszystkich testów wybierz pozycję **Usuń** , aby zakończyć proces.

## <a name="if-you-cant-delete-the-organization"></a>Jeśli nie możesz usunąć organizacji

Po skonfigurowaniu organizacji usługi Azure AD można także aktywować subskrypcje oparte na licencjach dla swojej organizacji, takie jak Azure AD — wersja Premium P2, Microsoft 365 Business Standard lub Enterprise Mobility + Security E5. Aby uniknąć przypadkowej utraty danych, nie można usunąć organizacji, dopóki subskrypcje nie zostaną całkowicie usunięte. Aby można było usunąć **organizację, subskrypcje** muszą być w stanie niedostępnym. **Wygasła** lub **anulowana** subskrypcja przechodzi do stanu **wyłączone** , a końcowym etapem jest stan **anulowania** aprowizacji.

W poniższej tabeli znajdują się informacje o tym, czego można oczekiwać, gdy subskrypcja wersji próbnej Microsoft 365 wygaśnie (bez uwzględnienia płatnego partnera/dostawcy CSP, Umowa Enterprise lub licencjonowania zbiorowego). Aby uzyskać więcej informacji na temat Microsoft 365 przechowywania danych i cyklu życia subskrypcji, zobacz [co się stanie z moimi danymi i dostępem po zakończeniu subskrypcji Microsoft 365 dla firm?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stan subskrypcji | Dane | Dostęp do danych
----- | ----- | -----
Aktywne (30 dni dla wersji próbnej) | Dostęp do danych dla wszystkich | Użytkownicy mają normalny dostęp do plików Microsoft 365 lub aplikacji<br>Administratorzy mają normalny dostęp do centrum administracyjnego Microsoft 365 i zasobów 
Wygasłe (30 dni) | Dostęp do danych dla wszystkich| Użytkownicy mają normalny dostęp do plików Microsoft 365 lub aplikacji<br>Administratorzy mają normalny dostęp do centrum administracyjnego Microsoft 365 i zasobów
Wyłączone (30 dni) | Dane dostępne tylko dla administratora | Użytkownicy nie mogą uzyskać dostępu do Microsoft 365 plików ani aplikacji<br>Administratorzy mogą uzyskać dostęp do centrum administracyjnego Microsoft 365, ale nie mogą przypisywać licencji ani aktualizować użytkowników
Anulowanie aprowizacji (po upływie 30 dni od wyłączenia) | Usunięte dane (automatycznie usunięte, jeśli nie są używane żadne inne usługi) | Użytkownicy nie mogą uzyskać dostępu do Microsoft 365 plików ani aplikacji<br>Administratorzy mogą uzyskać dostęp do centrum administracyjnego Microsoft 365, aby kupić inne subskrypcje i zarządzać nimi

## <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Aby można było usunąć subskrypcję w stanie **anulowania** aprowizacji w ciągu trzech dni, należy użyć centrum administracyjnego Microsoft 365.

1. Zaloguj się do [Centrum administracyjnego Microsoft 365](https://admin.microsoft.com) przy użyciu konta, które jest administratorem globalnym w Twojej organizacji. Jeśli próbujesz usunąć organizację "contoso" z początkową domyślną domeną contoso.onmicrosoft.com, zaloguj się przy użyciu nazwy UPN, takiej jak admin@contoso.onmicrosoft.com .

2. Wyświetl podgląd nowego centrum administracyjnego Microsoft 365, upewniając się, że jest włączona Nowa funkcja przełączania **Centrum administracyjnego** .

   ![Podgląd nowego środowiska centrum administracyjnego M365](./media/directory-delete-howto/preview-toggle.png)

3. Po włączeniu nowego centrum administracyjnego należy anulować subskrypcję, aby można było je usunąć. Wybierz pozycję **rozliczenia** i wybierz pozycję **produkty & usługi**, a następnie wybierz pozycję **Anuluj subskrypcję** dla subskrypcji, którą chcesz anulować. Nastąpi przełączenie na stronę opinii.

   ![Wybierz subskrypcję, aby anulować](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Wypełnij formularz opinii i wybierz pozycję **Anuluj subskrypcję** , aby anulować subskrypcję.

   ![Anuluj polecenie w wersji zapoznawczej subskrypcji](./media/directory-delete-howto/cancel-command.png)

5. Teraz możesz usunąć subskrypcję. Wybierz pozycję **Usuń** dla subskrypcji, którą chcesz usunąć. Jeśli nie możesz znaleźć subskrypcji na stronie **produkty & Services** , upewnij się, że masz **stan subskrypcji** ustawiony na **wszystkie**.

   ![Usuwanie linku do usuwania subskrypcji](./media/directory-delete-howto/delete-command.png)

6. Wybierz pozycję **Usuń subskrypcję** , aby usunąć subskrypcję i zaakceptować warunki i postanowienia. Wszystkie dane zostaną trwale usunięte w ciągu trzech dni. Jeśli zmienisz zdanie, możesz [ponownie aktywować subskrypcję](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) w ciągu trzech dni.
  
   ![dokładne odczytywanie warunków i postanowień](./media/directory-delete-howto/delete-terms.png)

7. Teraz stan subskrypcji został zmieniony i subskrypcja została oznaczona do usunięcia. Subskrypcja wprowadza stan **anulowania** aprowizacji 72 godzin później.

8. Po usunięciu subskrypcji w organizacji i upływie 72 godzin można ponownie zalogować się do centrum administracyjnego usługi Azure AD, a nie powinna istnieć żadna wymagana akcja i nie ma żadnych subskrypcji blokujących usuwanie organizacji. Należy mieć możliwość pomyślnego usunięcia organizacji usługi Azure AD.
  
   ![ekran przekazywania subskrypcji po usunięciu](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mam subskrypcję wersji próbnej, która blokuje usuwanie

Istnieją produkty do samodzielnej [rejestracji](/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , takie jak Microsoft Power BI, Rights Management Services, Microsoft PowerShell Apps lub Dynamics 365, indywidualni użytkownicy mogą rejestrować się za pośrednictwem Microsoft 365, co powoduje także utworzenie użytkownika-gościa na potrzeby uwierzytelniania w organizacji usługi Azure AD. Te produkty samoobsługi blokują operacje usuwania katalogów, dopóki produkty nie zostaną całkowicie usunięte z organizacji, aby uniknąć utraty danych. Mogą zostać usunięte tylko przez administratora usługi Azure AD, niezależnie od tego, czy użytkownik zarejestrował się indywidualnie, czy też został przypisany do produktu.

W sposobie ich przypisywania istnieją dwa typy produktów do samodzielnej rejestracji: 

* Przypisanie na poziomie organizacji: administrator usługi Azure AD przypisuje produkt do całej organizacji, a użytkownik może aktywnie używać usługi z tym przypisaniem na poziomie organizacji, nawet jeśli nie są indywidualnie licencjonowane.
* Przypisanie na poziomie użytkownika: indywidualny użytkownik podczas samoobsługowego rejestrowania, zasadniczo przypisuje produkt do samego siebie bez administratora. Gdy organizacja stanie się zarządzana przez administratora (zobacz [przejęcie przez administratora niezarządzanej organizacji](domains-admin-takeover.md)), administrator może bezpośrednio przypisać produkt użytkownikom bez rejestracji samoobsługowej.  

Po rozpoczęciu usuwania produktu samoobsługowego rejestracji, akcja spowoduje trwałe usunięcie danych i usunięcie wszystkich użytkowników dostępu do usługi. Każdy użytkownik, któremu przypisano ofertę pojedynczą lub na poziomie organizacji, jest zablokowany do logowania się lub uzyskiwania dostępu do istniejących danych. Aby zapobiec utracie danych za pomocą samoobsługowego tworzenia konta, takiego jak [pulpity nawigacyjne firmy Microsoft Power BI](/power-bi/service-export-to-pbix) lub [konfiguracja zasad usług Rights Management Services](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), należy się upewnić, że kopia zapasowa danych została utworzona i zapisana w innym miejscu.

Aby uzyskać więcej informacji na temat aktualnie dostępnych produktów i usług do samoobsługowego rejestrowania, zobacz [dostępne programy](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)samoobsługowe.

W poniższej tabeli znajdują się informacje o tym, czego można oczekiwać, gdy subskrypcja wersji próbnej Microsoft 365 wygaśnie (bez uwzględnienia płatnego partnera/dostawcy CSP, Umowa Enterprise lub licencjonowania zbiorowego). Aby uzyskać więcej informacji na temat Microsoft 365 przechowywania danych i cyklu życia subskrypcji, zobacz [co się stanie z moimi danymi i dostępem po zakończeniu subskrypcji Microsoft 365 dla firm?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stan produktu | Dane | Dostęp do danych
------------- | ---- | --------------
Aktywne (30 dni dla wersji próbnej) | Dostęp do danych dla wszystkich | Użytkownicy mają normalny dostęp do samoobsługowego do rejestracji produktu, plików lub aplikacji.<br>Administratorzy mają normalny dostęp do centrum administracyjnego Microsoft 365 i zasobów
Usunięte | Usunięte dane | Użytkownicy nie mogą uzyskać dostępu do samoobsługowego produktu, plików ani aplikacji.<br>Administratorzy mogą uzyskać dostęp do centrum administracyjnego Microsoft 365, aby kupić inne subskrypcje i zarządzać nimi

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak usunąć samoobsługowy produkt do samodzielnej rejestracji w Azure Portal?

Możesz utworzyć samoobsługowe produkty do rejestracji, takie jak Microsoft Power BI lub Azure Rights Management Services w stanie **usuwania** , aby natychmiast usunąć je w portalu usługi Azure AD.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym w organizacji. Jeśli próbujesz usunąć organizację "contoso" z początkową domyślną domeną contoso.onmicrosoft.com, zaloguj się przy użyciu nazwy UPN, takiej jak admin@contoso.onmicrosoft.com .

2. Wybierz pozycję **licencje**, a następnie wybierz pozycję **produkty do rejestracji**samoobsługowej. Wszystkie produkty do samodzielnej rejestracji można zobaczyć niezależnie od subskrypcji opartych na miejscu. Wybierz produkt, który chcesz trwale usunąć. Oto przykład w programie Microsoft Power BI:

    ![Zrzut ekranu przedstawiający stronę "licencje-samoobsługowe produkty do rejestracji".](./media/directory-delete-howto/licenses-page.png)

3. Wybierz pozycję **Usuń** , aby usunąć produkt i zaakceptować warunki, że dane są usuwane natychmiast i nieodwołalnie. Spowoduje to usunięcie wszystkich użytkowników i usunięcie dostępu organizacji do produktu. Kliknij przycisk tak, aby przejść do przodu z usunięciem.  

    ![Zrzut ekranu przedstawiający stronę "licencje — produkty samoobsługowego tworzenia konta" z otwartym oknem "Usuń produkt samoobsługowego tworzenia nowego konta".](./media/directory-delete-howto/delete-product.png)

4. Wybranie **opcji tak**spowoduje zainicjowanie usuwania produktu samoobsługi. Istnieje powiadomienie informujące o tym, że usuwanie jest w toku.  

    ![Zrzut ekranu przedstawiający stronę "licencje — produkty samoobsługowego tworzenia konta" z wyświetlonym powiadomieniem "Usuwanie w toku".](./media/directory-delete-howto/progress-message.png)

5. Teraz stan produktu samoobsługowego rejestrowania został zmieniony na **usunięty**. Po odświeżeniu strony produkt powinien zostać usunięty z poziomu strony **samoobsługowego rejestrowania produktów** .  

    ![Zrzut ekranu przedstawiający stronę "licencje — produkty samoobsługowego tworzenia konta" z okienkiem "usunięto produkt samoobsługowego tworzenia konta" po prawej stronie.](./media/directory-delete-howto/product-deleted.png)

6. Po usunięciu wszystkich produktów możesz ponownie zalogować się do centrum administracyjnego usługi Azure AD, a nie powinna istnieć żadna wymagana akcja, a żadne produkty nie blokują usuwania w organizacji. Należy mieć możliwość pomyślnego usunięcia organizacji usługi Azure AD.

    ![Nazwa użytkownika jest nieprawidłowa lub nie została znaleziona](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Następne kroki

[Dokumentacja usługi Azure Active Directory](../index.yml)