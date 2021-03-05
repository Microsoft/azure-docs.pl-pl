---
title: 'Samouczek: mapowanie istniejącej niestandardowej nazwy DNS'
description: Dowiedz się, jak dodać istniejącą niestandardową nazwę domeny DNS (znaczącym Domain) do aplikacji sieci Web, zaplecza aplikacji mobilnej lub aplikacji interfejsu API w Azure App Service.
keywords: App Service, Azure App Service, mapowanie domeny, nazwa domeny, Istniejąca domena, nazwa hosta, domena znaczącym
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ca1308c969227336bfb4970f7c5c77b9f2e0cc22
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216534"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service

W tym samouczku przedstawiono sposób mapowania wszelkich istniejących <abbr title="Nazwa domeny zakupionej od rejestratora domen, takiej jak GoDaddy, lub poddomeną zakupionej domeny.">niestandardowa nazwa domeny DNS</abbr> na wartość <abbr title="Usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i aplikacji zaplecza mobilnego.">Azure App Service</abbr>.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny za pomocą <abbr title="Rekord kanonicznej nazwy DNS mapuje jedną nazwę domeny na inną.">Rekord CNAME</abbr>.
> * Mapowanie domeny katalogu głównego przy użyciu <abbr title="Rekord adresu w systemie DNS mapuje nazwę hosta na adres IP.">Rekord A</abbr>.
> * Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME.
> * Przekieruj domyślny adres URL do katalogu niestandardowego.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

