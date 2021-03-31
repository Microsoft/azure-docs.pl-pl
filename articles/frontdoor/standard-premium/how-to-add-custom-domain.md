---
title: Jak dodać domenę niestandardową do konfiguracji jednostki SKU usługi Azure Front-Standard/Premium
description: W ramach tego samouczka nauczysz się, jak dołączyć domenę niestandardową do platformy Azure (wersja standardowa/Premium) dla warstwy przedniej.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 36cb5720e409c86fcb4bc1a97863e5d3523cd3bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588753"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Utwórz domenę niestandardową w wersji Standard/Premium drzwi platformy Azure (wersja zapoznawcza) przy użyciu Azure Portal

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W przypadku dostarczania aplikacji przy użyciu usługi Azure Front-Standard/Premium domena niestandardowa jest wymagana, jeśli chcesz, aby Twoja nazwa domeny była widoczna w żądaniach użytkowników końcowych. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem.

Po utworzeniu profilu warstwy Standardowa/Premium platformy Azure domyślny Host frontonu będzie miał poddomenę azurefd.net. Ta poddomena zostanie uwzględniona w adresie URL, gdy usługa Azure Front-Standard/Premium udostępnia zawartość z zaplecza domyślnie. Na przykład `https://contoso-frontend.azurefd.net/activeusers.htm`. Dla Twojej wygody usługa Azure Front Door udostępnia opcję kojarzenia domeny niestandardowej z hostem domyślnym. W przypadku tej opcji dostarczasz zawartość z domeną niestandardową w adresie URL zamiast nazwy domeny w warstwie Standardowa/Premium platformy Azure. Na przykład https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [**dodatkowe warunki użytkowania wersji**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="prerequisites"></a>Wymagania wstępne
* Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie warstwy przedniej Standard/Premium](create-front-door-portal.md).

* Jeśli nie masz jeszcze domeny niestandardowej, musisz najpierw zakupić ją u dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](../../app-service/manage-custom-dns-buy-domain.md).

* Jeśli używasz platformy Azure do hostowania [domen DNS](../../dns/dns-overview.md), musisz delegować system nazw domen (DNS) dostawcy domeny do Azure DNS. Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, musisz ręcznie zweryfikować domenę, wprowadzając monity o rekordy TXT DNS.

## <a name="add-a-new-custom-domain"></a>Dodaj nową domenę niestandardową

> [!NOTE]
> W publicznej wersji zapoznawczej używanie Azure DNS do tworzenia domen Apex nie jest obsługiwane w standardowym lub Premium platformy Azure. Istnieją inni dostawcy DNS, którzy obsługują spłaszczanie CNAME lub kartach DNS, które umożliwiają korzystanie z domen WIERZCHOŁKów na platformie Azure — warstwa standardowa/Premium.

Domena niestandardowa jest zarządzana przez sekcje w portalu. Domenę niestandardową można utworzyć i zweryfikować przed skojarzeniem z punktem końcowym. Domena niestandardowa i jej domeny podrzędne można skojarzyć tylko z jednym punktem końcowym w danym momencie. Można jednak używać różnych poddomen z tej samej domeny niestandardowej dla różnych drzwi zewnętrznych. Możesz również mapować domeny niestandardowe z różnymi domenami poddomeny do tego samego punktu końcowego czołowego drzwi.

1. W obszarze Ustawienia profilu usługi Azure front-drzwi wybierz pozycję *domeny*  , a następnie przycisk **Dodaj domenę** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Zrzut ekranu przedstawiający przycisk Dodaj domenę na stronie docelowej domeny.":::

1. Zostanie wyświetlona strona **Dodawanie domeny** , na której można wprowadzać informacje o domenie niestandardowej. Można wybrać usługę DNS zarządzaną przez platformę Azure, która jest zalecana lub można użyć własnego dostawcy DNS. W przypadku wybrania usługi DNS zarządzanej przez platformę Azure wybierz istniejącą strefę DNS, a następnie wybierz niestandardową poddomenę lub Utwórz nową. Jeśli używasz innego dostawcy DNS, ręcznie wprowadź niestandardową nazwę domeny. Wybierz pozycję **Dodaj** , aby dodać domenę niestandardową.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie domeny.":::

    Zostanie utworzona nowa domena niestandardowa ze stanem weryfikacji **przesyłania**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Zrzut ekranu przedstawiający stan weryfikacji domeny.":::

    Poczekaj, aż stan walidacji zmieni się na **oczekujące**. Ta operacja może potrwać kilka minut.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Zrzut ekranu stanu sprawdzania poprawności domeny.":::

