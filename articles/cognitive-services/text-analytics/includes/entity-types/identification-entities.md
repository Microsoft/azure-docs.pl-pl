---
title: Jednostki identyfikacji
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599308"
---
### <a name="financial-account-identification"></a>Identyfikacja konta finansowego

Ta kategoria jednostki obejmuje informacje finansowe i oficjalne formy identyfikacji.

#### <a name="category-aba-routing-number"></a>Kategoria: numer routingu ABA

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        ABA numer routingu

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Numery routingu tranzytowego stowarzyszenia bankowego (ABA).

        Aby uzyskać tę kategorię jednostki, Dodaj `ABARoutingNumber` do `pii-categories` parametru. `ABARoutingNumber` w razie wykrycia zostanie również zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>Kategoria: kod SWIFT

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Kod SWIFT

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Kody SWIFT dla informacji o instrukcji płatności.

        Aby uzyskać tę kategorię jednostki, Dodaj `SWIFTCode` do `pii-categories` parametru. `SWIFTCode` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategoria: karta kredytowa

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Karta kredytowa

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Numery kart kredytowych. 

        Aby uzyskać tę kategorię jednostki, Dodaj `CreditCardNumber` do `pii-categories` parametru. `CreditCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.

    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategoria: Międzynarodowy numer konta bankowego (IBAN) 

Ta kategoria zawiera następującą jednostkę:

:::row:::
    :::column span="":::
        **Jednostka**

        Karta kredytowa

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Kody IBAN dla informacji o instrukcji płatności.

        Aby uzyskać tę kategorię jednostki, Dodaj `InternationlBankingAccountNumber` do `pii-categories` parametru. `InternationlBankingAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="2":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identyfikacja specyficzna dla instytucji rządowych i krajów/regionów

> [!NOTE]
> Następujące jednostki finansowe i specyficzne dla kraju nie są zwracane z `domain=phi` parametrem:
> * Numery paszportów
> * Identyfikatory podatków

Następujące jednostki są pogrupowane i wyszczególnione według kraju:

#### <a name="argentina"></a>Argentyna

:::row:::
    :::column span="":::
        **Jednostka**

        Numer tożsamości narodowej (DNI) Argentyna

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `ARNationalIdentityNumber` do `pii-categories` parametru. `ARNationalIdentityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Austria

:::row:::
    :::column span="":::
        **Jednostka**

        Karta tożsamości Austria

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `ATIdentityCard` do `pii-categories` parametru. `ATIdentityCard` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Austria numer identyfikacyjny podatku

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ATTaxIdentificationNumber` do `pii-categories` parametru. `ATTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Austria wartość dodana podatku (VAT)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ATValueAddedTaxNumber` do `pii-categories` parametru. `ATValueAddedTaxNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Australii

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `AUDriversLicenseNumber` do `pii-categories` parametru. `AUDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australijski numer firmy

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `AUBusinessNumber` do `pii-categories` parametru. `AUBusinessNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer firmy w Australii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `AUCompanyNumber` do `pii-categories` parametru. `AUCompanyNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licencja na sterownik Australii  

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `AUDriversLicense` do `pii-categories` parametru. `AUDriversLicense` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer konta medycznego Australii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `AUMedicalAccountNumber` do `pii-categories` parametru. `AUMedicalAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Australii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ATPassportNumber` do `pii-categories` parametru. `ATPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer pliku podatku Australii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ATTaxIdentificationNumber` do `pii-categories` parametru. `ATTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belgia

:::row:::
    :::column span="":::
        **Jednostka**

        Belgijski numer krajowy

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `BENationalNumber` do `pii-categories` parametru. `BENationalNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgia Wartość dodana podatku (VAT)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `BEValueAddedTaxNumber` do `pii-categories` parametru. `BEValueAddedTaxNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brazylia 

:::row:::
    :::column span="":::
        **Jednostka**

        Numer podmiotu prawnego w Brazylii (numer CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `BRLegalEntityNumber` do `pii-categories` parametru. `BRLegalEntityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer numer CPF Brazylii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `BRCPFNumber` do `pii-categories` parametru. `BRCPFNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Krajowa karta IDENTYFIKACYJNa (RG)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `BRNationalIDRG` do `pii-categories` parametru. `BRNationalIDRG` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Kanady

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `CABankAccountNumber` do `pii-categories` parametru. `CABankAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji na sterownik Kanady

    :::column-end:::

    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `CADriversLicenseNumber` do `pii-categories` parametru. `CADriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer usługi kondycji Kanady

        
    :::column-end:::

    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `CAHealthServiceNumber` do `pii-categories` parametru. `CAHealthServiceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Kanady

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `CAPassportNumber` do `pii-categories` parametru. `CAPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Osobisty numer identyfikacyjny kondycji Kanady (PHIN)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `CAPersonalHealthIdentification` do `pii-categories` parametru. `CAPersonalHealthIdentification` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego w Kanadzie

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `CASocialInsuranceNumber` do `pii-categories` parametru. `CASocialInsuranceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Chile

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `CLIdentityCardNumber` do `pii-categories` parametru. `CLIdentityCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Chiny

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości rezydentnej Chin (ChRL)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `CNResidentIdentityCardNumber` do `pii-categories` parametru. `CNResidentIdentityCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Unia Europejska (UE)

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty debetowej UE

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `EUDebitCardNumber` do `pii-categories` parametru. `EUDebitCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji sterownika UE

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `EUDriversLicenseNumber` do `pii-categories` parametru. `EUDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Współrzędne procesora GPU UE

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `EUGPSCoordinates` do `pii-categories` parametru. `EUGPSCoordinates` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Krajowy numer identyfikacyjny UE

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `EUNationalIdentificationNumber` do `pii-categories` parametru. `EUNationalIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu UE

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `EUPassportNumber` do `pii-categories` parametru. `EUPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego (SSN) UE lub równoważny identyfikator

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `EUSocialSecurityNumber` do `pii-categories` parametru. `EUSocialSecurityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej UE (NIP)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `EUTaxIdentificationNumber` do `pii-categories` parametru. `EUTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Francja

