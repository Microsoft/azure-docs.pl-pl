---
title: 'Samouczek: mapowanie istniejącej niestandardowej nazwy DNS'
description: Dowiedz się, jak dodać istniejącą niestandardową nazwę domeny DNS (domenę niestandardową) do aplikacji internetowej, aplikacji mobilnej lub aplikacji interfejsu API w Azure App Service.
keywords: app service, azure app service, mapowanie domeny, nazwa domeny, istniejąca domena, nazwa hosta
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 8e310cb0507146eb53c7b55c2aaed492baa79521
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833254"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną i samonachowalną usługę hostingu w Internecie. W tym samouczku pokazano, jak zamapować istniejącą niestandardową nazwę systemu nazw domen (DNS) na App Service.

![Zrzut ekranu przedstawiający Azure Portal nawigacji do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny (na przykład `www.contoso.com` ) przy użyciu rekordu CNAME.
> * Mapowanie domeny głównej (na przykład `contoso.com` ) przy użyciu rekordu A.
> * Mapowanie domeny z symbolami wieloznacznych (na przykład `*.contoso.com` ) przy użyciu rekordu CNAME.
> * Przekieruj domyślny adres URL do katalogu niestandardowego.
> * Zautomatyzuj mapowanie domeny za pomocą skryptów.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](./index.yml) lub użyj aplikacji utworzonej w innym samouczku.
* Kup nazwę domeny i upewnij się, że masz dostęp do rejestru DNS dostawcy domeny (takiego jak GoDaddy).

  Aby na przykład dodać wpisy DNS dla domen `contoso.com` i `www.contoso.com`, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny katalogu głównego `contoso.com`.

  > [!NOTE]
  > Jeśli nie masz istniejącej nazwy domeny, rozważ zakup domeny przy użyciu usługi [Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zamapować niestandardową nazwę DNS na aplikację internetową, plan usługi [App Service](https://azure.microsoft.com/pricing/details/app-service/) aplikacji internetowej musi być warstwą płatną (Współdzielona, Podstawowa, Standardowa, Premium lub Zużycie dla Azure Functions). W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > Usunięcie lub dodanie domeny niestandardowej do aplikacji internetowej spowoduje ponowne uruchomienie witryny.
### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz [Azure Portal](https://portal.azure.com)i zaloguj się przy użyciu konta platformy Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Wybierz aplikację w Azure Portal

1. Wyszukaj i wybierz **pozycję App Services**.

   ![Zrzut ekranu przedstawiający wybieranie App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na **App Services** wybierz nazwę aplikacji platformy Azure.

   ![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

1. W lewym okienku strony aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję Skaluj w górę **(App Service planu).**

   ![Zrzut ekranu przedstawiający menu Skaluj w górę (App Service planu).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1.** Niestandardowy system DNS nie jest obsługiwany w **warstwie F1.**

   ![Zrzut ekranu przedstawiający zalecane warstwy cenowe.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Jeśli plan App Service nie znajduje się w warstwie **F1,** zamknij stronę Skalowanie w górę i przejdź do tematu  [Mapowanie rekordu CNAME.](#map-a-cname-record)

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

1. Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, wybierz **pozycję Zobacz dodatkowe opcje.**

1. Wybierz przycisk **Zastosuj**.

   ![Zrzut ekranu przedstawiający sprawdzanie warstwy cenowej.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

   ![Zrzut ekranu przedstawiający potwierdzenie operacji skalowania.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Uzyskiwanie identyfikatora weryfikacji domeny

Aby dodać domenę niestandardową do aplikacji, musisz zweryfikować własność domeny, dodając identyfikator weryfikacyjny jako rekord TXT u dostawcy domeny. W lewym okienku strony aplikacji wybierz pozycję **Domeny niestandardowe.** Skopiuj identyfikator z pola **Custom Domain Identyfikator weryfikacji** na stronie **Domeny** niestandardowe w następnym kroku.

![Zrzut ekranu przedstawiający identyfikator w polu Custom Domain identyfikator weryfikacji.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Dodanie identyfikatorów weryfikacji domeny do domeny niestandardowej może uniemożliwić blokowanie nieaktualnych wpisów DNS i unikanie przejęcia poddomeny. W przypadku domen niestandardowych skonfigurowanych wcześniej bez tego identyfikatora weryfikacji należy chronić je przed tym samym ryzykiem przez dodanie identyfikatora weryfikacji do rekordu DNS. Aby uzyskać więcej informacji na temat tego typowego zagrożenia o wysokiej ważności, zobacz [Poddomena przejęcia](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Mapowanie domeny

Do mapowania niestandardowej nazwy DNS na usługę App Service możesz użyć rekordu CNAME lub rekordu A. Postępuj zgodnie z odpowiednimi instrukcjami:

- [Mapowanie rekordu CNAME](#map-a-cname-record)
- [Mapowanie rekordu A](#map-an-a-record)
- [Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME](#map-a-wildcard-domain)

> [!NOTE]
> W przypadku wszystkich niestandardowych nazw DNS z wyjątkiem domen katalogu głównego (na przykład `contoso.com`) należy używać rekordów CNAME. Dla domen katalogu głównego należy używać rekordów A.

### <a name="map-a-cname-record"></a>Mapowanie rekordu CNAME

W przykładzie znajdującym się w tym samouczku dodasz rekord CNAME dla poddomeny `www` (na przykład `www.contoso.com`).

Jeśli masz poddomenę inną niż , zastąp nazwą swojej poddomeny (na przykład , jeśli domena `www` `www` `sub` niestandardowa to `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS przy użyciu dostawcy domeny

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Zamapuj poddomenę na domyślną nazwę domeny aplikacji ( `<app-name>.azurewebsites.net` , gdzie to nazwa `<app-name>` aplikacji). Aby utworzyć mapowanie CNAME dla `www` poddomeny, utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid.www` | [Identyfikator weryfikacji, który został wcześniej nadany](#get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

Po dodaniu rekordów CNAME i TXT strona rekordów DNS wygląda podobnie do następującego przykładu:

![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Włączanie mapowania rekordów CNAME na platformie Azure

1. W okienku po lewej stronie aplikacji w okienku Azure Portal wybierz **pozycję Domeny niestandardowe.**

    ![Zrzut ekranu przedstawiający menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stronie **Domeny niestandardowe** aplikacji dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS ( `www.contoso.com` ).

1. Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający element Dodaj nazwę hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład `www.contoso.com`.

1. Wybierz przycisk **Weryfikuj**. Zostanie **wyświetlona strona Dodawanie domeny** niestandardowej.

1. Upewnij się, **że typ rekordu Nazwa hosta** jest ustawiony na wartość **CNAME (www \. example.com lub dowolna poddomena).** Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający przycisk Dodaj domenę niestandardową.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Może mieć trochę czasu, aby nowa domena niestandardowa została odzwierciedlona na stronie Domeny **niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu przedstawiający dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Etykieta ostrzegawcza dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL. Każde żądanie PROTOKOŁU HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie protokołu TLS, zobacz [Secure a custom DNS name with a TLS/SSL binding in Azure App Service](configure-ssl-bindings.md)(Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL w Azure App Service ).

    Jeśli pominięto krok lub zrobiliśmy literówkę wcześniej, w dolnej części strony pojawi się błąd weryfikacji.

    ![Zrzut ekranu przedstawiający błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapowanie rekordu A

W przykładzie znajdującym się w tym samouczku dodasz rekord A dla domeny katalogu głównego (na przykład `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Kopiowanie adresu IP aplikacji

Aby móc zamapować rekord A, musisz znać zewnętrzny adres IP aplikacji. Ten adres IP można znaleźć na stronie Domeny niestandardowe aplikacji **w** Azure Portal.

1. W lewym okienku strony aplikacji w okienku aplikacji wybierz Azure Portal **domeny niestandardowe.**

   ![Zrzut ekranu przedstawiający menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

   ![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS przy użyciu dostawcy domeny

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Tworzenie rekordu A

Aby zamapować rekord A na aplikację, zazwyczaj na domenę główną, utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) | Samo mapowanie domeny `@` (zazwyczaj reprezentuje domenę główną). |
| TXT | `asuid` | [Identyfikator weryfikacji, który został wcześniej nadany](#get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. W przypadku domeny głównej użyj nazwy `asuid` . |

> [!NOTE]
> Aby dodać poddomenę (na przykład ) przy użyciu rekordu A zamiast zalecanego rekordu CNAME, rekord A i rekord TXT powinny wyglądać podobnie do `www.contoso.com` poniższej tabeli: [](#map-a-cname-record)
>
> | Typ rekordu | Host | Wartość |
> | - | - | - |
> | A | `www` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
> | TXT | `asuid.www` | [Identyfikator weryfikacji, który został wcześniej nadany](#get-a-domain-verification-id) |
>

Po dodaniu rekordów strona rekordy DNS wygląda podobnie do poniższego przykładu:

![Zrzut ekranu przedstawiający stronę rekordów DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Włączanie mapowania rekordu A w aplikacji

Na stronie Domeny  niestandardowe aplikacji w Azure Portal dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS `contoso.com` (na przykład ).

1. Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający dodawanie nazwy hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowaną nazwę domeny, dla której skonfigurowano rekord A, na przykład `contoso.com`. 

1. Wybierz przycisk **Weryfikuj**. Zostanie **pokazana strona Dodawanie** domeny niestandardowej.

1. Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **Rekord A (example.com)**. Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Może mieć trochę czasu, aby nowa domena niestandardowa została odzwierciedlona na stronie Domeny **niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu przedstawiający dodawanie rekordu A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Etykieta ostrzegawcza dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL. Każde żądanie HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie protokołu TLS, zobacz [Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL](configure-ssl-bindings.md)w Azure App Service .
    
    Jeśli pominięto krok lub gdzieś wcześniej zrobiliśmy literówkę, w dolnej części strony zostanie wyświetlony błąd weryfikacji.
    
    ![Zrzut ekranu przedstawiający błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapowanie domeny z symbolami wieloznacznymi

W przykładzie znajdującym się w tym samouczku zmapujesz [wieloznaczną nazwę DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (na przykład `*.contoso.com`) na aplikację usługi App Service przez dodanie rekordu CNAME.

#### <a name="access-dns-records-with-a-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS za pomocą dostawcy domeny

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Zamapuj nazwę symbolu wieloznacznego na domyślną nazwę `*` domeny aplikacji ( , gdzie to nazwa `<app-name>.azurewebsites.net` `<app-name>` aplikacji). Aby zamapować nazwę symbolu wieloznacznego, utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid` | [Identyfikator weryfikacji, który został wcześniej nadany](#get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

Dla przykładowej domeny `*.contoso.com` rekord CNAME zmapuje nazwę `*` na nazwę `<app-name>.azurewebsites.net`.

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Zrzut ekranu przedstawiający nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Włączanie mapowania rekordów CNAME w aplikacji

Do aplikacji można teraz dodać dowolną poddomenę, która jest taka sama jak nazwa symbolu wieloznacznego (na przykład `sub1.contoso.com` , i obie te nazwy są `sub2.contoso.com` `*.contoso.com` `*.contoso.com` zgodne).

1. W lewym okienku strony aplikacji w okienku aplikacji wybierz Azure Portal **domeny niestandardowe.**

    ![Zrzut ekranu przedstawiający menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający dodawanie nazwy hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowany nazwę domeny, która pasuje do domeny z symbolami wieloznacznymi (na przykład `sub1.contoso.com`), a następnie wybierz przycisk **Weryfikuj**.

    Przycisk **Dodaj domenę niestandardową** został aktywowany.

1. Upewnij się, **że typ rekordu Nazwa hosta** jest ustawiony na rekord **CNAME (www \. example.com lub dowolną poddomenę).** Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Może mieć trochę czasu, aby nowa domena niestandardowa została odzwierciedlona na stronie Domeny **niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

1. Wybierz ponownie **+** ikonę , aby dodać inną domenę niestandardową, która pasuje do domeny z symbolami wieloznacznymi. Na przykład dodaj `sub2.contoso.com`.

    ![Zrzut ekranu przedstawiający dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL. Każde żądanie PROTOKOŁU HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie protokołu TLS, zobacz [Secure a custom DNS name with a TLS/SSL binding in Azure App Service](configure-ssl-bindings.md)(Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL w Azure App Service ).
    
## <a name="test-in-a-browser"></a>Testowanie w przeglądarce

Przejdź do skonfigurowanych wcześniej nazw DNS (na przykład `contoso.com` , `www.contoso.com` , i `sub1.contoso.com` `sub2.contoso.com` ).

![Zrzut ekranu przedstawiający nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Rozwiązywanie problemu 404 "Nie znaleziono"

Jeśli podczas przeglądania adresu URL domeny niestandardowej zostanie wyświetlony błąd HTTP 404 (Nie znaleziono), sprawdź, czy domena jest rozpoznawana jako adres IP aplikacji przy użyciu nslookup.io <a href="https://www.nslookup.io/" target="_blank">.</a> Jeśli nie, sprawdź, czy rekordy A i CNAME są poprawnie skonfigurowane przy użyciu tej samej lokacji. Jeśli adres IP zostanie poprawnie rozpoznany, ale nadal jest wyświetlany adres 404, przeglądarka może buforować stary adres IP domeny. Wyczyść pamięć podręczną i ponownie przetestuj rozpoznawanie nazw DNS. Do czyszczenia pamięci podręcznej na komputerze z systemem Windows należy użyć polecenia `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrowanie aktywnej domeny

Aby przeprowadzić migrację aktywnej witryny oraz jej nazwy domeny DNS do usługi App Service bez przestojów, zobacz [Migrate an active DNS name to Azure App Service](manage-custom-dns-migrate-domain.md) (Migrowanie aktywnej nazwy DNS do usługi Azure App Service).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Przekierowywanie do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Jednak niektóre struktury internetowe nie są rozpoczynane w katalogu głównym. Na przykład platforma [Laravel](https://laravel.com/) uruchamia się w podkatalogu `public`. Aby kontynuować przykład DNS, taka aplikacja jest dostępna pod adres , ale zamiast tego chcesz `contoso.com` `http://contoso.com/public` skierować `http://contoso.com` do `public` katalogu. Ten krok nie obejmuje rozpoznawania nazw DNS, ale dotyczy dostosowywania katalogu wirtualnego.

Aby dostosować katalog wirtualny dla aplikacji systemu Windows, wybierz **pozycję Ustawienia aplikacji** w lewym okienku strony aplikacji internetowej. 

> [!NOTE]
> Aplikacje systemu Linux nie mają tej strony. Aby zmienić katalog główny witryny dla aplikacji systemu Linux, zobacz przewodniki dotyczące konfiguracji specyficzne dla języka (na przykład[PHP).](configure-language-php.md?pivots=platform-linux#change-site-root)

W dolnej części strony widać, że główny katalog wirtualny `/` domyślnie wskazuje na katalog `site\wwwroot`, który jest katalogiem głównym kodu aplikacji. Zmień to ustawienie, aby główny katalog wirtualny wskazywał na przykład na katalog `site\wwwroot\public`, i zapisz zmiany.

![Zrzut ekranu przedstawiający dostosowywanie katalogu wirtualnego.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po zakończeniu operacji aplikacja powinna zwrócić prawą stronę w ścieżce głównej (na przykład `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie domenami niestandardowymi za pomocą skryptów można zautomatyzować przy użyciu interfejsu wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) [lub Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie dodaje skonfigurowaną niestandardową nazwę DNS do aplikacji usługi App Service.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Aby uzyskać więcej informacji, zobacz [Map a custom domain to a web app](scripts/cli-configure-custom-domain.md) (Mapowanie domeny niestandardowej na aplikację internetową).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie dodaje skonfigurowaną niestandardową nazwę DNS do aplikacji usługi App Service.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Aby uzyskać więcej informacji, zobacz [Assign a custom domain to a web app](scripts/powershell-configure-custom-domain.md) (Przypisywanie domeny niestandardowej do aplikacji internetowej).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny przy użyciu rekordu CNAME.
> * Mapowanie domeny głównej przy użyciu rekordu A.
> * Mapowanie domeny z symbolami wieloznacznych przy użyciu rekordu CNAME.
> * Przekieruj domyślny adres URL do katalogu niestandardowego.
> * Zautomatyzuj mapowanie domeny za pomocą skryptów.

Przejdź do następnego samouczka, aby dowiedzieć się, jak powiązać niestandardowy certyfikat TLS/SSL z aplikacją internetową.

> [!div class="nextstepaction"]
> [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania protokołu TLS/SSL w Azure App Service](configure-ssl-bindings.md)
