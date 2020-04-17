---
title: Kupowanie niestandardowej nazwy domeny
description: Dowiedz się, jak kupić domenę usługi App Service i używać jej jako domeny niestandardowej dla aplikacji Usługi Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 47daf4ecd034c390a1460610e78d4fffd9277ac7
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535710"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Kupowanie niestandardowej nazwy domeny dla usługi Azure App Service

Domeny usługi App Service to domeny najwyższego poziomu, którymi zarządza się bezpośrednio na platformie Azure. Ułatwiają one zarządzanie domenami niestandardowymi usługi [Azure App Service.](overview.md) W tym samouczku pokazano, jak kupić domenę usługi App Service i przypisać nazwy DNS do usługi Azure App Service.

W przypadku maszyny Wirtualnej platformy Azure lub usługi Azure Storage zobacz [Przypisywanie domeny usługi app service do maszyny Wirtualnej platformy Azure lub usługi Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). W przypadku usług w chmurze zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.
* [Usuń limit wydatków na subskrypcję](../cost-management-billing/manage/spending-limit.md#remove). Nie można kupić domen usługi App Service z bezpłatnymi kredytami subskrypcyjnymi.

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Aby korzystać z domen niestandardowych w usłudze Azure App Service, [plan usługi app service](https://azure.microsoft.com/pricing/details/app-service/) musi być warstwą płatną **(Wspólna,** **Podstawowa,** **Standardowa**lub **Premium).** W tym kroku upewnij się, że aplikacja znajduje się w obsługiwanej warstwie cenowej.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Nawigowanie do aplikacji w witrynie Azure Portal

W lewym menu wybierz pozycję **App Services**, a następnie wybierz nazwę swojej aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.  

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W lewym obszarze nawigacji na stronie aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)**.

![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1**. Niestandardowe nazwy DNS nie są obsługiwane w warstwie **F1**. 

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Jeśli plan usługi app service nie znajduje się w warstwie **F1,** zamknij stronę **Skalowanie w górę** i przejdź do [opcji Kup domenę](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij przycisk **Zastosuj**.

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Potwierdzenie operacji skalowania](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Kup domenę

### <a name="pricing-information"></a>Informacje o cenach
Aby uzyskać informacje o cenach w domenach usługi Azure App Service, odwiedź [stronę Cennik usługi app service](https://azure.microsoft.com/pricing/details/app-service/windows/) i przewiń w dół do domeny usługi app service.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Otwórz witrynę [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu konta platformy Azure.

### <a name="launch-buy-domains"></a>Uruchamianie kupowania domen
Na karcie **Usługi aplikacji** kliknij nazwę aplikacji, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Domeny niestandardowe**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na stronie **Domeny niestandardowe** kliknij pozycję **Kup domenę**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Jeśli nie widzisz sekcji **Domeny usługi app service,** musisz usunąć limit wydatków na [koncie](#prerequisites)platformy Azure (zobacz Wymagania wstępne ).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurowanie zakupu domeny

Na stronie **Domena usługi app service** w polu **Wyszukaj domenę** wpisz nazwę domeny, którą chcesz kupić i wpisać `Enter`. Sugerowane dostępne domeny są wyświetlane tuż pod polem tekstowym. Wybierz jedną lub więcej domen, które chcesz kupić.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Następujące [domeny najwyższego poziomu](https://wikipedia.org/wiki/Top-level_domain) są obsługiwane przez domeny usługi App Service: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_i _co.in_.
>
>

Kliknij informacje **kontaktowe** i wypełnij formularz z informacjami kontaktowych domeny. Po zakończeniu kliknij przycisk **OK,** aby powrócić do domeny usługi app service.

Ważne jest, aby wypełnić wszystkie wymagane pola z jak największą dokładnością. Nieprawidłowe dane dotyczące informacji kontaktowych mogą spowodować niepowodzenie zakupu domen.

Następnie wybierz odpowiednie opcje dla domeny. Szczegółowe informacje można znaleźć w poniższej tabeli:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Ochrona prywatności | Włączanie | Zgłoś się do "Ochrony prywatności", która jest wliczona w cenę zakupu _za darmo._ Niektóre domeny najwyższego poziomu są zarządzane przez rejestratorów, którzy nie obsługują ochrony prywatności i są wymienione na stronie **Ochrona prywatności.** |
| Przypisywanie domyślnych nazwy hostów | **www** i**\@** | W razie potrzeby wybierz żądane powiązania nazwy hosta. Po zakończeniu operacji zakupu domeny aplikacja jest dostępna pod wybranymi nazwami hostów. Jeśli aplikacja znajduje się za [usługą Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nie widać opcji przypisywania domeny głównej (@), ponieważ usługa Traffic Manager nie obsługuje rekordów A. Zmiany w przypisaniach nazwy hosta można wprowadzać po zakończeniu zakupu domeny. |

### <a name="accept-terms-and-purchase"></a>Akceptuj warunki i zakup

Kliknij **pozycję Warunki prawne,** aby przejrzeć warunki i opłaty, a następnie kliknij pozycję **Kup**.

> [!NOTE]
> Domeny usługi app service używają goDaddy do rejestracji domeny i usługi Azure DNS do hostowania domen. Oprócz opłaty za rejestrację domeny obowiązują opłaty za użycie usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Cennik DNS platformy Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

Na stronie **Domena usługi app service** kliknij przycisk **OK**. Podczas operacji jest w toku, są wyświetlane następujące powiadomienia:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Przetestuj nazwy hostów

Jeśli do aplikacji przypisano domyślne nazwy hostów, zostanie wyświetlone również powiadomienie o powodach dla każdej wybranej nazwy hosta.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Wybrane nazwy hostów są również widoczne na stronie **Domeny niestandardowe** w sekcji **Niestandardowe nazwy hostów.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Etykieta **Nie bezpieczny** dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL, a każde żądanie HTTPS od przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby skonfigurować powiązanie TLS, zobacz [Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL w usłudze Azure App Service](configure-ssl-bindings.md).
>

Aby przetestować nazwy hostów, przejdź do wymienionych nazwy hostów w przeglądarce. W przykładzie na poprzednim zrzucie ekranu spróbuj przejść do _kontoso.net_ i _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Przypisywanie nazwy hostów do aplikacji

Jeśli nie chcesz przypisywać jednej lub więcej domyślnych nazwy hosta do aplikacji podczas procesu zakupu lub jeśli chcesz przypisać niena liście nazwy hosta, możesz przypisać nazwa hosta w dowolnym momencie.

Nazwy hostów w domenie usługi App Service można również przypisać do dowolnej innej aplikacji. Kroki zależą od tego, czy domena usługi app service i aplikacja należą do tej samej subskrypcji.

- Inna subskrypcja: mapuj niestandardowe rekordy DNS z domeny usługi app service na aplikację, jak na domenę zakupioną zewnętrznie. Aby uzyskać informacje dotyczące dodawania niestandardowych nazw DNS do domeny usługi app service, zobacz [Zarządzanie niestandardowymi rekordami DNS](#custom). Aby zamapować zewnętrzną kupiona domenę na aplikację, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Ta sama subskrypcja: użyj następujących kroków.

### <a name="launch-add-hostname"></a>Uruchom dodaj nazwa hosta
Na stronie **Usługi aplikacji** wybierz nazwę aplikacji, do której chcesz przypisać nazwy hostów, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Domeny niestandardowe**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Upewnij się, że zakupiona domena znajduje się na liście w sekcji **Domeny usługi aplikacji,** ale nie zaznacz jej. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Wszystkie domeny usługi app service w tej samej subskrypcji są wyświetlane na stronie **Domeny niestandardowe** aplikacji. Jeśli twoja domena jest w subskrypcji aplikacji, ale nie widać jej na stronie **Domeny niestandardowe** aplikacji, spróbuj ponownie otworzyć stronę **Domeny niestandardowe** lub odśwież stronę sieci Web. Ponadto sprawdź dzwonek powiadomień w górnej części witryny Azure portal dla postępu lub tworzenia błędów.
>
>

Wybierz przycisk **Dodaj nazwę hosta**.

### <a name="configure-hostname"></a>Konfigurowanie nazwy hosta
W oknie dialogowym **Dodawanie nazwy hosta** wpisz w pełni kwalifikowaną nazwę domeny usługi App Service lub dowolną poddomenę. Przykład:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Po zakończeniu wybierz **pozycję Sprawdź poprawność**. Typ rekordu nazwy hosta jest wybierany automatycznie.

Wybierz przycisk **Dodaj nazwę hosta**.

Po zakończeniu operacji zostanie wyświetlone powiadomienie o powodzenie przypisanej nazwy hosta.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zamknij dodaj nazwa hosta
Na stronie **Dodaj nazwy hosta** przypisz dowolne inne nazwy hosta do aplikacji zgodnie z potrzebami. Po zakończeniu zamknij stronę **Dodaj nazwy hosta.**

Nowo przypisane nazwy hosta powinny być teraz widoczne na stronie **Domeny niestandardowe** aplikacji.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Przetestuj nazwy hostów

Przejdź do wymienionych nazwy hostów w przeglądarce. W przykładzie na poprzednim zrzucie ekranu spróbuj przejść do _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Odnawianie domeny

Kupiona domena usługi app service jest ważna przez rok od momentu zakupu. Domyślnie domena jest skonfigurowana do automatycznego odnawiania, pobierając opłatę za metodę płatności za następny rok. Możesz ręcznie odnowić nazwę domeny.

Jeśli chcesz wyłączyć automatyczne odnawianie lub ręcznie odnowić domenę, wykonaj następujące czynności tutaj.

Na karcie **Usługi aplikacji** kliknij nazwę aplikacji, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Domeny niestandardowe**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

W sekcji **Domeny usługi aplikacji** wybierz domenę, którą chcesz skonfigurować.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Z lewej strony obszaru domeny wybierz pozycję **Odnawianie domeny**. Aby automatycznie zatrzymać odnawianie domeny, wybierz pozycję **Wyłączone**, a następnie **pozycję Zapisz**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Aby ręcznie odnowić domenę, wybierz pozycję **Odnów domenę**. Jednak ten przycisk nie jest aktywny do [90 dni przed wygaśnięciem domeny](#when-domain-expires).

Jeśli odnowienie domeny zakończy się pomyślnie, w ciągu 24 godzin otrzymasz powiadomienie e-mail.

## <a name="when-domain-expires"></a>Po wygaśnięciu domeny

Platforma Azure zajmuje się wygasającymi lub wygasłymi domenami usługi App Service w następujący sposób:

* Jeśli automatyczne odnawianie jest wyłączone: 90 dni przed wygaśnięciem domeny, zostanie wysłana wiadomość e-mail z powiadomieniem o odnowieniu, a przycisk **Odnów domenę** zostanie aktywowany w portalu.
* Jeśli automatyczne odnawianie jest włączone: w dniu po dacie wygaśnięcia domeny platforma Azure próbuje naliczone opłaty za odnowienie nazwy domeny.
* Jeśli podczas automatycznego odnawiania wystąpi błąd (na przykład karta w pliku wygasła) lub jeśli automatyczne odnawianie jest wyłączone i zezwolisz na wygaśnięcie domeny, platforma Azure powiadomi Cię o wygaśnięciu domeny i parkuje nazwę domeny. Możesz [ręcznie odnowić](#renew-the-domain) domenę.
* W 4 i 12 dni po wygaśnięciu platforma Azure wysyła dodatkowe wiadomości e-mail z powiadomieniami. Możesz [ręcznie odnowić](#renew-the-domain) domenę.
* 19 dnia po wygaśnięciu twoja domena pozostaje wstrzymana, ale podlega opłacie za wykup. Możesz zadzwonić do działu obsługi klienta, aby odnowić nazwę domeny, z zastrzeżeniem wszelkich stosownych opłat za odnowienie i wykup.
* 25 dnia po wygaśnięciu platforma Azure wystawia twoją domenę na aukcję za pomocą usługi aukcyjnej w branży nazw domen. Możesz zadzwonić do działu obsługi klienta, aby odnowić nazwę domeny, z zastrzeżeniem wszelkich stosownych opłat za odnowienie i wykup.
* 30 dnia po wygaśnięciu nie możesz już realizować domeny.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Zarządzanie niestandardowymi rekordami DNS

Na platformie Azure rekordy DNS dla domeny usługi app service są zarządzane przy użyciu [usługi Azure DNS](https://azure.microsoft.com/services/dns/). Można dodawać, usuwać i aktualizować rekordy DNS, podobnie jak w przypadku domeny zakupionej zewnętrznie.

### <a name="open-app-service-domain"></a>Otwórz domenę usługi aplikacji

W witrynie Azure portal z lewego menu wybierz pozycję **Wszystkie usługi** > **Domeny usługi app service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wybierz domenę do zarządzania. 

### <a name="access-dns-zone"></a>Dostęp do strefy DNS

W menu po lewej stronie domeny wybierz **pozycję Strefa DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ta akcja powoduje otwarcie strony [strefy DNS](../dns/dns-zones-records.md) domeny usługi App Service w usłudze Azure DNS. Aby uzyskać informacje dotyczące edytowania rekordów DNS, zobacz [Jak zarządzać strefami DNS w witrynie Azure portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Anuluj zakup (usuń domenę)

Po zakupie domeny usługi app service masz pięć dni na anulowanie zakupu w celu uzyskania pełnego zwrotu pieniędzy. Po pięciu dniach możesz usunąć domenę usługi App Service, ale nie możesz otrzymać zwrotu pieniędzy.

### <a name="open-app-service-domain"></a>Otwórz domenę usługi aplikacji

W witrynie Azure portal z lewego menu wybierz pozycję **Wszystkie usługi** > **Domeny usługi app service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wybierz domenę, którą chcesz anulować lub usunąć. 

### <a name="delete-hostname-bindings"></a>Usuwanie powiązań nazwy hosta

W menu po lewej stronie domeny wybierz polecenie **Powiązania nazwy hosta**. Powiązania nazwy hosta ze wszystkich usług platformy Azure są wymienione w tym miejscu.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Nie można usunąć domeny usługi app service, dopóki nie zostaną usunięte wszystkie powiązania nazwy hosta.

Usuń każde powiązanie nazwy hosta, wybierając **...**  >  **Usuń**plik . Po usunięciu wszystkich powiązań wybierz pozycję **Zapisz**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Anulowanie lub usunięcie

W menu po lewej stronie domeny wybierz pozycję **Przegląd**. 

Jeśli okres anulowania w zakupionej domenie nie upłynął, wybierz **pozycję Anuluj zakup**. W przeciwnym razie zostanie wyświetlony przycisk **Usuń.** Aby usunąć domenę bez zwrotu pieniędzy, wybierz pozycję **Usuń**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Aby potwierdzić operację, **wybierz**tak .

Po zakończeniu operacji domena jest zwalniana z subskrypcji i dostępna dla każdego, kto może kupić ponownie. 

## <a name="direct-default-url-to-a-custom-directory"></a>Kierowanie domyślnego adresu URL do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Aby skierować je do podkatalogu, takiego jak `public`, zobacz Bezpośredni [domyślny adres URL do katalogu niestandardowego](app-service-web-tutorial-custom-domain.md#virtualdir).