:::row:::
    :::column span="":::
        **Jednostka**

        Numer licencji sterownika Francja

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `FRDriversLicenseNumber` do `pii-categories` parametru. `FRDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja — numer ubezpieczenia zdrowotnego

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `FRHealthInsuranceNumber` do `pii-categories` parametru. `FRHealthInsuranceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja National ID Card (CNI)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `FRNationalID` do `pii-categories` parametru. `FRNationalID` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Francji

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `FRPassportNumber` do `pii-categories` parametru. `FRPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja — numer ubezpieczenia społecznego (INSEE)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `FRSocialSecurityNumber` do `pii-categories` parametru. `FRSocialSecurityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francja — numer identyfikacji podatkowej (numéro WWH)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `FRTaxIdentificationNumber` do `pii-categories` parametru. `FRTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer NIP (Francja)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `FRValueAddedTaxNumber` do `pii-categories` parametru. `FRValueAddedTaxNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Niemcy

:::row:::
    :::column span="":::
        **Jednostka**

        Numer licencji sterownika niemieckiego

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `DEDriversLicenseNumber` do `pii-categories` parametru. `DEDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer karty tożsamości (Niemcy)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `DEIdentityCardNumber` do `pii-categories` parametru. `DEIdentityCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Niemiec

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `DEPassportNumber` do `pii-categories` parametru. `DEPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer NIP (Niemcy)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `DETaxIdentificationNumber` do `pii-categories` parametru. `DETaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer NIP dodany przez wartość Niemcy

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `DEValueAddedNumber` do `pii-categories` parametru. `DEValueAddedNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Hongkong (HKID)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `HKIdentityCardNumber` do `pii-categories` parametru. `HKIdentityCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Węgry

:::row:::
    :::column span="":::
        **Jednostka**

        Na Węgrzech osobisty numer identyfikacyjny

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `HUPersonalIdentificationNumber` do `pii-categories` parametru. `HUPersonalIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej na Węgrzech

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `HUTaxIdentificationNumber` do `pii-categories` parametru. `HUTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Wartość Węgier — dodany numer podatku

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `HUValueAddedNumber` do `pii-categories` parametru. `HUValueAddedNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Indie

:::row:::
    :::column span="":::
        **Jednostka**

        Numer stałego konta Indie (PAN)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `INPermanentAccount` do `pii-categories` parametru. `INPermanentAccount` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Liczba unikatowych identyfikatorów (AADHAAR) Indii

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `INUniqueIdentificationNumber` do `pii-categories` parametru. `INUniqueIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonezja

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Indonezja (KTP)

    :::column-end:::
    :::column span="2":::

        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `IDIdentityCardNumber` do `pii-categories` parametru. `IDIdentityCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlandia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer Personal Public Service (PPS)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `IEPersonalPublicServiceNumber` do `pii-categories` parametru. `IEPersonalPublicServiceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Irlandia — osobista usługa publiczna (PPS) numer v2

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `IEPersonalPublicServiceNumberV2` do `pii-categories` parametru. `IEPersonalPublicServiceNumberV2` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Izrael

