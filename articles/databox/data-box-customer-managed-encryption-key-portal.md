---
title: Użyj Azure Portal do zarządzania kluczami zarządzanymi przez klienta dla Azure Data Box
description: Dowiedz się, jak za pomocą usługi Azure Portal tworzyć klucze zarządzane przez klienta i zarządzać nimi za pomocą Azure Key Vault dla Azure Data Box. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: f75907dc1fa079cebb3b80874090c658fd7b8174
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302826"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Użyj kluczy zarządzanych przez klienta w Azure Key Vault Azure Data Box

Azure Data Box chroni klucz odblokowywania urządzenia (znany również jako hasło urządzenia), który jest używany do blokowania urządzenia przy użyciu klucza szyfrowania. Domyślnie ten klucz szyfrowania jest kluczem zarządzanym firmy Microsoft. Aby uzyskać dodatkową kontrolę, można użyć klucza zarządzanego przez klienta.

Użycie klucza zarządzanego przez klienta nie ma wpływu na sposób szyfrowania danych na urządzeniu. Ma to wpływ tylko na to, jak klucz odblokowywania urządzenia jest szyfrowany.

Aby zachować ten poziom kontroli w całym procesie zamówienia, podczas tworzenia zamówienia Użyj klucza zarządzanego przez klienta. Aby uzyskać więcej informacji, zobacz [Samouczek: Order Azure Data Box](data-box-deploy-ordered.md).

W tym artykule pokazano, jak włączyć klucz zarządzany przez klienta dla istniejącego zamówienia urządzenie Data Box w [Azure Portal](https://portal.azure.com/). Dowiesz się, jak zmienić Magazyn kluczy, klucz, wersję lub tożsamość dla bieżącego klucza zarządzanego przez klienta lub przełączyć się z powrotem do korzystania z klucza zarządzanego przez firmę Microsoft.

Ten artykuł ma zastosowanie do Azure Data Box i Azure Data Box Heavy urządzeń.

## <a name="requirements"></a>Wymagania

Klucz zarządzany przez klienta dla zamówienia urządzenie Data Box musi spełniać następujące wymagania:

- Klucz musi zostać utworzony i zapisany w Azure Key Vault, który ma **nietrwałe usuwanie** i **nie można go** włączyć. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md). Możesz utworzyć magazyn kluczy i klucz podczas tworzenia lub aktualizowania zamówienia.

- Klucz musi być kluczem RSA o rozmiarze 2048 lub większym.

## <a name="enable-key"></a>Włącz klucz

Aby włączyć klucz zarządzany przez klienta dla istniejącego zamówienia urządzenie Data Box w Azure Portal, wykonaj następujące kroki:

