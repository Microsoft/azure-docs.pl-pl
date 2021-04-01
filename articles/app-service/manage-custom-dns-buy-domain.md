---
title: Kupowanie niestandardowej nazwy domeny
description: Dowiedz się, jak kupić domenę App Service i użyć jej jako domeny niestandardowej dla Azure App Service aplikacji.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704858"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Kupowanie niestandardowej nazwy domeny dla usługi Azure App Service

Domeny App Service są domenami niestandardowymi zarządzanymi bezpośrednio na platformie Azure. Ułatwiają one Zarządzanie domenami niestandardowymi dla [Azure App Service](overview.md). W tym samouczku przedstawiono sposób kupowania domeny App Service i przypisywania nazw DNS do Azure App Service.

W przypadku maszyny wirtualnej platformy Azure lub usługi Azure Storage, zobacz [przypisywanie App Service domeny do maszyny wirtualnej platformy Azure lub usługi Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Aby uzyskać Cloud Services, zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](./index.yml) lub użyj aplikacji utworzonej w innym samouczku. Aplikacja powinna znajdować się w regionie publicznym platformy Azure. W tej chwili chmury narodowe platformy Azure nie są obsługiwane.
* [Usuń limit wydatków dla subskrypcji](../cost-management-billing/manage/spending-limit.md#remove). Nie można kupować App Service domen z bezpłatnymi środkami subskrypcji.

## <a name="buy-an-app-service-domain"></a>Kupowanie domeny App Service

Aby uzyskać informacje o cenach App Service domenach, odwiedź [stronę cennika App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) i przewiń w dół do App Service domeny.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **App Service domeny**.

    ![Nawigacja w portalu do domen Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. W widoku **domeny App Service** kliknij pozycję **Dodaj**.

    ![Kliknij pozycję Dodaj w obszarze domeny App Service](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Wybierz **pozycję kliknij, aby wypróbować nową wersję App Service domen**.

    ![Utwórz domenę App Service przy użyciu nowego środowiska](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **podstawy** Skonfiguruj ustawienia, korzystając z następującej tabeli:  

   | Ustawienie  | Opis |
   | -------- | ----------- |
   | **Subskrypcja** | Subskrypcja do użycia w celu zakupienia domeny. |
   | **Grupa zasobów** | Grupa zasobów, w której ma zostać umieszczona domena. Na przykład grupa zasobów, w której znajduje się aplikacja. |
   | **Domeny** | Wpisz żądaną domenę. Na przykład **contoso.com**. Jeśli domena, której potrzebujesz, jest niedostępna, możesz wybrać z listy sugestii dostępnych domen lub użyć innej domeny. |

    > [!NOTE]
    > Następujące [domeny najwyższego poziomu](https://wikipedia.org/wiki/Top-level_domain) są obsługiwane przez domeny App Service: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _BIZ_, _org.uk_ i _co.in_.
    >
    >
    
2. Po zakończeniu kliknij przycisk **Dalej: informacje kontaktowe**.

### <a name="contact-information-tab"></a>Karta informacje kontaktowe

1. Podaj informacje wymagane przez [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) do rejestracji domeny. 

    Ważne jest, aby wypełnić wszystkie wymagane pola o możliwie największej dokładności. Nieprawidłowe dane dotyczące informacji kontaktowych mogą spowodować niepowodzenie zakupu domeny.

1. Po zakończeniu kliknij przycisk **Dalej: Zaawansowane**.

### <a name="advanced-tab"></a>Karta Zaawansowane

1. Na karcie **Zaawansowane** Skonfiguruj ustawienia opcjonalne:  

   | Ustawienie  | Opis |
   | -------- | ----------- |
   | **Automatyczne odnawianie** | Domyślnie włączony. Twoja domena App Service jest zarejestrowana w przyrostach rocznych. Automatyczne odnawianie gwarantuje, że rejestracja domeny nie wygasa i że użytkownik zachowuje własność domeny. Subskrypcja platformy Azure zostanie automatycznie obciążona opłatą za rejestrację w domenie rocznej w momencie odnowienia. Aby zrezygnować z programu, wybierz pozycję **Wyłącz**. Jeśli automatyczne odnawianie jest wyłączone, można [je odnowić ręcznie](#renew-the-domain). |
   | **Ochrona prywatności** | Domyślnie włączony. Ochrona prywatności ukrywa informacje kontaktowe rejestracji domeny z bazy danych WHOIS. Ochrona prywatności jest już uwzględniona w rocznej opłacie za rejestrację domeny. Aby zrezygnować z programu, wybierz pozycję **Wyłącz**. |

2. Po zakończeniu kliknij przycisk **Dalej: Tagi**.

### <a name="finish"></a>Zakończ

1. Na karcie **Tagi** Ustaw odpowiednie Tagi dla domeny App Service. Nie jest wymagane tagowanie przy użyciu domen App Service, ale jest to [Funkcja platformy Azure, która ułatwia zarządzanie zasobami](../azure-resource-manager/management/tag-resources.md).

1. Kliknij przycisk **Dalej: przegląd + Utwórz**.

1. Na karcie **Recenzja + tworzenie** Sprawdź kolejność domen. Po zakończeniu kliknij pozycję **Gotowe**.

    > [!NOTE]
    > Domeny App Service używają GoDaddy do rejestracji domeny i Azure DNS do hostowania domen. Oprócz rocznej opłaty za rejestrację w domenie należy stosować opłaty za użycie Azure DNS. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Po zakończeniu rejestracji domeny zostanie wyświetlony przycisk **Przejdź do zasobu** . Wybierz go, aby wyświetlić stronę zarządzania.

    ![Utworzono App Service domeny. Przechodzenie do zasobu](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Teraz możesz przystąpić do przypisywania aplikacji App Service do tej domeny niestandardowej.

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zmapować niestandardową nazwę DNS na aplikację sieci Web, [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) aplikacji sieci Web musi być płatną warstwą (współdzielona, podstawowa, standardowa, Premium lub zużycie dla Azure Functions). W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Nawigowanie do aplikacji w witrynie Azure Portal

1. Na górnym pasku wyszukiwania Wyszukaj i wybierz pozycję **App Services**.

    ![Wyszukaj App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Wybierz nazwę aplikacji.

    ![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.  

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

1. W lewym obszarze nawigacji na stronie aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)**.

    ![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1**. Niestandardowe nazwy DNS nie są obsługiwane w warstwie **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Zrzut ekranu przedstawiający menu nawigacji po lewej stronie aplikacji z wybranym przyciskiem Skaluj w górę (plan App Service).":::

1. Jeśli plan App Service nie znajduje się w warstwie **F1** , zamknij stronę **skalowanie w górę** i przejdź do [opcji Kup domenę](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

1. Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

1. Kliknij pozycję **Zastosuj**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Zrzut ekranu przedstawiający warstwy cenowe niestandardowych domen w kategorii produkcja z zakładkami produkcja, plan B1 i przycisk Zastosuj wyróżniony.":::

    Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

    ![Potwierdzenie operacji skalowania](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Mapowanie domeny App Service do aplikacji

Można łatwo zmapować nazwę hosta w domenie App Service do aplikacji App Service, o ile jest ona w tej samej subskrypcji. Zamapujesz domenę App Service lub dowolną z jej poddomeny bezpośrednio w aplikacji, a platforma Azure utworzy wymagane rekordy DNS.

> [!NOTE]
> Jeśli domena i aplikacja znajdują się w różnych subskrypcjach, zamapuj domenę App Service do aplikacji podobnie jak [Mapowanie domeny zakupionej zewnętrznie](app-service-web-tutorial-custom-domain.md). W takim przypadku Azure DNS jest dostawcą domeny zewnętrznej i trzeba [ręcznie dodać wymagane rekordy DNS](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mapuj domenę

1. W lewym panelu nawigacyjnym strony aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **domeny niestandardowe**.

    ![Zrzut ekranu pokazujący menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie elementu nazwa hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz domenę App Service (na przykład **contoso.com**) lub poddomenę (na przykład **www.contoso.com**), a następnie kliknij przycisk **Weryfikuj**.

    > [!NOTE]
    > Jeśli w nazwie domeny App Service została wprowadzona literówka, w dolnej części strony pojawi się komunikat o błędzie weryfikacji informującej o braku niektórych rekordów DNS. Nie musisz ręcznie dodawać tych rekordów dla domeny App Service. Upewnij się, że nazwa domeny została wpisana poprawnie, a następnie ponownie kliknij przycisk **Weryfikuj** .
    >
    > ![Zrzut ekranu pokazujący błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Zaakceptuj **Typ rekordu nazwy hosta** i kliknij pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący przycisk Dodaj domenę niestandardową.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu pokazujący Dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Etykieta **niezabezpieczona** dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Testowanie domeny niestandardowej

Aby przetestować domenę niestandardową, przejdź do jej w przeglądarce.

## <a name="renew-the-domain"></a>Odnów domenę

Zakupiona domena App Service jest ważna przez rok od momentu zakupu. Domyślnie domena jest konfigurowana do odnawiania automatycznie przez załadowanie metody płatności w następnym roku. Nazwę domeny można ręcznie odnowić.

Jeśli chcesz wyłączyć automatyczne odnawianie lub jeśli chcesz ręcznie odnowić domenę, wykonaj kroki opisane tutaj.

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **App Service domeny**.

    ![Nawigacja w portalu do domen Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. W sekcji **domeny App Service** wybierz domenę, którą chcesz skonfigurować.

1. W lewym panelu nawigacyjnym domeny wybierz pozycję **odnowienie domeny**. Aby wyłączyć automatyczne odnawianie domeny, wybierz pozycję **wyłączone**. To ustawienie jest stosowane od razu.

    ![Zrzut ekranu pokazujący opcję automatycznego odnowienia domeny.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Podczas nawigowania ze strony zignoruj komunikat o błędzie "niezapisane zmiany zostaną odrzucone", klikając przycisk **OK**.
    >

Aby ręcznie odnowić domenę, wybierz pozycję **Odnów domenę**. Jednak ten przycisk nie jest aktywny do [90 dni przed wygaśnięciem domeny](#when-domain-expires).

Jeśli odnowienie domeny zakończyło się pomyślnie, otrzymasz powiadomienie e-mail w ciągu 24 godzin.

## <a name="when-domain-expires"></a>Po wygaśnięciu domeny

Platforma Azure zajmuje się wygaśniemi lub wygasłymi domenami App Service w następujący sposób:

* Jeśli automatyczne odnawianie jest wyłączone: 90 dni przed wygaśnięciem domeny, zostanie do Ciebie wysłana wiadomość e-mail z powiadomieniem o odnowieniu, a przycisk **Odnów domenę** zostanie aktywowany w portalu.
* Jeśli automatyczne odnawianie jest włączone: w dniu po dacie wygaśnięcia domeny, platforma Azure podejmie próbę naliczania opłat za odnowienie nazwy domeny.
* Jeśli wystąpi błąd podczas automatycznego odnawiania (na przykład Twoja karta w pliku wygasła) lub jeśli automatyczne odnawianie jest wyłączone i zezwolisz na wygaśnięcie domeny, system Azure powiadamia użytkownika o wygaśnięciu domeny i parkach nazwy domeny. Możesz [ręcznie odnowić](#renew-the-domain) domenę.
* W dniu 4 i 12 dni po wygaśnięciu na platformie Azure są wysyłane dodatkowe wiadomości e-mail z powiadomieniami. Możesz [ręcznie odnowić](#renew-the-domain) domenę. W piątym dniu po wygaśnięciu, rozpoznawanie nazw DNS zostaje zatrzymane dla wygasłej domeny.
* W dniu 19 po wygaśnięciu domena pozostaje wstrzymana, ale podlega opłacie za realizację. Można skontaktować się z pomocą techniczną, aby odnowić nazwę domeny, z zastrzeżeniem odpowiednich opłat za odnowienie i umorzenie.
* W 25-dniowym dniu po wygaśnięciu platforma Azure umieszcza swoją domenę na potrzeby aukcji za pomocą usługi aukcyjnej o nazwie domeny. Można skontaktować się z pomocą techniczną, aby odnowić nazwę domeny, z zastrzeżeniem odpowiednich opłat za odnowienie i umorzenie.
* Na 30 dzień po wygaśnięciu nie można już zrealizować Twojej domeny.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Zarządzanie niestandardowymi rekordami DNS

Na platformie Azure rekordy DNS dla domeny App Service są zarządzane przy użyciu [Azure DNS](https://azure.microsoft.com/services/dns/). Rekordy DNS można dodawać, usuwać i aktualizować, podobnie jak w przypadku domeny zakupionej zewnętrznie. Aby zarządzać niestandardowymi rekordami DNS:

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **App Service domeny**.

    ![Nawigacja w portalu do domen Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. W sekcji **domeny App Service** wybierz domenę, którą chcesz skonfigurować.

1. Na stronie **Przegląd** wybierz pozycję **Zarządzaj rekordami DNS**.

    ![Zrzut ekranu pokazujący, gdzie uzyskać dostęp do rekordów DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Aby uzyskać informacje na temat edytowania rekordów DNS, zobacz [jak zarządzać strefy DNS w Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Anuluj zakup (Usuń domenę)

Po zakupieniu domeny App Service masz pięć dni, aby anulować zakup w celu uzyskania pełnego zwrotu pieniędzy. Po upływie pięciu dni można usunąć domenę App Service, ale nie będzie można jej odebrać.

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **App Service domeny**.

    ![Nawigacja w portalu do domen Azure App Service](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. W sekcji **domeny App Service** wybierz domenę, którą chcesz skonfigurować.

1. W lewym panelu nawigacyjnym domeny wybierz pozycję **Nazwa hosta powiązania**. W tym miejscu są wyświetlane powiązania nazwy hosta ze wszystkich usług platformy Azure.

    ![Zrzut ekranu przedstawiający stronę powiązania nazwy hosta.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Usuń każde powiązanie nazwy hosta, wybierając pozycję **...**  >  **Usuń**. Po usunięciu wszystkich powiązań wybierz pozycję **Zapisz**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. W lewym panelu nawigacyjnym domeny wybierz pozycję **Przegląd**. 

1. Jeśli okres anulowania w zakupionej domenie nie upłynął, wybierz pozycję **Anuluj zakup**. W przeciwnym razie zobaczysz przycisk **Usuń** . Aby usunąć domenę bez zwrotu pieniędzy, wybierz pozycję **Usuń**.

    ![Zrzut ekranu pokazujący miejsce usunięcia lub anulowania zakupionej domeny.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Potwierdź operację, wybierając pozycję **tak**.

    Po zakończeniu operacji domena zostanie wydzielona z subskrypcji i będzie dostępna dla każdego do zakupu. 

## <a name="direct-default-url-to-a-custom-directory"></a>Kierowanie domyślnego adresu URL do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Aby skierować je do podkatalogu, na przykład `public` , zobacz [przekierowanie do katalogu niestandardowego](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak powiązać niestandardowy certyfikat TLS/SSL z App Service.

> [!div class="nextstepaction"]
> [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS w Azure App Service](configure-ssl-bindings.md)
