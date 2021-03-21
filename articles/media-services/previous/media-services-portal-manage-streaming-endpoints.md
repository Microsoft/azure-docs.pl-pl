---
title: Zarządzaj punktami końcowymi przesyłania strumieniowego za pomocą Azure Portal | Microsoft Docs
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego za pomocą Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017396"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego przy użyciu witryny Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym artykule pokazano, jak za pomocą Azure Portal zarządzać punktami końcowymi przesyłania strumieniowego. 

>[!NOTE]
>Zapoznaj się z artykułem [Przegląd](media-services-streaming-endpoints-overview.md) . 

Informacje o sposobie skalowania punktu końcowego przesyłania strumieniowego znajdują się w [tym](media-services-portal-scale-streaming-endpoints.md) artykule.

## <a name="start-managing-streaming-endpoints"></a>Rozpocznij zarządzanie punktami końcowymi przesyłania strumieniowego 

Aby rozpocząć zarządzanie punktami końcowymi przesyłania strumieniowego dla Twojego konta, wykonaj następujące czynności.

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W bloku **Ustawienia** wybierz pozycję **punkty końcowe przesyłania strumieniowego**.
   
    ![Zrzut ekranu przedstawiający stronę "Media Services" z opcją "punkty końcowe przesyłania strumieniowego" wybraną w bloku "Ustawienia".](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

## <a name="adddelete-a-streaming-endpoint"></a>Dodawanie/Usuwanie punktu końcowego przesyłania strumieniowego

>[!NOTE]
>Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego.

Aby dodać/usunąć punkt końcowy przesyłania strumieniowego przy użyciu Azure Portal, wykonaj następujące czynności:

1. Aby dodać punkt końcowy przesyłania strumieniowego, kliknij przycisk **+ punkt końcowy** w górnej części strony. 

    Można potrzebować wielu punktów końcowych przesyłania strumieniowego, jeśli planujesz korzystać z różnych sieci CDN lub sieci CDN i bezpośredniego dostępu.

2. Aby usunąć punkt końcowy przesyłania strumieniowego, naciśnij przycisk **Usuń** .      
3. Kliknij przycisk **Uruchom** , aby uruchomić punkt końcowy przesyłania strumieniowego.
   
    ![Zrzut ekranu pokazujący wybraną akcję "punkt końcowy" i wyświetlenie okienka "Szczegóły punktu końcowego przesyłania strumieniowego".](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Konfigurowanie punktu końcowego przesyłania strumieniowego
Punkt końcowy przesyłania strumieniowego umożliwia skonfigurowanie następujących właściwości:

* Kontrola dostępu
* Kontrola pamięci podręcznej
* Zasady dostępu między lokacjami

Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Po włączeniu usługi CDN nie można uzyskać dostępu do dostępu do adresu IP. Dostęp do adresu IP ma zastosowanie tylko wtedy, gdy nie masz usługi CDN.

Punkt końcowy przesyłania strumieniowego można skonfigurować, wykonując następujące czynności:

1. Wybierz punkt końcowy przesyłania strumieniowego, który chcesz skonfigurować.
2. Kliknij polecenie **Ustawienia**.

Poniżej znajduje się krótki opis poniższych pól.

![Zrzut ekranu, na którym jest wyświetlana akcja "Ustawienia" wybrana dla punktu końcowego przesyłania strumieniowego.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maksymalna liczba zasad pamięci podręcznej: służy do konfigurowania okresu istnienia pamięci podręcznej dla zasobów udostępnianych za poorednictwem tego punktu końcowego Jeśli wartość nie jest ustawiona, używany jest domyślny. Wartości domyślne można także definiować bezpośrednio w usłudze Azure Storage. Jeśli Azure CDN jest włączona dla punktu końcowego przesyłania strumieniowego, nie należy ustawiać wartości zasad pamięci podręcznej na wartość mniejszą niż 600 sekund.  
2. Dozwolone adresy IP: służy do określania adresów IP, które mogą nawiązywać połączenia z opublikowanym punktem końcowym przesyłania strumieniowego. Jeśli nie określono adresów IP, każdy adres IP będzie mógł nawiązać połączenie. Adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub zakres adresów IP przy użyciu adresu IP i maski podsieci dziesiętnej (na przykład "10.0.0.1 (255.255.255.0)").
3. Konfiguracja uwierzytelniania nagłówka sygnatury Akamai: służy do określania sposobu skonfigurowania żądania uwierzytelniania nagłówka podpisu z serwerów Akamai. Wygaśnięcie jest w formacie UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skalowanie punktu końcowego przesyłania strumieniowego Premium

Aby uzyskać więcej informacji, zobacz [ten](media-services-portal-scale-streaming-endpoints.md) artykuł.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Włącz integrację Azure CDN

Podczas tworzenia nowego konta domyślnie włączona jest integracja domyślnego punktu końcowego przesyłania strumieniowego Azure CDN.

Jeśli zechcesz później wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi znajdować się w stanie **zatrzymanym** . Włączenie integracji Azure CDN może potrwać do dwóch godzin, a zmiany zostaną uaktywnione we wszystkich punktach pop usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i strumień bez przerw w punkcie końcowym przesyłania strumieniowego, a po zakończeniu integracji zostanie dostarczony strumień z sieci CDN. W trakcie okresu aprowizacji punkt końcowy przesyłania strumieniowego będzie w stanie **uruchomienia** i może wystąpić spadek wydajności.

Integracja z usługą CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem Chin i federalnych regionów rządowych.

Gdy ta funkcja jest włączona, **Access Control**, * * niestandardowa nazwa hosta i konfiguracja **uwierzytelniania podpisów Akamai** zostaną wyłączone.
 
> [!IMPORTANT]
> Integracja Azure Media Services z Azure CDN jest zaimplementowana w **Azure CDN z Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **Azure CDN warstw cenowych i dostawców**. Aby uzyskać więcej informacji na temat funkcji Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Gdy Usługa CDN jest włączona dla punktu końcowego przesyłania strumieniowego, klienci nie mogą zażądać zawartości bezpośrednio z lokalizacji źródłowej. Jeśli potrzebujesz możliwości testowania zawartości z użyciem sieci CDN lub bez niej, możesz utworzyć inny punkt końcowy przesyłania strumieniowego, który nie jest włączony w sieci CDN.
* Nazwa hosta punktu końcowego przesyłania strumieniowego pozostaje taka sama po włączeniu sieci CDN. Nie musisz wprowadzać żadnych zmian w przepływie pracy usługi Media Services po włączeniu sieci CDN. Jeśli na przykład nazwa hosta punktu końcowego przesyłania strumieniowego to strasbourg.streaming.mediaservices.windows.net, po włączeniu sieci CDN zostanie użyta dokładna nazwa hosta.
* W przypadku nowych punktów końcowych przesyłania strumieniowego można włączyć usługę CDN po prostu, tworząc nowy punkt końcowy. w przypadku istniejących punktów końcowych przesyłania strumieniowego należy najpierw zatrzymać punkt końcowy, a następnie włączyć/wyłączyć usługę CDN.
* Standardowy punkt końcowy przesyłania strumieniowego można skonfigurować tylko przy użyciu **standardowego dostawcy usługi CDN (Verizon** ) przy użyciu klasycznego portalu Azure. Można jednak włączyć innych dostawców Azure CDN przy użyciu interfejsów API REST.

## <a name="configure-cdn-profile"></a>Konfigurowanie profilu CDN

Profil usługi CDN można skonfigurować, wybierając przycisk **Zarządzaj CDN** w górnej części strony.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
