---
title: 'Samouczek: mapowanie istniejącej niestandardowej nazwy DNS'
description: Dowiedz się, jak dodać istniejącą niestandardową nazwę domeny DNS (domenę niestandardową) do aplikacji internetowej, aplikacji mobilnej lub aplikacji interfejsu API w Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname, vanity domain
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1fcf8c681f4fad65209c27663045d4974be633f7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833255"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service

W tym samouczku pokazano, jak mapować istniejące <abbr title="Nazwa domeny zakupiona u rejestratora domen, takiego jak GoDaddy, lub poddomena zakupionej domeny.">niestandardowa nazwa domeny DNS</abbr> na wartość <abbr title="Oparta na protokółie HTTP usługa do hostowania aplikacji internetowych, interfejsów API REST i aplikacji mobilnych.">Azure App Service</abbr>.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny przy użyciu elementu <abbr title="Rekord nazwy kanonicznej DNS mapuje jedną nazwę domeny na inną.">Rekord CNAME</abbr>.
> * Mapowanie domeny głównej przy użyciu <abbr title="Rekord adresu w systemie DNS mapuje nazwę hosta na adres IP.">Rekord A</abbr>.
> * Mapowanie domeny z symbolami wieloznacznych przy użyciu rekordu CNAME.
> * Przekieruj domyślny adres URL do katalogu niestandardowego.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

