---
title: Migrowanie aktywnej nazwy DNS
description: Dowiedz się, jak migrować niestandardową nazwę domeny DNS, która jest już przypisana do działającej witryny, aby Azure App Service bez przestojów.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89484306"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrowanie aktywnej nazwy DNS do Azure App Service

W tym artykule przedstawiono sposób migrowania aktywnej nazwy DNS w celu [Azure App Service](../app-service/overview.md) bez przestojów.

W przypadku migrowania aktywnej witryny i jej nazwy domeny DNS do App Service, nazwa DNS ma już obsługiwać ruch na żywo. Można uniknąć przestojów rozpoznawania nazw DNS podczas migracji przez powiązanie aktywnej nazwy DNS z aplikacją App Service App zapobiegawczo.

Jeśli nie martwisz się o przestoju w rozpoznawaniu nazw DNS, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- Upewnij się [, że aplikacja App Service nie znajduje się w warstwie Bezpłatna](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Powiąż nazwę domeny zapobiegawczo

W przypadku powiązania niestandardowej domeny zapobiegawczo należy wykonać obie następujące czynności przed wprowadzeniem jakichkolwiek zmian w istniejących rekordach DNS:

- Weryfikowanie własności domeny
- Włączanie nazwy domeny dla aplikacji

Po zakończeniu migracji niestandardowej nazwy DNS ze starej lokacji do aplikacji App Service nie będzie żadnych przestojów w rozpoznawaniu nazw DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Pobierz identyfikator weryfikacji domeny

Uzyskaj identyfikator weryfikacji domeny dla aplikacji, wykonując czynności opisane w sekcji [Pobieranie identyfikatora weryfikacji domeny](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).

### <a name="create-domain-verification-record"></a>Utwórz rekord weryfikacji domeny

Aby sprawdzić własność domeny, Dodaj rekord TXT dla weryfikacji domeny. Nazwa hosta dla rekordu TXT zależy od typu rekordu DNS, który ma zostać zmapowany. Zapoznaj się z poniższą tabelą ( `@` zazwyczaj reprezentuje domenę główną):

| Przykład rekordu DNS | Host TXT | Wartość TXT |
| - | - | - |
| \@ pierwiastek | _asuid_ | [Identyfikator weryfikacji domeny dla aplikacji](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (Sub) | _asuid. www_ | [Identyfikator weryfikacji domeny dla aplikacji](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* znaku | _asuid_ | [Identyfikator weryfikacji domeny dla aplikacji](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

Na stronie rekordów DNS Zwróć uwagę na typ rekordu nazwy DNS, którą chcesz migrować. App Service obsługuje mapowania z rekordów CNAME i A.

> [!NOTE]
> Rekordy z symbolami wieloznacznymi `*` nie sprawdzają domen poddomen przy użyciu istniejącego rekordu CNAME. Może być konieczne jawne utworzenie rekordu TXT dla każdej poddomeny.

### <a name="enable-the-domain-for-your-app"></a>Włącz domenę dla aplikacji

1. W [Azure Portal](https://portal.azure.com)w lewym okienku nawigacji strony aplikacji wybierz pozycję **domeny niestandardowe**. 

    ![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stronie **domeny niestandardowe** wybierz pozycję **Dodaj domenę niestandardową**.

    ![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Wpisz w pełni kwalifikowaną nazwę domeny, która ma zostać poddana migracji, która odnosi się do tworzonego rekordu TXT, takiego jak `contoso.com` , `www.contoso.com` lub `*.contoso.com` . Wybierz przycisk **Weryfikuj**.

    Przycisk **Dodaj domenę niestandardową** jest aktywowany. 

1. Upewnij się, że **Typ rekordu nazwy hosta** jest ustawiony na typ rekordu DNS, który ma zostać zmigrowany. Wybierz przycisk **Dodaj nazwę hosta**.

    ![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

    ![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    Niestandardowa nazwa DNS jest teraz włączona w aplikacji platformy Azure. 

## <a name="remap-the-active-dns-name"></a>Ponowne mapowanie aktywnej nazwy DNS

Jedyną czynnością, którą pozostało do wykonania, jest ponowne mapowanie aktywnego rekordu DNS w celu wskazywania App Service. Teraz nadal wskazuje Twoją starą lokację.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Skopiuj adres IP aplikacji (tylko rekord)

Jeśli ponownie mapujesz rekord CNAME, Pomiń tę sekcję. 

Aby ponownie zamapować rekord A, potrzebny jest zewnętrzny adres IP aplikacji App Service, który jest wyświetlany na stronie **domeny niestandardowe** .

Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizowanie rekordu DNS

Na stronie rekordy DNS dostawcy domeny wybierz rekord DNS do ponownego mapowania.

W `contoso.com` przykładzie domeny katalogu głównego ponownie zamapuj rekord A lub CNAME, jak przykłady w poniższej tabeli: 

| Przykład nazwy FQDN | Typ rekordu | Host | Wartość |
| - | - | - | - |
| contoso.com (główny) | A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
| \.contoso.com www (Sub) | CNAME | `www` | _&lt;nazwa_aplikacji>. azurewebsites.net_ |
| \*. contoso.com (symbol wieloznaczny) | CNAME | _\*_ | _&lt;nazwa_aplikacji>. azurewebsites.net_ |

Zapisz ustawienia.

Zapytania DNS powinny rozpoczynać rozpoznawanie do aplikacji App Service natychmiast po wykonaniu propagacji DNS.

## <a name="migrate-domain-from-another-app"></a>Migrowanie domeny z innej aplikacji

Możesz migrować aktywną domenę niestandardową na platformie Azure między subskrypcjami lub w ramach tej samej subskrypcji. Jednak taka migracja bez przestoju wymaga, aby aplikacja źródłowa i aplikacja docelowa była przypisana do tej samej domeny niestandardowej w określonym czasie. Z tego względu należy upewnić się, że dwie aplikacje nie są wdrożone w tej samej jednostce wdrożenia (wewnętrznie znanej jako przestrzeń internetowa). Nazwa domeny może być przypisana tylko do jednej aplikacji w każdej jednostce wdrożenia.

Jednostkę wdrożenia aplikacji można znaleźć, przeglądając nazwę domeny adresu URL FTP/S `<deployment-unit>.ftp.azurewebsites.windows.net` . Sprawdź i upewnij się, że jednostka wdrożenia różni się między aplikacją źródłową a aplikacją docelową. Jednostka wdrożenia aplikacji jest określana na podstawie [planu App Service](overview-hosting-plans.md) . Jest ona wybierana losowo przez platformę Azure podczas tworzenia planu i nie można jej zmienić. Na platformie Azure upewnij się, że dwa plany znajdują się w tej samej jednostce wdrożenia podczas [tworzenia ich w tej samej grupie zasobów *i* w tym samym regionie](app-service-plan-manage.md#create-an-app-service-plan), ale nie ma żadnych logiki, aby upewnić się, że plany znajdują się w różnych jednostkach wdrożenia. Jedynym sposobem utworzenia planu w innej jednostce wdrożenia jest utworzenie planu w nowej grupie zasobów lub regionie do momentu uzyskania innej jednostki wdrożenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak powiązać niestandardowy certyfikat TLS/SSL z App Service.

> [!div class="nextstepaction"]
> [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS w Azure App Service](configure-ssl-bindings.md)
