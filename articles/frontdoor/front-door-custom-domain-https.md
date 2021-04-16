---
title: Samouczek — konfigurowanie protokołu HTTPS w domenie niestandardowej dla Azure Front Door | Microsoft Docs
description: Z tego samouczka dowiesz się, jak włączyć i wyłączyć protokół HTTPS Azure Front Door konfiguracji domeny niestandardowej.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.openlocfilehash: 4291a7d46c723f799cf9d09ca0e7a3f6d614971f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389744"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Samouczek: konfigurowanie protokołu HTTPS w niestandardowej domenie usługi Front Door

W tym samouczku przedstawiono, jak włączyć protokół HTTPS dla niestandardowej domeny skojarzonej z usługą Front Door w sekcji hostów frontonu. Używając protokołu HTTPS w domenie niestandardowej (na przykład https: /www.contoso.com), zapewniasz, że poufne dane są bezpiecznie dostarczane za pośrednictwem szyfrowania TLS/SSL, gdy są wysyłane przez \/ Internet. Gdy przeglądarka internetowa łączy się z witryną internetową za pośrednictwem protokołu HTTPS, weryfikuje, czy certyfikat zabezpieczeń witryny internetowej jest poprawny i czy został wystawiony przez autentyczny urząd certyfikacji. Ten proces zapewnia bezpieczeństwo i chroni aplikacje internetowe przed atakami.

Azure Front Door domyślnie obsługuje protokół HTTPS Front Door domyślnej nazwie hosta. Jeśli na przykład utworzysz nową Front Door (na przykład ), protokół HTTPS zostanie automatycznie włączony `https://contoso.azurefd.net` dla żądań do usługi `https://contoso.azurefd.net` . Jednak po dołączyć domenę niestandardową "www.contoso.com" należy dodatkowo włączyć protokół HTTPS dla tego hosta frontonie.   

Niektóre z kluczowych atrybutów niestandardowej funkcji HTTPS to:

- Brak dodatkowych kosztów: nie ma kosztów pozyskiwania ani odnawiania certyfikatów ani dodatkowych kosztów ruchu HTTPS. 

