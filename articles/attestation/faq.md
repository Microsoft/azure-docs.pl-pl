---
title: Często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące zaświadczania Microsoft Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89236709"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Często zadawane pytania dotyczące zaświadczania Microsoft Azure

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące [zaświadczania platformy Azure](overview.md).

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, możesz również przesłać żądanie pomocy technicznej platformy Azure na [stronie pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Co to jest usługa Azure PCK buforowania i jej rola w zaświadczeniu enklawy

Usługa Azure PCK cache definiuje linię bazową zabezpieczeń platformy Azure dla węzłów [usługi Azure CONFIDENTIAL (ACC)](../confidential-computing/overview.md) firmy Intel i buforuje dane. Informacje w pamięci podręcznej będą dodatkowo używane przez zaświadczanie o platformie Azure w ramach weryfikacji zaufanych środowisk wykonywania (TEEs).  

Usługa buforowania PCK platformy Azure:
   - Oferuje wysoką dostępność 
   - Zmniejsza zależności od zewnętrznych usług hostowanych i łączności z Internetem.
   - Pobiera najnowsze wersje certyfikatów firmy Intel, list CRL, zaufanych obliczeń podstawowych (TCB) i enklawy tożsamość z węzłów kont z firmy Intel. W związku z tym usługa potwierdza podstawę zabezpieczeń platformy Azure, która jest określana na podstawie zaświadczania platformy Azure podczas sprawdzania poprawności TEEs, co znacznie zmniejsza liczbę błędów zaświadczania spowodowanych nieprawidłowymi lub odwołaniami certyfikatów firmy Intel.  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>SGX zaświadczanie obsługiwane przez zaświadczanie platformy Azure w środowiskach innych niż Azure

Zaświadczanie platformy Azure zależy od linii bazowej zabezpieczeń podanej przez usługę buforowania PCK Azure, aby zweryfikować TEEs. Usługa Azure PCK buforowania jest obecnie zaprojektowana do obsługi tylko węzłów danych poufnych na platformie Azure. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Jakie są walidacje wykonywane przez zaświadczanie platformy Azure na potrzeby zaświadczania SGX enclaves

Zaświadczanie platformy Azure to ujednolicona platforma służąca do zdalnego zaświadczania różnych typów TEEs. Zaświadczanie platformy Azure:

   - Sprawdza, czy zaufany certyfikat główny podpisanego enklawy należy do firmy Intel.
   - Sprawdza, czy oferta enklawy jest zgodna z linią bazową zabezpieczeń platformy Azure zgodnie z definicją w usłudze Azure PCKing Service.
   - Sprawdza, czy Skrót SHA256 enklawy danych przechowywanych (EHD) w obiekcie żądania zaświadczania dopasowuje pierwsze 32 bajtów reportData w cudzysłowie enklawy.
   - Umożliwia klientom Tworzenie dostawcy zaświadczania i Konfigurowanie zasad niestandardowych. Oprócz powyższych poprawń zaświadczanie platformy Azure szacuje cytat enklawy względem zasad. Zasady definiują reguły autoryzacji dla enklawy, a także wymuszają reguły wystawiania w celu wygenerowania tokenu zaświadczania. Aby potwierdzić, że zamierzone oprogramowanie jest uruchomione w enklawy, klienci mogą dodać reguły autoryzacji, aby sprawdzić, czy pola **mrsigner** i **mrenclave** w ofercie enklawy pasują do wartości plików binarnych klienta.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Jak weryfikator może uzyskać dodatkowe informacje dotyczące zaświadczania SGX obsługiwanego przez zaświadczanie o platformie Azure

Ogólnie rzecz biorąc, w przypadku modeli zaświadczania z technologią Intel jako rdzeń zaufania klient zaświadczania komunikuje się z interfejsami API enklawy, aby pobrać dowody enklawy. Interfejsy API enklawy wewnętrznie wywołania usługi buforowania Intel PCK w celu pobrania certyfikatów firmy Intel węzła do zaświadczania. Certyfikaty są używane do podpisywania dowodu enklawy, co powoduje wygenerowanie zdalnego zaświadczania zabezpieczeń.  

Ten sam proces można zaimplementować na potrzeby zaświadczania platformy Azure. Aby jednak korzystać z zalet oferowanych przez usługę Azure PCK buforowania, po zainstalowaniu maszyny wirtualnej z KONTAmi zaleca się zainstalowanie [biblioteki usługi Azure DCAP](https://www.nuget.org/packages/Microsoft.Azure.DCAP). W oparciu o umowę z technologią Intel, gdy zainstalowana jest biblioteka DCAP platformy Azure, żądania wygenerowania dowodu enklawy są przekierowywane z usługi PCK buforowania firmy Intel do usługi Azure PCK buforowania. Biblioteka DCAP platformy Azure jest obsługiwana w środowiskach opartych na systemie Windows i Linux.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Jak przejść do zaświadczania platformy Azure z innych modeli zaświadczania

- Po zainstalowaniu maszyny wirtualnej do przetwarzania poufnego platformy Azure Zainstaluj bibliotekę DCAP platformy Azure ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)), aby korzystać z zalet oferowanych przez usługę Azure PCK buforowania.
- Klient zaświadczania zdalnego musi zostać utworzony, który może pobrać dowody enklawy i wysyłać żądania do zaświadczania platformy Azure. Zobacz [przykłady kodu](/samples/browse/?expanded=azure&terms=attestation) , aby uzyskać informacje 
- Żądania zaświadczania mogą być wysyłane do punktu końcowego interfejsu API REST dla domyślnych dostawców lub niestandardowych dostawców zaświadczania 
- Interfejsy API zaświadczania platformy Azure są chronione za pomocą uwierzytelniania usługi Azure AD. W związku z tym klient, który wywołuje interfejsy API zaświadczania, musi mieć możliwość uzyskania i przekazania prawidłowego tokenu dostępu usługi Azure AD w żądaniu zaświadczania 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Jak Jednostka uzależniona może zweryfikować integralność tokenu zaświadczania

