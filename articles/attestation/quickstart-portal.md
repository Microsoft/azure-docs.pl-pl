---
title: Konfigurowanie zaświadczania platformy Azure za pomocą Azure Portal
description: Jak skonfigurować i skonfigurować dostawcę zaświadczania przy użyciu Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 86adac557c6de133e95e97bfedbd302cc6a2b27e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429161"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą Azure Portal

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Postępuj zgodnie z poniższymi instrukcjami, aby zarządzać dostawcą zaświadczania przy użyciu Azure Portal.

## <a name="1-attestation-provider"></a>1. dostawca zaświadczania

### <a name="11-create-an-attestation-provider"></a>1,1 Tworzenie dostawcy zaświadczania

#### <a name="111-to-configure-the-provider-with-unsigned-policies"></a>1.1.1 skonfigurowanie dostawcy z niepodpisanymi zasadami

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

#### <a name="112-to-configure-the-provider-with-signed-policies"></a>1.1.2 Aby skonfigurować dostawcę z podpisanymi zasadami

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

### <a name="12-view-attestation-provider"></a>1,2 Wyświetl dostawcę zaświadczania

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania i wybierz ją.

### <a name="13-delete-attestation-provider"></a>1,3 Usuwanie dostawcy zaświadczania

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Zaznacz pole wyboru i kliknij przycisk **Usuń** .
4.  Wpisz Yes i kliknij przycisk **delete** [lub]
1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij przycisk **Usuń** w górnym menu, a następnie kliknij przycisk **tak** .


## <a name="2-attestation-policy-signers"></a>2. osoby podpisujące zasady zaświadczania

### <a name="21-view-policy-signer-certificates"></a>2,1 Wyświetlanie certyfikatów osoby podpisującej zasady

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij opcję **Certyfikaty osoby podpisującej zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij pozycję **Pobierz certyfikaty osoby podpisującej zasady** (przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad)
6.  Pobrany plik tekstowy będzie miał wszystkie certyfikaty w formacie JWS.
a.  Sprawdź, czy liczba certyfikatów i certyfikaty zostały pobrane.

### <a name="22-add-policy-signer-certificate"></a>2,2 Dodawanie certyfikatu osoby podpisującej zasady

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij opcję **Certyfikaty osoby podpisującej zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij przycisk **Dodaj** w górnym menu (przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad).
6.  Przekaż plik certyfikatu osoby podpisującej zasady, a następnie kliknij przycisk **Dodaj**. Zobacz przykłady w [tym miejscu](./policy-signer-examples.md)

### <a name="23-delete-policy-signer-certificate"></a>2,3 Usuwanie certyfikatu podpisywania zasad

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij opcję **Certyfikaty osoby podpisującej zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Kliknij przycisk **Usuń** w górnym menu (przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad).
6.  Przekaż plik certyfikatu osoby podpisującej zasady i kliknij pozycję **Usuń**. Zobacz przykłady w [tym miejscu](./policy-signer-examples.md) 

## <a name="3-attestation-policy"></a>3. zaświadczanie zasad

### <a name="31-view-attestation-policy"></a>3,1 Wyświetl zasady zaświadczania

1.  Z menu Azure Portal lub ze strony głównej wybierz pozycję **wszystkie zasoby**
2.  W polu Filtr wprowadź nazwę dostawcy zaświadczania
3.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd
4.  Kliknij pozycję **zasady** w menu zasobów po lewej stronie lub w dolnym okienku.
5.  Wybierz preferowany **Typ zaświadczania** i Wyświetl **bieżące zasady**

### <a name="32-configure-attestation-policy"></a>3,2 Konfigurowanie zasad zaświadczania

#### <a name="321-when-attestation-provider-is-created-without-policy-signing-requirement"></a>3.2.1 gdy dostawca zaświadczania został utworzony bez wymagania dotyczącego podpisywania zasad

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

#### <a name="322-when-attestation-provider-is-created-with-policy-signing-requirement"></a>3.2.2 gdy dostawca zaświadczania został utworzony przy użyciu wymagania dotyczącego podpisywania zasad

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

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Zaświadcz SGX enklawy przy użyciu przykładów kodu](/samples/browse/?expanded=azure&terms=attestation)

