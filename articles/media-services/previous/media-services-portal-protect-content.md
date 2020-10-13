---
title: Skonfiguruj zasady ochrony zawartości przy użyciu Azure Portal | Microsoft Docs
description: W tym artykule przedstawiono sposób konfigurowania zasad ochrony zawartości przy użyciu Azure Portal. W tym artykule pokazano również, jak włączyć szyfrowanie dynamiczne dla zasobów.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 061d66f08e79f935c778eb84dd47f71cab1a62d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266633"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurowanie zasad ochrony zawartości przy użyciu Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > żadne nowe funkcje lub funkcje nie są dodawane do Media Services V2. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)
>

 Za pomocą Azure Media Services można zabezpieczyć nośnik od momentu, w którym komputer przejdzie przez magazyn, przetwarzanie i dostarczanie. Za pomocą Media Services można zapewnić zaszyfrowaną zawartość dynamicznie przy użyciu Advanced Encryption Standard (AES), używając kluczy szyfrowania 128-bitowych. Można go również używać z typowym szyfrowaniem (CENC) przy użyciu usług PlayReady i/lub Widevine Digital Rights Management (DRM) i Apple FairPlay. 

Media Services udostępnia usługi do dostarczania licencji DRM i kluczy przezroczystych AES do autoryzowanych klientów. Azure Portal można użyć do utworzenia jednej zasady autoryzacji kluczy/licencji dla wszystkich typów szyfrowania.

W tym artykule przedstawiono sposób konfigurowania zasad ochrony zawartości przy użyciu portalu. W tym artykule pokazano również, jak zastosować szyfrowanie dynamiczne do zasobów.

## <a name="start-to-configure-content-protection"></a>Rozpocznij Konfigurowanie ochrony zawartości
Aby użyć portalu do skonfigurowania globalnej ochrony zawartości przy użyciu konta Media Services, wykonaj następujące czynności:

1. W [portalu](https://portal.azure.com/)wybierz konto Media Services.

1. Wybierz pozycję **Ustawienia**  >  **Ochrona zawartości**.

    ![Ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zasady autoryzacji kluczy/licencji
Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania kluczy lub licencji. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient musi spełnić te zasady, aby można było do niego dostarczyć klucz/licencję. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu.

Za pomocą portalu można utworzyć jedną zasadę autoryzacji klucza/licencji dla wszystkich typów szyfrowania.

### <a name="open-authorization"></a>Otwórz autoryzację
Ograniczenie Open oznacza, że system dostarcza klucz do każdego, kto wykonuje żądanie Key. To ograniczenie może być przydatne w celach testowych. 

### <a name="token-authorization"></a>Autoryzacja tokenu
Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów: prosty token sieci Web (SWT) i token sieci Web JSON (JWT). Media Services nie udostępnia usługi STS. Możesz utworzyć niestandardową usługę STS lub użyć usługi Azure Access Control Service do wystawiania tokenów. Usługa STS musi być skonfigurowana w taki sposób, aby utworzyć token podpisany przy użyciu określonego klucza i wystawiać oświadczenia określone w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z tymi skonfigurowanymi dla klucza (lub licencji), usługa dostarczania kluczy Media Services Zwraca żądany klucz (lub licencję) do klienta.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacyjny, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacyjny zawiera klucz, za pomocą którego token został podpisany. Wystawca jest usługą bezpiecznego tokenu, która wystawia token. Odbiorcy (czasami nazywane zakresem) opisują zamiar tokenu lub zasobu, do którego token autoryzuje dostęp. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasują do wartości w szablonie.

![Zasady autoryzacji kluczy/licencji](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Szablon licencji PlayReady
Szablon licencji PlayReady ustawia funkcjonalność, która jest włączona na licencji PlayReady. Aby uzyskać więcej informacji na temat szablonu licencji PlayReady, zobacz [Media Services Omówienie szablonu licencji PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nietrwałe
W przypadku skonfigurowania licencji jako nietrwałej jest ona przechowywana w pamięci tylko podczas korzystania z licencji.  

![Nietrwała ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Stale
W przypadku skonfigurowania licencji jako trwałej jest ona zapisywana w magazynie trwałym na komputerze klienckim.

![Trwała ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Szablon licencji Widevine
Szablon licencji Widevine służy do ustawiania funkcjonalności włączonej na licencjach Widevine.

### <a name="basic"></a>Podstawowy
Po wybraniu opcji **podstawowa**szablon zostanie utworzony ze wszystkimi wartościami domyślnymi.

### <a name="advanced"></a>Zaawansowane
Aby uzyskać więcej informacji na temat szablonu praw Widevine, zobacz [Omówienie szablonu licencji Widevine](media-services-widevine-license-template-overview.md).

![Zaawansowana ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfiguracja FairPlay
Aby włączyć szyfrowanie FairPlay, wybierz pozycję **Konfiguracja FairPlay**. Następnie wybierz **certyfikat aplikacji** i wprowadź **klucz tajny aplikacji**. Aby uzyskać więcej informacji o konfiguracji i wymaganiach FairPlay, zobacz [Ochrona zawartości HLS za pomocą Apple FairPlay lub Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Konfiguracja FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Zastosuj szyfrowanie dynamiczne do zasobu
Aby skorzystać z szyfrowania dynamicznego, zakodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Wybierz element zawartości, który chcesz zaszyfrować
Aby wyświetlić wszystkie zasoby, wybierz pozycję **Ustawienia**  >  **zasoby**.

![Opcja zasobów](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Szyfrowanie za pomocą algorytmu AES lub DRM
Po wybraniu opcji Szyfruj dla elementu zawartości widoczne **są** dwie opcje: **AES** lub **DRM**. 

#### <a name="aes"></a>AES
Szyfrowanie klucza Wyczyść AES jest włączone dla wszystkich protokołów przesyłania strumieniowego: Smooth Streaming, HLS i MPEG-KRESKa.

![Konfiguracja szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>ZASTOSOWANIE
1. Po wybraniu funkcji **DRM**zostaną wyświetlone różne zasady ochrony zawartości (które muszą być konfigurowane przez ten punkt) oraz zestaw protokołów przesyłania strumieniowego:

    a. Technologie **PlayReady i Widevine z użyciem MPEG-pauz** dynamicznie szyfrują strumień MPEG-pauzowy przy użyciu oprogramowania PlayReady i Widevine protokołów DRM.

    b. Technologie **PlayReady i Widevine z użyciem formatu MPEG-myślnik + FairPlay z HLS** dynamicznie szyfrują strumień MPEG-kreskowany przy użyciu oprogramowania PlayReady i Widevine protokołów DRM. Ta opcja szyfruje także strumienie HLS z FairPlay.

    c. Technologia **PlayReady tylko z Smooth Streaming, HLS i MPEG-kreskowa** dynamicznie szyfruje strumienie Smooth Streaming, HLS i MPEG-kreskowane za pomocą technologii PlayReady DRM.

    d. **Widevine tylko z opcją MPEG-kreska** dynamicznie SZYFRUJe pauzę MPEG przy użyciu funkcji Widevine DRM.
    
    e. **FairPlay tylko z HLS** dynamicznie szyfruje strumień HLS za pomocą FairPlay.

1. Aby włączyć szyfrowanie FairPlay, w bloku **Ustawienia globalne Content Protection** wybierz pozycję **Konfiguracja FairPlay**. Następnie wybierz **certyfikat aplikacji**, a następnie wprowadź **klucz tajny aplikacji**.

    ![Typ szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Po wybraniu opcji szyfrowania wybierz pozycję **Zastosuj**.

>[!NOTE] 
>Jeśli planujesz odtwarzanie HLS szyfrowanych algorytmem AES w przeglądarce Safari, zapoznaj się z wpisem w blogu [HLS w przeglądarce Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