* [Utwórz aplikację usługi App Service](./index.yml) lub użyj aplikacji utworzonej w innym samouczku.
* Upewnij się, że można edytować rekordy DNS dla domeny niestandardowej. Jeśli nie masz jeszcze domeny niestandardowej, możesz [zakupić domenę App Serviceową](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Co muszę edytować rekordy DNS?</summary>
        Wymaga dostępu do rejestru DNS dostawcy domeny, takiego jak GoDaddy. Aby na przykład dodać wpisy DNS dla domen <code>contoso.com</code> i <code>www.contoso.com</code>, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny katalogu głównego <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Przygotuj aplikację

Aby zmapować niestandardową nazwę DNS na aplikację, aplikacja <abbr title="Określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.">Plan usługi App Service</abbr> musi być płatną warstwą (nie <abbr title="Warstwa Azure App Service, w której aplikacja jest uruchamiana na tych samych maszynach wirtualnych co inne aplikacje, w tym w aplikacjach innych klientów. Ta warstwa jest przeznaczona do programowania i testowania.">**Bezpłatnie (F1)**</abbr>). Aby uzyskać więcej informacji, zobacz [Omówienie planu Azure App Service](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz [Azure Portal](https://portal.azure.com)i zaloguj się przy użyciu konta platformy Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Wybierz aplikację w Azure Portal

1. Wyszukaj i wybierz **App Services**.

   ![Zrzut ekranu pokazujący wybór App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na stronie **App Services** wybierz nazwę swojej aplikacji platformy Azure.

   ![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

1. W lewym okienku strony aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan App Service)**.

   ![Zrzut ekranu pokazujący menu Skalowanie w górę (plan App Service).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1** . Niestandardowa usługa DNS nie jest obsługiwana w warstwie **F1** .

   ![Zrzut ekranu pokazujący zalecane warstwy cenowe.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Jeśli plan App Service nie znajduje się w warstwie **F1** , zamknij stronę **skalowanie w górę** i przejdź do [3. Pobierz identyfikator weryfikacji domeny](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

1. Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, wybierz opcję **Zobacz dodatkowe opcje**.

1. Wybierz przycisk **Zastosuj**.

   ![Zrzut ekranu pokazujący sprawdzanie warstwy cenowej.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

   ![Zrzut ekranu pokazujący potwierdzenie operacji skalowania.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Uzyskaj identyfikator weryfikacji domeny

Aby dodać domenę niestandardową do aplikacji, musisz zweryfikować własność domeny, dodając identyfikator weryfikacyjny jako rekord TXT z dostawcą domeny. 

1. W lewym okienku strony aplikacji wybierz pozycję **domeny niestandardowe**. 
1. Skopiuj identyfikator w polu **Identyfikator weryfikacyjnej domeny niestandardowej** na stronie **domeny niestandardowe** , aby przejść do następnego kroku.

    ![Zrzut ekranu pokazujący identyfikator w polu Identyfikator weryfikacji domeny niestandardowej.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Dlaczego potrzebuję tego?</summary>
        Dodawanie identyfikatorów weryfikacji domeny do domeny niestandardowej może uniemożliwić zawieszonego wpisów DNS i pomaga uniknąć przejęcia domen podrzędnych. W przypadku domen niestandardowych, które zostały wcześniej skonfigurowane bez tego identyfikatora weryfikacyjnego, należy chronić je przed tym samym ryzykiem przez dodanie identyfikatora weryfikacji do rekordu DNS. Aby uzyskać więcej informacji o tym typowym zagrożeniu o wysokiej ważności, zobacz <a href="/azure/security/fundamentals/subdomain-takeover">przejęcie domeny</a>podrzędnej.
    </details>
    
<a name="info"></a>

3. **(Tylko rekord A)** Aby zmapować <abbr title="Rekord adresu w systemie DNS mapuje nazwę hosta na adres IP.">Rekord A</abbr>wymagany jest zewnętrzny adres IP aplikacji. Na stronie **domeny niestandardowe** skopiuj wartość **adres IP**.

   ![Zrzut ekranu pokazujący nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. Utwórz rekordy DNS

1. Zaloguj się do witryny internetowej dostawcy domeny.

    <details>
        <summary>Czy mogę zarządzać systemem DNS od mojego dostawcy domeny przy użyciu platformy Azure?</summary>
        Jeśli chcesz, możesz użyć Azure DNS do zarządzania rekordami DNS dla domeny i skonfigurować niestandardową nazwę DNS dla Azure App Service. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns">Samouczek: Hostowanie domeny w Azure DNS></a>.
    </details>

1. Znajdź stronę służącą do zarządzania rekordami DNS. 

    <details>
        <summary>Jak mogę znaleźć stronę?</summary>
        <p>Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą <strong>Nazwa domeny</strong>, <strong>DNS</strong> lub <strong>Zarządzanie serwerami nazw</strong>.</p>
        <p>Często można znaleźć stronę rekordów DNS, wyświetlając informacje o koncie, a następnie szukając linku, takiego jak <strong>Moje domeny</strong>. Przejdź do tej strony, a następnie poszukaj linku o nazwie, takiego jak <strong>plik strefy</strong>, <strong>rekordy DNS</strong>lub <strong>Konfiguracja zaawansowana</strong>.</p>
    </details>

   Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

   ![Zrzut ekranu pokazujący przykładową stronę rekordów DNS.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Wybierz pozycję **Dodaj** lub odpowiedni widżet, aby utworzyć rekord. 

1. Wybierz typ rekordu do utworzenia i postępuj zgodnie z instrukcjami. Można użyć <abbr title="Rekord nazwy kanonicznej w systemie DNS mapuje jedną nazwę domeny (alias) na inną (nazwę kanoniczną).">Rekord CNAME</abbr> lub <abbr title="Rekord adresu w systemie DNS mapuje nazwę hosta na adres IP.">Rekord A</abbr> Aby zmapować niestandardową nazwę DNS na App Service. 

    <details>
        <summary>Który rekord należy wybrać?</summary>
        <div>
            <ul>
            <li>Aby zmapować domenę główną (na przykład <code>contoso.com</code> ), użyj rekordu A. Nie używaj rekordu CNAME dla rekordu głównego (Aby uzyskać informacje, zobacz <a href="https://en.wikipedia.org/wiki/CNAME_record">wpis Wikipedia</a>).</li>
            <li>Aby zmapować poddomenę (na przykład <code>www.contoso.com</code> ), użyj rekordu CNAME.</li>
            <li>Domenę podrzędnej można mapować na adres IP aplikacji bezpośrednio z rekordem, ale można <a href="https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">zmienić adres IP</a>. Rekord CNAME jest mapowany na nazwę hosta aplikacji, co jest mniej podatne na zmianę.</li>
            <li>Aby zmapować <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">domenę symboli wieloznacznych</a> (na przykład <code>*.contoso.com</code> ), użyj rekordu CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

W przypadku domeny podrzędnej podobnej `www` `www.contoso.com` do programu Utwórz dwa rekordy zgodnie z poniższą tabelą:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `<subdomain>` (na przykład `www` ) | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid.<subdomain>` (na przykład `asuid.www` ) | [Wcześniej identyfikator weryfikacyjny](#3-get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

![Zrzut ekranu pokazujący nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Dla domeny katalogu głównego `contoso.com` , na przykład, Utwórz dwa rekordy zgodnie z poniższą tabelą:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#3-get-a-domain-verification-id) | Samo mapowanie domeny ( `@` zazwyczaj reprezentuje domenę główną). |
| TXT | `asuid` | [Wcześniej identyfikator weryfikacyjny](#3-get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. W przypadku domeny głównej Użyj `asuid` . |

![Zrzut ekranu przedstawiający stronę rekordów DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Co zrobić, jeśli chcę zmapować poddomenę na rekord A?</summary>
Aby zmapować poddomenę podobną do `www.contoso.com` rekordu a zamiast zalecanego rekordu CNAME, rekord rekordu i txt powinien wyglądać podobnie do poniższej tabeli:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tabela 3</caption>
<thead>
<tr>
<th>Typ rekordu</th>
<th>Host</th>
<th>Wartość</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (na przykład <code>www</code> )</td>
<td>Adres IP z sekcji <a href="#info" data-linktype="self-bookmark">Kopiowanie adresu IP aplikacji</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (na przykład <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">Wcześniej identyfikator weryfikacyjny</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Symbol wieloznaczny (CNAME)](#tab/wildcard)

W przypadku symboli wieloznacznych, takich jak `*` w programie `*.contoso.com` , Utwórz dwa rekordy zgodnie z poniższą tabelą:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid` | [Wcześniej identyfikator weryfikacyjny](#3-get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

![Zrzut ekranu pokazujący nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Moje zmiany są wymazywane po opuszczeniu strony.</summary>
<p>W przypadku niektórych dostawców, np. GoDaddy, zmiany rekordów DNS nie zaczynają obowiązywać, dopóki nie wybierzesz oddzielnego linku <strong>Zapisz zmiany</strong>.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Włącz mapowanie w aplikacji

1. W lewym okienku strony aplikacji w Azure Portal wybierz pozycję **domeny niestandardowe**.

    ![Zrzut ekranu pokazujący menu domen niestandardowych.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie elementu nazwa hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład `www.contoso.com`.

1. Wybierz przycisk **Weryfikuj**. Zostanie wyświetlona strona **Dodaj domenę niestandardową** .

1. Upewnij się, że **Typ rekordu nazwy hosta** ma wartość **CNAME ( \. example.com www lub dowolna poddomena)**. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący przycisk Dodaj domenę niestandardową.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu pokazujący Dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Co to jest etykieta ostrzeżenie <strong>niezabezpieczona</strong> ?</summary>
        Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service</a>.
    </details>

    Jeśli pominięto krok lub wystąpił błąd w dowolnym miejscu, w dolnej części strony pojawi się komunikat o błędzie weryfikacji.

    ![Zrzut ekranu pokazujący błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Wpisz w pełni kwalifikowaną nazwę domeny, dla której skonfigurowano rekord A, na przykład `contoso.com`. 

1. Wybierz przycisk **Weryfikuj**. Zostanie wyświetlona strona **Dodaj domenę niestandardową** .

1. Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **Rekord A (example.com)**. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu pokazujący Dodawanie rekordu A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Co to jest etykieta ostrzeżenie <strong>niezabezpieczona</strong> ?</summary>
        Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service</a>.
    </details>
    
    Jeśli pominięto krok lub wystąpił błąd w dowolnym miejscu, w dolnej części strony pojawi się komunikat o błędzie weryfikacji.
    
    ![Zrzut ekranu przedstawiający błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Symbol wieloznaczny (CNAME)](#tab/wildcard)

3. Wpisz w pełni kwalifikowaną nazwę domeny, która pasuje do domeny z symbolem wieloznacznym. Na przykład, `*.contoso.com` można użyć `sub1.contoso.com` ,, `sub2.contoso.com` `*.contoso.com` lub dowolnego innego ciągu, który pasuje do wzorca wieloznacznego. Następnie wybierz pozycję **Weryfikuj**.

    Przycisk **Dodaj domenę niestandardową** jest aktywowany.

1. Upewnij się, że **Typ rekordu nazwy hosta** jest ustawiony na **rekord CNAME ( \. example.com www lub dowolna poddomena)**. Wybierz pozycję **Dodaj domenę niestandardową**.

    ![Zrzut ekranu pokazujący Dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Może upłynąć trochę czasu, zanim nowa domena niestandardowa zostanie odzwierciedlona na stronie **domeny niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    <details>
        <summary>Co to jest etykieta ostrzeżenie <strong>niezabezpieczona</strong> ?</summary>
        Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem TLS/SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie TLS, zobacz temat <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Testowanie w przeglądarce

Przejdź do wcześniej skonfigurowanych nazw DNS.

![Zrzut ekranu pokazujący nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Otrzymuję błąd HTTP 404 (nie znaleziono).</summary>
<ul>
<li>W skonfigurowanej domenie niestandardowej brakuje rekordu lub rekordu CNAME.</li>
<li>Klient przeglądarki umieścił w pamięci podręcznej stary adres IP Twojej domeny. Wyczyść pamięć podręczną i ponownie przetestuj rozpoznawanie nazw DNS. Do czyszczenia pamięci podręcznej na komputerze z systemem Windows należy użyć polecenia <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Migrowanie aktywnej domeny

Aby przeprowadzić migrację aktywnej witryny oraz jej nazwy domeny DNS do usługi App Service bez przestojów, zobacz [Migrate an active DNS name to Azure App Service](manage-custom-dns-migrate-domain.md) (Migrowanie aktywnej nazwy DNS do usługi Azure App Service).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Przekierowywanie do katalogu niestandardowego

<details>
<summary>Czy potrzebuję tego?</summary>
<p>Zależy to od aplikacji. Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Ale niektóre struktury sieci Web nie&#39;t Start w katalogu głównym. Na przykład platforma <a href="https://laravel.com/">Laravel</a> uruchamia się w podkatalogu <code>public</code>. Aby kontynuować <code>contoso.com</code> przykład DNS, taka aplikacja jest dostępna w <code>http://contoso.com/public</code> , ale <code>http://contoso.com</code> zamiast tego chcesz skierować do <code>public</code> katalogu. </p>
</details>

Chociaż jest to typowy scenariusz, nie wiąże się to z niestandardowym mapowaniem domeny, ale ma na celu dostosowanie katalogu wirtualnego w aplikacji.

1. Wybierz pozycję **Ustawienia aplikacji** w lewym okienku strony aplikacji sieci Web.

1. W dolnej części strony widać, że główny katalog wirtualny `/` domyślnie wskazuje na katalog `site\wwwroot`, który jest katalogiem głównym kodu aplikacji. Zmień to ustawienie, aby główny katalog wirtualny wskazywał na przykład na katalog `site\wwwroot\public`, i zapisz zmiany.

    ![Zrzut ekranu pokazujący Dostosowywanie katalogu wirtualnego.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Po zakończeniu operacji Sprawdź, przechodząc do ścieżki głównej swojej aplikacji w przeglądarce (na przykład `http://contoso.com` lub `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie domenami niestandardowymi za pomocą skryptów można zautomatyzować za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie dodaje skonfigurowaną niestandardową nazwę DNS do aplikacji usługi App Service.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Aby uzyskać więcej informacji, zobacz [Map a custom domain to a web app](scripts/cli-configure-custom-domain.md) (Mapowanie domeny niestandardowej na aplikację internetową).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie dodaje skonfigurowaną niestandardową nazwę DNS do aplikacji usługi App Service.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Aby uzyskać więcej informacji, zobacz [Assign a custom domain to a web app](scripts/powershell-configure-custom-domain.md) (Przypisywanie domeny niestandardowej do aplikacji internetowej).

<hr/> 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak powiązać niestandardowy certyfikat TLS/SSL z aplikacją internetową.

> [!div class="nextstepaction"]
> [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md)