:::row:::
    :::column span="":::
        **Jednostka**

        Krajowy identyfikator Izraela

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `ILNationalID` do `pii-categories` parametru. `ILNationalID` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer konta bankowego Izraela

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ILBankAccountNumber` do `pii-categories` parametru. `ILBankAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Włochy

:::row:::
    :::column span="":::
        **Jednostka**

        Identyfikator licencji sterownika Włochy

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `ITDriversLicenseNumber` do `pii-categories` parametru. `ITDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kod Obrachunkowy Włoch

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ITFiscalCode` do `pii-categories` parametru. `ITFiscalCode` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer podatkowy dodany do wartości Włoch

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ITValueAddedTaxNumber` do `pii-categories` parametru. `ITValueAddedTaxNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japonia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Japonii

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `JPBankAccountNumber` do `pii-categories` parametru. `JPBankAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji sterownika Japonii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `JPDriversLicenseNumber` do `pii-categories` parametru. `JPDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonia "My Number" (osobista)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `JPMyNumberPersonal` do `pii-categories` parametru. `JPMyNumberPersonal` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonia "My Number" (firmowa)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `JPMyNumberCorporate` do `pii-categories` parametru. `JPMyNumberCorporate` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer rejestracji rezydenta Japonia

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ITValueAddedTaxNumber` do `pii-categories` parametru. `ITValueAddedTaxNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer karty pobytu Japonia

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `JPResidenceCardNumber` do `pii-categories` parametru. `JPResidenceCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego (Japonia)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `JPSocialInsuranceNumber` do `pii-categories` parametru. `JPSocialInsuranceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu Japonia

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `JPPassportNumber` do `pii-categories` parametru. `JPPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luksemburg

:::row:::
    :::column span="":::
        **Jednostka**

        Narodowy numer identyfikacyjny (osoby naturalne)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `LUNationalIdentificationNumberNatural` do `pii-categories` parametru. `LUNationalIdentificationNumberNatural` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Krajowy numer identyfikacyjny w Luksemburgu (osoby niebędące osobami naturalnymi)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `LUNationalIdentificationNumberNonNatural` do `pii-categories` parametru. `LUNationalIdentificationNumberNonNatural` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty tożsamości Malty

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `MTIdentityCardNumber` do `pii-categories` parametru. `MTIdentityCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej Malty

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `MTTaxIDNumber` do `pii-categories` parametru. `MTTaxIDNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nowa Zelandia

:::row:::
    :::column span="":::
        **Jednostka**

        Numer konta bankowego Nowej Zelandii

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `NZBankAccountNumber` do `pii-categories` parametru. `NZBankAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer licencji sterownika Nowej Zelandii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `NZDriversLicenseNumber` do `pii-categories` parametru. `NZDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer przychodu w Nowej Zelandii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `NZInlandRevenueNumber` do `pii-categories` parametru. `NZInlandRevenueNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer Ministerstwa kondycji Nowej Zelandii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `NZMinistryOfHealthNumber` do `pii-categories` parametru. `NZMinistryOfHealthNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer opieki społecznej dla Nowej Zelandii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `NZSocialWelfareNumber` do `pii-categories` parametru. `NZSocialWelfareNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipiny

:::row:::
    :::column span="":::
        **Jednostka**

        Filipiny — ujednolicony numer IDENTYFIKACYJNy dla wielu przeznaczeniów

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `PHUnifiedMultiPurposeIDNumber` do `pii-categories` parametru. `PHUnifiedMultiPurposeIDNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugalia 

:::row:::
    :::column span="":::
        **Jednostka**

        Numer karty obywatela Portugalia

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `PTCitizenCardNumber` do `pii-categories` parametru. `PTCitizenCardNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer identyfikacji podatkowej Portugalii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `PTTaxIdentificationNumber` do `pii-categories` parametru. `PTTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Jednostka**

        Numer krajowej karty rejestracyjnej rejestracji (NRIC)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `PTTaxIdentificationNumber` do `pii-categories` parametru. `PTTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Republika Południowej Afryki

