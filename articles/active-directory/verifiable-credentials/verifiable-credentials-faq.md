---
title: Często zadawane pytania — poświadczenia zweryfikowane przez platformę Azure (wersja zapoznawcza)
description: Znajdź odpowiedzi na często zadawane pytania dotyczące zweryfikowanych poświadczeń
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280721"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Na tej stronie znajdują się często zadawane pytania dotyczące zweryfikowanych poświadczeń i tożsamości zdecentralizowanej. Pytania są zorganizowane w poniższych sekcjach.

- [Słownictwo i podstawy](#the-basics)
- [Pytania koncepcyjne dotyczące tożsamości zdecentralizowanej](#conceptual-questions)
- [Pytania dotyczące korzystania z wersji zapoznawczej poświadczeń do zweryfikowania](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Podstawy

### <a name="what-is-a-did"></a>Co to jest? 

Zdecentralizowany Identifers (DIDs) to identyfikatory, których można użyć do zabezpieczenia dostępu do zasobów, podpisywania i weryfikowania poświadczeń oraz ułatwiania wymiany danych aplikacji. W przeciwieństwie do tradycyjnych nazw użytkowników i adresów e-mail, DIDs są własnością i są kontrolowane przez samą jednostkę (być osobą, urządzeniem lub firmą). DIDs istnieje niezależnie od dowolnej organizacji zewnętrznej lub zaufanego pośrednika. [Specyfikacja identyfikatora zdecentralizowanego W3C](https://www.w3.org/TR/did-core/) objaśnia ten temat bardziej szczegółowo.

### <a name="why-do-we-need-a-did"></a>Dlaczego jesteśmy musieli?

Zaufanie cyfrowe zasadniczo wymaga od uczestników posiadania i kontroli tożsamości, a tożsamość rozpoczyna się od identyfikatora.
W wieku codziennym i atakami systemu na dużą skalę na scentralizowanym identyfikatorze honeypots, zdecentralizowana tożsamość jest istotna dla klientów i firm.
Osoby posiadające i kontrolujące ich tożsamości mogą wymieniać dane zweryfikowane i sprawdzać. Rozproszone środowisko poświadczeń umożliwia automatyzację wielu procesów roboczych, które są obecnie pracochłonne i pracochłonne.

### <a name="what-is-a-verifiable-credential"></a>Co to jest poświadczenia podlegające weryfikacji? 

Poświadczenia są częścią naszego dziennego życia; licencje sterownika są używane w celu potwierdzenia, że możemy obsłużyć pojazd silnikowy, można użyć stopni akademickich w celu potwierdzenia naszego poziomu edukacji, a paszporty wystawione przez rząd umożliwiają nam poruszanie się między krajami. Zweryfikowane poświadczenia udostępniają mechanizm do wyrażania tych rodzajów poświadczeń w sieci Web w sposób, który jest w sposób bezpieczny, chroniony przez ochronę prywatności oraz do zweryfikowania maszynowego. [Specyfikacja poświadczeń z możliwością zweryfikowania W3C](https://www.w3.org/TR/vc-data-model//) wyjaśnia to bardziej szczegółowo.


## <a name="conceptual-questions"></a>Pytania koncepcyjne

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Co się stanie po utracie telefonu przez użytkownika? Czy można odzyskać swoją tożsamość?

Istnieje wiele sposobów oferowania użytkownikom mechanizmu odzyskiwania, z których każdy ma własne kompromisy. Obecnie oceniamy opcje i opracowujemy podejścia do odzyskiwania, które oferują wygodę i bezpieczeństwo, przy jednoczesnym poszanowaniu prywatności i samodzielności użytkownika.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Jak użytkownik może ufać żądaniu od emitenta lub weryfikatora? Jak wiadomo, że jest to prawdziwa firma?

Wdrożono [dobrze znane źródło konfiguracji programu Identity Foundation](https://identity.foundation/.well-known/resources/did-configuration/) , aby można było połączyć się z wysoce znanymi istniejącymi systemami, nazwami domen. Każda została utworzona przy użyciu poświadczeń do zweryfikowania Azure Active Directory ma opcję dołączenia nazwy domeny głównej, która zostanie zaszyfrowana w dokumencie. Aby dowiedzieć się więcej, postępuj zgodnie z artykułem zatytułowanym [łączenie domeny z identyfikatorem rozproszonym](how-to-dnsbind.md) .  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Dlaczego zweryfikowane poświadczenia w wersji zapoznawczej używają jonu jako metody wykonanej, a tym samym Bitcoin do zapewnienia zdecentralizowanej infrastruktury kluczy publicznych?

JONU to zdecentralizowana, niewymagająca, skalowalna sieć jednoscentralizowanego identyfikatora, która korzysta z korzystającego Bitcoin. Zapewnia ona skalowalność bez uwzględniania specjalnych tokenów cryptoasset, zaufanych modułów sprawdzania poprawności lub scentralizowanych mechanizmów konsensusu. Firma Microsoft używa Bitcoin dla podłoża warstwy 1 podstawowego ze względu na siłę scentralizowanej sieci, aby zapewnić wysoki poziom niezmienności dla chronologicznego systemu rejestrowania zdarzeń.

## <a name="using-the-preview"></a>Korzystanie z wersji zapoznawczej

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Dlaczego należy używać NodeJS do zweryfikowania poświadczeń z poświadczeniami? Czy istnieją plany dla innych języków programowania? 

Wybieramy NodeJS, ponieważ jest to bardzo popularna platforma dla deweloperów aplikacji. Zostanie wystawiony interfejs API REST, który umożliwi deweloperom wystawianie i weryfikowanie poświadczeń. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Czy dowolny kod używany w wersji zapoznawczej Open Source?

Tak! Następujące repozytoria to składniki typu "open source" naszych usług.

1. [SideTree, w witrynie GitHub](https://github.com/decentralized-identity/sidetree)
2. [Zestaw VC SDK dla węzła w serwisie GitHub](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [Android SDK tworzenia zdecentralizowanych portfeli tożsamości w witrynie GitHub](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [Zestaw SDK systemu iOS służący do tworzenia zdecentralizowanych portfeli tożsamości w witrynie GitHub](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Jakie są wymagania dotyczące licencjonowania?

Licencja usługi Azure AD P2 jest wymagana do korzystania z wersji zapoznawczej poświadczeń do zweryfikowania. Jest to tymczasowy wymóg, ponieważ oczekujemy, że ceny tej usługi są naliczane na podstawie użycia. 


## <a name="next-steps"></a>Następne kroki

- [Jak dostosować Azure Active Directory poświadczenia do zweryfikowania](credential-design.md)
