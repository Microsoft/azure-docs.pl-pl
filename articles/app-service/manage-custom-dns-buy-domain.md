---
title: Kupowanie niestandardowej nazwy domeny
description: Dowiedz się, jak kupić domenę App Service i użyć jej jako domeny niestandardowej dla Azure App Service aplikacji.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 0111da00962f267387e66978952e8a7c9f5d7308
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970049"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Kupowanie niestandardowej nazwy domeny dla usługi Azure App Service

Domeny App Service są domen najwyższego poziomu, które są zarządzane bezpośrednio na platformie Azure. Ułatwiają one Zarządzanie domenami niestandardowymi dla [Azure App Service](overview.md). W tym samouczku przedstawiono sposób kupowania domeny App Service i przypisywania nazw DNS do Azure App Service.

W przypadku maszyny wirtualnej platformy Azure lub usługi Azure Storage, zobacz [przypisywanie App Service domeny do maszyny wirtualnej platformy Azure lub usługi Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Aby uzyskać Cloud Services, zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](./index.yml) lub użyj aplikacji utworzonej w innym samouczku.
* [Usuń limit wydatków dla subskrypcji](../cost-management-billing/manage/spending-limit.md#remove). Nie można kupować App Service domen z bezpłatnymi środkami subskrypcji.

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Aby można było używać domen niestandardowych w Azure App Service, [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) aplikacji musi być płatną warstwą **(współdzielona**, **podstawowa**, **standardowa**lub **Premium**). W tym kroku upewnij się, że aplikacja jest w obsługiwanej warstwie cenowej.

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

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Zrzut ekranu przedstawiający menu nawigacji po lewej stronie aplikacji z wybranym przyciskiem Skaluj w górę (plan App Service).":::

Jeśli plan App Service nie znajduje się w warstwie **F1** , zamknij stronę **skalowanie w górę** i przejdź do [opcji Kup domenę](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij pozycję **Zastosuj**.

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Zrzut ekranu przedstawiający menu nawigacji po lewej stronie aplikacji z wybranym przyciskiem Skaluj w górę (plan App Service).":::

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Potwierdzenie operacji skalowania](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Kupowanie domeny

### <a name="pricing-information"></a>Informacje o cenach
Aby uzyskać informacje o cenach Azure App Service domenach, odwiedź [stronę cennika App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) i przewiń w dół do App Service domeny.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Otwórz witrynę [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu konta platformy Azure.

### <a name="launch-buy-domains"></a>Uruchamianie kupowania domen
Na karcie **App Services** kliknij nazwę aplikacji, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **domeny niestandardowe** .
   
![Zrzut ekranu, na którym są wyróżnione domeny niestandardowe.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na stronie **domeny niestandardowe** kliknij pozycję **Kup domenę**.

![Zrzut ekranu, na którym widać wyróżnione kupowanie domeny.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Jeśli nie widzisz sekcji **domen App Service** , musisz usunąć limit wydatków na koncie platformy Azure (zobacz [wymagania wstępne](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurowanie zakupu domeny

Na stronie **domena App Service** w polu **Wyszukaj domenę** wpisz nazwę domeny, którą chcesz kupić i wpisz `Enter` . Sugerowane dostępne domeny są wyświetlane tuż poniżej pola tekstowego. Wybierz co najmniej jedną domenę, którą chcesz kupić.

![Zrzut ekranu przedstawiający pole wyszukiwania domeny.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Następujące [domeny najwyższego poziomu](https://wikipedia.org/wiki/Top-level_domain) są obsługiwane przez domeny App Service: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _BIZ_, _org.uk_i _co.in_.
>
>

Kliknij **informacje kontaktowe** i wypełnij formularz informacje kontaktowe domeny. Po zakończeniu kliknij przycisk **OK** , aby powrócić do strony domena App Service.

Ważne jest, aby wypełnić wszystkie wymagane pola o możliwie największej dokładności. Nieprawidłowe dane dotyczące informacji kontaktowych mogą spowodować niepowodzenie zakupu domen.

Następnie wybierz odpowiednie opcje dla domeny. Zapoznaj się z poniższymi tabelami wyjaśnień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Ochrona prywatności | Włącz | Zezwól na "ochronę prywatności", która jest oferowana _bezpłatnie_w cenie zakupu. Niektóre domeny najwyższego poziomu są zarządzane przez rejestratory, które nie obsługują ochrony prywatności i są wyświetlane na stronie **Ochrona prywatności** . |
| Przypisywanie domyślnych nazw hostów | **www** i **\@** | Wybierz żądane powiązania nazwy hosta, jeśli jest to wymagane. Po zakończeniu operacji zakupu domeny dostęp do aplikacji można uzyskać na wybranych nazwach hostów. Jeśli aplikacja jest za [Traffic managerem platformy Azure](https://azure.microsoft.com/services/traffic-manager/), nie widzisz opcji przypisywania domeny głównej (@), ponieważ Traffic Manager nie obsługuje rekordów. Po zakończeniu zakupu domeny możesz wprowadzić zmiany w przypisaniach do nazwy hosta. |

### <a name="accept-terms-and-purchase"></a>Zaakceptuj warunki i zakup

Kliknij pozycję **postanowienia prawne** , aby przejrzeć warunki i opłaty, a następnie kliknij przycisk **Kup**.

> [!NOTE]
> Domeny App Service używają GoDaddy do rejestracji domeny i Azure DNS do hostowania domen. Oprócz opłaty za rejestrację domeny naliczane są opłaty za użycie Azure DNS. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
>
>

Na stronie **domena App Service** kliknij przycisk **OK**. Gdy operacja jest w toku, zobaczysz następujące powiadomienia:

![Zrzut ekranu przedstawiający komunikat walidacji w toku.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![Zrzut ekranu pokazujący powiadomienie o pomyślnym zakupie zakupu.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testowanie nazw hostów

Jeśli do aplikacji zostały przypisane domyślne nazwy hostów, zobaczysz również powiadomienie o powodzeniu dla każdej wybranej nazwy hosta.

![Zrzut ekranu pokazujący powiadomienie o powodzeniu dla każdej wybranej nazwy hosta.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Na stronie niestandardowe **nazwy** hostów **widoczne są także** wybrane nazwy hostów.

![Zrzut ekranu pokazujący wybrane nazwy hostów w sekcji niestandardowe nazwy hostów na stronie domeny niestandardowe.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Etykieta **niezabezpieczona** dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL, a wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby skonfigurować powiązanie protokołu TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).
>

Aby przetestować nazwy hostów, przejdź do listy nazw hostów w przeglądarce. W przykładzie na poprzednim zrzucie ekranu spróbuj przejść do _kontoso.NET_ i _www \. kontoso.NET_.

## <a name="assign-hostnames-to-app"></a>Przypisywanie nazw hostów do aplikacji

Jeśli nie przypiszesz co najmniej jednej domyślnej nazwy hosta do aplikacji podczas procesu zakupu lub jeśli musisz przypisać nazwę hosta, który nie jest wymieniony, możesz w dowolnym momencie przypisać nazwę hosta.

Nazwy hostów można także przypisywać w domenie App Service do dowolnej innej aplikacji. Kroki te zależą od tego, czy domena App Service i aplikacja należą do tej samej subskrypcji.

- Inna subskrypcja: Mapuj niestandardowe rekordy DNS z domeny App Service do aplikacji, takiej jak domena zakupiona zewnętrznie. Aby uzyskać informacje na temat dodawania niestandardowych nazw DNS do domeny App Service, zobacz [Zarządzanie niestandardowymi rekordami DNS](#custom). Aby zmapować zewnętrznie zakupioną domenę do aplikacji, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Ta sama subskrypcja: wykonaj poniższe kroki.

### <a name="launch-add-hostname"></a>Uruchom Dodawanie nazwy hosta
Na stronie **App Services** wybierz nazwę aplikacji, do której chcesz przypisać nazwy hostów, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **domeny niestandardowe**.

![Zrzut ekranu, na którym są wyróżnione domeny niestandardowe.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Upewnij się, że zakupiona domena znajduje się na liście w sekcji **domeny App Service** , ale nie wybierz ją. 

![Zrzut ekranu przedstawiający zakupioną domenę w sekcji domeny App Service.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Wszystkie domeny App Service w tej samej subskrypcji są wyświetlane na stronie **domeny niestandardowe** aplikacji. Jeśli Twoja domena znajduje się w subskrypcji aplikacji, ale nie możesz jej zobaczyć na stronie **domeny niestandardowe** aplikacji, spróbuj ponownie otworzyć stronę **domeny niestandardowe** lub Odśwież stronę sieci Web. Ponadto sprawdź, czy dzwonek powiadomień znajduje się na szczycie Azure Portal w przypadku błędów dotyczących postępu lub tworzenia.
>
>

Wybierz przycisk **Dodaj nazwę hosta**.

### <a name="configure-hostname"></a>Konfigurowanie nazwy hosta
W oknie dialogowym **Dodawanie nazwy hosta** wpisz w pełni kwalifikowaną nazwę domeny domeny App Service lub dowolna poddomena. Na przykład:

- kontoso.net
- \.kontoso.NET www
- abc.kontoso.net

Po zakończeniu wybierz pozycję **Weryfikuj**. Typ rekordu nazwy hosta jest automatycznie wybierany.

Wybierz przycisk **Dodaj nazwę hosta**.

Po zakończeniu operacji zobaczysz powiadomienie o powodzeniu dla przypisanej nazwy hosta.  

![Zrzut ekranu przedstawiający powiadomienie o powodzeniu dla przypisanej nazwy hosta.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zamknij Dodawanie nazwy hosta
Na stronie **Dodawanie nazwy hosta** Przypisz dowolną inną nazwę hosta do aplikacji zgodnie z potrzebami. Po zakończeniu zamknij stronę **Dodawanie nazwy hosta** .

Należy teraz zobaczyć nowo przypisane nazwy hostów na stronie **niestandardowych domen** aplikacji.

![Zrzut ekranu pokazujący nowo przypisane nazwy hostów na stronie niestandardowych domen aplikacji.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testowanie nazw hostów

Przejdź do listy nazw hostów w przeglądarce. W przykładzie na poprzednim zrzucie ekranu, spróbuj przejść do _ABC.kontoso.NET_.

## <a name="renew-the-domain"></a>Odnów domenę

Zakupiona domena App Service jest ważna przez rok od momentu zakupu. Domyślnie domena jest konfigurowana do odnawiania automatycznie przez załadowanie metody płatności w następnym roku. Nazwę domeny można ręcznie odnowić.

Jeśli chcesz wyłączyć automatyczne odnawianie lub jeśli chcesz ręcznie odnowić domenę, wykonaj kroki opisane tutaj.

Na karcie **App Services** kliknij nazwę aplikacji, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **domeny niestandardowe**.

![Zrzut ekranu, na którym są wyróżnione domeny niestandardowe.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

W sekcji **domeny App Service** wybierz domenę, którą chcesz skonfigurować.

![Zrzut ekranu przedstawiający zakupioną domenę w sekcji domeny App Service.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

W lewym panelu nawigacyjnym domeny wybierz pozycję **odnowienie domeny**. Aby zrezygnować z automatycznego odnawiania domeny, wybierz pozycję **wyłączone**, a następnie **Zapisz**.

![Zrzut ekranu pokazujący opcję automatycznego odnowienia domeny.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Aby ręcznie odnowić domenę, wybierz pozycję **Odnów domenę**. Jednak ten przycisk nie jest aktywny do [90 dni przed wygaśnięciem domeny](#when-domain-expires).

Jeśli odnowienie domeny zakończyło się pomyślnie, otrzymasz powiadomienie e-mail w ciągu 24 godzin.

## <a name="when-domain-expires"></a>Po wygaśnięciu domeny

Platforma Azure zajmuje się wygaśniemi lub wygasłymi domenami App Service w następujący sposób:

* Jeśli automatyczne odnawianie jest wyłączone: 90 dni przed wygaśnięciem domeny, zostanie do Ciebie wysłana wiadomość e-mail z powiadomieniem o odnowieniu, a przycisk **Odnów domenę** zostanie aktywowany w portalu.
* Jeśli automatyczne odnawianie jest włączone: w dniu po dacie wygaśnięcia domeny, platforma Azure podejmie próbę naliczania opłat za odnowienie nazwy domeny.
* Jeśli wystąpi błąd podczas automatycznego odnawiania (na przykład Twoja karta w pliku wygasła) lub jeśli automatyczne odnawianie jest wyłączone i zezwolisz na wygaśnięcie domeny, system Azure powiadamia użytkownika o wygaśnięciu domeny i parkach nazwy domeny. Możesz [ręcznie odnowić](#renew-the-domain) domenę.
* W dniu 4 i 12 dni po wygaśnięciu na platformie Azure są wysyłane dodatkowe wiadomości e-mail z powiadomieniami. Możesz [ręcznie odnowić](#renew-the-domain) domenę.
* W dniu 19 po wygaśnięciu domena pozostaje wstrzymana, ale podlega opłacie za realizację. Można skontaktować się z pomocą techniczną, aby odnowić nazwę domeny, z zastrzeżeniem odpowiednich opłat za odnowienie i umorzenie.
* W 25-dniowym dniu po wygaśnięciu platforma Azure umieszcza swoją domenę na potrzeby aukcji za pomocą usługi aukcyjnej o nazwie domeny. Można skontaktować się z pomocą techniczną, aby odnowić nazwę domeny, z zastrzeżeniem odpowiednich opłat za odnowienie i umorzenie.
* Na 30 dzień po wygaśnięciu nie można już zrealizować Twojej domeny.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Zarządzanie niestandardowymi rekordami DNS

Na platformie Azure rekordy DNS dla domeny App Service są zarządzane przy użyciu [Azure DNS](https://azure.microsoft.com/services/dns/). Rekordy DNS można dodawać, usuwać i aktualizować, podobnie jak w przypadku domeny zakupionej zewnętrznie.

### <a name="open-app-service-domain"></a>Otwórz domenę App Service

W Azure Portal z menu po lewej stronie wybierz pozycję **wszystkie usługi**  >  **App Service domeny**.

![Zrzut ekranu pokazujący, w jaki sposób uzyskać dostęp do App Service domen.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wybierz domenę, którą chcesz zarządzać. 

### <a name="access-dns-zone"></a>Dostęp do strefy DNS

W menu po lewej stronie domeny wybierz pozycję **strefa DNS**.

![Zrzut ekranu pokazujący lokalizację, w której ma zostać wybrana strefa DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ta akcja powoduje otwarcie strony [strefa DNS](../dns/dns-zones-records.md) domeny App Service w Azure DNS. Aby uzyskać informacje na temat edytowania rekordów DNS, zobacz [jak zarządzać strefy DNS w Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Anuluj zakup (Usuń domenę)

Po zakupieniu domeny App Service masz pięć dni, aby anulować zakup w celu uzyskania pełnego zwrotu pieniędzy. Po upływie pięciu dni można usunąć domenę App Service, ale nie będzie można jej odebrać.

### <a name="open-app-service-domain"></a>Otwórz domenę App Service

W Azure Portal z menu po lewej stronie wybierz pozycję **wszystkie usługi**  >  **App Service domeny**.

![Zrzut ekranu pokazujący, w jaki sposób uzyskać dostęp do App Service domen.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wybierz domenę, do której chcesz anulować lub usunąć. 

### <a name="delete-hostname-bindings"></a>Usuń powiązania nazwy hosta

W menu po lewej stronie domeny wybierz pozycję **powiązania nazwy hosta**. W tym miejscu są wyświetlane powiązania nazwy hosta ze wszystkich usług platformy Azure.

![Zrzut ekranu przedstawiający stronę powiązania nazwy hosta.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Nie można usunąć domeny App Service, dopóki nie zostaną usunięte wszystkie powiązania nazwy hosta.

Usuń każde powiązanie nazwy hosta, wybierając pozycję **...**  >  **Usuń**. Po usunięciu wszystkich powiązań wybierz pozycję **Zapisz**.

![Zrzut ekranu pokazujący, gdzie usunąć powiązania nazwy hosta.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Anuluj lub Usuń

W menu po lewej stronie domeny wybierz pozycję **Przegląd**. 

Jeśli okres anulowania w zakupionej domenie nie upłynął, wybierz pozycję **Anuluj zakup**. W przeciwnym razie zobaczysz przycisk **Usuń** . Aby usunąć domenę bez zwrotu pieniędzy, wybierz pozycję **Usuń**.

![Zrzut ekranu pokazujący miejsce usunięcia lub anulowania zakupionej domeny.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Aby potwierdzić operację, wybierz pozycję **tak**.

Po zakończeniu operacji domena zostanie wydzielona z subskrypcji i będzie dostępna dla każdego do zakupu. 

## <a name="direct-default-url-to-a-custom-directory"></a>Kierowanie domyślnego adresu URL do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Aby skierować je do podkatalogu, na przykład `public` , zobacz [bezpośredni domyślny adres URL do katalogu niestandardowego](app-service-web-tutorial-custom-domain.md#virtualdir).