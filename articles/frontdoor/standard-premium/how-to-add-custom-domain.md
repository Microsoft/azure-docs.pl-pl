---
title: Jak dodać domenę niestandardową do konfiguracji Azure Front Door Standardowa/Premium
description: Z tego samouczka dowiesz się, jak do dołączać domenę niestandardową do Azure Front Door standardowa/Premium.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387925"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Tworzenie domeny niestandardowej w Azure Front Door standardowa/Premium (wersja zapoznawcza) przy użyciu Azure Portal

> [!Note]
> Ta dokumentacja dotyczy wersji Azure Front Door Standardowa/Premium (wersja zapoznawcza). Szukasz informacji na temat Azure Front Door? Wyświetl [tutaj](../front-door-overview.md).

Jeśli używasz usługi Azure Front Door Standard/Premium do dostarczania aplikacji, domena niestandardowa jest niezbędna, jeśli chcesz, aby twoja nazwa domeny była widoczna w żądaniach użytkowników końcowych. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem.

Po utworzeniu profilu Azure Front Door Standardowa/Premium domyślny host frontendu będzie miał poddomenę azurefd.net. Ta poddomena jest uwzględniana w adresie URL, gdy Azure Front Door Standardowa/Premium domyślnie dostarcza zawartość z zaplecza. Na przykład `https://contoso-frontend.azurefd.net/activeusers.htm`. Dla Twojej wygody usługa Azure Front Door udostępnia opcję kojarzenia domeny niestandardowej z hostem domyślnym. W przypadku tej opcji zawartość jest dostarczana w adresie URL przy użyciu domeny niestandardowej zamiast nazwy domeny należącej Azure Front Door Standardowa/Premium. Na przykład " https://www.contoso.com/photo.png ".

> [!IMPORTANT]
> Azure Front Door Standardowa/Premium (wersja zapoznawcza) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz Dodatkowe warunki użytkowania dotyczące wersji [**Microsoft Azure zapoznawczych.**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Wymagania wstępne
* Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie Front Door Standardowa/Premium.](create-front-door-portal.md)

* Jeśli nie masz jeszcze domeny niestandardowej, musisz najpierw kupić domenę u dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](../../app-service/manage-custom-dns-buy-domain.md).

