---
title: Konfigurowanie protokołu HTTPS dla domeny niestandardowej w konfiguracji jednostki SKU platformy Azure Front-Standard/Premium
description: W tym artykule dowiesz się, jak dołączyć domenę niestandardową do jednostki SKU usługi Azure Front-Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: f992f7d4295f380e4b1f2b13cc5da33df89354ab
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099616"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Konfigurowanie protokołu HTTPS w domenie niestandardowej (wersja zapoznawcza) w warstwie Standardowa/Premium przy użyciu Azure Portal

> [!NOTE]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Platforma Azure Front-Standard/Premium domyślnie umożliwia bezpieczne dostarczanie do aplikacji protokołu TLS w przypadku dodania domeny niestandardowej. Korzystając z protokołu HTTPS w domenie niestandardowej, upewnij się, że poufne dane są bezpiecznie dostarczane przy użyciu szyfrowania TLS/SSL, gdy są wysyłane przez Internet. Gdy przeglądarka internetowa łączy się z witryną internetową za pośrednictwem protokołu HTTPS, weryfikuje, czy certyfikat zabezpieczeń witryny internetowej jest poprawny i czy został wystawiony przez autentyczny urząd certyfikacji. Ten proces zapewnia bezpieczeństwo i chroni aplikacje internetowe przed atakami.

Usługa Azure Front-Standard/Premium obsługuje zarówno certyfikat zarządzany przez platformę Azure, jak i certyfikaty zarządzane przez klienta. Drzwi frontonu platformy Azure domyślnie automatycznie włączają protokół HTTPS do wszystkich domen niestandardowych przy użyciu certyfikatów zarządzanych przez platformę Azure. Nie są wymagane żadne dodatkowe kroki w celu uzyskania certyfikatu zarządzanego platformy Azure. Certyfikat jest tworzony podczas procesu weryfikacji domeny. Możesz również użyć własnego certyfikatu, integrując usługę Azure Front-Standard/Premium z Key Vault.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [**dodatkowe warunki użytkowania wersji**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby można było skonfigurować protokół HTTPS dla domeny niestandardowej, należy najpierw utworzyć profil Standard/Premium platformy Azure z przodu. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie profilu usługi Azure Front-Standard/Premium](create-front-door-portal.md).

* Jeśli nie masz jeszcze domeny niestandardowej, musisz najpierw zakupić ją u dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](../../app-service/manage-custom-dns-buy-domain.md).

* Jeśli używasz platformy Azure do hostowania [domen DNS](../../dns/dns-overview.md), musisz delegować system nazw domen (DNS) dostawcy domeny do Azure DNS. Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, musisz ręcznie zweryfikować domenę, wprowadzając monity o rekordy TXT DNS.

## <a name="azure-managed-certificates"></a>Certyfikaty zarządzane na platformie Azure

1. W obszarze Ustawienia dla profilu platformy Azure w warstwie Standardowa/Premium wybierz pozycję **domeny** , a następnie wybierz pozycję **+ Dodaj** , aby dodać nową domenę.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Zrzut ekranu strony docelowej konfiguracja domeny.":::

1. Na stronie **Dodawanie domeny** w celu *zarządzania systemem DNS* wybierz opcję **zarządzana usługa DNS platformy Azure** . 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie domeny z wybraną usługą Azure Managed DNS.":::

1. Zweryfikuj i skojarz domenę niestandardową z punktem końcowym, wykonując czynności opisane w temacie Włączanie [domeny niestandardowej](how-to-add-custom-domain.md).

1. Po pomyślnym skojarzeniu domeny niestandardowej z punktem końcowym zostanie wdrożony certyfikat zarządzany przez platformę Azure do przodu. Ten proces może potrwać kilka minut.

## <a name="using-your-own-certificate"></a>Korzystanie z własnego certyfikatu

Możesz również użyć własnego certyfikatu TLS. Ten certyfikat należy zaimportować do Azure Key Vault, zanim będzie można go używać z usługami Azure Front-Standard/Premium. Zobacz [Importowanie certyfikatu](../../key-vault/certificates/tutorial-import-certificate.md) do Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Przygotowywanie certyfikatu i konta usługi Azure Key Vault
 
1. Musisz mieć uruchomione konto Azure Key Vault w ramach tej samej subskrypcji, co w warstwie Standardowa/Premium platformy Azure, która umożliwia włączenie niestandardowego protokołu HTTPS. Utwórz konto usługi Azure Key Vault, jeśli go nie masz.

    > [!WARNING]
    > Drzwi frontonu platformy Azure obecnie obsługują tylko Key Vault kont w tej samej subskrypcji co konfiguracja drzwi z przodu. Wybranie Key Vault w ramach innej subskrypcji niż wersja Standard/Premium platformy Azure w warstwie Standardowa spowoduje awarię.

