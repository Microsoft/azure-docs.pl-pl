---
title: Konfigurowanie nazw DNS przy użyciu Traffic Manager
description: Dowiedz się, jak skonfigurować domenę niestandardową dla aplikacji Azure App Service, która integruje się z Traffic Manager na potrzeby równoważenia obciążenia.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 2910ea3f896ba3920126737965ca9c9dbabcfeb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709108"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurowanie niestandardowej nazwy domeny w Azure App Service z integracją Traffic Manager

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Aby uzyskać Cloud Services, zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

W przypadku używania [usługi Azure Traffic Manager](../traffic-manager/index.yml) do równoważenia obciążenia ruchu do [Azure App Service](overview.md)dostęp do aplikacji App Service można uzyskać za pomocą **\<traffic-manager-endpoint> . trafficmanager.NET**. Możesz przypisać niestandardową nazwę domeny, taką jak \. contoso.com www, za pomocą aplikacji App Service, aby zapewnić użytkownikom bardziej rozpoznawalną nazwę domeny.

W tym artykule pokazano, jak skonfigurować niestandardową nazwę domeny za pomocą aplikacji App Service zintegrowanej z [Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Tylko rekordy [CNAME](https://en.wikipedia.org/wiki/CNAME_record) są obsługiwane podczas konfigurowania nazwy domeny za pomocą punktu końcowego Traffic Manager. Ponieważ rekordy nie są obsługiwane, mapowanie domeny głównej, takie jak contoso.com, nie jest również obsługiwane.
> 

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zmapować niestandardową nazwę DNS na aplikację zintegrowaną z usługą Azure Traffic Manager, [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) aplikacji sieci Web musi znajdować się w warstwie **standardowa** lub wyższej. W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **App Services**.

Na stronie **App Services** wybierz nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

W lewym panelu nawigacyjnym strony aplikacji wybierz pozycję **Skaluj w górę (plan App Service)**.

![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja znajduje się w warstwie **standardowa** lub powyżej (każda warstwa w kategorii **produkcja** lub **izolowana** ). Jeśli tak, zamknij stronę **skalowanie w górę** i Pomiń, aby [utworzyć mapowanie CNAME](#create-the-cname-mapping).

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Jeśli potrzebujesz skalować w górę aplikacji, wybierz dowolną warstwę cenową w kategorii **produkcja** . Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij pozycję **Zastosuj**.

## <a name="create-traffic-manager-endpoint"></a>Utwórz punkt końcowy Traffic Manager

Postępując zgodnie z instrukcjami w obszarze [punkty końcowe dodawania lub usuwania](../traffic-manager/traffic-manager-manage-endpoints.md), dodaj aplikację App Service jako punkt końcowy w profilu Traffic Manager.

Gdy aplikacja App Service jest w obsługiwanej warstwie cenowej, zostanie wyświetlona na liście dostępnych App Service docelowych podczas dodawania punktu końcowego. Jeśli Twoja aplikacja nie znajduje się na liście, [Sprawdź warstwę cenową swojej aplikacji](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Tworzenie mapowania CNAME
> [!NOTE]
> Aby skonfigurować [zakupioną domenę App Service](manage-custom-dns-buy-domain.md), Pomiń tę sekcję i przejdź do pozycji [Włącz domenę niestandardową](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Poszczególne dostawcy domen różnią się w zależności *od* [nazwy domeny niestandardowej innej niż główna](#what-about-root-domains) **(takiej** jak **www.contoso.com** *Traffic Manager), która jest* zintegrowana z aplikacją. 

> [!NOTE]
> Jeśli rekord jest już używany i musisz zapobiegawczo powiązać z nim aplikacje, możesz utworzyć dodatkowy rekord CNAME. Aby na przykład zapobiegawczo powiązać **www \. contoso.com** z Twoją aplikacją, Utwórz rekord CNAME z **awverify. www** w **contoso.trafficmanager.NET**. Następnie można dodać "www \. contoso.com" do aplikacji bez konieczności zmiany rekordu CNAME "www". Aby uzyskać więcej informacji, zobacz [Migrowanie aktywnej nazwy DNS do Azure App Service](manage-custom-dns-migrate-domain.md).

Po zakończeniu dodawania lub modyfikowania rekordów DNS u dostawcy domeny Zapisz zmiany.

### <a name="what-about-root-domains"></a>Co z domenami głównymi?

Ponieważ Traffic Manager obsługuje tylko niestandardowe mapowanie domen z rekordami CNAME i ponieważ standardy DNS nie obsługują rekordów CNAME do mapowania domen głównych (na przykład **contoso.com**), Traffic Manager nie obsługuje mapowania do domen głównych. Aby obejść ten problem, użyj adresu URL przekierowania z poziomu aplikacji. Na przykład w ASP.NET Core można użyć ponownego [zapisywania adresów URL](/aspnet/core/fundamentals/url-rewriting). Następnie użyj Traffic Manager do równoważenia obciążenia poddomeny (**www.contoso.com**). Innym rozwiązaniem jest [utworzenie rekordu aliasu dla wierzchołka nazwy domeny w celu odwoływania się do profilu usługi Azure Traffic Manager](../dns/tutorial-alias-tm.md). Przykładowa domena to contoso.com. Zamiast korzystać z usługi przekierowywania, można skonfigurować Azure DNS do odwoływania się do profilu Traffic Manager bezpośrednio ze strefy. 

W przypadku scenariuszy o wysokiej dostępności można zaimplementować konfigurację systemu DNS równoważenia obciążenia bez Traffic Manager przez utworzenie wielu *rekordów a* , które wskazują na adres IP każdej z kopii aplikacji. Następnie [zamapuj tę samą domenę główną na wszystkie kopie aplikacji](app-service-web-tutorial-custom-domain.md#map-an-a-record). Ponieważ ta sama nazwa domeny nie może zostać zmapowana do dwóch różnych aplikacji w tym samym regionie, ta konfiguracja działa tylko wtedy, gdy kopie aplikacji znajdują się w różnych regionach.

## <a name="enable-custom-domain"></a>Włącz domenę niestandardową
Po rozpropagowaniu rekordów dla nazwy domeny Użyj przeglądarki, aby sprawdzić, czy nazwa domeny niestandardowej jest rozpoznawana jako aplikacja App Service.

> [!NOTE]
> Propagacja rekordu CNAME przez system DNS może zająć trochę czasu. Możesz użyć usługi, takiej jak, <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> Aby sprawdzić, czy rekord CNAME jest dostępny.
> 
> 

1. Po pomyślnym zakończeniu rozpoznawania domeny z powrotem do strony aplikacji w [witrynie Azure Portal](https://portal.azure.com)
2. W lewym okienku nawigacji wybierz pozycję **domeny niestandardowe**  >  **Dodaj nazwę hosta**.
4. Wpisz niestandardową nazwę domeny, która została zmapowana wcześniej, i wybierz pozycję **Weryfikuj**.
5. Upewnij się, że **Typ rekordu nazwy hosta** ma wartość **CNAME ( \. example.com www lub dowolna poddomena)**.

6. Ponieważ aplikacja App Service jest teraz zintegrowana z punktem końcowym Traffic Manager, w obszarze **Konfiguracja CNAME** powinna zostać wyświetlona nazwa domeny Traffic Manager. Zaznacz go i kliknij przycisk **Dodaj domenę niestandardową**.

    ![Dodawanie nazwy DNS do aplikacji](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania SSL w usłudze Azure App Service](configure-ssl-bindings.md)