Token zaświadczania wygenerowany przez zaświadczenie platformy Azure jest podpisywany przy użyciu certyfikatu z podpisem własnym. Certyfikaty są udostępniane za pośrednictwem [punktu końcowego metadanych OpenID Connect](/rest/api/attestation/metadataconfiguration/get). Jednostka uzależniona może pobrać certyfikaty podpisywania z tego punktu końcowego i przeprowadzić weryfikację sygnatury tokenu zaświadczania. Czas ważności tokenu zaświadczania to 8 godzin. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Jak zidentyfikować certyfikat, który ma być używany do weryfikacji podpisu z punktu końcowego metadanych OpenID Connect

Wiele certyfikatów uwidocznionych w punkcie końcowym metadanych OpenID Connect odpowiada różnym przypadkom użycia (np. zaświadczeniu SGX) obsługiwanym przez zaświadczanie platformy Azure. Zgodnie z normami określonymi w [dokumencie RFC 7515](https://tools.ietf.org/html/rfc7515), certyfikat z identyfikatorem klucza (dziecko) zgodny z parametrem *dzieciaka* w nagłówku tokenu zaświadczania ma służyć do weryfikacji podpisu. Jeśli nie zostanie znaleziony pasujący **dzieci** , będzie można wypróbować wszystkie certyfikaty uwidocznione przez punkt końcowy metadanych OpenID Connect.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Czy istnieje możliwość współdzielenia przez jednostkę uzależnioną wpisów tajnych z zweryfikowanymi środowiskami wykonywania (TEEs).

Klucz publiczny wygenerowany w ramach enklawy może być wyrażony we właściwości Enklawyed Data (EHD) obiektu żądania zaświadczania wysyłanego przez klienta do zaświadczania platformy Azure. Po potwierdzeniu, czy Skrót SHA256 EHD jest wyrażony w polu reportData w ofercie, zaświadczanie platformy Azure obejmuje EHD w token zaświadczania. Jednostka uzależniona może używać EHD z zweryfikowanej odpowiedzi zaświadczania, aby szyfrować wpisy tajne i udostępniać je enklawy. Aby uzyskać więcej informacji, zobacz [podstawowe pojęcia dotyczące zaświadczania platformy Azure](basic-concepts.md) .
