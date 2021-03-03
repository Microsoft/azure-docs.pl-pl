---
title: 'Szybki Start: Konfigurowanie zaświadczania platformy Azure przy użyciu Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować i skonfigurować dostawcę zaświadczania przy użyciu Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729399"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Szybki Start: Konfigurowanie zaświadczania platformy Azure przy użyciu Azure Portal

Postępuj zgodnie z tym przewodnikiem Szybki Start, aby rozpocząć zaświadczanie platformy Azure. Dowiedz się, jak zarządzać dostawcą zaświadczania, podpisywaniem zasad i zasadami przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="attestation-provider"></a>Dostawca zaświadczania

W tej sekcji utworzysz dostawcę zaświadczania i skonfigurujesz go z niepodpisanymi zasadami lub podpisanymi zasadami. Dowiesz się również, jak wyświetlać i usuwać dostawcę zaświadczania.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Tworzenie i Konfigurowanie dostawcy z niepodpisanymi zasadami

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **Utwórz zasób**.
1. W polu wyszukiwania wprowadź **zaświadczenie**.
1. Na liście wyników wybierz pozycję **Zaświadczanie Microsoft Azure**.
1. Na stronie **Zaświadczanie Microsoft Azure** wybierz pozycję **Utwórz**.
1. Na stronie **Tworzenie dostawcy zaświadczania** podaj następujące dane wejściowe:

   - **Subskrypcja**: wybierz subskrypcję.
   - **Grupa zasobów**: wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
   - **Nazwa**: Wprowadź unikatową nazwę.
   - **Lokalizacja**: Wybierz lokalizację.
   - **Plik certyfikatów podpisywania zasad**: nie przekazuj pliku certyfikatów osoby podpisującej zasady, aby skonfigurować dostawcę z niepodpisanymi zasadami.

1. Po podaniu wymaganych danych wejściowych wybierz pozycję **Przegląd + Utwórz**.
1. Jeśli występują problemy z walidacją, usuń je, a następnie wybierz pozycję **Utwórz**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Utwórz i Skonfiguruj dostawcę z podpisanymi zasadami

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **Utwórz zasób**.
1. W polu wyszukiwania wprowadź **zaświadczenie**.
1. Na liście wyników wybierz pozycję **Zaświadczanie Microsoft Azure**.
1. Na stronie **Zaświadczanie Microsoft Azure** wybierz pozycję **Utwórz**.
1. Na stronie **Tworzenie dostawcy zaświadczania** podaj następujące informacje:

   - **Subskrypcja**: wybierz subskrypcję.
   - **Grupa zasobów**: wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
   - **Nazwa**: Wprowadź unikatową nazwę.
   - **Lokalizacja**: Wybierz lokalizację.
   - **Plik certyfikatów osoby podpisującej**: Przekaż plik certyfikatów osoby podpisującej zasady, aby skonfigurować dostawcę zaświadczania z podpisanymi zasadami. [Zobacz przykłady certyfikatów osoby podpisującej zasady](./policy-signer-examples.md).

1. Po podaniu wymaganych danych wejściowych wybierz pozycję **Przegląd + Utwórz**.
1. Jeśli występują problemy z walidacją, usuń je, a następnie wybierz pozycję **Utwórz**.

### <a name="view-the-attestation-provider"></a>Wyświetlanie dostawcy zaświadczania

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1. W polu Filtr wprowadź nazwę dostawcy zaświadczania i wybierz ją.

### <a name="delete-the-attestation-provider"></a>Usuwanie dostawcy zaświadczania

Istnieją dwa sposoby usunięcia dostawcy zaświadczania. Oto co możesz zrobić:

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1. W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1. Zaznacz pole wyboru i wybierz pozycję **Usuń**.
1. Wprowadź **wartość tak** i wybierz pozycję **Usuń**.

Możesz też:

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1. W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1. Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1. Na pasku menu wybierz pozycję **Usuń** , a następnie wybierz pozycję **tak**.

## <a name="attestation-policy-signers"></a>Osoby podpisujące zasady zaświadczania

Postępuj zgodnie z instrukcjami w tej sekcji, aby wyświetlać, dodawać i usuwać certyfikaty podpisywania zasad.

### <a name="view-the-policy-signer-certificates"></a>Wyświetlanie certyfikatów osoby podpisującej zasady

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1. W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1. Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1. Wybierz opcję **Certyfikaty osoby podpisującej zasady** w menu zasób po lewej stronie okna lub w dolnym okienku. Zostanie wyświetlony monit o wybranie certyfikatu do uwierzytelnienia. Wybierz odpowiednią opcję, aby wykonać operację.
1. Wybierz pozycję **Pobierz certyfikaty podpisywania zasad**. Przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad.
1. Pobrany plik tekstowy będzie miał wszystkie certyfikaty w formacie JWS.
1. Sprawdź liczbę certyfikatów i pobrane certyfikaty.

