---
title: Konfigurowanie zaświadczania platformy Azure za pomocą Azure Portal
description: Jak skonfigurować i skonfigurować dostawcę zaświadczania przy użyciu Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ab1e6011a1c127c9ac5a2c7652a4bf458372e1e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733941"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą Azure Portal

Postępuj zgodnie z poniższymi instrukcjami, aby zarządzać dostawcą zaświadczania przy użyciu Azure Portal.

## <a name="attestation-provider"></a>Dostawca zaświadczania

### <a name="create-an-attestation-provider"></a>Tworzenie dostawcy zaświadczania

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>Aby skonfigurować dostawcę z niepodpisanymi zasadami

1.  W menu Azure Portal lub na stronie głównej wybierz pozycję **Utwórz zasób**
2.  W polu wyszukiwania wprowadź **zaświadczenie**
3.  Z listy wyników wybierz pozycję **Zaświadczanie Microsoft Azure**
4.  Na stronie zaświadczanie Microsoft Azure wybierz pozycję **Utwórz** .
5.  Na stronie Tworzenie dostawcy zaświadczania podaj następujące dane wejściowe:
    
    **Subskrypcja**: wybierz subskrypcję
    
    **Grupa zasobów**: wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    
    **Nazwa**: wymagana jest unikatowa nazwa

    **Lokalizacja**: Wybierz lokalizację 
    
    **Plik certyfikatów podpisywania zasad**: nie przekazuj pliku certyfikatów osoby podpisującej zasady, aby skonfigurować dostawcę z niepodpisanymi zasadami 
6.  Po wprowadzeniu wymaganych danych wejściowych kliknij przycisk **Przegląd + Utwórz** .
7.  Usuń problemy z walidacją, jeśli istnieją, a następnie kliknij przycisk **Utwórz**.

#### <a name="to-configure-the-provider-with-signed-policies"></a>Aby skonfigurować dostawcę przy użyciu podpisanych zasad

1.  W menu Azure Portal lub na stronie głównej wybierz pozycję **Utwórz zasób**
2.  W polu wyszukiwania wprowadź **zaświadczenie**
3.  Z listy wyników wybierz pozycję **Zaświadczanie Microsoft Azure**
4.  Na stronie zaświadczanie Microsoft Azure wybierz pozycję **Utwórz** .
5.  Na stronie Tworzenie dostawcy zaświadczania podaj następujące informacje:
    
    a. **Subskrypcja**: wybierz subskrypcję
    
    b. **Grupa zasobów**: wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    
    c. **Nazwa**: wymagana jest unikatowa nazwa

    d. **Lokalizacja**: Wybierz lokalizację 
    
    e. **Plik certyfikatów podpisywania zasad**: Aby skonfigurować dostawcę zaświadczania przy użyciu certyfikatów podpisywania zasad, Przekaż plik certyfikatów. Zobacz przykłady w [tym miejscu](./policy-signer-examples.md) 
6.  Po wprowadzeniu wymaganych danych wejściowych kliknij przycisk **Przegląd + Utwórz** .
7.  Usuń problemy z walidacją, jeśli istnieją, a następnie kliknij przycisk **Utwórz**.

### <a name="view-attestation-provider"></a>Wyświetl dostawcę zaświadczania

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania i wybierz ją.

### <a name="delete-attestation-provider"></a>Usuń dostawcę zaświadczania

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Zaznacz pole wyboru i kliknij przycisk **Usuń** .
4.  Wpisz Yes i kliknij przycisk **delete** [lub]
1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij przycisk **Usuń** w górnym menu, a następnie kliknij przycisk **tak** .


## <a name="attestation-policy-signers"></a>Osoby podpisujące zasady zaświadczania

### <a name="view-policy-signer-certificates"></a>Wyświetlanie certyfikatów osoby podpisującej zasady

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij opcję **Certyfikaty osoby podpisującej zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij pozycję **Pobierz certyfikaty osoby podpisującej zasady** (przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad)
6.  Pobrany plik tekstowy będzie miał wszystkie certyfikaty w formacie JWS.
a.  Sprawdź, czy liczba certyfikatów i certyfikaty zostały pobrane.

