---
title: Korzystanie z własnych certyfikatów przy użyciu urządzeń Azure Data Box/Azure Data Box Heavy
description: Jak korzystać z własnych certyfikatów do uzyskiwania dostępu do lokalnego interfejsu użytkownika sieci Web i magazynu blogów na urządzeniu urządzenie Data Box lub Data Box Heavy.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/08/2020
ms.author: alkohli
ms.openlocfilehash: dab34b26d8237d743e22149ed0da2dd9471d7431
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096105"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Korzystanie z własnych certyfikatów przy użyciu urządzeń urządzenie Data Box i Data Box Heavy

Podczas przetwarzania zamówienia certyfikaty z podpisem własnym są generowane w celu uzyskania dostępu do lokalnego interfejsu użytkownika sieci Web i usługi BLOB Storage dla urządzenie Data Box lub Data Box Heavy urządzenia. Jeśli wolisz, aby komunikować się z urządzeniem za pośrednictwem zaufanego kanału, możesz użyć własnych certyfikatów.

W tym artykule opisano sposób instalowania własnych certyfikatów oraz przywracania domyślnych certyfikatów przed zwróceniem urządzenia do centrum danych. Zawiera również Podsumowanie wymagań dotyczących certyfikatów.

## <a name="about-certificates"></a>Informacje o certyfikatach

Certyfikat zawiera łącze między **kluczem publicznym** i jednostką (taką jak nazwa domeny), która została **podpisana** (zweryfikowana) przez zaufaną stronę trzecią, taką jak **urząd certyfikacji**.  Certyfikat zapewnia wygodny sposób dystrybucji zaufanych publicznych kluczy szyfrowania. W ten sposób certyfikaty zapewniają, że komunikacja jest zaufana, a dane zaszyfrowane są wysyłane na właściwy serwer.

Po wstępnym skonfigurowaniu urządzenia urządzenie Data Box certyfikaty z podpisem własnym są generowane automatycznie. Opcjonalnie możesz przenieść własne certyfikaty. Istnieją wskazówki, które należy wykonać, jeśli planujesz przeprowadzenie własnych certyfikatów.

Na urządzeniu urządzenie Data Box lub Data Box Heavy są używane dwa typy certyfikatów punktów końcowych:

- Certyfikat magazynu obiektów BLOB
- Certyfikat lokalnego interfejsu użytkownika

### <a name="certificate-requirements"></a>Wymagania certyfikatu

Certyfikaty muszą spełniać następujące wymagania:

- Certyfikat punktu końcowego musi być w `.pfx` formacie z kluczem prywatnym, który można wyeksportować.
- Można użyć pojedynczego certyfikatu dla każdego punktu końcowego, certyfikatu wielodomenowego dla wielu punktów końcowych lub certyfikatu wieloznacznego punktu końcowego.
- Właściwości certyfikatu punktu końcowego są podobne do właściwości typowego certyfikatu SSL.
- Odpowiedni certyfikat w formacie DER ( `.cer` rozszerzenie nazwy pliku) jest wymagany na komputerze klienckim.
- Po przekazaniu certyfikatu lokalnego interfejsu użytkownika należy ponownie uruchomić przeglądarkę i wyczyścić pamięć podręczną. Zapoznaj się z określonymi instrukcjami dla przeglądarki.
- Certyfikaty należy zmienić w przypadku zmiany nazwy urządzenia lub nazwy domeny DNS.
- Podczas tworzenia certyfikatów punktów końcowych należy skorzystać z następującej tabeli:

  |Typ |Nazwa podmiotu (SN)  |Alternatywna nazwa podmiotu (SAN)  |Przykład nazwy podmiotu |
  |---------|---------|---------|---------|
  |Lokalny interfejs użytkownika| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob Storage|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Pojedynczy certyfikat w sieci SAN|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące certyfikatów](../../articles/databox-online/azure-stack-edge-j-series-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Dodawanie certyfikatów do urządzenia

Możesz użyć własnych certyfikatów do uzyskiwania dostępu do lokalnego interfejsu użytkownika sieci Web i uzyskiwania dostępu do magazynu obiektów BLOB.

> [!IMPORTANT]
> W przypadku zmiany nazwy urządzenia lub domeny DNS należy utworzyć nowe certyfikaty. Certyfikaty klienta i certyfikaty urządzeń należy zaktualizować przy użyciu nowej nazwy urządzenia i domeny DNS.

Aby dodać własny certyfikat do urządzenia, wykonaj następujące kroki:

1. Przejdź do obszaru **Zarządzanie**  >  **certyfikatami**.

   **Nazwa** zawiera nazwę urządzenia. **Domena DNS** pokazuje nazwę domeny dla serwera DNS.

   Dolna część ekranu pokazuje certyfikaty, które są obecnie używane. Dla nowego urządzenia zobaczysz certyfikaty z podpisem własnym, które zostały wygenerowane podczas przetwarzania zamówienia.

   ![Strona certyfikaty dla urządzenia urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Jeśli musisz zmienić **nazwę** (nazwę urządzenia) lub **domenę DNS** (domenę serwera DNS dla urządzenia), zrób to teraz przed dodaniem certyfikatu. Następnie wybierz przycisk **Zastosuj**.

   Certyfikat należy zmienić w przypadku zmiany nazwy urządzenia lub nazwy domeny DNS.

   ![Zastosuj nową nazwę urządzenia i domenę DNS dla urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Aby dodać certyfikat, wybierz pozycję **Dodaj certyfikat** , aby otworzyć panel **Dodawanie certyfikatu** . Następnie wybierz **Typ certyfikatu** — **Magazyn obiektów BLOB** lub **lokalny interfejs użytkownika sieci Web**.

   ![Panel dodawania certyfikatów na stronie certyfikaty dla urządzenia urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Wybierz plik certyfikatu (w `.pfx` formacie) i wprowadź hasło, które zostało ustawione podczas eksportowania certyfikatu. Następnie wybierz pozycję **weryfikuj & Dodaj**.

   ![Ustawienia dodawania certyfikatu punktu końcowego obiektu BLOB do urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Po pomyślnym dodaniu certyfikatu na ekranie **Certyfikaty** zostanie wyświetlony odcisk palca dla nowego certyfikatu. Stan certyfikatu jest **prawidłowy**.

   ![Prawidłowy nowy certyfikat, który został pomyślnie dodany](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Aby wyświetlić szczegóły certyfikatu, wybierz i kliknij nazwę certyfikatu. Certyfikat wygaśnie po roku.

   ![Wyświetlanie szczegółów certyfikatu dla urządzenia urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Zainstaluj nowy certyfikat na komputerze klienckim, którego używasz, aby uzyskać dostęp do lokalnego interfejsu użytkownika sieci Web. Aby uzyskać instrukcje, zobacz [Importowanie certyfikatów do klienta](#import-certificates-to-client)poniżej.

7. Jeśli zmieniono certyfikat dla lokalnego interfejsu użytkownika sieci Web, należy ponownie uruchomić przeglądarkę, a następnie lokalny interfejs użytkownika sieci Web. Ten krok jest niezbędny, aby uniknąć problemów z pamięcią podręczną SSL.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

## <a name="import-certificates-to-client"></a>Importowanie certyfikatów do klienta

Po dodaniu certyfikatu do urządzenia urządzenie Data Box należy zaimportować certyfikat do komputera klienckiego, który jest używany w celu uzyskania dostępu do urządzenia. Certyfikat zostanie zaimportowany do magazynu zaufanych głównych urzędów certyfikacji dla komputera lokalnego.

Aby zaimportować certyfikat na kliencie systemu Windows, wykonaj następujące kroki:

1. W Eksploratorze plików kliknij prawym przyciskiem myszy plik certyfikatu (z `.cer` formatowaniem), a następnie wybierz pozycję **Zainstaluj certyfikat**. Ta akcja powoduje uruchomienie Kreatora importowania certyfikatów.

    ![Importuj certyfikat 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. W polu **Lokalizacja magazynu** wybierz pozycję **komputer lokalny**, a następnie wybierz pozycję **dalej**.

    ![Wybierz maszynę lokalną jako lokalizację magazynu w Kreatorze importu certyfikatów](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, wybierz pozycję **zaufany główny urząd certyfikacji**, a następnie wybierz przycisk **dalej**.

   ![Wybierz magazyn zaufanych głównych urzędów certyfikacji w Kreatorze importu certyfikatów](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ**. Zostanie wyświetlony komunikat informujący o tym, że importowanie zakończyło się pomyślnie.

   ![Przejrzyj ustawienia certyfikatu i Zakończ pracę Kreatora importu certyfikatów](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Przywróć domyślne certyfikaty

Przed zwróceniem urządzenia do centrum danych platformy Azure należy przywrócić oryginalne certyfikaty, które zostały wygenerowane podczas przetwarzania zamówienia.

Aby przywrócić certyfikaty wygenerowane podczas przetwarzania zamówienia, wykonaj następujące kroki:

1. Przejdź do obszaru **Zarządzanie**  >  **certyfikatami** i wybierz pozycję **Przywróć certyfikaty**.

   Przywrócenie certyfikatów zwraca do korzystania z certyfikatów z podpisem własnym, które zostały wygenerowane podczas przetwarzania zamówienia. Twoje własne certyfikaty są usuwane z urządzenia.

   ![Opcja Odwróć certyfikaty w temacie Zarządzanie certyfikatami dla urządzenia urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Po pomyślnym zakończeniu ponownej wersji certyfikatu przejdź do pozycji **Zamknij lub Uruchom ponownie**, a następnie wybierz pozycję **Uruchom ponownie**. Ten krok jest niezbędny, aby uniknąć problemów z pamięcią podręczną SSL.

   ![Zamykanie lub ponowne uruchamianie lokalnego interfejsu użytkownika sieci Web po przywróceniu certyfikatów na urządzeniu urządzenie Data Box](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Poczekaj kilka minut, a następnie zaloguj się ponownie do lokalnego interfejsu użytkownika sieci Web.
