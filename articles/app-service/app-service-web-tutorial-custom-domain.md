---
title: 'Samouczek: mapowanie istniejącej niestandardowej nazwy DNS'
description: Dowiedz się, jak dodać istniejącą niestandardową nazwę domeny DNS (znaczącym Domain) do aplikacji sieci Web, zaplecza aplikacji mobilnej lub aplikacji interfejsu API w Azure App Service.
keywords: app service, azure app service, mapowanie domeny, nazwa domeny, istniejąca domena, nazwa hosta
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: b3ff1b344852d57f0effbd978c06aa617682ea4f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720333"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. W tym samouczku przedstawiono sposób mapowania istniejącej nazwy niestandardowego systemu nazw domen (DNS) na App Service.

![Zrzut ekranu pokazujący Azure Portal nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Mapowanie domeny podrzędnej (na przykład `www.contoso.com` ) przy użyciu rekordu CNAME.
> * Mapowanie domeny katalogu głównego (na przykład `contoso.com` ) przy użyciu rekordu a.
> * Mapowanie domeny z symbolami wieloznacznymi (na przykład `*.contoso.com` ) przy użyciu rekordu CNAME.
> * Przekieruj domyślny adres URL do katalogu niestandardowego.
> * Automatyzowanie mapowania domeny za pomocą skryptów.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](./index.yml) lub użyj aplikacji utworzonej w innym samouczku.
* Kup nazwę domeny i upewnij się, że masz dostęp do rejestru DNS dla dostawcy domeny (np. GoDaddy).

  Aby na przykład dodać wpisy DNS dla domen `contoso.com` i `www.contoso.com`, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny katalogu głównego `contoso.com`.

  > [!NOTE]
  > Jeśli nie masz istniejącej nazwy domeny, rozważ [zakupienie domeny przy użyciu Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zmapować niestandardową nazwę DNS na aplikację sieci Web, [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) aplikacji sieci Web musi być płatną warstwą (współdzielona, podstawowa, standardowa, Premium lub zużycie dla Azure Functions). W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz [Azure Portal](https://portal.azure.com)i zaloguj się przy użyciu konta platformy Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Wybierz aplikację w Azure Portal

1. Wyszukaj i wybierz **App Services**.

   ![Zrzut ekranu pokazujący wybór App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na stronie **App Services** wybierz nazwę swojej aplikacji platformy Azure.

   ![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

1. W lewym okienku strony aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan App Service)**.

   ![Zrzut ekranu pokazujący menu Skalowanie w górę (plan App Service).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1** . Niestandardowa usługa DNS nie jest obsługiwana w warstwie **F1** .

   ![Zrzut ekranu pokazujący zalecane warstwy cenowe.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Jeśli plan App Service nie znajduje się w warstwie **F1** , zamknij stronę **skalowanie w górę** i przejdź do [mapowania rekordu CNAME](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

1. Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, wybierz opcję **Zobacz dodatkowe opcje**.

1. Wybierz przycisk **Zastosuj**.

   ![Zrzut ekranu pokazujący sprawdzanie warstwy cenowej.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

   ![Zrzut ekranu pokazujący potwierdzenie operacji skalowania.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Pobieranie identyfikatora weryfikacji domeny

Aby dodać domenę niestandardową do aplikacji, musisz zweryfikować własność domeny, dodając identyfikator weryfikacyjny jako rekord TXT z dostawcą domeny. W lewym okienku strony aplikacji wybierz pozycję **domeny niestandardowe**. Skopiuj identyfikator w polu **Identyfikator weryfikacyjnej domeny niestandardowej** na stronie **domeny niestandardowe** , aby przejść do następnego kroku.

![Zrzut ekranu pokazujący identyfikator w polu Identyfikator weryfikacji domeny niestandardowej.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Dodawanie identyfikatorów weryfikacji domeny do domeny niestandardowej może uniemożliwić zawieszonego wpisów DNS i pomaga uniknąć przejęcia domen podrzędnych. W przypadku domen niestandardowych, które zostały wcześniej skonfigurowane bez tego identyfikatora weryfikacyjnego, należy chronić je przed tym samym ryzykiem przez dodanie identyfikatora weryfikacji do rekordu DNS. Aby uzyskać więcej informacji o tym typowym zagrożeniu o wysokiej ważności, zobacz [przejęcie domeny](../security/fundamentals/subdomain-takeover.md)podrzędnej.

## <a name="map-your-domain"></a>Mapowanie domeny

Do mapowania niestandardowej nazwy DNS na usługę App Service możesz użyć rekordu CNAME lub rekordu A. Postępuj zgodnie z odpowiednimi instrukcjami:

- [Mapowanie rekordu CNAME](#map-a-cname-record)
- [Mapowanie rekordu A](#map-an-a-record)
- [Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME](#map-a-wildcard-domain)

> [!NOTE]
> W przypadku wszystkich niestandardowych nazw DNS z wyjątkiem domen katalogu głównego (na przykład `contoso.com`) należy używać rekordów CNAME. Dla domen katalogu głównego należy używać rekordów A.

### <a name="map-a-cname-record"></a>Mapowanie rekordu CNAME

W przykładzie znajdującym się w tym samouczku dodasz rekord CNAME dla poddomeny `www` (na przykład `www.contoso.com`).

Jeśli masz poddomenę inną niż `www` , Zastąp ją `www` poddomeną (na przykład, `sub` Jeśli domena niestandardowa to `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>Dostęp do rekordów DNS z dostawcą domeny

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Zamapuj poddomenę na domyślną nazwę domeny aplikacji ( `<app-name>.azurewebsites.net` gdzie `<app-name>` jest nazwą aplikacji). Aby utworzyć mapowanie CNAME dla domeny podrzędnej `www` , Utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid.www` | [Wcześniej identyfikator weryfikacyjny](#get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

Po dodaniu rekordów CNAME i TXT Strona rekordów DNS wygląda następująco:

![Zrzut ekranu pokazujący nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Włączanie mapowania rekordów CNAME na platformie Azure

1. W lewym okienku strony aplikacji w Azure Portal wybierz pozycję **domeny niestandardowe**.

    ![Zrzut ekranu pokazujący menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stronie **domeny niestandardowe** aplikacji Dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS ( `www.contoso.com` ).

1. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie elementu nazwa hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład `www.contoso.com`.

1. Wybierz przycisk **Weryfikuj**. Zostanie wyświetlona strona **Dodaj domenę niestandardową** .

1. Upewnij się, że **Typ rekordu nazwy hosta** ma wartość **CNAME ( \. example.com www lub dowolna poddomena)**. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący przycisk Dodaj domenę niestandardową.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu pokazujący Dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).

    Jeśli pominięto krok lub wystąpił błąd w dowolnym miejscu, w dolnej części strony pojawi się komunikat o błędzie weryfikacji.

    ![Zrzut ekranu pokazujący błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapowanie rekordu A

W przykładzie znajdującym się w tym samouczku dodasz rekord A dla domeny katalogu głównego (na przykład `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Kopiowanie adresu IP aplikacji

Aby móc zamapować rekord A, musisz znać zewnętrzny adres IP aplikacji. Ten adres IP można znaleźć na stronie **domeny niestandardowe** aplikacji w Azure Portal.

1. W lewym okienku strony aplikacji w Azure Portal wybierz pozycję **domeny niestandardowe**.

   ![Zrzut ekranu pokazujący menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stronie **domeny niestandardowe** Skopiuj adres IP aplikacji.

   ![Zrzut ekranu pokazujący nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Dostęp do rekordów DNS z dostawcą domeny

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Tworzenie rekordu A

Aby zmapować Rekord A do aplikacji, zwykle do domeny głównej, Utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) | Samo mapowanie domeny ( `@` zazwyczaj reprezentuje domenę główną). |
| TXT | `asuid` | [Wcześniej identyfikator weryfikacyjny](#get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. W przypadku domeny głównej Użyj `asuid` . |

> [!NOTE]
> Aby dodać poddomenę ( `www.contoso.com` na przykład) przy użyciu rekordu a zamiast zalecanego [rekordu CNAME](#map-a-cname-record), rekord rekordu i txt powinien wyglądać podobnie do poniższej tabeli:
>
> | Typ rekordu | Host | Wartość |
> | - | - | - |
> | A | `www` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
> | TXT | `asuid.www` | [Wcześniej identyfikator weryfikacyjny](#get-a-domain-verification-id) |
>

Po dodaniu rekordów strona rekordów DNS wygląda podobnie do poniższego przykładu:

![Zrzut ekranu przedstawiający stronę rekordów DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Włączanie mapowania rekordu A w aplikacji

Z powrotem na stronie **domeny niestandardowe** aplikacji w Azure Portal Dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS (na przykład `contoso.com` ).

1. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie nazwy hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowaną nazwę domeny, dla której skonfigurowano rekord A, na przykład `contoso.com`. 

1. Wybierz przycisk **Weryfikuj**. Zostanie wyświetlona strona **Dodaj domenę niestandardową** .

1. Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **Rekord A (example.com)**. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu pokazujący Dodawanie rekordu A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).
    
    Jeśli pominięto krok lub wystąpił błąd w dowolnym miejscu, w dolnej części strony pojawi się komunikat o błędzie weryfikacji.
    
    ![Zrzut ekranu przedstawiający błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapowanie domeny z symbolami wieloznacznymi

W przykładzie znajdującym się w tym samouczku zmapujesz [wieloznaczną nazwę DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (na przykład `*.contoso.com`) na aplikację usługi App Service przez dodanie rekordu CNAME.

#### <a name="access-dns-records-with-a-domain-provider"></a>Dostęp do rekordów DNS z dostawcą domeny

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Mapuj nazwę symbolu wieloznacznego `*` na domyślną nazwę domeny aplikacji ( `<app-name>.azurewebsites.net` gdzie `<app-name>` jest nazwą aplikacji). Aby zmapować nazwę symbolu wieloznacznego, Utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid` | [Wcześniej identyfikator weryfikacyjny](#get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

Dla przykładowej domeny `*.contoso.com` rekord CNAME zmapuje nazwę `*` na nazwę `<app-name>.azurewebsites.net`.

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Zrzut ekranu pokazujący nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Włączanie mapowania rekordów CNAME w aplikacji

Teraz możesz dodać dowolną poddomenę zgodną z nazwą symbolu wieloznacznego w aplikacji (na przykład, `sub1.contoso.com` `sub2.contoso.com` i `*.contoso.com` oba te dopasowania `*.contoso.com` ).

1. W lewym okienku strony aplikacji w Azure Portal wybierz pozycję **domeny niestandardowe**.

    ![Zrzut ekranu pokazujący menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie nazwy hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowany nazwę domeny, która pasuje do domeny z symbolami wieloznacznymi (na przykład `sub1.contoso.com`), a następnie wybierz przycisk **Weryfikuj**.

    Przycisk **Dodaj domenę niestandardową** jest aktywowany.

1. Upewnij się, że **Typ rekordu nazwy hosta** jest ustawiony na **rekord CNAME ( \. example.com www lub dowolna poddomena)**. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

1. Wybierz **+** ponownie ikonę, aby dodać kolejną domenę niestandardową, która pasuje do domeny z symbolami wieloznacznymi. Na przykład dodaj `sub2.contoso.com`.

    ![Zrzut ekranu pokazujący Dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Testowanie w przeglądarce

Przejdź do nazw DNS, które zostały wcześniej skonfigurowane (na przykład,,, `contoso.com` `www.contoso.com` `sub1.contoso.com` i `sub2.contoso.com` ).

![Zrzut ekranu pokazujący nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Nie znaleziono 404 "

Jeśli po przejściu do adresu URL domeny niestandardowej zostanie wyświetlony komunikat o błędzie HTTP 404 (nie znaleziono), sprawdź, czy Twoja domena jest rozpoznawana jako adres IP aplikacji za pomocą <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.NET</a>. W przeciwnym razie może to być spowodowane jedną z następujących przyczyn:

- W skonfigurowanej domenie niestandardowej brakuje rekordu lub rekordu CNAME.
- Klient przeglądarki umieścił w pamięci podręcznej stary adres IP Twojej domeny. Wyczyść pamięć podręczną i ponownie przetestuj rozpoznawanie nazw DNS. Do czyszczenia pamięci podręcznej na komputerze z systemem Windows należy użyć polecenia `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrowanie aktywnej domeny

Aby przeprowadzić migrację aktywnej witryny oraz jej nazwy domeny DNS do usługi App Service bez przestojów, zobacz [Migrate an active DNS name to Azure App Service](manage-custom-dns-migrate-domain.md) (Migrowanie aktywnej nazwy DNS do usługi Azure App Service).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Przekierowywanie do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Ale niektóre platformy sieci Web nie są uruchamiane w katalogu głównym. Na przykład platforma [Laravel](https://laravel.com/) uruchamia się w podkatalogu `public`. Aby kontynuować `contoso.com` przykład DNS, taka aplikacja jest dostępna w `http://contoso.com/public` , ale `http://contoso.com` zamiast tego chcesz skierować do `public` katalogu. Ten krok nie obejmuje rozpoznawania nazw DNS, ale ma na celu dostosowanie katalogu wirtualnego.

Aby dostosować katalog wirtualny dla aplikacji systemu Windows, wybierz pozycję **Ustawienia aplikacji** w lewym okienku strony aplikacji sieci Web. 

> [!NOTE]
> Aplikacje systemu Linux nie mają tej strony. Aby zmienić katalog główny witryny dla aplikacji systemu Linux, zobacz przewodniki dotyczące konfiguracji specyficzne dla języka (na przykład[php](configure-language-php.md?pivots=platform-linux#change-site-root)).

W dolnej części strony widać, że główny katalog wirtualny `/` domyślnie wskazuje na katalog `site\wwwroot`, który jest katalogiem głównym kodu aplikacji. Zmień to ustawienie, aby główny katalog wirtualny wskazywał na przykład na katalog `site\wwwroot\public`, i zapisz zmiany.

![Zrzut ekranu pokazujący Dostosowywanie katalogu wirtualnego.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po zakończeniu operacji aplikacja powinna zwrócić odpowiednią stronę w ścieżce katalogu głównego (na przykład `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie domenami niestandardowymi za pomocą skryptów można zautomatyzować za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/).

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
> * Mapuj poddomenę przy użyciu rekordu CNAME.
> * Mapowanie domeny katalogu głównego przy użyciu rekordu A.
> * Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME.
> * Przekieruj domyślny adres URL do katalogu niestandardowego.
> * Automatyzowanie mapowania domeny za pomocą skryptów.

Przejdź do następnego samouczka, aby dowiedzieć się, jak powiązać niestandardowy certyfikat TLS/SSL z aplikacją internetową.

> [!div class="nextstepaction"]
> [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md)
