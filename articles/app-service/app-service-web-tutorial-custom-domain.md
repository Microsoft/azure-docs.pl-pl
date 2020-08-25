---
title: 'Samouczek: mapowanie istniejącej niestandardowej nazwy DNS'
description: Dowiedz się, jak dodać istniejącą niestandardową nazwę domeny DNS (domenę niestandardową) do aplikacji internetowej, zaplecza aplikacji mobilnej lub aplikacji interfejsu API w usłudze Azure App Service.
keywords: app service, azure app service, mapowanie domeny, nazwa domeny, istniejąca domena, nazwa hosta
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/13/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 1496f46eb29831dfb858f061ccc00c9e3dbc2e75
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782315"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. W tym samouczku przedstawiono, w jaki sposób można zmapować istniejącą niestandardową nazwę DNS na usługę Azure App Service.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny (na przykład `www.contoso.com`) przy użyciu rekordu CNAME
> * Mapowanie domeny katalogu głównego (na przykład `contoso.com`) przy użyciu rekordu A
> * Mapowanie domeny z symbolami wieloznacznymi (na przykład `*.contoso.com`) przy użyciu rekordu CNAME
> * Przekierowywanie domyślnego adresu URL do katalogu niestandardowego
> * Automatyzacja mapowania domen przy użyciu skryptów

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.
* Kup nazwę domeny i upewnij się, że masz dostęp do rejestru DNS u swojego dostawcy domeny (na przykład GoDaddy).

  Aby na przykład dodać wpisy DNS dla domen `contoso.com` i `www.contoso.com`, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny katalogu głównego `contoso.com`.

  > [!NOTE]
  > Jeśli nie masz istniejącej nazwy domeny, możesz [zakupić domenę za pośrednictwem witryny Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zamapować niestandardową nazwę DNS na aplikację internetową, dla tej aplikacji internetowej musisz mieć płatną warstwę [planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/) (**Współdzielona**, **Podstawowa**, **Standardowa**, ** Premium** lub **Zużycie** dla usługi Azure Functions). W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Wybierz aplikację w Azure Portal

Wyszukaj i wybierz **App Services**.

![Wybierz App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

Na stronie **App Services** wybierz nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W lewym obszarze nawigacji na stronie aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)**.

![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1**. Niestandardowe nazwy DNS nie są obsługiwane w warstwie **F1**. 

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Jeśli plan usługi App Service nie znajduje się w warstwie **F1**, zamknij stronę **Skalowanie w górę** i przejdź od razu do sekcji [Mapowanie rekordu CNAME](#cname).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij pozycję **Zastosuj**.

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Potwierdzenie operacji skalowania](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-domain-verification-id"></a>Pobierz identyfikator weryfikacji domeny

Aby dodać domenę niestandardową do aplikacji, musisz zweryfikować własność domeny, dodając identyfikator weryfikacyjny jako rekord TXT z dostawcą domeny. W lewym panelu nawigacyjnym strony aplikacji kliknij pozycję **domeny niestandardowe**. Aby wykonać następny krok, skopiuj **niestandardową nazwę weryfikacyjną domeny** na stronie **domeny niestandardowe** .

![Pobierz identyfikator weryfikacji domeny niestandardowej](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Dodawanie identyfikatorów weryfikacji domeny do domeny niestandardowej może uniemożliwić zawieszonego wpisów DNS i uniknąć przejęcia domen podrzędnych. Aby uzyskać więcej informacji o tym typowym zagrożeniu o wysokiej ważności, zobacz [przejęcie domeny](../security/fundamentals/subdomain-takeover.md)podrzędnej.

## <a name="map-your-domain"></a>Mapowanie domeny

Do mapowania niestandardowej nazwy DNS na usługę App Service możesz użyć **rekordu CNAME** lub **rekordu A**. Postępuj zgodnie z odpowiednimi instrukcjami:

- [Mapowanie rekordu CNAME](#map-a-cname-record)
- [Mapowanie rekordu A](#map-an-a-record)
- [Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME](#map-a-wildcard-domain)

> [!NOTE]
> W przypadku wszystkich niestandardowych nazw DNS z wyjątkiem domen katalogu głównego (na przykład `contoso.com`) należy używać rekordów CNAME. Dla domen katalogu głównego należy używać rekordów A.

### <a name="map-a-cname-record"></a>Mapowanie rekordu CNAME

W przykładzie znajdującym się w tym samouczku dodasz rekord CNAME dla poddomeny `www` (na przykład `www.contoso.com`).

Jeśli masz poddomenę inną niż `www` , Zastąp ją `www` poddomeną (na przykład, `sub` Jeśli domena niestandardowa to `sub.constoso.com` ).

#### <a name="access-dns-records-with-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS u dostawcy domen

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Zamapuj poddomenę na domyślną nazwę domeny aplikacji ( `<app-name>.azurewebsites.net` gdzie `<app-name>` jest nazwą aplikacji). Aby utworzyć mapowanie CNAME dla domeny podrzędnej `www` , Utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid.www` | [Wcześniej identyfikator weryfikacyjny](#get-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

Po dodaniu rekordów CNAME i TXT Strona rekordów DNS wygląda następująco:

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Włączanie mapowania rekordów CNAME na platformie Azure

W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**.

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **Domeny niestandardowe** aplikacji dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS (`www.contoso.com`).

Wybierz **+** ikonę obok pozycji **Dodaj domenę niestandardową**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład `www.contoso.com`.

Wybierz przycisk **Weryfikuj**.

Zostanie wyświetlona strona **Dodaj domenę niestandardową** .

Upewnij się, że **Typ rekordu nazwy hosta** ma wartość **CNAME ( \. example.com www lub dowolna poddomena)**.

Wybierz pozycję **Dodaj domenę niestandardową**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Etykieta **niezabezpieczona** dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL, a wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają komunikat o błędzie lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).

Jeśli pominięto jakiś krok lub popełniono gdzieś błąd w pisowni, w dolnej części strony zostanie wyświetlony komunikat o błędzie weryfikacji.

![Błąd weryfikacji](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapowanie rekordu A

W przykładzie znajdującym się w tym samouczku dodasz rekord A dla domeny katalogu głównego (na przykład `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Kopiowanie adresu IP aplikacji

Aby móc zamapować rekord A, musisz znać zewnętrzny adres IP aplikacji. Ten adres IP można znaleźć na stronie **Domeny niestandardowe** aplikacji w witrynie Azure Portal.

W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**.

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS u dostawcy domen

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Tworzenie rekordu A

Aby zmapować Rekord A do aplikacji, zwykle do domeny głównej, Utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) | Samo mapowanie domeny ( `@` zazwyczaj reprezentuje domenę główną). |
| TXT | `asuid` | [Wcześniej identyfikator weryfikacyjny](#get-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. W przypadku domeny głównej Użyj `asuid` . |

> [!NOTE]
> Aby dodać poddomeny (takie jak `www.contoso.com`) przy użyciu rekordu A zamiast zalecanego [rekordu CNAME](#map-a-cname-record), Twój rekord A i rekord TXT powinny zamiast tego wyglądać podobnie jak w poniższej tabeli:
>
> | Typ rekordu | Host | Wartość |
> | - | - | - |
> | A | `www` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

Po dodaniu tych rekordów strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Strona rekordów DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Włączanie mapowania rekordu A w aplikacji

Ponownie na stronie **Domeny niestandardowe** aplikacji w witrynie Azure Portal dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS (na przykład `contoso.com`).

Wybierz **+** ikonę obok pozycji **Dodaj domenę niestandardową**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowaną nazwę domeny, dla której skonfigurowano rekord A, na przykład `contoso.com`.

Wybierz przycisk **Weryfikuj**.

Zostanie wyświetlona strona **Dodaj domenę niestandardową** .

Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **Rekord A (example.com)**.

Wybierz pozycję **Dodaj domenę niestandardową**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord A](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Etykieta **niezabezpieczona** dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL, a wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają komunikat o błędzie lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).

Jeśli pominięto jakiś krok lub popełniono gdzieś błąd w pisowni, w dolnej części strony zostanie wyświetlony komunikat o błędzie weryfikacji.

![Błąd weryfikacji](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapowanie domeny z symbolami wieloznacznymi

W przykładzie znajdującym się w tym samouczku zmapujesz [wieloznaczną nazwę DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (na przykład `*.contoso.com`) na aplikację usługi App Service przez dodanie rekordu CNAME.

#### <a name="access-dns-records-with-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS u dostawcy domen

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Mapuj nazwę symbolu wieloznacznego `*` na domyślną nazwę domeny aplikacji ( `<app-name>.azurewebsites.net` gdzie `<app-name>` jest nazwą aplikacji). Aby zmapować nazwę symbolu wieloznacznego, Utwórz dwa rekordy:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid` | [Wcześniej identyfikator weryfikacyjny](#get-domain-verification-id) | App Service uzyskuje dostęp do `asuid` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

Dla przykładowej domeny `*.contoso.com` rekord CNAME zmapuje nazwę `*` na nazwę `<app-name>.azurewebsites.net`.

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Włączanie mapowania rekordów CNAME w aplikacji

Teraz możesz dodać dowolną poddomenę zgodną z nazwą symbolu wieloznacznego w aplikacji (na przykład, `sub1.contoso.com` `sub2.contoso.com` i `*.contoso.com` oba te dopasowania `*.contoso.com` ).

W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**.

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Wybierz **+** ikonę obok pozycji **Dodaj domenę niestandardową**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowany nazwę domeny, która pasuje do domeny z symbolami wieloznacznymi (na przykład `sub1.contoso.com`), a następnie wybierz przycisk **Weryfikuj**.

Przycisk **Dodaj domenę niestandardową** jest aktywowany.

Upewnij się, że **Typ rekordu nazwy hosta** jest ustawiony na **rekord CNAME ( \. example.com www lub dowolna poddomena)**.

Wybierz pozycję **Dodaj domenę niestandardową**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

Wybierz **+** ponownie ikonę, aby dodać kolejną domenę niestandardową, która pasuje do domeny z symbolami wieloznacznymi. Na przykład dodaj `sub2.contoso.com`.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> **Uwaga bezpieczna** etykieta dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL, a wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej będą odbierane oraz błędne lub ostrzegawcze w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Testowanie w przeglądarce

Przejdź do nazw DNS, które zostały wcześniej skonfigurowane (na przykład `contoso.com`, `www.contoso.com`, `sub1.contoso.com` lub `sub2.contoso.com`).

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Nie znaleziono 404 "

Jeśli podczas przechodzenia do adresu URL domeny niestandardowej występuje błąd HTTP 404 (Nie znaleziono), sprawdź, czy domena jest rozstrzygana na adres IP aplikacji, korzystając z witryny <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Jeśli nie, może to być spowodowane jedną z następujących przyczyn:

- W skonfigurowanej domenie niestandardowej brakuje rekordu A i/lub rekordu CNAME.
- Klient przeglądarki umieścił w pamięci podręcznej stary adres IP Twojej domeny. Wyczyść pamięć podręczną i ponownie przetestuj rozpoznawanie nazwy DNS. Do czyszczenia pamięci podręcznej na komputerze z systemem Windows należy użyć polecenia `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Migrowanie aktywnej domeny

Aby przeprowadzić migrację aktywnej witryny oraz jej nazwy domeny DNS do usługi App Service bez przestojów, zobacz [Migrate an active DNS name to Azure App Service](manage-custom-dns-migrate-domain.md) (Migrowanie aktywnej nazwy DNS do usługi Azure App Service).

## <a name="redirect-to-a-custom-directory"></a>Przekierowywanie do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Jednak niektóre platformy internetowe nie uruchamiają się z poziomu katalogu głównego. Na przykład platforma [Laravel](https://laravel.com/) uruchamia się w podkatalogu `public`. W przypadku używanej tu przykładowej nazwy DNS `contoso.com` taka aplikacja byłaby dostępna w katalogu `http://contoso.com/public`, ale zamiast tego lepiej byłoby przekierować nazwę `http://contoso.com` do katalogu `public`. W tym kroku nie opisano rozpoznawania nazw DNS tylko dostosowywanie katalogu wirtualnego.

W tym celu na stronie aplikacji internetowej wybierz pozycję **Ustawienia aplikacji** w obszarze nawigacji po lewej stronie. 

W dolnej części strony widać, że główny katalog wirtualny `/` domyślnie wskazuje na katalog `site\wwwroot`, który jest katalogiem głównym kodu aplikacji. Zmień to ustawienie, aby główny katalog wirtualny wskazywał na przykład na katalog `site\wwwroot\public`, i zapisz zmiany.

![Dostosowywanie katalogu wirtualnego](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po zakończeniu operacji aplikacja powinna zwrócić odpowiednią stronę w ścieżce katalogu głównego (na przykład `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie domenami niestandardowymi można zautomatyzować za pomocą skryptów [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/). 

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
> * Mapowanie poddomeny przy użyciu rekordu CNAME
> * Mapowanie domeny katalogu głównego przy użyciu rekordu A
> * Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME
> * Przekierowywanie domyślnego adresu URL do katalogu niestandardowego
> * Automatyzacja mapowania domen przy użyciu skryptów

Przejdź do następnego samouczka, aby dowiedzieć się, jak powiązać niestandardowy certyfikat TLS/SSL z aplikacją internetową.

> [!div class="nextstepaction"]
> [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md)
