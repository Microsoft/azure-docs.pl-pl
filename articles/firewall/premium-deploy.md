---
title: Wdrażanie i Konfigurowanie usługi Azure firewall Premium Preview
description: Dowiedz się, jak wdrożyć i skonfigurować usługę Azure firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721790"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Wdrażanie i Konfigurowanie usługi Azure firewall Premium Preview

> [!IMPORTANT]
> Usługa Azure firewall Premium jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Zapora Azure Premium w wersji zapoznawczej to Zapora nowej generacji z możliwościami, które są wymagane w środowiskach wysoce wrażliwych i regulowanych. Obejmuje następujące funkcje:

- **Inspekcja TLS** — odszyfrowuje ruch wychodzący, przetwarza dane, a następnie szyfruje dane i wysyła je do miejsca docelowego.
- **Dostawców tożsamości** — system wykrywania i zapobiegania włamaniom w sieci (dostawców tożsamości) pozwala monitorować działania sieciowe w poszukiwaniu złośliwego działania, rejestrować informacje o tym działaniu, zgłaszać je i opcjonalnie próbować go zablokować.
- **Filtrowanie adresów URL** — rozszerza możliwości filtrowania nazw FQDN zapory platformy Azure w celu uwzględnienia całego adresu URL. Na przykład `www.contoso.com/a/c` zamiast `www.contoso.com` .
- **Kategorie sieci Web** — Administratorzy mogą zezwalać użytkownikom na dostęp do takich kategorii witryn sieci Web, jak witryny hazard, witryny sieci Web mediów społecznościowych i inne osoby, jak na nie.

Aby uzyskać więcej informacji, zobacz [funkcje zapory platformy Azure w warstwie Premium](premium-features.md).

Szablon zostanie użyty do wdrożenia środowiska testowego, które ma centralną sieć wirtualną (10.0.0.0/16) z trzema podsieciami:
- podsieć procesu roboczego (10.0.10.0/24)
- podsieć usługi Azure bastionu (10.0.20.0/24)
- podsieć zapory (10.0.100.0/24)

W tym środowisku testowym jest używana jedna Centralna Sieć wirtualna dla uproszczenia. W celach produkcyjnych [topologia gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) z sieci wirtualnych równorzędnym jest częścią wspólną.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Topologia centralnej sieci wirtualnej":::

Maszyna wirtualna procesu roboczego jest klientem, który wysyła żądania HTTP/S za pośrednictwem zapory.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury

Szablon wdraża kompletne środowisko testowe dla zapory platformy Azure w warstwie Premium włączonej przy użyciu dostawców tożsamości, inspekcji TLS, filtrowania adresów URL i kategorii sieci Web:

- nowe zasady warstwy Premium i zapory platformy Azure ze wstępnie zdefiniowanymi ustawieniami, aby umożliwić łatwe sprawdzanie poprawności podstawowych możliwości (dostawców tożsamości, inspekcja TLS, filtrowanie adresów URL i kategorie sieci Web)
- wdraża wszystkie zależności, w tym Key Vault i zarządzaną tożsamość. W środowisku produkcyjnym te zasoby mogą być już utworzone i niewymagane w tym samym szablonie.
- generuje podpis głównego urzędu certyfikacji z podpisem własnym i wdraża go na wygenerowanym Key Vault
-  generuje pochodny pośredni urząd certyfikacji i wdraża go na testowej maszynie wirtualnej z systemem Windows (WorkerVM)
- Host bastionu (BastionHost) został również wdrożony i może służyć do nawiązywania połączenia z maszyną do testowania systemu Windows (WorkerVM)



