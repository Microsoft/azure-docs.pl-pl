---
title: Zabezpieczanie niestandardowego serwera DNS przy użyciu powiązania TLS/SSL
description: Zabezpiecz dostęp HTTPS do domeny niestandardowej przez utworzenie powiązania TLS/SSL z certyfikatem. Popraw zabezpieczenia witryny sieci Web, wymuszając protokół HTTPS lub TLS 1,2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: a713d869a754f17f4030c531999c822a2cbb76ee
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039805"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service

W tym artykule pokazano, jak zabezpieczyć [domenę niestandardową](app-service-web-tutorial-custom-domain.md) w [aplikacji App Service](./index.yml) lub [aplikacji funkcji](../azure-functions/index.yml) przez utworzenie powiązania certyfikatu. Po zakończeniu możesz uzyskać dostęp do aplikacji App Service w `https://` punkcie końcowym dla niestandardowej nazwy DNS (na przykład `https://www.contoso.com` ). 

![Aplikacja internetowa z niestandardowym certyfikatem TLS/SSL](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Zabezpieczanie [domeny niestandardowej](app-service-web-tutorial-custom-domain.md) za pomocą certyfikatu obejmuje dwie czynności:

- [Dodaj prywatny certyfikat do App Service](configure-ssl-certificate.md) , który spełnia wszystkie [wymagania dotyczące certyfikatu prywatnego](configure-ssl-certificate.md#private-certificate-requirements).
-  Utwórz powiązanie TLS z odpowiednią domeną niestandardową. Ten drugi krok jest objęty tym artykułem.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Uaktualnienie warstwy cenowej aplikacji
> * Zabezpieczanie domeny niestandardowej przy użyciu certyfikatu
> * Wymuszanie protokołu HTTPS
> * Wymuszanie protokołu TLS 1.1/1.2
> * Automatyzacja zarządzania protokołami TLS za pomocą skryptów

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- [Utwórz aplikację usługi App Service](./index.yml)
- [Mapowanie nazwy domeny do aplikacji](app-service-web-tutorial-custom-domain.md) lub [kupowanie i Konfigurowanie na platformie Azure](manage-custom-dns-buy-domain.md)
- [Dodawanie certyfikatu prywatnego do aplikacji](configure-ssl-certificate.md)

> [!NOTE]
> Najprostszym sposobem dodawania certyfikatu prywatnego jest [utworzenie bezpłatnego App Service certyfikatu zarządzanego](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (wersja zapoznawcza).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpieczanie domeny niestandardowej

Wykonaj następujące czynności:

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji Uruchom okno dialogowe **powiązania protokołu TLS/SSL** , wykonując następujące działania:

- Wybieranie **domen niestandardowych**  >  **Dodawanie powiązania**
- Wybieranie **ustawień protokołu TLS/SSL**  >  **Dodawanie powiązania TLS/SSL**

![Dodawanie powiązania do domeny](./media/configure-ssl-bindings/secure-domain-launch.png)

W polu **domena niestandardowa** wybierz domenę niestandardową, dla której chcesz dodać powiązanie.

Jeśli aplikacja ma już certyfikat dla wybranej domeny niestandardowej, przejdź do pozycji [Utwórz powiązanie](#create-binding) bezpośrednio. W przeciwnym razie Kontynuuj.

### <a name="add-a-certificate-for-custom-domain"></a>Dodaj certyfikat dla domeny niestandardowej

Jeśli aplikacja nie ma certyfikatu dla wybranej domeny niestandardowej, dostępne są dwie opcje:

- **Przekaż certyfikat PFX** — postępuj zgodnie z przepływem pracy w obszarze [Przekaż certyfikat prywatny](configure-ssl-certificate.md#upload-a-private-certificate), a następnie wybierz tę opcję tutaj.
- **Zaimportuj certyfikat usługi App Service** — postępuj zgodnie z przepływem pracy podczas [importowania certyfikatu App Service](configure-ssl-certificate.md#import-an-app-service-certificate), a następnie wybierz tę opcję w tym miejscu.

> [!NOTE]
> Możesz również [utworzyć bezpłatny certyfikat](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (wersja zapoznawcza) lub [zaimportować certyfikat Key Vault](configure-ssl-certificate.md#import-a-certificate-from-key-vault), ale należy to zrobić oddzielnie, a następnie powrócić do okna dialogowego **powiązania protokołu TLS/SSL** .

### <a name="create-binding"></a>Utwórz powiązanie

Skorzystaj z poniższej tabeli, aby skonfigurować powiązanie protokołu TLS w oknie dialogowym **powiązanie protokołu TLS/SSL** , a następnie kliknij przycisk **Dodaj powiązanie**.

| Ustawienie | Opis |
|-|-|
| Domena niestandardowa | Nazwa domeny, dla której ma zostać dodane powiązanie protokołu TLS/SSL. |
| Odcisk palca certyfikatu prywatnego | Certyfikat do powiązania. |
| Typ protokołu TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** — można dodać wiele SNI SSL powiązań. Ta opcja umożliwia wielu certyfikatom protokołu TLS/SSL zabezpieczenie wielu domen na tym samym adresie IP. Większość nowoczesnych przeglądarek (w tym Internet Explorer, Chrome, Firefox i Opera) obsługuje SNI (Aby uzyskać więcej informacji, zobacz [oznaczanie nazwy serwera](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**Połączenie SSL z adresu IP** — można dodać tylko jedno powiązanie połączenie SSL z adresu IP. Ta opcja zezwala na zabezpieczenie dedykowanego publicznego adresu IP tylko jednemu certyfikatowi TLS/SSL. Po skonfigurowaniu powiązania postępuj zgodnie z instrukcjami w sekcji ponowne [Mapowanie rekordów dla połączenie SSL z adresu IP](#remap-records-for-ip-ssl).<br/>Połączenie SSL z adresu IP jest obsługiwana tylko w warstwie **standardowa** lub wyższej. </li></ul> |

Po zakończeniu operacji stan protokołu TLS/SSL domeny niestandardowej zostanie zmieniony na **bezpieczny**.

![Powiązanie protokołu TLS/SSL powiodło się](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> **Bezpiecznym** stanem w **domenach niestandardowych** jest zabezpieczenie certyfikatu, ale App Service nie sprawdza, czy certyfikat ma podpis własny lub wygasł, na przykład może spowodować, że przeglądarki wyświetlają błąd lub ostrzeżenie.

## <a name="remap-records-for-ip-ssl"></a>Ponowne mapowanie rekordów dla Połączenie SSL z adresu IP

Jeśli nie używasz Połączenie SSL z adresu IP w aplikacji, przejdź do [testowania protokołu HTTPS dla domeny niestandardowej](#test-https).

Istnieją dwie zmiany, które należy wprowadzić, potencjalnie:

- Domyślnie aplikacja używa udostępnionego publicznego adresu IP. Po powiązaniu certyfikatu z Połączenie SSL z adresu IP, App Service tworzy nowy, dedykowany adres IP dla aplikacji. Jeśli rekord A został zmapowany do aplikacji, należy zaktualizować rejestr domeny za pomocą tego nowego, dedykowanego adresu IP.

    Strona **Domena niestandardowa** Twojej aplikacji zostanie zaktualizowana za pomocą nowego, dedykowanego adresu IP. [Skopiuj ten adres IP](app-service-web-tutorial-custom-domain.md#info), a następnie [ponownie mapuj rekord A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na ten nowy adres IP.

- Jeśli masz powiązanie SNI SSL z, ponownie `<app-name>.azurewebsites.net` [zamapuj każde mapowanie CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record) na punkt `sni.<app-name>.azurewebsites.net` zamiast tego (Dodaj `sni` prefiks).

## <a name="test-https"></a>Testowanie protokołu HTTPS

W różnych przeglądarkach przejdź do, aby `https://<your.custom.domain>` sprawdzić, czy program obsługuje aplikację.

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Zrzut ekranu przedstawiający przykład przeglądania do domeny niestandardowej z wyróżnionym adresem URL contoso.com.":::

Kod aplikacji może sprawdzić protokół za pośrednictwem nagłówka "x-appService-proto". Nagłówek będzie miał wartość `http` lub `https` . 

> [!NOTE]
> Jeśli Twoja aplikacja wyświetla błędy walidacji certyfikatu, prawdopodobnie używasz certyfikatu z podpisem własnym.
>
> Jeśli tak nie jest, certyfikaty pośrednie mogły zostać pominięte podczas eksportowania certyfikatu do pliku PFX.

## <a name="prevent-ip-changes"></a>Zapobiegaj zmianom adresów IP

Adres IP ruchu przychodzącego może ulec zmianie po usunięciu powiązania, nawet jeśli to powiązanie jest Połączenie SSL z adresu IP. Jest to szczególnie ważne w przypadku odnowienia certyfikatu, który znajduje się już w powiązaniu Połączenie SSL z adresu IP. Aby uniknąć zmian w adresie IP aplikacji, wykonaj kolejno następujące kroki:

1. Przekaż nowy certyfikat.
2. Powiąż nowy certyfikat z wybraną domeną niestandardową bez usuwania starego certyfikatu. Ta akcja zastępuje powiązanie zamiast usuwania go.
3. Usuń stary certyfikat. 

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS

Domyślnie każda osoba nadal może uzyskać dostęp do Twojej aplikacji przy użyciu protokołu HTTP. Możesz przekierować wszystkie żądania HTTP do portu HTTPS.

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w pozycji **Tylko HTTPS** wybierz opcję **Włączone**.

![Wymuszanie protokołu HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Po zakończeniu operacji przejdź do dowolnego adresu URL protokołu HTTP, który wskazuje Twoją aplikację. Na przykład:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Wymuszanie wersji protokołu TLS

Aplikacja domyślnie umożliwia korzystanie z protokołu [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, który jest zalecanym poziomem protokołu TLS przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Aby wymusić inne wersje protokołu TLS, wykonaj następujące kroki:

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w obszarze **wersji protokołu TLS** wybierz minimalną wersję protokołu TLS do użycia. To ustawienie kontroluje tylko wywołania przychodzące. 

![Wymuszanie protokołu TLS 1.1 lub 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Po ukończeniu operacji aplikacja odrzuca wszystkie połączenia z niższymi wersjami protokołu TLS.

## <a name="handle-tls-termination"></a>Obsługa zakończenia protokołu TLS

W App Service [wygaśnięcie protokołu TLS](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, więc wszystkie żądania HTTPS docierają do aplikacji jako nieszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

Wskazówki dotyczące konfiguracji specyficzne dla języka, takie jak przewodnik [konfiguracji Node.js systemu Linux](configure-language-nodejs.md#detect-https-session) , przedstawiają sposób wykrywania sesji HTTPS w kodzie aplikacji.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Więcej zasobów

* [Używanie certyfikatu TLS/SSL w kodzie w usłudze Azure App Service](configure-ssl-certificate-in-code.md)
* [Często zadawane pytania: certyfikaty App Service](./faq-configuration-and-management.md)