1. Wybierz stan **oczekiwania na** weryfikację. Zostanie wyświetlona nowa strona z informacjami o rekordzie DNS TXT, które są konieczne do zweryfikowania domeny niestandardowej. Rekord TXT ma postać `_dnsauth.<your_subdomain>` . Jeśli używasz strefy opartej na Azure DNS, wybierz przycisk **Dodaj** , a w strefie Azure DNS zostanie utworzony nowy rekord TXT z wyświetlaną wartością rekordu. Jeśli używasz innego dostawcy DNS, ręcznie Utwórz nowy rekord TXT o nazwie `dnsauth.<your_subdomain>` z wartością rekordu, jak pokazano na stronie.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Zrzut ekranu przedstawiający stronę Weryfikowanie domeny niestandardowej.":::

1. Wybierz stan odświeżania. Po sprawdzeniu poprawności domeny przy użyciu rekordu TXT DNS stan sprawdzania poprawności zmieni się na **zweryfikowane**. Sprawdzenie poprawności tej operacji może potrwać kilka minut.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Zrzut ekranu z zweryfikowaną domeną niestandardową.":::

1. Zamknij stronę, aby powrócić do strony docelowej listy domen niestandardowych. Stan aprowizacji domeny niestandardowej powinien zmienić się na **zainicjowany** i stan walidacji powinien zmienić się na **zatwierdzony**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Zrzut ekranu przedstawiający stan aprowizacji i zatwierdzenia.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Kojarzenie domeny niestandardowej z punktem końcowym przednim Drzwiem

Po sprawdzeniu poprawności domeny niestandardowej możesz dodać ją do punktu końcowego Standard/Premium drzwi platformy Azure.

1. Po sprawdzeniu poprawności domeny niestandardowej możesz skojarzyć ją z istniejącym punktem końcowym Standard/Premium platformy Azure w warstwie Standardowa. Wybierz link **skojarzenia punktu końcowego** , aby otworzyć stronę **Skojarz punkt końcowy i trasy** . Wybierz punkt końcowy i trasy, z którymi chcesz skojarzyć. Następnie wybierz pozycję **Skojarz**. Zamknij stronę po zakończeniu operacji kojarzenia.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Zrzut ekranu przedstawiający stronę kojarzenie punktów końcowych i tras.":::

    Stan skojarzenia punktu końcowego powinien zmienić się, aby odzwierciedlić punkt końcowy, do którego jest obecnie skojarzona domena niestandardowa. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Zrzut ekranu linku skojarzenia punktu końcowego.":::

1. Wybierz łącze stanu DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Zrzut ekranu przedstawiający link stanu DNS.":::

1. Zostanie wyświetlona strona **Dodawanie lub aktualizacja rekordu CNAME** i zostanie wyświetlona informacja o rekordzie CNAME, które należy podać przed rozpoczęciem przepływu ruchu. Jeśli używasz Azure DNS strefach hostowanych, rekordy CNAME można utworzyć, wybierając przycisk **Dodaj** na stronie. Jeśli używasz innego dostawcy DNS, musisz ręcznie wprowadzić nazwę i wartość rekordu CNAME, jak pokazano na stronie.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Zrzut ekranu przedstawiający Dodawanie lub aktualizowanie rekordu CNAME.":::

1. Gdy rekord CNAME zostanie utworzony i zostanie skojarzona domena niestandardowa z zakończeniem punktu końcowego drzwi platformy Azure, przepływ ruchu zacznie przepływać.

    > [!NOTE]
    > Jeśli protokół HTTPS jest włączony, Inicjowanie obsługi certyfikatów i Propagacja może potrwać kilka minut, ponieważ Propagacja jest wykonywana we wszystkich lokalizacjach brzegowych. 

## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po sprawdzeniu poprawności i potwierdzeniu domeny niestandardowej upewnij się, że domena niestandardowa jest prawidłowo odwołująca się do punktu końcowego.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Zrzut ekranu przedstawiający zweryfikowaną i powiązaną domenę niestandardową.":::

Następnie sprawdź, czy zawartość aplikacji jest obsługiwana przy użyciu przeglądarki.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć protokół HTTPS dla domeny niestandardowej, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie protokołu HTTPS dla domeny niestandardowej]()