* [Utwórz aplikację usługi App Service](./index.yml) lub użyj aplikacji utworzonej w innym samouczku.
* Upewnij się, że możesz edytować rekordy DNS dla domeny niestandardowej. Jeśli nie masz jeszcze domeny niestandardowej, możesz kupić [domenę niestandardową App Service domeny](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Co jest potrzebne do edytowania rekordów DNS?</summary>
        Wymaga dostępu do rejestru DNS dla dostawcy domeny, takiego jak GoDaddy. Aby na przykład dodać wpisy DNS dla domen <code>contoso.com</code> i <code>www.contoso.com</code>, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny katalogu głównego <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Przygotowywanie aplikacji

Aby zamapować niestandardową nazwę DNS na aplikację, <abbr title="Określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.">Plan usługi App Service</abbr> musi być warstwą płatną (nie <abbr title="Warstwa Azure App Service, w której aplikacja działa na tych samych maszyn wirtualnych co inne aplikacje, w tym aplikacje innych klientów. Ta warstwa jest przeznaczona do tworzenia i testowania.">**Bezpłatna (F1)**</abbr>). Aby uzyskać więcej informacji, [zobacz Azure App Service omówienie planu .](overview-hosting-plans.md)

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz adres [Azure Portal](https://portal.azure.com)i zaloguj się przy użyciu konta platformy Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Wybierz aplikację w Azure Portal

1. Wyszukaj i wybierz **App Services**.

   ![Zrzut ekranu przedstawiający wybieranie App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na **App Services** wybierz nazwę aplikacji platformy Azure.

   ![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

1. W lewym okienku strony aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję Skaluj w górę **(App Service planu).**

   ![Zrzut ekranu przedstawiający menu Skaluj w górę (App Service planu).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1.** Niestandardowy system DNS nie jest obsługiwany w **warstwie F1.**

   ![Zrzut ekranu przedstawiający zalecane warstwy cenowe.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Jeśli plan App Service nie znajduje się w warstwie **F1,** zamknij stronę **Skalowanie** w górę i przejdź do [3. Pobierz identyfikator weryfikacji domeny](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

1. Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, wybierz **pozycję Zobacz dodatkowe opcje.**

1. Wybierz przycisk **Zastosuj**.

   ![Zrzut ekranu przedstawiający sprawdzanie warstwy cenowej.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

   ![Zrzut ekranu przedstawiający potwierdzenie operacji skalowania.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Uzyskiwanie identyfikatora weryfikacji domeny

Aby dodać domenę niestandardową do aplikacji, musisz zweryfikować własność domeny, dodając identyfikator weryfikacyjny jako rekord TXT u dostawcy domeny. 

1. W lewym okienku strony aplikacji wybierz pozycję **Domeny niestandardowe.** 
1. Skopiuj identyfikator w polu **Custom Domain identyfikator** weryfikacji na stronie **Domeny niestandardowe** w następnym kroku.

    ![Zrzut ekranu przedstawiający identyfikator w Custom Domain identyfikator weryfikacji.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Dlaczego jest to potrzebne?</summary>
        Dodanie identyfikatorów weryfikacji domeny do domeny niestandardowej może uniemożliwić blokowanie wpisów DNS i unikanie przejęcia poddomeny. W przypadku domen niestandardowych skonfigurowanych wcześniej bez tego identyfikatora weryfikacji należy chronić je przed tym samym ryzykiem przez dodanie identyfikatora weryfikacji do rekordu DNS. Aby uzyskać więcej informacji na temat tego wspólnego zagrożenia o wysokiej ważności, zobacz <a href="/azure/security/fundamentals/subdomain-takeover">Subdomain takeover (Przejęcie poddomeny).</a>
    </details>
    
<a name="info"></a>

3. **(Tylko rekord A)** Aby zamapować <abbr title="Rekord adresu w systemie DNS mapuje nazwę hosta na adres IP.">Rekord A</abbr>, potrzebny jest zewnętrzny adres IP aplikacji. Na stronie **Domeny niestandardowe** skopiuj wartość adresu **IP**.

   ![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. Tworzenie rekordów DNS

1. Zaloguj się do witryny internetowej dostawcy domeny.

    <details>
        <summary>Czy mogę zarządzać systemem DNS od mojego dostawcy domeny przy użyciu platformy Azure?</summary>
        Jeśli chcesz, możesz użyć usługi Azure DNS do zarządzania rekordami DNS dla domeny i konfigurowania niestandardowej nazwy DNS dla Azure App Service. Aby uzyskać więcej informacji, zobacz <a href="/azure/dns/dns-delegate-domain-azure-dns">Tutorial: Host your domain in Azure DNS></a>.
    </details>

1. Znajdź stronę służącą do zarządzania rekordami DNS. 

    <details>
        <summary>Jak mogę znaleźć stronę?</summary>
        <p>Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą <strong>Nazwa domeny</strong>, <strong>DNS</strong> lub <strong>Zarządzanie serwerami nazw</strong>.</p>
        <p>Często stronę rekordów DNS można znaleźć, wyświetlając informacje o koncie, a następnie szukając linku, takiego jak <strong>Moje domeny.</strong> Przejdź do tej strony, a następnie poszukaj linku o nazwie podobnej do pliku <strong>strefy,</strong>rekordów <strong>DNS</strong>lub konfiguracji <strong>zaawansowanej.</strong></p>
    </details>

   Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

   ![Zrzut ekranu przedstawiający przykładową stronę rekordów DNS.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Wybierz **pozycję Dodaj** lub odpowiedni widżet, aby utworzyć rekord. 

1. Wybierz typ rekordu do utworzenia i postępuj zgodnie z instrukcjami. Możesz użyć jednego z <abbr title="Rekord nazwy kanonicznej w systemie DNS mapuje jedną nazwę domeny (alias) na inną (nazwę kanoniczną).">Rekord CNAME</abbr> lub <abbr title="Rekord adresu w systemie DNS mapuje nazwę hosta na adres IP.">Rekord A</abbr> aby zamapować niestandardową nazwę DNS na App Service. 

    <details>
        <summary>Który rekord należy wybrać?</summary>
        <div>
            <ul>
            <li>Aby zamapować domenę główną (na przykład <code>contoso.com</code> ), użyj rekordu A. Nie używaj rekordu CNAME dla rekordu głównego (aby uzyskać więcej informacji, zobacz wpis <a href="https://en.wikipedia.org/wiki/CNAME_record">w Wikipedii).</a></li>
            <li>Aby zamapować poddomenę (na przykład <code>www.contoso.com</code> ), użyj rekordu CNAME.</li>
            <li>Poddomenę można mapować na adres IP aplikacji bezpośrednio przy użyciu rekordu A, ale adres <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">IP może ulec zmianie.</a> Zamiast tego rekord CNAME jest mapowy na nazwę hosta aplikacji, co jest mniej podatne na zmiany.</li>
            <li>Aby <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">zamapować domenę z symbolami</a> wieloznacznymi (na przykład <code>*.contoso.com</code> ), użyj rekordu CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Dla poddomeny, `www` np. w `www.contoso.com` programie , utwórz dwa rekordy zgodnie z następującą tabelą:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `<subdomain>` (na przykład `www` ) | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid.<subdomain>` (na przykład `asuid.www` ) | [Identyfikator weryfikacji, który został wcześniej nadany](#3-get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

![Zrzut ekranu przedstawiający nawigację w portalu do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

W przypadku domeny głównej, na `contoso.com` przykład , utwórz dwa rekordy zgodnie z następującą tabelą:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#3-get-a-domain-verification-id) | Mapowanie domeny ( `@` zazwyczaj reprezentuje domenę główną). |
| TXT | `asuid` | [Identyfikator weryfikacji, który został wcześniej nadany](#3-get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid.<subdomain>` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. W przypadku domeny głównej `asuid` użyj . |

![Zrzut ekranu przedstawiający stronę rekordów DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Co zrobić, jeśli chcę zamapować poddomenę na rekord A?</summary>
Aby zamapować poddomenę, na przykład przy użyciu rekordu A zamiast zalecanego rekordu CNAME, rekord A i rekord TXT powinny wyglądać podobnie do `www.contoso.com` poniższej tabeli:

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
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">Identyfikator weryfikacji, który został wcześniej nadany</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Symbol wieloznaczny (CNAME)](#tab/wildcard)

Dla nazwy z symbolami wieloznacznych, takich jak w pliku , utwórz `*` dwa rekordy zgodnie z `*.contoso.com` następującą tabelą:

| Typ rekordu | Host | Wartość | Komentarze |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Mapowanie domeny. |
| TXT | `asuid` | [Identyfikator weryfikacji, który został wcześniej nadany](#3-get-a-domain-verification-id) | App Service uzyskuje dostęp do `asuid` rekordu TXT w celu zweryfikowania własności domeny niestandardowej. |

![Zrzut ekranu przedstawiający nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Moje zmiany zostaną usunięte po opuszczeniu strony.</summary>
<p>W przypadku niektórych dostawców, np. GoDaddy, zmiany rekordów DNS nie zaczynają obowiązywać, dopóki nie wybierzesz oddzielnego linku <strong>Zapisz zmiany</strong>.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Włączanie mapowania w aplikacji

1. W lewym okienku strony aplikacji w okienku aplikacji wybierz Azure Portal **domeny niestandardowe.**

    ![Zrzut ekranu przedstawiający menu Domeny niestandardowe.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający element Dodaj nazwę hosta.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład `www.contoso.com`.

1. Wybierz przycisk **Weryfikuj**. Zostanie **wyświetlona strona Dodawanie domeny** niestandardowej.

1. Upewnij się, **że typ rekordu Nazwa hosta** jest ustawiony na wartość **CNAME (www \. example.com lub dowolna poddomena).** Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający przycisk Dodaj domenę niestandardową.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Może mieć trochę czasu, aby nowa domena niestandardowa została odzwierciedlona na stronie Domeny **niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu przedstawiający dodawanie rekordu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Co to jest etykieta <strong>ostrzeżenia Nie zabezpieczona?</strong></summary>
        Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL. Każde żądanie PROTOKOŁU HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie protokołu TLS, zobacz <a href="/azure/app-service/configure-ssl-bindings">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</a>(Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL w Azure App Service ).
    </details>

    Jeśli pominięto krok lub gdzieś wcześniej zrobiliśmy literówkę, w dolnej części strony zostanie wyświetlony błąd weryfikacji.

    ![Zrzut ekranu przedstawiający błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Wpisz w pełni kwalifikowaną nazwę domeny, dla której skonfigurowano rekord A, na przykład `contoso.com`. 

1. Wybierz przycisk **Weryfikuj**. Zostanie **pokazana strona Dodawanie** domeny niestandardowej.

1. Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **Rekord A (example.com)**. Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Może mieć trochę czasu, aby nowa domena niestandardowa została odzwierciedlona na stronie Domeny **niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    ![Zrzut ekranu przedstawiający dodawanie rekordu A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Co to jest etykieta <strong>ostrzeżenia Nie zabezpiecz?</strong></summary>
        Etykieta ostrzegawcza dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL. Każde żądanie HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie protokołu TLS, zobacz <a href="/azure/app-service/configure-ssl-bindings">Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL</a>w Azure App Service .
    </details>
    
    Jeśli pominięto krok lub gdzieś wcześniej zrobiliśmy literówkę, w dolnej części strony zostanie wyświetlony błąd weryfikacji.
    
    ![Zrzut ekranu przedstawiający błąd weryfikacji.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Symbol wieloznaczny (CNAME)](#tab/wildcard)

3. Wpisz w pełni kwalifikowaną nazwę domeny, która odpowiada domenie z symbolami wieloznacznymi. Na przykład możesz użyć , , , lub dowolnego innego ciągu, który `*.contoso.com` pasuje do wzorca z `sub1.contoso.com` `sub2.contoso.com` `*.contoso.com` symbolami wieloznacznych. Następnie wybierz pozycję **Weryfikuj**.

    Przycisk **Dodaj domenę niestandardową** został aktywowany.

1. Upewnij się, **że typ rekordu Nazwa hosta** jest ustawiony na rekord **CNAME (example.com \. lub dowolną poddomenę).** Wybierz **pozycję Dodaj domenę niestandardową.**

    ![Zrzut ekranu przedstawiający dodawanie nazwy DNS do aplikacji.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Może mieć trochę czasu, aby nowa domena niestandardowa została odzwierciedlona na stronie Domeny **niestandardowe** aplikacji. Odśwież przeglądarkę, aby zaktualizować dane.

    <details>
        <summary>Co to jest etykieta <strong>ostrzeżenia Nie zabezpieczona?</strong></summary>
        Etykieta ostrzeżenia dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem TLS/SSL. Każde żądanie PROTOKOŁU HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie, w zależności od przeglądarki. Aby dodać powiązanie protokołu TLS, zobacz <a href="/azure/app-service/configure-ssl-bindings">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</a>(Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL w Azure App Service ).
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Testowanie w przeglądarce

Przejdź do skonfigurowanych wcześniej nazw DNS.

![Zrzut ekranu przedstawiający nawigację do aplikacji platformy Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Otrzymuję błąd HTTP 404 (nie znaleziono).</summary>
<ul>
<li>W skonfigurowanej domenie niestandardowej brakuje rekordu A lub rekordu CNAME.</li>
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
<summary>Czy muszę to zrobić?</summary>
<p>Zależy to od aplikacji. Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Jednak niektóre struktury internetowe nie&#39;się w katalogu głównym. Na przykład platforma <a href="https://laravel.com/">Laravel</a> uruchamia się w podkatalogu <code>public</code>. Aby kontynuować przykład DNS, taka aplikacja jest dostępna pod adres , ale zamiast tego chcesz <code>contoso.com</code> <code>http://contoso.com/public</code> skierować <code>http://contoso.com</code> do katalogu <code>public</code> . </p>
</details>

Chociaż jest to powszechny scenariusz, w rzeczywistości nie obejmuje on mapowania domen niestandardowych, ale dotyczy dostosowywania katalogu wirtualnego w aplikacji.

1. Wybierz **pozycję Ustawienia** aplikacji w lewym okienku strony aplikacji internetowej.

1. W dolnej części strony widać, że główny katalog wirtualny `/` domyślnie wskazuje na katalog `site\wwwroot`, który jest katalogiem głównym kodu aplikacji. Zmień to ustawienie, aby główny katalog wirtualny wskazywał na przykład na katalog `site\wwwroot\public`, i zapisz zmiany.

    ![Zrzut ekranu przedstawiający dostosowywanie katalogu wirtualnego.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Po zakończeniu operacji sprawdź, przechodząc do ścieżki głównej aplikacji w przeglądarce (na przykład `http://contoso.com` lub `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie domenami niestandardowymi za pomocą skryptów można zautomatyzować przy użyciu interfejsu wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) [lub Azure PowerShell](/powershell/azure/).

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
> [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania protokołu TLS/SSL w Azure App Service](configure-ssl-bindings.md)
