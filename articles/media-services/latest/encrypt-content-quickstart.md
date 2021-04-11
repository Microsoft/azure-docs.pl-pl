---
title: Szyfruj zawartość za pomocą Azure Portal
description: W tym przewodniku szybki start pokazano, jak skonfigurować szyfrowanie zawartości przy użyciu Azure Media Services w Azure Portal.
ms.topic: quickstart
ms.date: 08/31/2020
ms.openlocfilehash: cef943d61b1f3073ced658454c702ac54a0eef6d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963159"
---
# <a name="quickstart-use-portal-to-encrypt-content"></a>Szybki Start: używanie portalu do szyfrowania zawartości

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Użyj Azure Media Services, aby pomóc w zabezpieczeniu nośnika od momentu, gdy komputer przejdzie przez cały proces w ramach magazynu, przetwarzania i dostarczania. Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie przy użyciu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługi do dostarczania kluczy AES oraz licencji DRM (PlayReady, Widevine i FairPlay) do autoryzowanych klientów. 
 
Aby określić opcje szyfrowania (jeśli istnieją) w strumieniu, użyj **zasad przesyłania strumieniowego** i skojarz je z lokalizatorem przesyłania strumieniowego. Tworzenie **zasad klucza zawartości** w celu skonfigurowania sposobu, w jaki klucz zawartości (zapewniający bezpieczny dostęp do **zasobów**) jest dostarczany do klientów końcowych. Należy ustawić wymagania (ograniczenia) dotyczące zasad kluczy zawartości, które muszą zostać spełnione, aby klucze z określoną konfiguracją były dostarczane do klientów. 

> [!NOTE]
> Zasady kluczy zawartości nie są wymagane do wyczyszczenia przesyłania strumieniowego ani pobierania.

Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu klucza Wyczyść AES lub szyfrowania DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy Media Services lub usługi dostarczania kluczy określonej przez użytkownika. Aby zdecydować, czy użytkownik jest autoryzowany do pobrania klucza, usługa szacuje  **Zasady kluczy zawartości** określone dla klucza.

W tym przewodniku szybki start przedstawiono sposób tworzenia zasad klucza zawartości, w których można określić, jakie szyfrowanie ma być stosowane do zasobu w przypadku przesyłania strumieniowego. Ten przewodnik Szybki Start pokazuje również, jak ustawić skonfigurowane szyfrowanie dla elementu zawartości.

### <a name="suggested-pre-reading"></a>Sugerowane przed odczytem

* [Szyfrowanie dynamiczne i dostarczanie kluczy](content-protection-overview.md)
* [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady kluczy zawartości](content-key-policy-concept.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przekaż i przetwórz zawartość zgodnie z opisem w artykule [Zarządzanie zasobami w Azure Portal](asset-create-asset-upload-portal-quickstart.md)

## <a name="create-a-content-key-policy"></a>Tworzenie zasad klucza zawartości

Utwórz **Zasady kluczy zawartości** , aby skonfigurować sposób, w jaki klucz zawartości (zapewniający bezpieczny dostęp do **zasobów**) jest dostarczany do klientów końcowych.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).
1. Znajdź i kliknij konto Media Services.
1. Wybierz pozycję **Zasady kluczy zawartości (nowe)**.
1. Naciśnij pozycję **+ Dodaj Zasady kluczy zawartości** w górnej części okna. 

Zostanie wyświetlone okno **Tworzenie zasad klucza zawartości** . W tym oknie wybiera się opcje szyfrowania. Możesz chronić multimedia, wybierając pozycję Digital Rights Management (DRM), szyfrowanie Advanced Encryption Standard (AES) lub oba te opcje.  

![Tworzenie zasad klucza zawartości](./media/encrypt-content-quickstart/create-content-key-policy.png)

Bez względu na to, czy wybierasz jedną z opcji DRM, czy opcję Wyczyść klucz AES-128, zaleca się określenie sposobu konfigurowania ograniczeń. Istnieje możliwość wybrania ograniczenia dotyczącego otwarcia lub tokenu. Aby uzyskać szczegółowe wyjaśnienie, zobacz [kontrolowanie dostępu do zawartości](content-protection-overview.md#controlling-content-access).

### <a name="add-a-drm-content-key"></a>Dodawanie klucza zawartości DRM

Możesz zdecydować się na ochronę zawartości za pomocą oprogramowania Microsoft PlayReady i/lub Google Widevine lub Apple FairPlay. Każdy typ dostarczania licencji sprawdzi klucze zawartości na podstawie poświadczeń w zaszyfrowanym formacie.

#### <a name="license-templates"></a>Szablony licencji

Aby uzyskać szczegółowe informacje na temat szablonów licencji, zobacz:

* [Szablon licencji usługi Google Widevine](widevine-license-template-overview.md)

    > [!NOTE]
    > Można utworzyć pusty szablon licencji bez wartości, po prostu " {} ." Następnie tworzony jest szablon licencji z wartościami domyślnymi. Wartość domyślna działa w większości przypadków.
* [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](fairplay-license-overview.md)
* [Szablon licencji PlayReady](playready-license-template-overview.md)

### <a name="add-aes-clear-key"></a>Dodaj czysty klucz AES

Możesz również dodać do zawartości szyfrowanie klucza AES-128. Klucz zawartości jest przesyłany do klienta w nieszyfrowanym formacie.

![Klucz niezaszyfrowany AES](./media/encrypt-content-quickstart/aes-clear-key-policy.png)

## <a name="create-a-streaming-locator-for-your-asset"></a>Tworzenie lokalizatora przesyłania strumieniowego dla zasobu

1. Znajdź i kliknij konto Media Services.
1. Wybierz pozycję **zasoby (nowe)**.
1. Z listy zasobów wybierz ten, który chcesz zaszyfrować.  
1. W sekcji **lokalizator przesyłania strumieniowego** wybranego elementu zawartości naciśnij pozycję **+ Dodaj lokalizator przesyłania strumieniowego**. 
1. Wybierz **zasady przesyłania strumieniowego** odpowiednie dla skonfigurowanych **zasad klucza zawartości** .

    Temat [zasady przesyłania strumieniowego](streaming-policy-concept.md) zawiera szczegółowe informacje dotyczące zasad przesyłania strumieniowego, które są zgodne z zasadami klucza zawartości.
1. Po wybraniu odpowiednich zasad przesyłania strumieniowego możesz wybrać z listy rozwijanej zasady dotyczące klucza zawartości.
1. Naciśnij przycisk **Dodaj** , aby dodać lokalizator przesyłania strumieniowego do Twojego elementu zawartości.

    Spowoduje to opublikowanie elementu zawartości i wygenerowanie adresów URL przesyłania strumieniowego.

![Lokalizator przesyłania strumieniowego](./media/encrypt-content-quickstart/multi-drm.png)

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz wypróbować inne Przewodniki Szybki Start, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie elementami zawartości](asset-create-asset-upload-portal-quickstart.md)