1. Przejdź do ekranu **Przegląd** dla zamówienia urządzenie Data Box.

    ![Ekran przeglądu urządzenie Data Box Order-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Przejdź do pozycji **ustawienia > szyfrowanie** i wybierz pozycję **klucz zarządzany przez klienta**. Następnie wybierz pozycję **Wybierz klucz i Magazyn kluczy**.

    ![Wybierz opcję szyfrowania klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Na ekranie **Wybieranie klucza z Azure Key Vault** subskrypcja zostanie automatycznie wypełniona.

 3. W przypadku **magazynu kluczy** można wybrać istniejący magazyn kluczy z listy rozwijanej lub wybrać pozycję **Utwórz nowy** i utworzyć nowy magazyn kluczy.

     ![Opcje magazynu kluczy podczas wybierania klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Aby utworzyć nowy magazyn kluczy, wprowadź subskrypcję, grupę zasobów, nazwę magazynu kluczy i inne informacje na ekranie **Tworzenie nowego magazynu kluczy** . W obszarze **Opcje odzyskiwania** upewnij się, że ochrona **usuwania nietrwałego** i **przeczyszczania** jest włączona. Następnie wybierz pozycję **Przegląd + Utwórz**.

      ![Przeglądanie i tworzenie Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Zapoznaj się z informacjami dotyczącymi magazynu kluczy, a następnie wybierz pozycję **Utwórz**. Poczekaj kilka minut na ukończenie tworzenia magazynu kluczy.

       ![Tworzenie Azure Key Vault przy użyciu ustawień](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Na ekranie **Wybieranie klucza z Azure Key Vault** można wybrać istniejący klucz z magazynu kluczy lub utworzyć nowy.

    ![Wybierz klucz z Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Jeśli chcesz utworzyć nowy klucz, wybierz pozycję **Utwórz nowy**. Musisz użyć klucza RSA. Rozmiar może wynosić 2048 lub więcej.

    ![Utwórz nowy klucz w Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Wprowadź nazwę nowego klucza, zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**. Zostanie wyświetlony monit o utworzenie klucza w magazynie kluczy.

    ![Nazwa nowego klucza](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. W przypadku **wersji** można wybrać istniejącą wersję klucza z listy rozwijanej.

    ![Wybierz wersję dla nowego klucza](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Jeśli chcesz wygenerować nową wersję klucza, wybierz pozycję **Utwórz nową**.

    ![Otwieranie okna dialogowego służącego do tworzenia nowej wersji klucza](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Wybierz opcję Ustawienia dla nowej wersji klucza i wybierz pozycję **Utwórz**.

    ![Utwórz nową wersję klucza](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Po wybraniu magazynu kluczy, klucza i wersji klucza wybierz **pozycję Wybierz**.

    ![Klucz w Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    Ustawienia **typu szyfrowania** pokazują wybrany magazyn kluczy i klucz.

    ![Klucz i Magazyn kluczy dla klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Wybierz typ tożsamości, która ma być używana do zarządzania kluczem zarządzanym przez klienta dla tego zasobu. Możesz użyć **przypisanej do systemu** tożsamości, która została wygenerowana podczas tworzenia zamówienia, lub wybrać tożsamość przypisaną przez użytkownika.

    Tożsamość przypisana przez użytkownika jest niezależnym zasobem, którego można używać do zarządzania dostępem do zasobów. Aby uzyskać więcej informacji, zobacz [zarządzane typy tożsamości](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Wybierz typ tożsamości](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Aby przypisać tożsamość użytkownika, wybierz pozycję **przypisano użytkownika**. Następnie wybierz pozycję **Wybierz tożsamość użytkownika**, a następnie wybierz zarządzaną tożsamość, która ma być używana.

    ![Wybierz tożsamość do użycia](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    W tym miejscu nie można utworzyć nowej tożsamości użytkownika. Aby dowiedzieć się, jak ją utworzyć, zobacz [Tworzenie, wyświetlanie, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

    Wybrana tożsamość użytkownika jest wyświetlana w ustawieniach **typu szyfrowania** .

    ![Wybrana tożsamość użytkownika pokazana w ustawieniach typu szyfrowania](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowane ustawienia **typu szyfrowania** .

     ![Zapisz klucz zarządzany przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Adres URL klucza jest wyświetlany w obszarze **typ szyfrowania**.

    ![Adres URL klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Zmień klucz

Aby zmienić Magazyn kluczy, klucz i/lub wersję klucza dla aktualnie używanego klucza zarządzanego przez klienta, wykonaj następujące kroki:

1. Na ekranie **Omówienie** kolejności urządzenie Data Box przejdź do pozycji **Ustawienia**  >  **szyfrowanie**, a następnie kliknij przycisk **Zmień klucz**.

    ![Ekran omówienia urządzenie Data Box kolejności z kluczem zarządzanym przez klienta — 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Wybierz **pozycję Wybierz inny magazyn kluczy i klucz**.

    ![Ekran przeglądu kolejności urządzenie Data Box, wybierania innego klucza i opcji magazynu kluczy](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. Na ekranie **Wybieranie klucza z magazynu kluczy** jest wyświetlana subskrypcja, ale nie jest to magazyn kluczy, klucz lub wersja klucza. Można wprowadzić dowolne z następujących zmian:

   - Wybierz inny klucz z tego samego magazynu kluczy. Przed wybraniem klucza i wersji należy wybrać Magazyn kluczy.

   - Wybierz inny magazyn kluczy i przypisz nowy klucz.

   - Zmień wersję bieżącego klucza.
   
    Po zakończeniu wprowadzania zmian wybierz **pozycję Wybierz**.

    ![Wybierz opcję szyfrowania-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Wybierz pozycję **Zapisz**.

    ![Zapisz zaktualizowane ustawienia szyfrowania-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Zmień tożsamość

Aby zmienić tożsamość używaną do zarządzania dostępem do klucza zarządzanego przez klienta w ramach tej kolejności, wykonaj następujące kroki:

1. Na ekranie **Przegląd** dla ukończonej kolejności urządzenie Data Box przejdź do pozycji **Ustawienia**  >  **szyfrowanie**.

2. Wprowadź jedną z następujących zmian:

     - Aby zmienić tożsamość użytkownika na inną, kliknij pozycję **Wybierz inną tożsamość użytkownika**. Następnie wybierz inną tożsamość w panelu po prawej stronie ekranu, a następnie wybierz **pozycję Wybierz**.

       ![Opcja zmiany tożsamości przypisanej przez użytkownika dla klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Aby przełączyć się do tożsamości przypisanej do systemu wygenerowanej podczas tworzenia zamówienia, wybierz pozycję **system przypisany** przez **Wybieranie typu tożsamości**.

     ![Opcja zmiany na system przypisany do klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Wybierz pozycję **Zapisz**.

    ![Zapisz zaktualizowane ustawienia szyfrowania-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Użyj klucza zarządzanego firmy Microsoft

Aby zmienić użycie klucza zarządzanego przez klienta na klucz zarządzany przez firmę Microsoft dla zamówienia, wykonaj następujące kroki:

1. Na ekranie **Przegląd** dla ukończonej kolejności urządzenie Data Box przejdź do pozycji **Ustawienia**  >  **szyfrowanie**.

2. Wybierz pozycję **Typ**, a następnie wybierz opcję **klucz zarządzany przez firmę Microsoft**.

    ![Ekran przeglądu urządzenie Data Box Order-5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Wybierz pozycję **Zapisz**.

    ![Zapisz zaktualizowane ustawienia szyfrowania dla klucza zarządzanego przez firmę Microsoft](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Rozwiązywanie problemów

Jeśli otrzymasz jakiekolwiek błędy związane z kluczem zarządzanym przez klienta, Skorzystaj z poniższej tabeli, aby rozwiązać problem.

| Kod błędu| Szczegóły błędu| Odzyskiwaln?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Nie można pobrać klucza dostępu, ponieważ klucz zarządzany przez klienta jest wyłączony.| Tak, włączając wersję klucza.|
| SsemUserErrorEncryptionKeyExpired| Nie można pobrać klucza dostępu, ponieważ klucz zarządzany przez klienta wygasł.| Tak, włączając wersję klucza.|
| SsemUserErrorKeyDetailsNotFound| Nie można pobrać klucza dostępu, ponieważ nie można odnaleźć klucza zarządzanego przez klienta.| Jeśli magazyn kluczy został usunięty, nie można odzyskać klucza zarządzanego przez klienta.  Jeśli przeprowadzono migrację magazynu kluczy do innej dzierżawy, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../key-vault/general/move-subscription.md). Jeśli magazyn kluczy został usunięty:<ol><li>Tak, jeśli jest to czas trwania funkcji przeczyszczania i ochrony, wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nie, jeśli przekracza czas trwania ochrony przed usunięciem.</li></ol><br>W przeciwnym razie, jeśli Magazyn kluczy przeszedł do migracji dzierżawy, można go odzyskać, wykonując jedną z poniższych czynności: <ol><li>Przywróć Magazyn kluczy z powrotem do starej dzierżawy.</li><li>Ustaw `Identity = None` , a następnie ustaw wartość z powrotem na `Identity = SystemAssigned` . Spowoduje to usunięcie i odtworzenie tożsamości po utworzeniu nowej tożsamości. Włącz `Get` , `Wrap` i `Unwrap` uprawnienia do nowej tożsamości w zasadach dostępu magazynu kluczy.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Zastosowano klucz zarządzany przez klienta, ale dostęp do klucza nie został udzielony lub został odwołany lub nie można uzyskać dostępu do magazynu kluczy ze względu na włączenie zapory. | Dodaj tożsamość wybraną do magazynu kluczy, aby umożliwić dostęp do klucza zarządzanego przez klienta. Jeśli w magazynie kluczy jest włączona Zapora, przełącz się na tożsamość przypisaną do systemu, a następnie Dodaj klucz zarządzany przez klienta. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Nie można pobrać klucza dostępu, ponieważ nie można znaleźć skojarzonego magazynu kluczy dla klucza zarządzanego przez klienta. | Jeśli magazyn kluczy został usunięty, nie można odzyskać klucza zarządzanego przez klienta.  Jeśli przeprowadzono migrację magazynu kluczy do innej dzierżawy, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../key-vault/general/move-subscription.md). Jeśli magazyn kluczy został usunięty:<ol><li>Tak, jeśli jest to czas trwania funkcji przeczyszczania i ochrony, wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nie, jeśli przekracza czas trwania ochrony przed usunięciem.</li></ol><br>W przeciwnym razie, jeśli Magazyn kluczy przeszedł do migracji dzierżawy, można go odzyskać, wykonując jedną z poniższych czynności: <ol><li>Przywróć Magazyn kluczy z powrotem do starej dzierżawy.</li><li>Ustaw `Identity = None` , a następnie ustaw wartość z powrotem na `Identity = SystemAssigned` . Spowoduje to usunięcie i odtworzenie tożsamości po utworzeniu nowej tożsamości. Włącz `Get` , `Wrap` i `Unwrap` uprawnienia do nowej tożsamości w zasadach dostępu magazynu kluczy.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Nie można pobrać klucza dostępu, ponieważ nie można odnaleźć klucza zarządzanego przez klienta.| Tak, sprawdź, czy: <ol><li>Magazyn kluczy ma nadal plik MSI w zasadach dostępu.</li><li>Tożsamość jest przypisana do systemu typu.</li><li>Włącz uprawnienia Get, zawijania i depakowania do tożsamości w zasadach dostępu magazynu kluczy.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Dodawanie nowej tożsamości przypisanej przez użytkownika nie powiodło się, ponieważ osiągnięto limit łącznej liczby tożsamości przypisanych do użytkownika, które można dodać. | Spróbuj ponownie wykonać operację, podając mniejszą liczbę tożsamości użytkowników, lub usuń niektóre tożsamości przypisane przez użytkownika z zasobu przed ponowną próbą. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Operacja zarządzanego dostępu do tożsamości nie powiodła się. <br> Uwaga: dotyczy to scenariusza, w którym subskrypcja została przeniesiona do innej dzierżawy. Klient musi ręcznie przenieść tożsamość do nowej dzierżawy. PFA pocztą e-mail, aby uzyskać więcej szczegółów. | Przenieś wybraną tożsamość do nowej dzierżawy, w której znajduje się subskrypcja. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | Zastosowano klucz zarządzany przez klienta, ale w usłudze Active Directory nie znaleziono tożsamości przypisanej przez użytkownika, która ma dostęp do klucza. <br> Uwaga: dotyczy to sytuacji, gdy tożsamość użytkownika jest usuwana z platformy Azure.| Spróbuj dodać inną tożsamość przypisanego użytkownika do magazynu kluczy, aby umożliwić dostęp do klucza zarządzanego przez klienta. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Nie można pobrać klucza dostępu, ponieważ nie można odnaleźć klucza zarządzanego przez klienta. | Nie można uzyskać dostępu do klucza zarządzanego przez klienta. Tożsamość przypisanych przez użytkownika (UAI) skojarzona z kluczem została usunięta lub typ UAI został zmieniony. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Operacja zarządzanego dostępu do tożsamości nie powiodła się. <br> Uwaga: dotyczy to scenariusza, w którym subskrypcja została przeniesiona do innej dzierżawy. Klient musi ręcznie przenieść tożsamość do nowej dzierżawy. PFA pocztą e-mail, aby uzyskać więcej szczegółów. | Spróbuj dodać inną tożsamość przypisanego użytkownika do magazynu kluczy, aby umożliwić dostęp do klucza zarządzanego przez klienta. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Zastosowano klucz zarządzany przez klienta, ale dostęp do klucza nie został udzielony lub został odwołany lub nie można uzyskać dostępu do magazynu kluczy ze względu na włączenie zapory. | Dodaj tożsamość wybraną do magazynu kluczy, aby umożliwić dostęp do klucza zarządzanego przez klienta. Jeśli w magazynie kluczy jest włączona Zapora, przełącz się na tożsamość przypisaną do systemu, a następnie Dodaj klucz zarządzany przez klienta. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](#enable-key). |
| Błąd rodzajowy  | Nie można pobrać klucza dostępu.| Jest to błąd ogólny. Skontaktuj się pomoc techniczna firmy Microsoft, aby rozwiązać problem z błędem i określić następne kroki.|

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
- [Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Key Vault przy użyciu witryny Azure Portal](../key-vault/secrets/quick-create-portal.md)