[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Testowanie zapory

Teraz można testować dostawców tożsamości, inspekcję protokołu TLS, filtrowanie sieci Web i kategorie sieci Web.

### <a name="add-firewall-diagnostics-settings"></a>Dodaj ustawienia diagnostyki zapory

Aby zbierać Dzienniki zapory, należy dodać ustawienia diagnostyczne w celu zbierania dzienników zapory.

1. Wybierz **DemoFirewall** i w obszarze **monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
2. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.
3. W obszarze **Nazwa ustawienia diagnostycznego** wpisz polecenie *PD-diag*.
4. W obszarze **Dziennik** wybierz opcję **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule**.
5. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics obszarze roboczym**.
6. Wybierz pozycję **Zapisz**.

### <a name="idps-tests"></a>Testy dostawców tożsamości

Aby przetestować dostawców tożsamości, należy wdrożyć własny wewnętrzny serwer sieci Web przy użyciu odpowiedniego certyfikatu serwera. Aby uzyskać więcej informacji o wymaganiach dotyczących certyfikatu usługi Azure firewall Premium w wersji zapoznawczej, zobacz [Azure firewall Premium Preview Certificates](premium-certificates.md).

Za pomocą `curl` programu można kontrolować różne nagłówki HTTP i symulować złośliwy ruch.

#### <a name="to-test-idps-for-http-traffic"></a>Aby przetestować dostawców tożsamości dla ruchu HTTP:

1. Na maszynie wirtualnej WorkerVM Otwórz okno wiersza polecenia administratora.
2. W wierszu polecenia wpisz następujące polecenie:

   `curl -A "BlackSun" <your web server address>`
3. Zostanie wyświetlona odpowiedź serwera sieci Web.
4. Przejdź do dziennika reguły sieci zapory w Azure Portal, aby znaleźć alert podobny do następującego:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Komunikat alertu":::

   > [!NOTE]
   > Rozpoczęcie wyświetlania danych w dziennikach może potrwać trochę czasu. Poczekaj co najmniej 20 minut na rozpoczęcie wyświetlania danych przez dzienniki.
5. Dodaj regułę sygnatury dla podpisu 2008983:

   1. Wybierz pozycję **DemoFirewallPolicy** , a następnie w obszarze **Ustawienia** wybierz pozycję **dostawców tożsamości (wersja zapoznawcza)**.
   1. Wybierz kartę **reguły podpisów** .
   1. W obszarze **Identyfikator podpisu** w polu tekstowym otwórz wpisz *2008983*.
   1. W obszarze **tryb** wybierz pozycję **Odmów**.
   1. Wybierz pozycję **Zapisz**.
   1. Przed kontynuowaniem Zaczekaj na ukończenie wdrożenia.



6. W systemie WorkerVM ponownie uruchom `curl` polecenie:

   `curl -A "BlackSun" <your web server address>`

   Ponieważ żądanie HTTP jest teraz blokowane przez zaporę, po upływie limitu czasu połączenia zostaną wyświetlone następujące dane wyjściowe:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Przejdź do dzienników monitorowania w Azure Portal i Znajdź komunikat dla zablokowanego żądania.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Aby przetestować dostawców tożsamości dla ruchu HTTPS

Powtórz te testy, używając protokołu HTTPS zamiast protokołu HTTP. Na przykład:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Powinny zostać wyświetlone te same wyniki, które były używane w testach HTTP.

### <a name="tls-inspection-with-url-filtering"></a>Inspekcja protokołu TLS z filtrowaniem adresów URL

Wykonaj następujące kroki, aby przetestować inspekcję protokołu TLS z filtrowaniem adresów URL.

1. Edytuj reguły aplikacji zasad zapory i Dodaj nową regułę o nazwie `AllowURL` do `AllowWeb` kolekcji reguł. Skonfiguruj docelowy adres URL `www.nytimes.com/section/world` , źródłowy adres IP **\* *_, typ docelowy _* URL (wersja zapoznawcza)**, wybierz pozycję **Inspekcja TLS (wersja zapoznawcza)** i protokoły **http i https**.

3. Po zakończeniu wdrażania Otwórz przeglądarkę w witrynie WorkerVM i przejdź do `https://www.nytimes.com/section/world` i sprawdź, czy odpowiedź HTML jest wyświetlana w oczekiwany sposób w przeglądarce.
4. W Azure Portal można wyświetlić cały adres URL w dziennikach monitorowania reguły aplikacji:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Komunikat alertu przedstawiający adres URL":::

Niektóre strony HTML mogą wyglądać niekompletne, ponieważ odwołują się do innych adresów URL, które są odrzucane. Aby rozwiązać ten problem, można podjąć następujące podejście:

- Jeśli strona HTML zawiera linki do innych domen, można dodać te domeny do nowej reguły aplikacji z opcją Zezwalaj na dostęp do tych nazw FQDN.
- Jeśli strona HTML zawiera linki do podrzędnych adresów URL, można zmodyfikować regułę i dodać gwiazdkę do adresu URL. Na przykład: `targetURLs=www.nytimes.com/section/world*`

   Alternatywnie możesz dodać nowy adres URL do reguły. Na przykład: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Testowanie kategorii sieci Web

Utwórzmy regułę aplikacji, aby zezwolić na dostęp do witryn sieci Web sportowych.
1. W portalu Otwórz grupę zasobów i wybierz pozycję **DemoFirewallPolicy**.
2. Wybierz pozycję **reguły aplikacji**, a następnie **Dodaj kolekcję reguł**.
3. W obszarze **Nazwa** wpisz *GeneralWeb*, **priorytet** *103*, **Grupa kolekcji reguł** wybierz pozycję **DefaultApplicationRuleCollectionGroup**.
4. W obszarze **reguły** dla pozycji **Nazwa** wpisz *AllowSports*, **Source** *\** , **protokołu** *http, https*, Select **Inspekcja TLS**, **Typ docelowy** wybierz *kategorię sieci Web (wersja zapoznawcza)*, **miejsce docelowe** wybierz *Sport*.
5. Wybierz pozycję **Dodaj**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Kategoria sieci Web Sport":::
6. Po zakończeniu wdrażania przejdź do  **WorkerVM** i Otwórz przeglądarkę internetową i przejdź do `https://www.nfl.com` .

   Powinna zostać wyświetlona strona sieci Web LIGOWEJ, a w dzienniku reguły aplikacji zostanie wyświetlona wartość **Kategoria sieci Web: reguła sportowa** została dopasowana, a żądanie było dozwolone.

## <a name="next-steps"></a>Następne kroki

- [Zapora Azure Premium w wersji zapoznawczej w Azure Portal](premium-portal.md)