1. Jeśli masz już certyfikat, możesz przekazać go bezpośrednio do konta Azure Key Vault. W przeciwnym razie Utwórz nowy certyfikat bezpośrednio za pośrednictwem Azure Key Vault z jednego z urzędów certyfikacji partnera, który Azure Key Vault integruje się z usługą. Przekaż swój certyfikat jako obiekt **certyfikatu** , a nie **wpis tajny**.

    > [!NOTE]
    > W przypadku własnego certyfikatu TLS/SSL nie są obsługiwane certyfikaty z algorytmami kryptografii.

#### <a name="register-azure-front-door"></a>Zarejestruj drzwi frontonu platformy Azure

Zarejestruj jednostkę usługi dla drzwi platformy Azure jako aplikację w Azure Active Directory za pomocą programu PowerShell.

> [!NOTE]
> Ta akcja wymaga uprawnień administratora globalnego i musi zostać wykonana tylko **raz** dla każdego dzierżawy.

1. Jeśli to konieczne, zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) za pomocą programu PowerShell na komputerze lokalnym.

1. W programie PowerShell uruchom następujące polecenie:

     `New-AzADServicePrincipal -ApplicationId 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8""`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Przyznaj dostęp do usługi Azure front-drzwi do Twojego magazynu kluczy
 
Przyznaj uprawnienie platformy Azure przed dostępem do certyfikatów na koncie Azure Key Vault.

1. Na koncie magazynu kluczy w obszarze Ustawienia wybierz pozycję **zasady dostępu**. Następnie wybierz pozycję **Dodaj nowe** , aby utworzyć nowe zasady.

1. W obszarze **Wybierz podmiot zabezpieczeń** Wyszukaj pozycję **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** i wybierz pozycję * * Microsoft. AzureFrontDoor-CDN * *. Kliknij pozycję **Wybierz**.

1. W obszarze **uprawnienia do klucza tajnego** wybierz pozycję **Pobierz** , aby umożliwić przednim drzwiom pobranie certyfikatu.

1. W obszarze **uprawnienia certyfikatu** wybierz pozycję **Pobierz** , aby umożliwić przednim drzwiom pobranie certyfikatu.

1. Wybierz przycisk **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Wybierz certyfikat dla drzwi frontonu platformy Azure do wdrożenia
 
1. Wróć do warstwy Standardowa/Premium platformy Azure w portalu.

1. Przejdź do pozycji wpisy **tajne** w obszarze *Ustawienia* , a następnie wybierz pozycję **Dodaj certyfikat**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Zrzut ekranu przedstawiający stronę początkową wpisu tajnego drzwi platformy Azure.":::

1. Na stronie **Dodawanie certyfikatu** zaznacz pole wyboru certyfikatu, który chcesz dodać do usługi Azure Front-Standard/Premium. Pozostaw wybraną wersję jako "Najnowsza" i wybierz pozycję **Dodaj**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie certyfikatu.":::

1. Po pomyślnym udostępnieniu certyfikatu można go użyć podczas dodawania nowej domeny niestandardowej.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Zrzut ekranu certyfikatu został pomyślnie dodany do wpisów tajnych.":::

1. Przejdź do pozycji **domeny** w obszarze *ustawienie* i wybierz pozycję **+ Dodaj** , aby dodać nową domenę niestandardową. Na stronie **Dodawanie domeny** wybierz pozycję "Przenieś własny certyfikat (BYOC)" dla *protokołu HTTPS*. Dla *wpisu tajnego* wybierz certyfikat, którego chcesz użyć z listy rozwijanej. 

    > [!NOTE]
    > Wybrany certyfikat musi mieć nazwę pospolitą (CN), która jest taka sama jak dodawana domena niestandardowa.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie niestandardowej domeny z protokołem HTTPS.":::

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby sprawdzić poprawność certyfikatu. Następnie skojarz nowo utworzoną domenę niestandardową z punktem końcowym, jak opisano w temacie [Tworzenie niestandardowego](how-to-add-custom-domain.md) podręcznika domeny.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Zmień z platformy Azure zarządzanej w celu przeniesienia własnego certyfikatu (BYOC)

1. Istniejący certyfikat zarządzany na platformie Azure można zmienić na certyfikat zarządzany przez użytkownika, wybierając stan certyfikatu, aby otworzyć stronę **Szczegóły certyfikatu** .

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Zrzut ekranu stanu certyfikatu na stronie docelowej domeny." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Na stronie **Szczegóły certyfikatu** można zmienić pozycję "zarządzane przez platformę Azure" na "Wywołaj własny certyfikat (BYOC)". Następnie wykonaj te same kroki co wcześniej, aby wybrać certyfikat. Wybierz pozycję **Aktualizuj** , aby zmienić skojarzony certyfikat z domeną.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Zrzut ekranu przedstawiający stronę szczegóły certyfikatu.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [buforowaniu przy użyciu standardu/Premium platformy Azure](concept-caching.md).
