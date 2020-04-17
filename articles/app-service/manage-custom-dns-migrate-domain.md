---
title: Migrowanie aktywnej nazwy DNS
description: Dowiedz się, jak przeprowadzić migrację niestandardowej nazwy domeny DNS, która jest już przypisana do aktywnej witryny do usługi Azure App Service bez żadnych przestojów.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 5c1760c746aca439e19ab5727e5be02f6dbad3cb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535693"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrowanie aktywnej nazwy DNS do usługi Azure App Service

W tym artykule pokazano, jak przeprowadzić migrację aktywnej nazwy DNS do [usługi Azure App Service](../app-service/overview.md) bez żadnych przestojów.

Podczas migracji aktywnej witryny i jej nazwy domeny DNS do usługi App Service ta nazwa DNS już obsługuje ruch na żywo. Można uniknąć przestojów w rozpoznawaniu DNS podczas migracji, wiążąc aktywną nazwę DNS z aplikacją usługi App Service prewuwnie.

Jeśli nie martwisz się o przestoje w rozdzielczości DNS, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten sposób:

- [Upewnij się, że aplikacja usługi App Service nie jest w warstwie FREE](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Powiąż nazwę domeny prewuktywnie

Po wywiązywaniu domeny niestandardowej z prewencyjnym, przed wprowadzeniem jakichkolwiek zmian w rekordach DNS należy wykonać obie następujące czynności:

- Weryfikowanie własności domeny
- Włączanie nazwy domeny aplikacji

Po krotnym migracji niestandardowej nazwy DNS ze starej witryny do aplikacji Usługi app service nie będzie żadnych przestojów w rozpoznawaniu DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Tworzenie rekordu weryfikacji domeny

Aby zweryfikować własność domeny, dodaj rekord TXT. Mapy rekordów TXT z _awverify.&lt;>poddomeny_ do _ &lt;appname>.azurewebsites.net_. 

Potrzebny rekord TXT zależy od rekordu DNS, który chcesz przeprowadzić migrację. Przykłady można znaleźć w`@` poniższej tabeli (zazwyczaj reprezentuje domenę główną):

| Przykład rekordu DNS | TXT Host | Wartość TXT |
| - | - | - |
| \@(korzeń) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \*(symbol wieloznaczny) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Na stronie rekordów DNS zanotuj typ rekordu nazwy DNS, którą chcesz przeprowadzić migrację. Usługa App Service obsługuje mapowania z rekordów CNAME i A.

> [!NOTE]
> Dla niektórych dostawców, takich `awverify.*` jak CloudFlare, nie jest prawidłowy rekord. Użyj `*` tylko zamiast tego.

> [!NOTE]
> Rekordy `*` symboli wieloznacznych nie sprawdzają poprawności poddomen z istniejącym rekordem CNAME. Może być konieczne jawne utworzenie rekordu TXT dla każdej poddomeny.


### <a name="enable-the-domain-for-your-app"></a>Włączanie domeny aplikacji

W [witrynie Azure portal](https://portal.azure.com)w lewej części strony aplikacji wybierz pozycję **Domeny niestandardowe**. 

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **Domeny niestandardowe** **+** wybierz ikonę obok pozycji **Dodaj nazwę hosta**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowaną nazwę domeny, dla której `www.contoso.com`dodano rekord TXT, na przykład . W przypadku domeny wieloznacznych (takiej jak \*contoso.com) można użyć dowolnej nazwy DNS zgodnej z domeną wieloznaczną. 

Wybierz przycisk **Weryfikuj**.

Zostanie aktywowany przycisk **Dodaj nazwę hosta**. 

Upewnij się, że **typ rekordu Nazwy Hosta** jest ustawiony na typ rekordu DNS, który chcesz przeprowadzić migrację.

Wybierz przycisk **Dodaj nazwę hosta**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Twoja niestandardowa nazwa DNS jest teraz włączona w aplikacji platformy Azure. 

## <a name="remap-the-active-dns-name"></a>Ponowne mapowanie aktywnej nazwy DNS

Jedyne, co pozostało do zrobienia, to ponowne mapowanie aktywnego rekordu DNS, aby wskazać usługę App Service. W tej chwili nadal wskazuje na swoją starą witrynę.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopiowanie adresu IP aplikacji (tylko rekord)

Jeśli ponownie mapujesz rekord CNAME, pomiń tę sekcję. 

Aby ponownie zamapować rekord A, potrzebny jest zewnętrzny adres IP aplikacji App Service, który jest wyświetlany na stronie **Domeny niestandardowe.**

Zamknij stronę **Dodaj nazwy hosta,** **wybierając** x w prawym górnym rogu. 

Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizowanie rekordu DNS

Na stronie rekordy DNS dostawcy domeny wybierz rekord DNS do ponownego zamapowania.

W `contoso.com` przykładzie domeny głównej ponownie mapuj rekord A lub CNAME, podobnie jak w poniższej tabeli: 

| Przykład FQDN | Typ rekordu | Host | Wartość |
| - | - | - | - |
| contoso.com (korzeń) | A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (symbol wieloznaczny) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Zapisz ustawienia.

Kwerendy DNS należy rozpocząć rozwiązywanie aplikacji usługi App Service natychmiast po propagacji DNS dzieje.

## <a name="active-domain-in-azure"></a>Aktywna domena na platformie Azure

Możesz migrować aktywną domenę niestandardową na platformie Azure, między subskrypcjami lub w ramach tej samej subskrypcji. Jednak taka migracja bez przestojów wymaga aplikacji źródłowej, a aplikacji docelowej przypisano tę samą domenę niestandardową w określonym czasie. W związku z tym należy upewnić się, że dwie aplikacje nie są wdrażane w tej samej jednostce wdrażania (wewnętrznie znany jako obszar sieci Web). Nazwę domeny można przypisać tylko do jednej aplikacji w każdej jednostce wdrożeniowej.

Jednostkę wdrażania aplikacji można znaleźć, patrząc na nazwę domeny adresu `<deployment-unit>.ftp.azurewebsites.windows.net`URL FTP/S . Sprawdź i upewnij się, że jednostka wdrożeniowa różni się między aplikacją źródłową a aplikacją docelową. Jednostka wdrażania aplikacji jest określana przez [plan usługi app service,](overview-hosting-plans.md) w który się znajduje. Jest wybierany losowo przez platformę Azure podczas tworzenia planu i nie można go zmienić. Platforma Azure tylko upewnia się, że dwa plany znajdują się w tej samej jednostce wdrażania podczas [tworzenia ich w tej samej grupie zasobów *i* w tym samym regionie,](app-service-plan-manage.md#create-an-app-service-plan)ale nie ma żadnej logiki, aby upewnić się, że plany znajdują się w różnych jednostkach wdrażania. Jedynym sposobem utworzenia planu w innej jednostce wdrożeniowej jest tworzenie planu w nowej grupie zasobów lub regionie, dopóki nie pojawi się inna jednostka wdrożeniowa.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak powiązać niestandardowy certyfikat TLS/SSL z usługą App Service.

> [!div class="nextstepaction"]
> [Zabezpiecz niestandardową nazwę DNS za pomocą powiązania TLS w usłudze Azure App Service](configure-ssl-bindings.md)
