---
title: Aktywowanie subskrypcji i kont platformy Azure
description: Umożliwianie dostępu za pomocą interfejsów API usługi Azure Resource Manager dla nowych i istniejących kont oraz rozwiązywanie typowych problemów dotyczących kont.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: secdec18
ROBOTS: NOINDEX
ms.openlocfilehash: b1b7ea7467be107bd1af9daf0869c77ff0b94c70
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537353"
---
# <a name="activate-azure-subscriptions-and-accounts-with-cloudyn"></a>Aktywowanie subskrypcji i kont platformy Azure za pomocą platformy Cloudyn

Dodawanie lub uaktualnianie poświadczeń usługi Azure Resource Manager umożliwia platformie Cloudyn odnajdowanie wszystkich kont i subskrypcji w ramach dzierżawy platformy Azure. Jeśli na swoich maszynach wirtualnych masz również włączone rozszerzenie Diagnostyka Azure, platforma Cloudyn może zbierać metryki rozszerzone, takie jak procesor i pamięć. W tym artykule opisano sposób włączania dostępu przy użyciu interfejsów API usługi Azure Resource Manager dla nowych i istniejących kont. Zawiera on również opis rozwiązywania typowych problemów dotyczących kont.

Platforma Cloudyn nie może uzyskiwać dostępu do większości danych subskrypcji platformy Azure, gdy subskrypcja jest _nieaktywowana_ . Aby platforma Cloudyn mogła uzyskiwać dostęp do kont _nieaktywowanych_ , należy je edytować.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="required-azure-permissions"></a>Wymagane uprawnienia platformy Azure

Do wykonania procedur opisanych w tym artykule potrzebne są określone uprawnienia. Użytkownik lub administrator dzierżawy musi mieć obydwa poniższe uprawnienia:

- Uprawnienia do rejestrowania aplikacji CloudynCollector w dzierżawie usługi Azure AD.
- Możliwość przypisywania aplikacji do roli w swoich subskrypcjach platformy Azure.

W subskrypcjach platformy Azure konta muszą mieć dostęp `Microsoft.Authorization/*/Write`, aby przypisywanie aplikacji CloudynCollector było możliwe. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator).

Jeśli konto ma przypisaną rolę **Współautor** , nie masz odpowiednich uprawnień do przypisywania tej aplikacji. W przypadku podjęcia próby przypisania aplikacji CloudynCollector do subskrypcji platformy Azure zostanie wyświetlony komunikat o błędzie.

### <a name="check-azure-active-directory-permissions"></a>Sprawdzanie uprawnień usługi Azure Active Directory

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal wybierz pozycję **Azure Active Directory** .
3. W usłudze Azure Active Directory wybierz pozycję **Ustawienia użytkownika** .
4. Sprawdź opcję **Rejestracje aplikacji** .
    - W przypadku ustawienia wartości na **Tak** użytkownicy inni niż administratorzy mogą rejestrować aplikacje usługi AD. To ustawienie oznacza, że każdy użytkownik w dzierżawie usługi Azure AD może zarejestrować aplikację.  
    ![Wybieranie opcji Rejestracje aplikacji w ustawieniach użytkownika](./media/activate-subs-accounts/app-register.png)
    - W przypadku ustawienia opcji **Rejestracje aplikacji** na **Nie** tylko użytkownicy administracyjni dzierżawy mogą rejestrować aplikacje usługi Azure Active Directory. Administrator dzierżawy musi zarejestrować aplikację CloudynCollector.


## <a name="add-an-account-or-update-a-subscription"></a>Dodawanie konta lub aktualizowanie subskrypcji

Dodając konto lub aktualizując subskrypcję przyznajesz platformie Cloudyn dostęp do danych platformy Azure.

### <a name="add-a-new-account-subscription"></a>Dodawanie nowego konta (subskrypcji)

1. W portalu Cloudyn kliknij symbol koła zębatego w prawym górnym rogu, a następnie wybierz pozycję **Cloud Accounts (Konta w chmurze)** .
2. Kliknij pozycję **Dodaj nowe konto** . Zostanie wyświetlone okno **Dodawanie nowego konta** . Wprowadź wymagane informacje.  
    ![Wprowadzanie wymaganych informacji w oknie Dodaj nowe konto](./media/activate-subs-accounts/add-new-account.png)

### <a name="update-a-subscription"></a>Aktualizowanie subskrypcji

1. Jeśli chcesz zaktualizować _nieaktywowaną_ subskrypcję, która już istnieje na platformie Cloudyn w rozwiązaniu do zarządzania kontami, kliknij symbol ołówka (edycja) z prawej strony nadrzędnego _identyfikatora GUID dzierżawy_ . Subskrypcje są grupowane w ramach dzierżawy nadrzędnej, należy więc unikać ich indywidualnego aktywowania.
    ![Wybieranie swojego identyfikatora dzierżawy w oknie Ponowne odnajdowanie subskrypcji](./media/activate-subs-accounts/existing-sub.png)