* Jeśli używasz platformy Azure do hostowania domen [DNS,](../../dns/dns-overview.md)musisz delegować system nazw domen (DNS) dostawcy domeny do Azure DNS. Aby uzyskać więcej informacji, [zobacz Delegowanie domeny do Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, musisz ręcznie zweryfikować domenę, wprowadzając rekordy TXT systemu DNS z monitem.

## <a name="add-a-new-custom-domain"></a>Dodawanie nowej domeny niestandardowej

> [!NOTE]
> W publicznej wersji zapoznawczej używanie Azure DNS do tworzenia domen Apex nie jest obsługiwane w Azure Front Door Standardowa/Premium. Istnieją inni dostawcy DNS, którzy obsługują spłaszczanie rekordu CNAME lub pogawędki DNS, które umożliwią domenom APEX Azure Front Door Standard/Premium.

Domena niestandardowa jest zarządzana przez sekcję Domeny w portalu. Domenę niestandardową można utworzyć i zweryfikować przed skojarzeniem z punktem końcowym. Domenę niestandardową i jej poddomeny można skojarzyć jednocześnie tylko z jednym punktem końcowym. Można jednak używać różnych domen podrzędnych z tej samej domeny niestandardowej dla różnych usługi Front Doors. Można również mapować domeny niestandardowe z różnymi poddomenami na ten sam Front Door punkt końcowy.

1. W obszarze Ustawienia Azure Front Door wybierz pozycję *Domeny,* a następnie przycisk **Dodaj domenę.**

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Zrzut ekranu przedstawiający przycisk Dodaj domenę na stronie docelowej domeny.":::

1. Zostanie **wyświetlone strona Dodawanie** domeny, na której można wprowadzić informacje o domenie niestandardowej. Możesz wybrać usługę DNS zarządzaną przez platformę Azure, co jest zalecane, lub użyć własnego dostawcy DNS. Jeśli wybierzesz usługę DNS zarządzaną przez platformę Azure, wybierz istniejącą strefę DNS, a następnie wybierz niestandardową poddomenę lub utwórz nową. Jeśli używasz innego dostawcy DNS, ręcznie wprowadź niestandardową nazwę domeny. Wybierz **pozycję Dodaj,** aby dodać domenę niestandardową.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Zrzut ekranu przedstawiający stronę dodawania domeny.":::

    Zostanie utworzona nowa domena niestandardowa ze stanem weryfikacji **Przesyłanie**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Zrzut ekranu przedstawiający przesyłanie stanu weryfikacji domeny.":::

    Poczekaj, aż stan weryfikacji zmieni się na **Oczekujące.** Ta operacja może potrwać kilka minut.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Zrzut ekranu przedstawiający oczekujący stan weryfikacji domeny.":::

1. Wybierz stan **Oczekiwanie** na walidację. Zostanie pojawi się nowa strona z informacjami o rekordzie TXT systemu DNS wymaganymi do zweryfikowania domeny niestandardowej. Rekord TXT ma postać `_dnsauth.<your_subdomain>` . Jeśli używasz strefy Azure DNS, wybierz przycisk Dodaj, a nowy rekord TXT z wyświetloną wartością rekordu zostanie utworzony w Azure DNS strefie.  Jeśli używasz innego dostawcy DNS, ręcznie utwórz nowy rekord TXT nazwy z wartością rekordu, jak `dnsauth.<your_subdomain>` pokazano na stronie.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Zrzut ekranu przedstawiający stronę weryfikowania domeny niestandardowej.":::

1. Wybierz stan odświeżania. Po zweryfikowaniu domeny przy użyciu rekordu TXT systemu DNS stan weryfikacji zmieni się na **zweryfikowany**. Walidacja tej operacji może potrwać kilka minut.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Zrzut ekranu przedstawiający weryfikację domeny niestandardowej.":::

1. Zamknij stronę, aby wrócić do strony docelowej listy domen niestandardowych. Stan aprowizowania domeny niestandardowej powinien zmienić się na **Aprowizowana,** a stan walidacji na **Zatwierdzone.**

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Zrzut ekranu przedstawiający stan aprowizowanych i zatwierdzonych.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Kojarzenie domeny niestandardowej z punktem Front Door końcowego

Po weryfikacji domeny niestandardowej możesz dodać ją do punktu końcowego Azure Front Door Standardowa/Premium.

1. Po weryfikacji domeny niestandardowej możesz skojarzyć ją z istniejącym punktem końcowym Azure Front Door Standard/Premium i trasą. Wybierz link **Skojarzenie punktu** końcowego, aby otworzyć stronę **Kojarzenie punktu końcowego i tras.** Wybierz punkt końcowy i trasy, z którym chcesz skojarzyć. Następnie wybierz pozycję **Skojarz**. Zamknij stronę po zakończeniu operacji kojarzenia.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Zrzut ekranu przedstawiający stronę kojarzenia punktu końcowego i tras.":::

    Stan skojarzenia punktu końcowego powinien ulec zmianie w celu odzwierciedlenia punktu końcowego, z którym domena niestandardowa jest obecnie skojarzona. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Zrzut ekranu przedstawiający link skojarzenia punktu końcowego.":::

1. Wybierz link Stan DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Zrzut ekranu przedstawiający link stanu DNS.":::

1. Zostanie **wyświetlone strona Dodawanie lub aktualizowanie rekordu CNAME** z informacjami o rekordzie CNAME, które należy podać, zanim ruch zacznie przepływać. Jeśli używasz stref Azure DNS hostowanych, rekordy CNAME można utworzyć, wybierając przycisk **Dodaj** na stronie. Jeśli używasz innego dostawcy DNS, musisz ręcznie wprowadzić nazwę i wartość rekordu CNAME, jak pokazano na stronie.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Zrzut ekranu przedstawiający dodawanie lub aktualizowanie rekordu CNAME.":::

1. Gdy rekord CNAME zostanie utworzony i domena niestandardowa zostanie skojarzona z punktem końcowym Azure Front Door, rozpocznie się przepływ ruchu.

    > [!NOTE]
    > Jeśli protokół HTTPS jest włączony, aprowizowanie i propagacja certyfikatu może potrwać kilka minut, ponieważ propagacja jest wykonywana we wszystkich lokalizacjach brzegowych. 

## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zweryfikowaniu i skojarzeniu domeny niestandardowej sprawdź, czy domena niestandardowa jest poprawnie przywołyowana do punktu końcowego.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Zrzut ekranu przedstawiający zweryfikowaną i skojarzoną domenę niestandardową.":::

Następnie zweryfikuj, czy zawartość aplikacji jest podana przy użyciu przeglądarki.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć protokół HTTPS dla domeny niestandardowej, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie protokołu HTTPS dla domeny niestandardowej]()
