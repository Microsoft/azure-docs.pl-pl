---
title: Integrowanie statycznej witryny internetowej z usługą Azure CDN Storage
description: Dowiedz się, jak buforować zawartość statycznej witryny internetowej z konta usługi Azure Storage za pomocą usługi Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: e458f98d82c910ec845ebf951b7f6470b6aba10d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95527330"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrowanie statycznej witryny sieci Web z Azure CDN

Możesz włączyć [usługę Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) w celu buforowania zawartości z [statycznej witryny sieci Web](storage-blob-static-website.md) , która jest hostowana na koncie usługi Azure Storage. Za pomocą Azure CDN można skonfigurować niestandardowy punkt końcowy domeny dla statycznej witryny sieci Web, zainicjować obsługę niestandardowych certyfikatów TLS/SSL i skonfigurować niestandardowe reguły ponownego zapisywania. Konfigurowanie usługi Azure CDN oznacza dodatkowe koszty, ale zapewnia stale niskie opóźnienia połączenia z witryną internetowej z dowolnego miejsca na świecie. Azure CDN udostępnia również szyfrowanie TLS z własnym certyfikatem. 

Aby uzyskać dodatkowe informacje na temat cennika usługi Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Włącz Azure CDN dla statycznej witryny sieci Web

Możesz włączyć Azure CDN dla statycznej witryny sieci Web bezpośrednio z konta magazynu. wysJeśli chcesz określić zaawansowane ustawienia konfiguracji dla punktu końcowego usługi CDN, takie jak [optymalizacja pobierania dużych plików](../../cdn/cdn-optimization-overview.md#large-file-download), możesz zamiast tego użyć [rozszerzenia usługi Azure CDN](../../cdn/cdn-create-new-endpoint.md), aby utworzyć punkt końcowy i profil usługi CDN.

1. Znajdź konto magazynu w Azure Portal i Wyświetl przegląd konta.

1. W menu **usługi BLOB** wybierz pozycję **Azure CDN** , aby otworzyć stronę **Azure CDN** :

    ![Tworzenie punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. W sekcji **profil usługi CDN** Określ, czy chcesz utworzyć nowy profil usługi CDN, czy użyć istniejącego. Profil CDN jest kolekcją punktów końcowych usługi CDN, które współużytkują warstwę cenową i dostawcę. Następnie wprowadź nazwę sieci CDN, która jest unikatowa w ramach subskrypcji.

1. Określ warstwę cenową punktu końcowego usługi CDN. Aby dowiedzieć się więcej o cenach, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/). Aby uzyskać więcej informacji o funkcjach dostępnych dla każdej warstwy, zobacz [Compare Azure CDN Product Features](../../cdn/cdn-features.md).

1. W polu **Nazwa punktu końcowego usługi CDN** Podaj nazwę punktu końcowego sieci CDN. Punkt końcowy usługi CDN musi być unikatowy w ramach platformy Azure i zawiera pierwszą część adresu URL punktu końcowego. Formularz sprawdza, czy nazwa punktu końcowego jest unikatowa.

1. Określ swój statyczny punkt końcowy witryny sieci Web w polu **Nazwa hosta źródła** . 

   Aby znaleźć punkt końcowy statycznej witryny internetowej, przejdź do ustawień **statycznej witryny internetowej** dla konta magazynu.  Skopiuj podstawowy punkt końcowy i wklej go do konfiguracji sieci CDN.

   > [!IMPORTANT]
   > Pamiętaj o usunięciu identyfikatora protokołu (*np.* https) oraz końcowego ukośnika w adresie URL. Na przykład jeśli punkt końcowy statycznej witryny sieci Web to `https://mystorageaccount.z5.web.core.windows.net/` , należy określić `mystorageaccount.z5.web.core.windows.net` wartość w polu **Nazwa hosta źródła** .

   Na poniższej ilustracji przedstawiono przykładową konfigurację punktu końcowego:

   ![Zrzut ekranu przedstawiający przykładową konfigurację punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Wybierz pozycję **Utwórz**, a następnie poczekaj na udostępnienie sieci CDN. Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych. (Jeśli w formularzu występują błędy, obok tego pola pojawia się wykrzyknik).

1. Aby sprawdzić, czy punkt końcowy usługi CDN został prawidłowo skonfigurowany, kliknij punkt końcowy, aby przejść do jego ustawień. W obszarze przegląd usługi CDN dla konta magazynu Znajdź nazwę hosta punktu końcowego i przejdź do punktu końcowego, jak pokazano na poniższej ilustracji. Format punktu końcowego usługi CDN będzie podobny do `https://staticwebsitesamples.azureedge.net` .

    ![Zrzut ekranu przedstawiający przegląd punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Po udostępnieniu punktu końcowego usługi CDN przejściu do punktu końcowego usługi CDN zostanie wyświetlona zawartość pliku index.html, który został wcześniej przekazany do statycznej witryny internetowej.

1. Aby przejrzeć ustawienia źródła dla punktu końcowego usługi CDN, przejdź do lokalizacji **źródłowej** w sekcji **Ustawienia** dla punktu końcowego usługi CDN. Zobaczysz, że w polu **Typ źródła** jest ustawiona wartość *Źródło niestandardowe* i że w polu **Nazwa hosta źródła** jest wyświetlany statyczny punkt końcowy witryny sieci Web.

    ![Zrzut ekranu przedstawiający ustawienia źródła dla punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Usuwanie zawartości z usługi Azure CDN

Jeśli nie chcesz już buforować obiektu w usłudze Azure CDN, możesz wykonać jedną z następujących czynności:

* Ustaw kontener jako prywatny, a nie publiczny. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym do odczytu do kontenerów i obiektów BLOB](./anonymous-read-access-configure.md).
* Wyłącz lub usuń punkt końcowy usługi CDN przy użyciu witryny Azure Portal.
* Zmodyfikuj usługę hostowaną w taki sposób, aby nie odpowiadała już na żądania dla obiektu.

Obiekt, który jest już buforowany w usłudze Azure CDN, pozostaje w pamięci podręcznej, dopóki nie zakończy się okres czasu wygaśnięcia dla obiektu lub dopóki punkt końcowy nie zostanie [przeczyszczony](../../cdn/cdn-purge-endpoint.md). Po zakończeniu okresu czasu wygaśnięcia usługa Azure CDN określa, czy punkt końcowy usługi CDN jest nadal ważny i czy obiekt jest nadal anonimowo dostępny. Jeżeli tak nie jest, obiekt nie będzie już buforowany.

## <a name="next-steps"></a>Następne kroki

Obowiązkowe Dodaj domenę niestandardową do punktu końcowego Azure CDN. Zobacz [Samouczek: Dodawanie domeny niestandardowej do punktu końcowego Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).