- Proste włączanie: w witrynie [Azure Portal](https://portal.azure.com) jest dostępna aprowizacja przy użyciu jednego kliknięcia. Aby włączyć tę funkcję, można użyć interfejsu API REST lub innych narzędzi dla deweloperów.

- Dostępne jest kompletne zarządzanie certyfikatami: użytkownik obsługuje wszystkie operacje nabywania certyfikatów i zarządzania nimi. Certyfikaty są automatycznie aprowizowane i odnawiane przed wygaśnięciem, co usuwa ryzyko przerwania usługi z powodu wygaśnięcia certyfikatu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Włączanie protokołu HTTPS w domenie niestandardowej
> - Używanie certyfikatu zarządzanego przez usługę AFD 
> - Użyj własnego certyfikatu, czyli niestandardowego certyfikatu TLS/SSL
> - Weryfikowanie domeny
> - Wyłączanie protokołu HTTPS w domenie niestandardowej


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door z co najmniej jedną dołączoną domeną niestandardową. Aby uzyskać więcej informacji, zobacz [Samouczek: dodawanie do usługi Front Door domeny niestandardowej](front-door-custom-domain.md).

## <a name="tlsssl-certificates"></a>Certyfikaty TLS/SSL

Aby włączyć protokół HTTPS w celu bezpiecznego dostarczania zawartości Front Door domenie niestandardowej, należy użyć certyfikatu TLS/SSL. Możesz użyć certyfikatu zarządzanego przez Azure Front Door lub użyć własnego certyfikatu.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Opcja 1 (ustawienie domyślne): użycie certyfikatu zarządzanego przez usługę Front Door

Jeśli używasz certyfikatu zarządzanego przez Azure Front Door, funkcję HTTPS można włączona za pomocą kilku kliknięć. Azure Front Door pełni obsługuje zadania zarządzania certyfikatami, takie jak zaopatrzenie i odnawianie. Po włączeniu funkcji proces rozpocznie się automatycznie. Jeśli domena niestandardowa jest już zamapowana do domyślnego hosta frontonu usługi Front Door (`{hostname}.azurefd.net`), nie są wymagane żadne dalsze działania. Usługa Front Door przetworzy kroki i wykona żądanie automatycznie. Jeśli jednak domena niestandardowa jest zmapowana w innej lokalizacji, należy użyć widomości e-mail, aby zweryfikować własność domeny.

Aby włączyć protokół HTTPS w domenie niestandardowej, wykonaj następujące kroki:

1. W [witrynie Azure Portal](https://portal.azure.com) przejdź do profilu **usługi Front Door**.

2. Na liście hostów frontonu wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

3. W sekcji **Domena niestandardowa HTTPS** wybierz pozycję **Włączone** i wybierz pozycję Front Door **zarządzane** jako źródło certyfikatu.

4. Wybierz pozycję Zapisz.

5. Przejdź do [weryfikowania domeny](#validate-the-domain).

> [!NOTE]
> * W przypadku certyfikatów zarządzanych przez usługę AFD jest wymuszany limit 64 znaków firmy DigiCert. Walidacja nie powiedzie się, jeśli ten limit zostanie przekroczony.
> * Włączanie protokołu HTTPS Front Door zarządzanym certyfikatem nie jest obsługiwane w przypadku domen wierzchołkowych/głównych (na przykład: contoso.com). W tym scenariuszu możesz użyć własnego certyfikatu.  Aby uzyskać więcej informacji, przejdź do opcji 2.

### <a name="option-2-use-your-own-certificate"></a>Opcja 2: użycie własnego certyfikatu

Możesz włączyć funkcję HTTPS przy użyciu własnego certyfikatu. Ten proces odbywa się dzięki integracji z usługą Azure Key Vault, umożliwiającą bezpieczne przechowywanie certyfikatów. Azure Front Door korzysta z tego bezpiecznego mechanizmu w celu uzyskania certyfikatu i wymaga kilku dodatkowych kroków. Podczas tworzenia certyfikatu protokołu TLS/SSL należy utworzyć go przy użyciu dozwolonego urzędu certyfikacji. W przeciwnym razie, jeśli skorzystasz z niedozwolonego urzędu certyfikacji, żądanie zostanie odrzucone. Aby uzyskać listę dozwolonych urzędów certyfikacji, zobacz Dozwolone urzędy certyfikacji umożliwiające włączenie niestandardowego [protokołu HTTPS](front-door-troubleshoot-allowed-ca.md)na Azure Front Door .

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Przygotowywanie certyfikatu i konta usługi Azure Key Vault
 
1. Usługa Azure Key Vault: musisz mieć konto usługi Azure Key Vault uruchomione w ramach tej samej subskrypcji co usługa Front Door, dla której chcesz włączyć niestandardowy protokół HTTPS. Utwórz konto usługi Azure Key Vault, jeśli go nie masz.

> [!WARNING]
> Azure Front Door obecnie obsługuje tylko konta Key Vault w tej samej subskrypcji co Front Door konfiguracji. Wybranie usługi Key Vault z innej subskrypcji niż usługa Front Door spowoduje błąd.

2. Certyfikaty usługi Azure Key Vault: jeśli masz już certyfikat, możesz przekazać go bezpośrednio na konto usługi Azure Key Vault lub utworzyć nowy certyfikat bezpośrednio za pomocą usługi Azure Key Vault za pośrednictwem jednego z partnerskich urzędów certyfikacji, z którymi zintegrowana jest usługa Azure Key Vault. Przekaż certyfikat jako **obiekt certyfikatu,** a nie jako klucz **tajny.**

> [!NOTE]
> W przypadku własnego certyfikatu TLS/SSL Front Door nie obsługuje certyfikatów przy użyciu algorytmów kryptografii EC.

#### <a name="register-azure-front-door"></a>Rejestrowanie Azure Front Door

Zarejestruj jednostkę usługi dla Azure Front Door jako aplikację w usłudze Azure Active Directory za pomocą programu PowerShell.

> [!NOTE]
> Ta akcja wymaga uprawnień administratora globalnego i musi być wykonywana tylko **raz dla** dzierżawy.

1. Jeśli to konieczne, zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) za pomocą programu PowerShell na komputerze lokalnym.

2. W programie PowerShell uruchom następujące polecenie:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Udzielanie Azure Front Door dostępu do magazynu kluczy
 
Przyznaj Azure Front Door dostępu do certyfikatów na twoim Azure Key Vault konta.

1. Na koncie magazynu kluczy w obszarze USTAWIENIA wybierz pozycję **Zasady dostępu**, a następnie wybierz pozycję **Dodaj nową**, aby utworzyć nową zasadę.

2. W obszarze **Wybierz podmiot zabezpieczeń** wyszukaj wartość **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** i wybierz pozycję **Microsoft.Azure.Frontdoor**. Kliknij pozycję **Wybierz**.

3. W **skrypcie** Uprawnienia w **kluczu** tajnym wybierz pozycję Pobierz Front Door aby umożliwić pobranie certyfikatu.

4. W **uprawnieniach certyfikatu** wybierz **pozycję Pobierz,** aby Front Door pobieranie certyfikatu.

5. Wybierz przycisk **OK**. 

    Azure Front Door uzyskać dostęp do tego Key Vault i certyfikatów przechowywanych w tym Key Vault.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Wybierz certyfikat do Azure Front Door wdrożenia
 
1. Wróć do usługi Front Door w portalu. 

2. Na liście domen niestandardowych wybierz domenę niestandardową, dla której chcesz włączyć protokół HTTPS.

    Zostanie wyświetlona strona **Domena niestandardowa**.

3. W obszarze Typ zarządzania certyfikatami wybierz pozycję **Użyj własnego certyfikatu**. 

4. Azure Front Door wymaga, aby subskrypcja konta Key Vault jest taka sama jak subskrypcja Front Door. Wybierz magazyn kluczy, wersję Tajne i Tajne.

    Azure Front Door zawiera następujące informacje: 
    - Konta magazynów kluczy dla Twojego identyfikatora subskrypcji. 
    - Wpisy tajne w wybranym magazynie kluczy. 
    - Dostępne wersje tajne.

    > [!NOTE]
    >  Aby certyfikat został automatycznie obrócony do najnowszej wersji, gdy w twojej aplikacji jest dostępna nowsza wersja certyfikatu Key Vault ustaw wersję tajnego na "Najnowsza". W przypadku wybrania określonej wersji należy ręcznie ponownie wybrać nową wersję do rotacji certyfikatów. Wdrożenie nowej wersji certyfikatu/tajnego może potrwać do 24 godzin. 
 
5. Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana. Kontynuuj [oczekiwanie na propagację](#wait-for-propagation).

## <a name="validate-the-domain"></a>Weryfikowanie domeny

Jeśli masz już używaną domenę niestandardową, która jest mapowana do niestandardowego punktu końcowego przy użyciu rekordu CNAME lub używasz własnego certyfikatu, przejdź do mapowania domeny niestandardowej na adres [Front Door](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). W przeciwnym razie, jeśli wpis rekordu CNAME dla domeny już nie istnieje lub zawiera domenę podrzędną afdverify, przejdź do pozycji Domena niestandardowa nie jest mapowana na domenę [Front Door](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Domena niestandardowa została zamapowana do usługi Front Door według rekordu CNAME

Gdy domena niestandardowa była dodawana do hostów frontonu usługi Front Door, został utworzony rekord CNAME w tabeli DNS rejestratora domen w celu zamapowania go na domyślną nazwę hosta .azurefd.net dla usługi Front Door. Jeśli ten rekord CNAME nadal istnieje i nie zawiera domeny podrzędnej afdverify, urząd certyfikacji DigiCert używa go do automatycznego weryfikowania własności domeny niestandardowej. 

Jeśli używasz własnego certyfikatu, walidacja domeny nie jest wymagana.

Rekord CNAME powinien mieć następujący format, gdzie *Nazwa* to nazwa domeny niestandardowej, a *Wartość* to domyślna nazwa hosta .azurefd.net usługi Front Door:

| Nazwa            | Typ  | Wartość                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz [Tworzenie rekordu DNS CNAME](../cdn/cdn-map-content-to-custom-domain.md).

Jeśli rekord CNAME na poprawny format, firma DigiCert weryfikuje nazwę domeny niestandardowej i tworzy dedykowany certyfikat dla nazwy domeny. Firma DigitCert nie wysyła weryfikacyjnej wiadomości e-mail i nie trzeba zatwierdzać swojego żądania. Certyfikat jest ważny przez jeden rok i zostanie automatycznie wye m.in. przed wygaśnięciem. Kontynuuj [oczekiwanie na propagację](#wait-for-propagation). 

Automatyczna walidacja przeważnie trwa kilka minut. Jeśli w ciągu godziny nie zobaczysz zweryfikowanej domeny, otwórz bilet pomocy technicznej.

>[!NOTE]
>Jeśli u swojego dostawcy DNS masz rekord autoryzacji urzędu certyfikacji (CAA, Certificate Authority Authorization), musi on zawierać firmę DigiCert jako prawidłowy urząd certyfikacji. Rekord CAA umożliwia właścicielom domen określanie za pomocą dostawców DNS, które urzędy certyfikacji będą upoważnione do wystawiania certyfikatów dla danej domeny. Jeśli urząd certyfikacji otrzymuje zamówienie na certyfikat dla domeny, która ma rekord CAA, a ten urząd certyfikacji nie jest wymieniony jako autoryzowany wystawca, nie może wystawić certyfikatu dla tej domeny ani domeny podrzędnej. Informacje o zarządzaniu rekordami CAA można znaleźć w temacie [Manage CAA records (Zarządzanie rekordami CAA)](https://support.dnsimple.com/articles/manage-caa-record/). Informacje o narzędziu obsługi rekordów CAA można znaleźć w temacie [CAA Record Helper (Pomocnik rekordów CAA)](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Domena niestandardowa nie została zamapowana do usługi Front Door

Jeśli wpis rekordu CNAME dla punktu końcowego już nie istnieje lub zawiera domenę podrzędną afdverify, postępuj zgodnie z dalszymi instrukcjami w tym kroku.

Po włączeniu protokołu HTTPS w domenie niestandardowej urząd certyfikacji DigiCert weryfikuje własność domeny, kontaktując się z jej rejestratorem zgodnie z informacjami o rejestratorze domeny z bazy danych [WHOIS](http://whois.domaintools.com/). Kontakt jest nawiązywany za pośrednictwem adresu e-mail (domyślnie) lub numeru telefonu wymienionego w rejestracji WHOIS. Zanim protokół HTTPS zostanie uaktywniony w domenie niestandardowej, należy ukończyć walidację domeny. Domenę należy zatwierdzić w ciągu sześciu dni roboczych. Żądania, które nie zostaną zatwierdzone w ciągu sześciu dni roboczych, zostaną automatycznie anulowane. 

![Rekord WHOIS](./media/front-door-custom-domain-https/whois-record.png)

Firma DigiCert wysyła również weryfikacyjną wiadomość e-mail na inne adresy e-mail. Jeśli informacje o rejestratorze WHOIS są prywatne, sprawdź, czy możesz zatwierdzić bezpośrednio z jednego z następujących adresów:

admin@&lt;nazwa-domeny.com&gt;  
administrator@&lt;nazwa-domeny.com&gt;  
webmaster@&lt;nazwa-domeny.com&gt;  
hostmaster@&lt;nazwa-domeny.com&gt;  
postmaster@&lt;nazwa-domeny.com&gt;  

W ciągu kilku minut otrzymasz wiadomość e-mail (podobną do poniższego przykładu) z prośbą o zatwierdzenie żądania. Jeśli używasz filtru spamu, dodaj go admin@digicert.com do listy zezwalania. Jeśli w ciągu 24 godzin nie otrzymasz wiadomości e-mail, skontaktuj się z działem pomocy technicznej firmy Microsoft.

Po wybraniu linku zatwierdzania zostanie skierowany do formularza zatwierdzania online. Postępuj zgodnie z instrukcjami w formularzu. Dostępne są dwie opcje weryfikacji:

- Możesz zatwierdzać wszystkie przyszłe zamówienia składane za pośrednictwem tego samego konta dla tej samej domeny głównej; na przykład contoso.com. To podejście jest zalecane, jeśli planujesz dodać więcej domen niestandardowych dla tej samej domeny głównej.

- Możesz zatwierdzać tylko nazwę określonego hosta używaną w tym żądaniu. Dodatkowe zatwierdzenie jest wymagane w przypadku kolejnych żądań.

Po zatwierdzeniu firma DigiCert kończy tworzenie certyfikatu dla niestandardowej nazwy domeny. Certyfikat jest ważny przez jeden rok i będzie autorejeterminowany przed wygaśnięciem.

## <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po zweryfikowaniu nazwy domeny aktywowanie funkcji protokołu HTTPS domeny niestandardowej może potrwać 6–8 godzin. Po ukończeniu procesu stan niestandardowego protokołu HTTPS w witrynie Azure Portal jest ustawiony na **Włączono**, a cztery kroki operacji w oknie dialogowym domeny niestandardowej są oznaczane jako ukończone. Domena niestandardowa jest teraz gotowa do korzystania z protokołu HTTPS.

### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku włączania protokołu HTTPS. Po włączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane cztery kroki operacji. Gdy każdy krok staje się aktywny, pod każdym krokiem w trakcie jego realizacji pojawia się więcej szczegółów podkropka. Nie wszystkie kroki podrzędne zostaną wykonane. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Krok operacji | Szczegóły kroku podrzędnego operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| | Trwa przesyłanie żądania HTTPS. |
| | Żądanie HTTPS zostało pomyślnie przesłane. |
| 2 Walidacja domeny | Domena jest automatycznie weryfikowana, jeśli jest to rekord CNAME zamapowany na domyślny host frontonu azurefd.net .Front Door. W przeciwnym razie żądanie weryfikacji zostanie wysłane na adres e-mail z listy w rekordzie rejestracji domeny (rejestrator WHOIS). Jak najszybciej zweryfikuj domenę. |
| | Własność domeny została pomyślnie zweryfikowana. |
| | Żądanie weryfikacji własności domeny wygasło (prawdopodobnie klient nie odpowiedział w ciągu 6 dni). Protokół HTTPS nie zostanie włączony w domenie. * |
| | Żądanie weryfikacji własności klienta zostało odrzucone przez klienta. Protokół HTTPS nie zostanie włączony w domenie. * |
| 3 Aprowizowanie certyfikatu | Trwa wystawianie przez urząd certyfikacji certyfikatu wymaganego do włączenia obsługi protokołu HTTPS w domenie. |
| | Certyfikat został wystawiony i trwa jego wdrażanie w usłudze Front Door. Ten proces może potrwać do 1 godziny. |
| | Certyfikat został pomyślnie wdrożony w usłudze Front Door. |
| 4 Ukończenie | Obsługa protokołu HTTPS została pomyślnie włączona w domenie. |

\* Ten komunikat jest wyświetlany tylko w przypadku wystąpienia błędu. 

Jeśli przed przesłaniem żądania wystąpi błąd, zostanie wyświetlony następujący komunikat o błędzie:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. *Kto jest dostawcą certyfikatów i jaki typ certyfikatu jest używany?*

    Dla domeny niestandardowej jest używany dedykowany/pojedynczy certyfikat dostarczony przez firmę DigiCert. 

2. *Używasz protokołu TLS/SSL SNI, czy opartego na protokole IP?*

    Azure Front Door korzysta z protokołu SNI TLS/SSL.

3. *Co zrobić, jeśli nie otrzymam wiadomości e-mail weryfikującej domenę od firmy DigiCert?*

    Jeśli masz wpis CNAME dla domeny niestandardowej, który wskazuje bezpośrednio na nazwę hosta punktu końcowego (i nie używasz nazwy domeny podrzędnej afdverify), nie otrzymasz wiadomości e-mail dotyczącej weryfikacji domeny. Walidacja będzie wykonywana automatycznie. W przeciwnym razie, jeśli nie masz wpisu CNAME i nie otrzymasz wiadomości e-mail w ciągu 24 godzin, skontaktuj się z działem pomocy technicznej firmy Microsoft.

4. *Czy używanie certyfikatu SAN jest mniejsze bezpieczne niż certyfikatu dedykowanego?*
    
    Certyfikat SAN działa zgodnie z tymi samymi standardami szyfrowania i zabezpieczeń, co certyfikat dedykowany. Wszystkie wystawione certyfikaty protokołu TLS/SSL używają szyfrowania SHA-256 w celu zwiększenia bezpieczeństwa serwera.

5. *Czy muszę mieć rekord autoryzacji urzędu certyfikacji z moim dostawcą DNS?*

    Nie, rekord autoryzacji urzędu certyfikacji nie jest obecnie wymagany. Jeśli jednak istnieje, musi zawierać firmę DigiCert jako prawidłowy urząd certyfikacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach protokół HTTPS został włączony w domenie niestandardowej. Jeśli nie chcesz już używać domeny niestandardowej z protokołem HTTPS, możesz wyłączyć protokół HTTPS, wykonując następujące czynności:

### <a name="disable-the-https-feature"></a>Wyłączanie funkcji protokołu HTTPS 

1. W [Azure Portal](https://portal.azure.com)przejdź do swojej **Azure Front Door** konfiguracji.

2. Na liście hostów frontendu wybierz domenę niestandardową, dla której chcesz wyłączyć protokół HTTPS.

3. Kliknij pozycję **Wyłączony**, aby wyłączyć protokół HTTPS, a następnie kliknij pozycję **Zapisz**.

### <a name="wait-for-propagation"></a>Oczekiwanie na propagację

Po wyłączeniu funkcji protokołu HTTPS w domenie niestandardowej wprowadzenie tej zmiany może potrwać 6–8 godzin. Po zakończeniu procesu stan niestandardowego protokołu HTTPS w witrynie Azure Portal zostanie ustawiony na wartość **Wyłączone,** a trzy kroki operacji w oknie dialogowym domeny niestandardowej zostaną oznaczone jako ukończone. Domena niestandardowa nie może już używać protokołu HTTPS.

#### <a name="operation-progress"></a>Postęp operacji

W poniższej tabeli przedstawiono postęp operacji w przypadku wyłączenia protokołu HTTPS. Po wyłączeniu protokołu HTTPS w oknie dialogowym domeny niestandardowej są wyświetlane trzy kroki operacji. Gdy każdy krok staje się aktywny, pod tym krokiem są wyświetlane dodatkowe szczegóły. Po pomyślnym ukończeniu kroku obok niego zostanie wyświetlony zielony znacznik wyboru. 

| Postęp operacji | Szczegóły operacji | 
| --- | --- |
| 1 Przesyłanie żądania | Przesyłanie żądania |
| 2 Anulowanie aprowizacji certyfikatu | Usuwanie certyfikatu |
| 3 Kończenie | Certyfikat został usunięty |

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Przekaż certyfikat do Key Vault.
* Weryfikowanie domeny.
* Włącz protokół HTTPS dla domeny niestandardowej.

Aby dowiedzieć się, jak skonfigurować zasady filtrowania geograficznego dla Front Door, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Konfigurowanie zasad filtrowania geograficznego](front-door-geo-filtering.md)