:::row:::
    :::column span="":::
        **Jednostka**

        Numer identyfikacyjny Republiki Południowej Afryki

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `ZAIdentificationNumber` do `pii-categories` parametru. `ZAIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Korea Południowa

:::row:::
    :::column span="":::
        **Jednostka**

        Numer rejestracji rezydenta Korea Południowa

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `KRResidentRegistrationNumber` do `pii-categories` parametru. `KRResidentRegistrationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Hiszpania

:::row:::
    :::column span="":::
        **Jednostka**

        Hiszpania — DNI

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `ESDNI` do `pii-categories` parametru. `ESDNI` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer ubezpieczenia społecznego (SSN) w Hiszpanii

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ESSocialSecurityNumber` do `pii-categories` parametru. `ESSocialSecurityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer identyfikacji podatkowej Hiszpania

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `ESTaxIdentificationNumber` do `pii-categories` parametru. `ESTaxIdentificationNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Szwajcaria

:::row:::
    :::column span="":::
        **Jednostka**

        Szwajcarski numer ubezpieczenia społecznego AHV

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `CHSocialSecurityNumber` do `pii-categories` parametru. `CHSocialSecurityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tajwan 

:::row:::
    :::column span="":::
        **Jednostka**

        Narodowy identyfikator Tajwanu

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `TWNationalID` do `pii-categories` parametru. `TWNationalID` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certyfikat rezydentny tajwański (łuk/TARC)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `TWResidentCertificate` do `pii-categories` parametru. `TWResidentCertificate` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numer paszportu tajwańskiego

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `TWPassportNumber` do `pii-categories` parametru. `TWPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Zjednoczone Królestwo

:::row:::
    :::column span="":::
        **Jednostka**

        kwart Numer licencji sterownika

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `UKDriversLicenseNumber` do `pii-categories` parametru. `UKDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       kwart Numer rzutowania wyborczyego

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `UKNationalInsuranceNumber` do `pii-categories` parametru. `UKNationalInsuranceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart Numer Narodowy Usługa kondycji (NHS)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `UKNationalHealthNumber` do `pii-categories` parametru. `UKNationalHealthNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart Krajowy numer ubezpieczenia (NINO)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `UKNationalInsuranceNumber` do `pii-categories` parametru. `UKNationalInsuranceNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart lub numer paszportu USA

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `USUKPassportNumber` do `pii-categories` parametru. `USUKPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       kwart Unikatowy numer referencyjny podatnika

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `UKUniqueTaxpayerNumber` do `pii-categories` parametru. `UKUniqueTaxpayerNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Stany Zjednoczone

:::row:::
    :::column span="":::
        **Jednostka**

        Numer ubezpieczenia społecznego USA (SSN)

    :::column-end:::
    :::column span="2":::
        **Szczegóły**

        Aby uzyskać tę kategorię jednostki, Dodaj `USSocialSecurityNumber` do `pii-categories` parametru. `USSocialSecurityNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::
      **Obsługiwane języki dokumentu**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer licencji sterownika USA

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `USDriversLicenseNumber` do `pii-categories` parametru. `USDriversLicenseNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Stany Zjednoczone i Zjednoczone Królestwo Numer paszportu

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `USUKPassportNumber` do `pii-categories` parametru. `USUKPassportNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer identyfikacji podatkowej w Stanach Zjednoczonych (ITIN)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `USIndividualTaxpayerIdentification` do `pii-categories` parametru. `USIndividualTaxpayerIdentification` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Liczba Stanów Zjednoczonych (DEA)

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `DrugEnforcementAgencyNumber` do `pii-categories` parametru. `DrugEnforcementAgencyNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numer konta bankowego USA

    :::column-end:::
    :::column span="2":::

        Aby uzyskać tę kategorię jednostki, Dodaj `USBankAccountNumber` do `pii-categories` parametru. `USBankAccountNumber` w razie wykrycia zostanie zwrócony w odpowiedzi interfejsu API.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