### <a name="add-policy-signer-certificate"></a>Dodaj certyfikat osoby podpisującej zasady

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij opcję **Certyfikaty osoby podpisującej zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij przycisk **Dodaj** w górnym menu (przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad).
6.  Przekaż plik certyfikatu osoby podpisującej zasady, a następnie kliknij przycisk **Dodaj**. Zobacz przykłady w [tym miejscu](./policy-signer-examples.md)

### <a name="delete-policy-signer-certificate"></a>Usuń certyfikat podpisywania zasad

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij opcję **Certyfikaty osoby podpisującej zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij przycisk **Usuń** w górnym menu (przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad).
6.  Przekaż plik certyfikatu osoby podpisującej zasady i kliknij pozycję **Usuń**. Zobacz przykłady w [tym miejscu](./policy-signer-examples.md) 

## <a name="attestation-policy"></a>Zasady zaświadczania

### <a name="view-attestation-policy"></a>Wyświetl zasady zaświadczania

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij pozycję **zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Wybierz preferowany **Typ zaświadczania** i Wyświetl **bieżące zasady**

### <a name="configure-attestation-policy"></a>Konfigurowanie zasad zaświadczania

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Gdy dostawca zaświadczania jest tworzony bez wymagania dotyczącego podpisywania zasad

##### <a name="upload-policy-in-jwt-format"></a>Przekaż zasady w formacie JWT

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij pozycję **zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij pozycję **Konfiguruj** w górnym menu.
6.  Gdy dostawca zaświadczania zostanie utworzony bez wymagania dotyczącego podpisywania zasad, użytkownik może przekazać zasady w formacie **JWT** lub **tekstowym**
7.  Wybierz **Format zasad** jako **JWT**
8.  Przekaż plik zasad z zawartością zasad w formacie **JWT bez znaku** i kliknij przycisk **Zapisz**. Zobacz przykłady w [tym miejscu](./policy-examples.md)
    
    W przypadku opcji przekazywania plików Podgląd zasad będzie wyświetlany w formacie tekstowym i Podgląd zasad nie będzie można edytować.

7.  Kliknij przycisk **Odśwież** w górnym menu, aby wyświetlić skonfigurowane zasady.

##### <a name="upload-policy-in-text-format"></a>Przekaż zasady w formacie tekstowym

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij pozycję **zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij pozycję **Konfiguruj** w górnym menu.
6.  Gdy dostawca zaświadczania zostanie utworzony bez wymagania dotyczącego podpisywania zasad, użytkownik może przekazać zasady w formacie **JWT** lub **tekstowym**
7.  Wybierz **Format zasad** jako **tekst**
8.  Przekaż plik zasad z zawartością w formacie **tekstowym** lub wprowadź treść zasady w obszarze tekstu, a następnie kliknij przycisk **Zapisz**. Zobacz przykłady w [tym miejscu](./policy-examples.md)

    W przypadku opcji przekazywania plików Podgląd zasad będzie wyświetlany w formacie tekstowym i Podgląd zasad nie będzie można edytować.

8.  Kliknij przycisk **Odśwież** , aby wyświetlić skonfigurowane zasady

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Gdy dostawca zaświadczania jest tworzony przy użyciu wymagania dotyczącego podpisywania zasad

##### <a name="upload-policy-in-jwt-format"></a>Przekaż zasady w formacie JWT

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij pozycję **zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij pozycję **Konfiguruj** w górnym menu.
6.  Po utworzeniu dostawcy zaświadczania przy użyciu wymagania dotyczącego podpisywania zasad użytkownik może przekazać zasady tylko w **formacie ze znakiem JWT**
7.  Plik zasad przekazywania jest **podpisanym FORMATEM JWT** i kliknij przycisk **Zapisz**. Zobacz przykłady w [tym miejscu](./policy-examples.md)

    W przypadku opcji przekazywania plików Podgląd zasad będzie wyświetlany w formacie tekstowym i Podgląd zasad nie będzie można edytować.
    
8.  Kliknij przycisk **Odśwież** , aby wyświetlić skonfigurowane zasady