2. W razie potrzeby wprowadź identyfikator dzierżawy. Jeśli nie znasz identyfikatora dzierżawy, wykonaj następujące czynności, aby go znaleźć:
    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. W witrynie Azure Portal wybierz pozycję **Azure Active Directory** .
    3. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.
    4. Skopiuj identyfikator GUID katalogu. Ta wartość jest Twoim identyfikatorem dzierżawy.
    Aby uzyskać więcej informacji, zobacz [Get tenant ID (Pobieranie identyfikatora dzierżawy)](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
3. W razie potrzeby wybierz identyfikator stawki. Jeśli nie znasz identyfikatora stawki, wykonaj następujące czynności, aby go znaleźć:
    1. W prawym górnym rogu witryny Azure Portal kliknij informacje o użytkowniku, a następnie kliknij pozycję **Wyświetl mój rachunek** .
    2. W obszarze **Konto rozliczeniowe** kliknij pozycję **Subskrypcje** .
    3. W obszarze **Moje subskrypcje** wybierz subskrypcję.
    4. Identyfikator stawki jest wyświetlany w obszarze **identyfikatora oferty** . Skopiuj identyfikator oferty dla subskrypcji.
4. W oknie Dodawanie nowego konta (lub Edytowanie subskrypcji) kliknij pozycję **Zapisz** (lub **Dalej** ). Nastąpi przekierowywanie do witryny Azure Portal.
5. Zaloguj się do portalu. Kliknij pozycję **Akceptuj** , aby autoryzować moduł zbierający platformy Cloudyn do uzyskiwania dostępu do konta platformy Azure.

    Nastąpi przekierowanie do strony zarządzania kontami platformy Cloudyn, a subskrypcja zostanie zaktualizowana przy użyciu stanu konta **Aktywne** . W kolumnie Resource Manager powinien pojawić się symbol zielonego znacznika wyboru.

    Jeśli nie widzisz symbolu zielonego znacznika wyboru dla co najmniej jednej z subskrypcji, oznacza to, że nie masz uprawnień do tworzenia aplikacji czytnika (CloudynCollector) dla subskrypcji. Użytkownik z wyższymi uprawnieniami subskrypcji musi powtórzyć ten proces.

Obejrzyj film [Connecting to Azure Resource Manager with Cloudyn (Łączenie z usługą Azure Resource Manager przy użyciu platformy Cloudyn)](https://youtu.be/oCIwvfBB6kk), który przeprowadzi Cię przez proces.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Rozwiązywanie typowych problemów z pośrednią konfiguracją przedsiębiorstwa

Przy pierwszym użyciu portalu Cloudyn możesz zobaczyć poniższe komunikaty, jeśli jesteś użytkownikiem z umową Enterprise Agreement lub dostawcą rozwiązań w chmurze (CSP, Cloud Solution Provider):

- Komunikat *Wybrany klucz interfejsu API nie jest kluczem rejestracji najwyższego poziomu rejestrowania* wyświetlany w kreatorze **Konfigurowanie platformy Cloudyn** .
- Komunikat *Bezpośrednia rejestracja — nie* wyświetlany w portalu umowy Enterprise Agreement.
- *Nie znaleziono danych użycia z ostatnich 30 dni. Skontaktuj się z dystrybutorem i upewnij się, że dla konta platformy Azure włączono znaczniki* wyświetlane w portalu Cloudyn.

Poprzednie komunikaty oznaczają, że umowa Azure Enterprise Agreement została zakupiona od odsprzedawcy lub dostawcy rozwiązań w chmurze. Odsprzedawca lub dostawca rozwiązań w chmurze musi włączyć _znaczniki_ dla konta platformy Azure, aby można było wyświetlać dane na platformie Cloudyn.

Oto sposoby rozwiązywania tych problemów:

1. Odsprzedawca musi włączyć _znaczniki_ dla Twojego konta. Aby uzyskać instrukcje, zapoznaj się z dokumentem [Indirect Customer Onboarding Guide (Przewodnik dołączania klienta pośredniego)](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Wygenerujesz klucz umowy Azure Enterprise Agreement do użycia na platformie Cloudyn.

Przed wygenerowaniem klucza interfejsu API umowy Azure Enterprise Agreement w celu skonfigurowania platformy Cloudyn należy włączyć interfejs API rozliczeń platformy Azure, wykonując następujące instrukcje:

- [Overview of Reporting APIs for Enterprise customers (Omówienie interfejsów API raportowania dla klientów korporacyjnych)](../manage/enterprise-api.md)
- [Interfejs API raportowania w witrynie Microsoft Azure dla przedsiębiorstw](https://ea.azure.com/helpdocs/reportingAPI) w obszarze **Włączanie dostępu danych do interfejsu API**

Być może trzeba będzie również nadać administratorom działów, właścicielom kont i administratorom przedsiębiorstwa uprawnienia do _wyświetlania opłat_ przy użyciu interfejsu API rozliczeń.

Tylko administrator usług platformy Azure może włączyć platformę Cloudyn. Uprawnienia współadministratora są niewystarczające. Można jednak obejść wymaganie uprawnień administratora. Można zażądać, aby administrator usługi Azure Active Directory przyznał uprawnienia do autoryzacji **CloudynAzureCollector** za pomocą skryptu programu PowerShell. Poniższy skrypt udziela uprawnień do rejestrowania jednostki usługi Azure Active Directory **CloudynAzureCollector** .


```powershell
#THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#Tenant - enter your tenant ID or Name
$tenant = "<ReplaceWithYourTenantID>"

#Cloudyn Collector application ID
$appId = "83e638ef-7885-479f-bbe8-9150acccdb3d"

#URL to activate the consent screen
$url = "https://login.windows.net/"+$tenant+"/oauth2/authorize?api-version=1&response_type=code&client_id="+$appId+"&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2FCloudynJava&prompt=consent"

#Choose your browser, the default is Internet Explorer

#Chrome
#[System.Diagnostics.Process]::Start("chrome.exe", "--incognito $url")

#Firefox
#[System.Diagnostics.Process]::Start("firefox.exe","-private-window $url" )

#IExplorer
[System.Diagnostics.Process]::Start("iexplore.exe","$url -private" )

```

## <a name="next-steps"></a>Następne kroki

- Jeśli zadania z pierwszego samouczka dotyczącego platformy Cloudyn nie zostały jeszcze wykonane, zapoznaj się z nim w temacie [Review usage and costs (Przeglądanie użycia i kosztów)](tutorial-review-usage.md).