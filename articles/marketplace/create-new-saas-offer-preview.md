---
title: Jak dodać odbiorcę w wersji zapoznawczej do oferty SaaS w komercyjnej witrynie Microsoft Marketplace
description: Jak dodać użytkowników wersji zapoznawczej do oferty oprogramowania jako usługi (SaaS) w centrum partnerskim firmy Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746424"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Jak dodać odbiorcę w wersji zapoznawczej dla oferty SaaS

W miarę tworzenia oferty oprogramowania jako usługi (SaaS) w centrum partnerskim należy zdefiniować odbiorców wersji zapoznawczej, którzy mogą przeglądać aukcję oferty przed jej rozpoczęciem. W tym artykule opisano sposób konfigurowania odbiorców w wersji zapoznawczej.

> [!NOTE]
> Jeśli zdecydujesz się na przetwarzanie transakcji niezależnie, ta opcja nie zostanie wyświetlona. Zamiast tego przejdź do [sposobu sprzedaży oferty SaaS](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Zdefiniuj odbiorcę w wersji zapoznawczej

Na karcie **odbiorca w wersji zapoznawczej** można zdefiniować ograniczonych odbiorców, którzy mogą zapoznać się z ofertą SaaSą przed opublikowaniem jej na żywo dla szerszego grona odbiorców w portalu Marketplace. Możesz wysyłać zaproszenia do adresów e-mail konta Microsoft (MSA) lub Azure Active Directory (Azure AD). Ręcznie Dodaj co najmniej jeden i maksymalnie 10 adresów e-mail lub zaimportuj do 20 za pomocą pliku CSV. W dowolnym momencie możesz zaktualizować listę odbiorców wersji zapoznawczej.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty przed opublikowaniem jej na żywo w sklepach online. Możesz również utworzyć plan i udostępnić go tylko dla odbiorców prywatnych. Plany prywatne zostały omówione w temacie [Tworzenie planów dla oferty SaaS](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>Ręczne dodawanie adresów e-mail

1. Na stronie **Podgląd odbiorców** Dodaj jeden adres E-mail usługi Azure AD lub MSA oraz opcjonalny opis w udostępnionych polach.
1. Aby dodać inny adres e-mail, wybierz łącze **Dodaj inne wiadomości e-mail** .
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: **konfiguracja techniczna**.
1. Kontynuuj, aby [dodać szczegóły techniczne dla oferty SaaS](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>Dodawanie adresów e-mail przy użyciu pliku CSV

1. Na stronie **Podgląd odbiorców** wybierz łącze **Eksportuj odbiorców (CSV)** .
1. Otwórz okno. Plik CSV w aplikacji, na przykład program Microsoft Excel.
1. W. Plik CSV, w kolumnie **Identyfikator** wprowadź adresy e-mail, które chcesz dodać do odbiorców w wersji zapoznawczej.
1. W kolumnie **Opis** możesz opcjonalnie dodać opis dla każdego adresu e-mail.
1. W kolumnie **Typ** Dodaj **MixedAadMsaId** do każdego wiersza, który ma adres e-mail.
1. Zapisz plik jako. Plik CSV.
1. Na stronie **Podgląd odbiorców** wybierz łącze **Importuj odbiorców (CSV)** .
1. W oknie dialogowym **potwierdzenia** wybierz pozycję **tak**.
1. Wybierz pozycję. Plik CSV, a następnie wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: **konfiguracja techniczna**.

## <a name="next-steps"></a>Następne kroki

- [Jak dodać szczegóły techniczne dla oferty SaaS](create-new-saas-offer-technical.md)
