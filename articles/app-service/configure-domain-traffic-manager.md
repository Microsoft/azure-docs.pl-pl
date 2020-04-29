---
title: Konfigurowanie nazw DNS przy użyciu Traffic Manager
description: Dowiedz się, jak skonfigurować domenę niestandardową dla aplikacji Azure App Service, która integruje się z Traffic Manager na potrzeby równoważenia obciążenia.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944128"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurowanie niestandardowej nazwy domeny w Azure App Service z integracją Traffic Manager

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Aby uzyskać Cloud Services, zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure](../cloud-services/cloud-services-custom-domain-name.md).

W przypadku używania [usługi Azure Traffic Manager](/azure/traffic-manager/) do równoważenia obciążenia ruchu do [Azure App Service](overview.md)można uzyskać dostęp do aplikacji App Service za pomocą polecenia ** \<Traffic-Manager-Endpoint>. trafficmanager.NET**. Możesz przypisać niestandardową nazwę domeny, taką jak contoso.com\.www, za pomocą aplikacji App Service, aby zapewnić użytkownikom bardziej rozpoznawalną nazwę domeny.

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

Kliknij przycisk **Zastosuj**.

## <a name="create-traffic-manager-endpoint"></a>Utwórz punkt końcowy Traffic Manager

Postępując zgodnie z instrukcjami w obszarze [punkty końcowe dodawania lub usuwania](../traffic-manager/traffic-manager-endpoints.md), dodaj aplikację App Service jako punkt końcowy w profilu Traffic Manager.

Gdy aplikacja App Service jest w obsługiwanej warstwie cenowej, zostanie wyświetlona na liście dostępnych App Service docelowych podczas dodawania punktu końcowego. Jeśli Twoja aplikacja nie znajduje się na liście, [Sprawdź warstwę cenową swojej aplikacji](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Tworzenie mapowania CNAME
> [!NOTE]
> Aby skonfigurować [zakupioną domenę App Service](manage-custom-dns-buy-domain.md), Pomiń tę sekcję i przejdź do pozycji [Włącz domenę niestandardową](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Chociaż poszczególne dostawcy domen różnią się od siebie, można mapować *z* niestandardowej nazwy domeny (takiej jak **contoso.com**) *na* nazwę domeny Traffic Manager (**contoso.trafficmanager.NET**), która jest zintegrowana z Twoją aplikacją.

> [!NOTE]
> Jeśli rekord jest już używany i musisz zapobiegawczo powiązać z nim aplikacje, możesz utworzyć dodatkowy rekord CNAME. Aby na przykład zapobiegawczo powiązać **www\.contoso.com** z Twoją aplikacją, Utwórz rekord CNAME z **awverify. www** w **contoso.trafficmanager.NET**. Następnie można dodać "www\.contoso.com" do aplikacji bez konieczności zmiany rekordu CNAME "www". Aby uzyskać więcej informacji, zobacz [Migrowanie aktywnej nazwy DNS do Azure App Service](manage-custom-dns-migrate-domain.md).

Po zakończeniu dodawania lub modyfikowania rekordów DNS u dostawcy domeny Zapisz zmiany.

## <a name="enable-custom-domain"></a>Włącz domenę niestandardową
Po rozpropagowaniu rekordów dla nazwy domeny Użyj przeglądarki, aby sprawdzić, czy nazwa domeny niestandardowej jest rozpoznawana jako aplikacja App Service.

> [!NOTE]
> Propagacja rekordu CNAME przez system DNS może zająć trochę czasu. Możesz użyć usługi, takiej jak <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> , aby sprawdzić, czy rekord CNAME jest dostępny.
> 
> 

1. Po pomyślnym zakończeniu rozpoznawania domeny z powrotem do strony aplikacji w [witrynie Azure Portal](https://portal.azure.com)
2. W lewym okienku nawigacji wybierz pozycję **domeny** > niestandardowe**Dodaj nazwę hosta**.
4. Wpisz niestandardową nazwę domeny, która została zmapowana wcześniej, i wybierz pozycję **Weryfikuj**.
5. Upewnij się, że **Typ rekordu nazwy hosta** ma wartość **CNAME (\.example.com www lub dowolna poddomena)**.

6. Ponieważ aplikacja App Service jest teraz zintegrowana z punktem końcowym Traffic Manager, w obszarze **Konfiguracja CNAME**powinna zostać wyświetlona nazwa domeny Traffic Manager. Zaznacz go i kliknij przycisk **Dodaj domenę niestandardową**.

    ![Dodawanie nazwy DNS do aplikacji](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania SSL w usłudze Azure App Service](configure-ssl-bindings.md)
