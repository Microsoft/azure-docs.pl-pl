---
title: 'Samouczek: konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN'
description: Z tego samouczka dowiesz się, jak włączać i wyłączać protokół HTTPS w domenie niestandardowej punktu końcowego usługi Azure CDN.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 61ba50f8ec9e1de18238160b23096670753cffd6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367507"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Samouczek: konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN

Ten samouczek pokazuje, jak włączyć protokół HTTPS dla domeny niestandardowej, którą skojarzono z punktem końcowym usługi Azure CDN. 

Protokół HTTPS w domenie niestandardowej (na przykład https: \/ /www.contoso.com) gwarantuje, że poufne dane są bezpiecznie dostarczane za pośrednictwem protokołu TLS/SSL. Gdy przeglądarka sieci Web jest połączona za pośrednictwem protokołu HTTPS, przeglądarka weryfikuje certyfikat witryny sieci Web. Przeglądarka sprawdzi, czy jest wystawiona przez uprawniony urząd certyfikacji. Ten proces zapewnia bezpieczeństwo i chroni aplikacje internetowe przed atakami.

Domyślnie usługa Azure CDN obsługuje protokół HTTPS w obrębie nazwy hosta punktu końcowego usługi CDN. Na przykład w przypadku tworzenia punktu końcowego usługi CDN (takiego jak https:\//contoso.azureedge.net) protokół HTTPS jest włączany automatycznie.  

Niektóre z kluczowych atrybutów niestandardowej funkcji HTTPS to:

- Bez dodatkowych kosztów: nie są naliczane opłaty za nabycie lub odnowienie certyfikatu ani żadne dodatkowe koszty związane z ruchem HTTPS. Płacisz tylko za ruch wychodzący z sieci CDN (GB).

- Proste włączanie: w witrynie [Azure Portal](https://portal.azure.com) jest dostępna aprowizacja przy użyciu jednego kliknięcia. Aby włączyć tę funkcję, można użyć interfejsu API REST lub innych narzędzi dla deweloperów.

- Dostępne jest kompletne zarządzanie certyfikatami: 
    * wszystkie operacje nabywania certyfikatów i zarządzania nimi są obsługiwane za użytkownika. 
    * Certyfikaty są automatycznie inicjowane i odnawiane przed wygaśnięciem.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Włączanie protokołu HTTPS w domenie niestandardowej
> - Używanie certyfikatu zarządzanego przez usługę CDN 
> - Używanie własnego certyfikatu
> - Weryfikowanie domeny
> - Wyłączanie protokołu HTTPS w domenie niestandardowej

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Przed wykonaniem kroków opisanych w tym samouczku Utwórz profil sieci CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

Skojarz Azure CDN domeną niestandardową w punkcie końcowym usługi CDN. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie domeny niestandardowej do punktu końcowego Azure CDN](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> Certyfikaty zarządzane przez usługę CDN nie są dostępne dla domen głównych lub nadrzędnych. Jeśli Azure CDN domena niestandardowa jest domeną katalogu głównego lub wierzchołka, należy użyć funkcji Przenieś własny certyfikat. 
>

---

## <a name="tlsssl-certificates"></a>Certyfikaty TLS/SSL

Aby włączyć protokół HTTPS w domenie niestandardowej Azure CDN, należy użyć certyfikatu TLS/SSL. Wybierz opcję użycia certyfikatu zarządzanego przez program Azure CDN lub Użyj certyfikatu.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opcja 1 (domyślna): włączanie funkcji HTTPS przy użyciu certyfikatu zarządzanego przez usługę Azure CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN obsługuje zadania zarządzania certyfikatami, takie jak zaopatrzenie i odnawianie. Po włączeniu funkcji proces rozpocznie się automatycznie. 

Jeśli domena niestandardowa jest już zamapowana na punkt końcowy usługi CDN, dalsze akcje nie są konieczne. Usługa Azure CDN przetworzy kroki i wykona żądanie automatycznie.

Jeśli domena niestandardowa jest mapowana w innym miejscu, Użyj poczty e-mail do zweryfikowania własności domeny.

Aby włączyć protokół HTTPS w domenie niestandardowej, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć certyfikat zarządzany przez Azure CDN. Wyszukaj i wybierz pozycję **Profile sieci CDN**. 

2. Wybierz swój profil:
    * **Azure CDN Standard od firmy Microsoft**
    * **Azure CDN Standard z Akamai**
    * **Azure CDN Standard z Verizon**
    * **Azure CDN Premium od Verizon**

3. Na liście punktów końcowych usługi CDN wybierz punkt końcowy zawierający domenę niestandardową.

    ![Lista punktów końcowych](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Zostanie wyświetlona strona **Punkt końcowy**.

4. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    ![Zrzut ekranu przedstawia stronę domena niestandardowa z opcją używania własnego certyfikatu.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Zostanie wyświetlona strona **Domena niestandardowa**.

5. W obszarze typu zarządzania certyfikatem wybierz pozycję **Zarządzany przez usługę CDN**.

6. Wybierz pozycję **Wł.**, aby włączyć protokół HTTPS.

    ![Stan protokołu HTTPS domeny niestandardowej](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Kontynuuj [Weryfikowanie domeny](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Opcja 2: włączanie funkcji HTTPS przy użyciu własnego certyfikatu](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Ta opcja jest dostępna tylko w przypadku **Azure CDN firmy Microsoft** i **Azure CDN z profilów Verizon** . 
>
 
Możesz włączyć funkcję HTTPS przy użyciu własnego certyfikatu. Ten proces odbywa się dzięki integracji z usługą Azure Key Vault, umożliwiającą bezpieczne przechowywanie certyfikatów. Azure CDN używa tego mechanizmu bezpiecznego do uzyskania certyfikatu i wymaga kilku dodatkowych kroków. Podczas tworzenia certyfikatu TLS/SSL należy go utworzyć z dozwolonym urzędem certyfikacji. W przeciwnym razie, jeśli skorzystasz z niedozwolonego urzędu certyfikacji, żądanie zostanie odrzucone. Aby uzyskać listę dozwolonych urzędów certyfikacji, zobacz [Dozwolone urzędy certyfikacji umożliwiające korzystanie z niestandardowego protokołu HTTPS w usłudze Azure CDN](cdn-troubleshoot-allowed-ca.md). W przypadku **Azure CDN z Verizon** każdy prawidłowy urząd certyfikacji zostanie zaakceptowany. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Przygotowywanie certyfikatu i konta usługi Azure Key Vault
 
1. Usługa Azure Key Vault: musisz mieć konto usługi Azure Key Vault uruchomione w ramach tej samej subskrypcji co profil usługi Azure CDN i punkty końcowe CDN, dla których chcesz włączyć niestandardowy protokół HTTPS. Utwórz konto usługi Azure Key Vault, jeśli go nie masz.
 
2. Certyfikaty Azure Key Vault: Jeśli masz certyfikat, przekaż go bezpośrednio do konta Azure Key Vault. Jeśli nie masz certyfikatu, Utwórz nowy certyfikat bezpośrednio za pomocą Azure Key Vault.

### <a name="register-azure-cdn"></a>Rejestrowanie usługi Azure CDN

Rejestrowanie usługi Azure CDN jako aplikacji w usłudze Azure Active Directory za pomocą programu PowerShell.

1. W razie potrzeby zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) na maszynie lokalnej.

2. W programie PowerShell uruchom następujące polecenie:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** to nazwa główna usługi dla **Microsoft. AzureFrontDoor-CDN**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Udzielanie usłudze Azure CDN dostępu do magazynu kluczy
 
Udziel usłudze Azure CDN uprawnień dostępu do certyfikatów (wpisów tajnych) na koncie usługi Azure Key Vault.

1. W magazynie kluczy w sekcji **Ustawienia** wybierz pozycję **zasady dostępu**. W okienku po prawej stronie wybierz pozycję **+ Dodaj zasady dostępu**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Tworzenie zasad dostępu do magazynu kluczy dla sieci CDN" border="true":::

2. Na stronie **Dodawanie zasad dostępu** wybierz pozycję **nie wybrano** obok pozycji **podmiot zabezpieczeń**. Na stronie **podmiotu zabezpieczeń** wprowadź **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**. Wybierz pozycję **Microsoft. AzureFrontdoor-CDN**.  Wybierz **pozycję Wybierz**:

2. W obszarze **Wybieranie podmiotu zabezpieczeń** Wyszukaj pozycję **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, wybierz pozycję **Microsoft. AzureFrontDoor-CDN**. Wybierz pozycję **Wybierz**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Wybierz nazwę główną usługi Azure CDN" border="true":::
    
3. Wybierz **uprawnienia certyfikatów**. Zaznacz pola wyboru dla opcji **Pobierz** i **Wyświetl** , aby zezwolić usłudze CDN na pobieranie i wyświetlanie listy certyfikatów.

4. Wybierz pozycję **uprawnienia tajne**. Zaznacz pola wyboru dla opcji **Pobierz** i **Wyświetl** , aby zezwolić usłudze CDN na pobieranie i wyświetlanie wpisów tajnych:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Wybierz uprawnienia dla usługi CDN do magazynu kluczy" border="true":::

5. Wybierz pozycję **Dodaj**. 

> [!NOTE]
> Usługa Azure CDN ma teraz dostęp do tego magazynu kluczy i certyfikatów (kluczy tajnych), które są przechowywane w tym magazynie kluczy. Każde wystąpienie usługi CDN utworzone w tej subskrypcji będzie miało dostęp do certyfikatów w tym magazynie kluczy. 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Wybieranie certyfikatu do wdrożenia dla usługi Azure CDN
 
1. Wróć do portalu usługi Azure CDN, a następnie wybierz profil i punkt końcowy CDN, dla którego chcesz włączyć niestandardowy protokół HTTPS. 

2. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    Zostanie wyświetlona strona **Domena niestandardowa**.

3. W obszarze Typ zarządzania certyfikatami wybierz pozycję **Użyj własnego certyfikatu**. 

    ![Konfigurowanie własnego certyfikatu](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Wybierz magazyn kluczy, certyfikat (klucz tajny) i wersję certyfikatu.

    Usługa Azure CDN wyświetli następujące informacje: 
    - Konta magazynów kluczy dla Twojego identyfikatora subskrypcji. 
    - Certyfikaty (klucze tajne) dla wybranego magazynu kluczy. 
    - Dostępne wersje certyfikatów. 
 
5. Wybierz pozycję **Wł.**, aby włączyć protokół HTTPS.
  
6. W przypadku korzystania z certyfikatu Walidacja domeny nie jest wymagana. Kontynuuj [oczekiwanie na propagację](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Weryfikowanie domeny

Jeśli masz domenę niestandardową w użyciu zamapowanej do niestandardowego punktu końcowego z rekordem CNAME lub używasz własnego certyfikatu, przejdź do [niestandardowej domeny zamapowanej na punkt końcowy usługi CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

W przeciwnym razie, Jeśli wpis rekordu CNAME dla punktu końcowego już nie istnieje lub zawiera poddomenę cdnverify, przejdź do [domeny niestandardowej, która nie jest zamapowana do punktu końcowego usługi CDN](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Domena niestandardowa została zamapowana na punkt końcowy usługi CDN według rekordu CNAME

Po dodaniu domeny niestandardowej do punktu końcowego został utworzony rekord CNAME w rejestratorze domeny DNS zamapowanego na nazwę hosta punktu końcowego usługi CDN. 

Jeśli ten rekord CNAME nadal istnieje i nie zawiera poddomeny cdnverify, urząd certyfikacji DigiCert używa go do automatycznego weryfikowania własności domeny niestandardowej. 

Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana.

Rekord CNAME powinien mieć następujący format:

* *Nazwa* to niestandardowa nazwa domeny.
* *Wartość* to nazwa hosta punktu końcowego usługi CDN.

| Nazwa            | Typ  | Wartość                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz [Tworzenie rekordu DNS CNAME](./cdn-map-content-to-custom-domain.md).

Jeśli rekord CNAME ma poprawny format, DigiCert automatycznie weryfikuje swoją niestandardową nazwę domeny i tworzy certyfikat dla domeny. Firma DigitCert nie wysyła weryfikacyjnej wiadomości e-mail i nie trzeba zatwierdzać swojego żądania. Certyfikat jest ważny przez jeden rok i zostanie odnowiony przed jego wygaśnięciem. Kontynuuj [oczekiwanie na propagację](#wait-for-propagation). 

Automatyczna weryfikacja zwykle trwa kilka godzin. Jeśli nie widzisz zweryfikowanej domeny w ciągu 24 godzin, Otwórz bilet pomocy technicznej.

>[!NOTE]
>Jeśli u swojego dostawcy DNS masz rekord autoryzacji urzędu certyfikacji (CAA, Certificate Authority Authorization), musi on zawierać firmę DigiCert jako prawidłowy urząd certyfikacji. Rekord CAA umożliwia właścicielom domen określanie za pomocą dostawców DNS, które urzędy certyfikacji będą upoważnione do wystawiania certyfikatów dla danej domeny. Jeśli urząd certyfikacji otrzymuje zamówienie na certyfikat dla domeny, która ma rekord CAA, a ten urząd certyfikacji nie jest wymieniony jako autoryzowany wystawca, nie może wystawić certyfikatu dla tej domeny ani domeny podrzędnej. Informacje o zarządzaniu rekordami CAA można znaleźć w temacie [Manage CAA records (Zarządzanie rekordami CAA)](https://support.dnsimple.com/articles/manage-caa-record/). Informacje o narzędziu obsługi rekordów CAA można znaleźć w temacie [CAA Record Helper (Pomocnik rekordów CAA)](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Domena niestandardowa nie jest zamapowana na punkt końcowy usługi CDN

>[!NOTE]
>Jeśli używasz **Azure CDN z Akamai**, należy skonfigurować następujący rekord CNAME, aby włączyć automatyczne sprawdzanie poprawności domeny. "_acme-wyzwania. &lt; niestandardowa nazwa hosta domeny &gt; -> CNAME > &lt; domena niestandardowa &gt; . AK-Acme-Challenge.azureedge.NET "

Jeśli wpis rekordu CNAME zawiera poddomenę cdnverify, postępuj zgodnie z pozostałymi instrukcjami w tym kroku.

DigiCert wysyła wiadomość e-mail weryfikacyjną na następujące adresy e-mail. Sprawdź, czy możesz zatwierdzić bezpośrednio z jednego z następujących adresów:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Aby można było zatwierdzić żądanie, w ciągu kilku minut powinna zostać wysłana wiadomość e-mail. W przypadku korzystania z filtru spamu Dodaj verification@digicert.com do listy dozwolonych. Jeśli w ciągu 24 godzin nie otrzymasz wiadomości e-mail, skontaktuj się z działem pomocy technicznej firmy Microsoft.
    
![Wiadomość e-mail dotycząca weryfikacji domeny](./media/cdn-custom-ssl/domain-validation-email.png)

Po wybraniu linku zatwierdzania nastąpi przekierowanie do następującego formularza zatwierdzania w trybie online: 
    
![Formularz weryfikacji domeny](./media/cdn-custom-ssl/domain-validation-form.png)

Postępuj zgodnie z instrukcjami w formularzu. Dostępne są dwie opcje weryfikacji:

- Możesz zatwierdzać wszystkie przyszłe zamówienia składane za pośrednictwem tego samego konta dla tej samej domeny głównej; na przykład contoso.com. Ta metoda jest zalecana, jeśli planujesz dodać inne domeny niestandardowe dla tej samej domeny głównej.

- Możesz zatwierdzać tylko nazwę określonego hosta używaną w tym żądaniu. W przypadku kolejnych żądań wymagane jest inne zatwierdzenie.

Po zatwierdzeniu firma DigiCert kończy tworzenie certyfikatu dla niestandardowej nazwy domeny. Certyfikat jest ważny przez jeden rok i zostanie odnowiony przed jego wygaśnięciem.

## <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po zweryfikowaniu nazwy domeny aktywowanie funkcji protokołu HTTPS domeny niestandardowej może potrwać 6–8 godzin. Po zakończeniu procesu niestandardowy stan protokołu HTTPS w Azure Portal zostanie zmieniony na **włączone**. Cztery kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa jest teraz gotowa do korzystania z protokołu HTTPS.

![Okno dialogowe włączania protokołu HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku włączania protokołu HTTPS. Po włączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane cztery kroki operacji. W miarę jak każdy krok zostanie uaktywniony, inne szczegóły podetapu są wyświetlane pod krokiem w miarę postępu. Nie wszystkie kroki podrzędne zostaną wykonane. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Krok operacji | Szczegóły kroku podrzędnego operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| | Trwa przesyłanie żądania HTTPS. |
| | Żądanie HTTPS zostało pomyślnie przesłane. |
| 2 Walidacja domeny | Domena jest automatycznie weryfikowana, jeśli rekord CNAME jest mapowany na punkt końcowy usługi CDN. W przeciwnym razie żądanie weryfikacji zostanie wysłane na adres e-mail z listy w rekordzie rejestracji domeny (rejestrator WHOIS).|
| | Własność domeny została pomyślnie zweryfikowana. |
| | Żądanie weryfikacji własności domeny wygasło (prawdopodobnie klient nie odpowiedział w ciągu 6 dni). Protokół HTTPS nie zostanie włączony w domenie. * |
| | Żądanie weryfikacji własności klienta zostało odrzucone przez klienta. Protokół HTTPS nie zostanie włączony w domenie. * |
| 3 Aprowizowanie certyfikatu | Trwa wystawianie przez urząd certyfikacji certyfikatu wymaganego do włączenia obsługi protokołu HTTPS w domenie. |
| | Certyfikat został wystawiony i trwa jego wdrażanie w sieci CDN. Może to potrwać do 6 godzin. |
| | Certyfikat został pomyślnie wdrożony w sieci CDN. |
| 4 Ukończenie | Obsługa protokołu HTTPS została pomyślnie włączona w domenie. |

\* Ten komunikat jest wyświetlany tylko w przypadku wystąpienia błędu. 

Jeśli przed przesłaniem żądania wystąpi błąd, zostanie wyświetlony następujący komunikat o błędzie:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Oczyszczanie zasobów — wyłączanie protokołu HTTPS

W tej sekcji dowiesz się, jak wyłączyć protokół HTTPS dla domeny niestandardowej.

### <a name="disable-the-https-feature"></a>Wyłączanie funkcji protokołu HTTPS 

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję profile sieci **CDN**. 

2. Wybierz **standard Azure CDN firmy Microsoft**, **Azure CDN Standard from Verizon** lub **Azure CDN Premium z poziomu profilu Verizon** .

3. Na liście punktów końcowych wybierz punkt końcowy zawierający domenę niestandardową.

4. Wybierz domenę niestandardową, dla której chcesz wyłączyć protokół HTTPS.

    ![Lista domen niestandardowych](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Wybierz opcję **Wyłącz** , aby wyłączyć protokół https, a następnie wybierz pozycję **Zastosuj**.

    ![Okno niestandardowego protokołu HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po wyłączeniu funkcji protokołu HTTPS w domenie niestandardowej wprowadzenie tej zmiany może potrwać 6–8 godzin. Po zakończeniu procesu niestandardowy stan protokołu HTTPS w Azure Portal zostanie zmieniony na **wyłączony**. Trzy kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa nie może już używać protokołu HTTPS.

![Okno dialogowe wyłączania protokołu HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku wyłączenia protokołu HTTPS. Po wyłączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej zostaną wyświetlone trzy kroki operacji. Gdy krok zostanie uaktywniony, szczegóły pojawią się w ramach kroku. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Postęp operacji | Szczegóły operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| 2 Anulowanie aprowizacji certyfikatu | Usuwanie certyfikatu |
| 3 Kończenie | Certyfikat został usunięty |

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. *Kto jest dostawcą certyfikatów i jaki typ certyfikatu jest używany?*

    Dedykowany certyfikat dostarczany przez DigiCert jest używany dla domeny niestandardowej dla:
    
    * **Azure CDN z Verizon**
    * **Azure CDN od firmy Microsoft**

2. *Używasz protokołu TLS/SSL SNI, czy opartego na protokole IP?*

    Zarówno usługa **Azure CDN od firmy Verizon**, jak i **usługa Azure CDN Standard od firmy Microsoft**, używają protokołu TLS/SSL z rozszerzeniem SNI.

3. *Co zrobić, jeśli nie otrzymam wiadomości e-mail weryfikującej domenę od firmy DigiCert?*

    Jeśli nie korzystasz z domeny podrzędnej *cdnverify* , a wpis CNAME dotyczy nazwy hosta punktu końcowego, nie otrzymasz wiadomości e-mail weryfikującej domenę.
     
    Walidacja będzie wykonywana automatycznie. W przeciwnym razie, jeśli nie masz wpisu CNAME i nie otrzymasz wiadomości e-mail w ciągu 24 godzin, skontaktuj się z działem pomocy technicznej firmy Microsoft.

4. *Czy używanie certyfikatu SAN jest mniejsze bezpieczne niż certyfikatu dedykowanego?*
    
    Certyfikat SAN działa zgodnie z tymi samymi standardami szyfrowania i zabezpieczeń, co certyfikat dedykowany. Wszystkie wystawione certyfikaty TLS/SSL używają algorytmu SHA-256 w celu zapewnienia bezpieczeństwa serwera rozszerzonego.

5. *Czy muszę mieć rekord autoryzacji urzędu certyfikacji z moim dostawcą DNS?*

    Rekord autoryzacji urzędu certyfikacji nie jest obecnie wymagany. Jeśli jednak istnieje, musi zawierać firmę DigiCert jako prawidłowy urząd certyfikacji.

6. *20 czerwca 2018 Azure CDN od Verizon uruchomione przy użyciu dedykowanego certyfikatu z opcją SNI TLS/SSL. Co się stanie z moją istniejącą domeną niestandardową przy użyciu certyfikatu alternatywnej nazwy podmiotu (SAN) i protokołu TLS/SSL opartego na protokole IP?*

    Istniejące domeny będą stopniowo migrowane do pojedynczego certyfikatu w ciągu najbliższych miesięcy, jeśli analiza przeprowadzona przez firmę Microsoft określi, że żądania skierowane do Twojej aplikacji wykonują tylko klienci SNI. 
    
    W przypadku wykrycia klientów innych niż SNI domeny są przechowywane w certyfikacie sieci SAN przy użyciu protokołu TLS/SSL opartego na protokole IP. Nie ma to żadnego oddziaływania na żądania kierowane do usługi lub klientów, którzy nie są SNI.

7. *Jak działają odnowienia certyfikatów przy użyciu pakietu do przenoszenia własnego certyfikatu?*

    Aby zapewnić, że nowszy certyfikat jest wdrażany w infrastrukturze PoP, Przekaż nowy certyfikat do magazynu kluczy platformy Azure. W ustawieniach protokołu TLS na Azure CDN wybierz najnowszą wersję certyfikatu i wybierz pozycję Zapisz. Azure CDN następnie będzie propagowany nowy zaktualizowany certyfikat. 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Włączanie protokołu HTTPS w domenie niestandardowej
> - Używanie certyfikatu zarządzanego przez usługę CDN 
> - Używanie własnego certyfikatu
> - Weryfikowanie domeny
> - Wyłączanie protokołu HTTPS w domenie niestandardowej

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować buforowanie w punkcie końcowym usługi CDN.

> [!div class="nextstepaction"]
> [Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)