### <a name="add-the-policy-signer-certificate"></a>Dodawanie certyfikatu osoby podpisującej zasady

1.  Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1.  W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1.  Wybierz opcję **Certyfikaty osoby podpisującej zasady** w menu zasób po lewej stronie okna lub w dolnym okienku.
1.  Wybierz pozycję **Dodaj** w górnym menu. Przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad.
1.  Przekaż plik certyfikatu osoby podpisującej zasady i wybierz pozycję **Dodaj**. [Zobacz przykłady certyfikatów osoby podpisującej zasady](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Usuwanie certyfikatów osoby podpisującej zasady

1.  Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1.  W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1.  Wybierz opcję **Certyfikaty osoby podpisującej zasady** w menu zasób po lewej stronie okna lub w dolnym okienku.
1.  W górnym menu wybierz pozycję **Usuń** . Przycisk zostanie wyłączony dla dostawców zaświadczania utworzonych bez wymagania dotyczącego podpisywania zasad.
1.  Przekaż plik certyfikatu osoby podpisującej zasady i wybierz pozycję **Usuń**. [Zobacz przykłady certyfikatów osoby podpisującej zasady](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Zasady zaświadczania

W tej sekcji opisano sposób wyświetlania zasad zaświadczania i konfigurowania zasad, które zostały utworzone przy użyciu i bez wymagania dotyczącego podpisywania zasad.

### <a name="view-an-attestation-policy"></a>Wyświetlanie zasad zaświadczania

1.  Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1.  W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1.  Wybierz pozycję **zasady** w menu zasób po lewej stronie okna lub w dolnym okienku. Zostanie wyświetlony monit o wybranie certyfikatu do uwierzytelnienia. Wybierz odpowiednią opcję, aby wykonać operację.
1.  Wybierz preferowany **Typ zaświadczania** i Wyświetl **bieżące zasady**.

### <a name="configure-an-attestation-policy"></a>Konfigurowanie zasad zaświadczania

Wykonaj następujące kroki, aby przekazać zasady w formacie JWT lub tekstowym, jeśli dostawca zaświadczania został utworzony bez wymagania dotyczącego podpisywania zasad.

1. Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1. W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1. Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1. Wybierz pozycję **zasady** w menu zasób po lewej stronie okna lub w dolnym okienku.
1. Wybierz pozycję **Konfiguruj** w górnym menu.
1. Wybierz **Format zasad** jako **JWT** lub jako **tekst**.

   Jeśli dostawca zaświadczania został utworzony bez wymagania dotyczącego podpisywania zasad, użytkownik może przekazać zasady w formacie **JWT** lub **tekstowym** .

      - W przypadku wybrania opcji format tokenu JWT Przekaż plik zasad z zawartością zasad w formacie **niepodpisanego/niepodpisanego tokenu JWT** i wybierz pozycję **Zapisz**. [Zobacz przykłady zasad](./policy-examples.md).
      - W przypadku wybrania formatu tekstu Przekaż plik zasad z zawartością w formacie **tekstowym** lub wprowadź zawartość zasad w obszarze tekstowym, a następnie wybierz pozycję **Zapisz**. [Zobacz przykłady zasad](./policy-examples.md).

   W przypadku opcji przekazywanie plików Podgląd zasad jest wyświetlany w formacie tekstowym i nie można go edytować.

1. Wybierz pozycję **Odśwież** w górnym menu, aby wyświetlić skonfigurowane zasady.


Jeśli dostawca zaświadczania został utworzony przy użyciu wymagania dotyczącego podpisywania zasad, wykonaj następujące kroki, aby przekazać zasady w formacie tokenu JWT.

1.  Przejdź do menu Azure Portal lub strony głównej, a następnie wybierz pozycję **wszystkie zasoby**.
1.  W polu Filtr wprowadź nazwę dostawcy zaświadczania.
1.  Wybierz dostawcę zaświadczania i przejdź do strony przegląd.
1.  Wybierz pozycję **zasady** w menu zasób po lewej stronie okna lub w dolnym okienku.
1.  Wybierz pozycję **Konfiguruj** w górnym menu.
1.  Przekaż plik zasad w **formacie z podpisem JWT** i wybierz pozycję **Zapisz**. [Zobacz przykłady zasad](./policy-examples.md).

    Jeśli dostawca zaświadczania został utworzony przy użyciu wymagania dotyczącego podpisywania zasad, użytkownik może przekazać zasady tylko w **formacie podpisanego tokenu JWT**.

    W przypadku opcji przekazywanie plików Podgląd zasad jest wyświetlany w formacie tekstowym i nie można go edytować.
    
1.  Wybierz pozycję **Odśwież** , aby wyświetlić skonfigurowane zasady.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Zaświadcz SGX enklawy przy użyciu przykładów kodu](/samples/browse/?expanded=azure&terms=attestation)

