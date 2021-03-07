---
title: Korzystanie z certyfikatów w programie Azure Stack EDGE Pro GPU | Microsoft Docs
description: W tym artykule opisano sposób korzystania z certyfikatów z urządzeniami wieloprocesorowymi w systemie Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4193e58c28b481297df38bca8f18d2ea766ce886
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443135"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Używanie certyfikatów z urządzeniem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano typy certyfikatów, które można zainstalować na urządzeniu z systemem Azure Stack brzeg Pro. Artykuł zawiera również szczegóły dotyczące każdego typu certyfikatu wraz z procedurą instalacji i identyfikacji daty wygaśnięcia. 

## <a name="about-certificates"></a>Informacje o certyfikatach

Certyfikat zawiera łącze między **kluczem publicznym** i jednostką (taką jak nazwa domeny), która została **podpisana** (zweryfikowana) przez zaufaną stronę trzecią (na przykład **urząd certyfikacji**).  Certyfikat zapewnia wygodny sposób dystrybucji zaufanych publicznych kluczy szyfrowania. Certyfikaty w ten sposób zapewniają, że komunikacja jest zaufana i że dane zaszyfrowane są wysyłane na właściwy serwer. 

Po wstępnym skonfigurowaniu urządzenia Azure Stack EDGE Pro certyfikaty z podpisem własnym są generowane automatycznie. Opcjonalnie możesz przenieść własne certyfikaty. Istnieją wskazówki, które należy wykonać, jeśli planujesz przeprowadzenie własnych certyfikatów.

## <a name="types-of-certificates"></a>Typy certyfikatów

Różne typy certyfikatów, które są używane na urządzeniu Azure Stack EDGE Pro, są następujące: 
- Certyfikaty podpisywania
    - Główny urząd certyfikacji
    - Średni

- Certyfikaty punktów końcowych
    - Certyfikat węzła
    - Certyfikaty lokalnego interfejsu użytkownika
    - Azure Resource Manager certyfikaty
    - Certyfikaty magazynu obiektów BLOB
    - Certyfikaty urządzeń IoT
    <!--- WiFi certificates
    - VPN certificates-->

- Certyfikaty szyfrowania
    - Obsługa certyfikatów sesji

Każdy z tych certyfikatów opisano szczegółowo w poniższych sekcjach.

## <a name="signing-chain-certificates"></a>Podpisywanie certyfikatów łańcucha

Są to certyfikaty dla urzędu, które podpisą certyfikaty lub urząd certyfikacji podpisywania. 

### <a name="types"></a>Typy

Te certyfikaty mogą być certyfikatami głównymi lub pośrednimi certyfikatami. Certyfikaty główne są zawsze z podpisem własnym (lub podpisane przez siebie). Certyfikaty pośrednie nie są z podpisem własnym i są podpisane przez Urząd podpisywania.

### <a name="caveats"></a>Zastrzeżenia

- Certyfikatami głównymi powinny być podpisywane certyfikaty łańcucha.
- Certyfikaty główne można przekazać na urządzeniu w następującym formacie: 
    - **Der** — są one dostępne jako `.cer` rozszerzenie pliku.
    - **Zakodowane w formacie Base-64** — są one dostępne jako `.cer` rozszerzenie pliku.
    - **P7b** — ten format jest używany tylko w przypadku podpisywania certyfikatów łańcucha, które obejmują certyfikaty główne i pośrednie.
- Certyfikaty łańcucha podpisywania są zawsze przekazywane przed przekazaniem innych certyfikatów.


## <a name="node-certificates"></a>Certyfikaty węzła

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Wszystkie węzły w urządzeniu stale komunikują się ze sobą i dlatego muszą mieć relację zaufania. Certyfikaty węzła zapewniają sposób ustanowienia tego zaufania. Certyfikaty węzłów są również odtwarzane podczas łączenia się z węzłem urządzenia za pomocą zdalnej sesji programu PowerShell za pośrednictwem protokołu HTTPS.

### <a name="caveats"></a>Zastrzeżenia

- Certyfikat węzła powinien być podany w `.pfx` formacie z kluczem prywatnym, który można wyeksportować. 
- Można utworzyć i przekazać 1 certyfikat wieloznacznego węzła lub 4 certyfikaty poszczególnych węzłów. 
- Jeśli domena DNS ulegnie zmianie, ale nazwa urządzenia nie ulegnie zmianie, należy zmienić certyfikat węzła. W przypadku przenoszenia własnego certyfikatu węzła nie można zmienić numeru seryjnego urządzenia, można zmienić tylko nazwę domeny.
- Poniższa tabela zawiera instrukcje podczas tworzenia certyfikatu węzła.
   
    |Typ |Nazwa podmiotu (SN)  |Alternatywna nazwa podmiotu (SAN)  |Przykład nazwy podmiotu |
    |---------|---------|---------|---------|
    |Węzeł|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certyfikaty punktów końcowych

Dla wszystkich punktów końcowych, które udostępnia urządzenie, certyfikat jest wymagany do komunikacji zaufanej. Certyfikaty punktów końcowych obejmują te wymagane podczas uzyskiwania dostępu do Azure Resource Manager i magazynu obiektów BLOB za pośrednictwem interfejsów API REST. 

Po dołączeniu własnego certyfikatu do własnych potrzeb potrzebny jest również odpowiedni łańcuch podpisywania certyfikatu. W przypadku łańcucha podpisywania, Azure Resource Manager i certyfikatów obiektów BLOB na urządzeniu wymagane są odpowiednie certyfikaty na komputerze klienckim, co umożliwia również uwierzytelnianie i komunikację z urządzeniem.

### <a name="caveats"></a>Zastrzeżenia

- Certyfikaty punktów końcowych muszą być w `.pfx` formacie z kluczem prywatnym. Łańcuch podpisywania powinien mieć format DER ( `.cer` rozszerzenie pliku). 
- Po przeniesieniu własnych certyfikatów punktu końcowego mogą to być pojedyncze certyfikaty lub certyfikaty wielodomenowe. 
- W przypadku przełączania łańcucha podpisywania należy przekazać certyfikat łańcucha podpisywania przed przekazaniem certyfikatu punktu końcowego.
- Te certyfikaty należy zmienić, jeśli nazwa urządzenia lub nazwy domeny DNS ulegną zmianie.
- Można użyć wieloznacznego certyfikatu punktu końcowego.
- Właściwości certyfikatów punktów końcowych są podobne do typowych certyfikatów SSL. 
- Podczas tworzenia certyfikatu punktu końcowego należy skorzystać z następującej tabeli:

    |Typ |Nazwa podmiotu (SN)  |Alternatywna nazwa podmiotu (SAN)  |Przykład nazwy podmiotu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Pojedynczy certyfikat sieci SAN dla obu punktów końcowych|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Certyfikaty lokalnego interfejsu użytkownika

Możesz uzyskać dostęp do lokalnego interfejsu użytkownika sieci Web urządzenia za pośrednictwem przeglądarki. Aby upewnić się, że ta komunikacja jest bezpieczna, możesz przekazać własny certyfikat. 

### <a name="caveats"></a>Zastrzeżenia

- Certyfikat lokalnego interfejsu użytkownika jest również przekazywany w `.pfx` formacie z kluczem prywatnym, który można wyeksportować.
- Po przekazaniu certyfikatu lokalnego interfejsu użytkownika należy ponownie uruchomić przeglądarkę i wyczyścić pamięć podręczną. Zapoznaj się z określonymi instrukcjami dla przeglądarki.

    |Typ |Nazwa podmiotu (SN)  |Alternatywna nazwa podmiotu (SAN)  |Przykład nazwy podmiotu |
    |---------|---------|---------|---------|
    |Lokalny interfejs użytkownika| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge certyfikaty urządzeń

Urządzenie Azure Stack EDGE Pro jest również urządzeniem IoT z obliczaniem włączonym przez urządzenie IoT Edge z nim połączone. Aby zapewnić bezpieczną komunikację między tym urządzeniem IoT Edge i urządzeniami podrzędnymi, które mogą się z nim połączyć, można także przekazać certyfikaty IoT Edge. 

Urządzenie ma certyfikaty z podpisem własnym, których można użyć, jeśli chcesz używać tylko scenariusza obliczeń z urządzeniem. Jeśli urządzenie Azure Stack EDGE Pro jest jednak połączone z urządzeniami podrzędnymi, należy je przenieść.

Istnieją trzy IoT Edge certyfikaty, które należy zainstalować, aby włączyć tę relację zaufania:

- **Główny urząd certyfikacji lub urząd certyfikacji właściciela**
- **Urząd certyfikacji urządzenia** 
- **Certyfikat klucza urządzenia**

### <a name="caveats"></a>Zastrzeżenia

- Certyfikaty IoT Edge są przekazywane w `.pem` formacie. 


Aby uzyskać więcej informacji na temat IoT Edge certyfikatów, zobacz [Azure IoT Edge szczegóły certyfikatu](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Obsługa certyfikatów sesji

Jeśli na urządzeniu z programem Azure Stack Edge wystąpią jakieś problemy, to aby rozwiązać te problemy, na urządzeniu może być otwarta sesja obsługi zdalnej programu PowerShell. Aby włączyć bezpieczną, zaszyfrowaną komunikację w ramach tej sesji obsługi, można przekazać certyfikat.

### <a name="caveats"></a>Zastrzeżenia

- Upewnij się, że odpowiedni `.pfx` certyfikat z kluczem prywatnym jest zainstalowany na komputerze klienckim przy użyciu narzędzia do odszyfrowywania.
- Sprawdź, czy pole **użycie klucza** certyfikatu nie ma **podpisu certyfikatu**. Aby to sprawdzić, kliknij prawym przyciskiem myszy certyfikat, wybierz pozycję **Otwórz** , a następnie na karcie **szczegóły** Znajdź pozycję **użycie klucza**. 


### <a name="caveats"></a>Zastrzeżenia

- Certyfikat sesji pomocy technicznej musi być podany w formacie DER z `.cer` rozszerzeniem.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Tworzenie certyfikatów (opcjonalnie)

W poniższej sekcji opisano procedurę tworzenia łańcucha podpisywania i certyfikatów punktów końcowych.

### <a name="certificate-workflow"></a>Przepływ pracy certyfikatu

Będziesz mieć zdefiniowany sposób tworzenia certyfikatów dla urządzeń działających w danym środowisku. Możesz korzystać z certyfikatów udostępnionych przez administratora IT. 

**Tylko do celów deweloperskich i testowych można także użyć środowiska Windows PowerShell do tworzenia certyfikatów w systemie lokalnym.** Podczas tworzenia certyfikatów dla klienta postępuj zgodnie z następującymi wskazówkami:

1. Można utworzyć dowolny z następujących typów certyfikatów:

    - Utwórz jeden certyfikat ważny do użycia z pojedynczą w pełni kwalifikowaną nazwą domeny (FQDN). Na przykład *mydomain.com*.
    - Utwórz certyfikat wieloznaczny w celu zabezpieczenia głównej nazwy domeny i wielu domen podrzędnych. Na przykład **. mydomain.com*.
    - Utwórz certyfikat alternatywnej nazwy podmiotu (SAN), który będzie obejmować wiele nazw domen w pojedynczym certyfikacie. 

2. Jeśli używasz własnego certyfikatu, będziesz potrzebować certyfikatu głównego dla łańcucha podpisywania. Zapoznaj się z instrukcjami [tworzenia certyfikatów łańcucha podpisywania](#create-signing-chain-certificate).

3. Można następnie utworzyć certyfikaty punktów końcowych dla lokalnego interfejsu użytkownika urządzenia, obiektów blob i Azure Resource Manager. Można utworzyć 3 oddzielne certyfikaty dla urządzeń, obiektów blob i Azure Resource Manager lub można utworzyć jeden certyfikat dla wszystkich trzech punktów końcowych. Szczegółowe instrukcje znajdują się w temacie [Create Signing and Endpoint Certificates](#create-signed-endpoint-certificates).

4. Niezależnie od tego, czy tworzysz trzy oddzielne certyfikaty, czy jeden certyfikat, określ nazwy podmiotów (SN) i alternatywne nazwy podmiotu (SAN) zgodnie ze wskazówkami podanymi dla każdego typu certyfikatu. 

### <a name="create-signing-chain-certificate"></a>Utwórz certyfikat łańcucha podpisywania

Utwórz te certyfikaty za pośrednictwem programu Windows PowerShell uruchomionym w trybie administratora. **Certyfikaty utworzone w ten sposób powinny być używane tylko do celów deweloperskich i testowych.**

Certyfikat łańcucha podpisywania należy utworzyć tylko raz. Pozostałe certyfikaty punktu końcowego odnoszą się do tego certyfikatu w celu podpisania.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Tworzenie podpisanych certyfikatów punktu końcowego

Utwórz te certyfikaty za pośrednictwem programu Windows PowerShell uruchomionym w trybie administratora.

W tych przykładach są tworzone certyfikaty punktów końcowych dla urządzenia z:
    - Nazwa urządzenia: `DBE-HWDC1T2`
    - Domena DNS: `microsoftdatabox.com`

Zamień na nazwę i domenę DNS urządzenia, aby utworzyć certyfikaty dla urządzenia.
 
**Certyfikat punktu końcowego obiektu BLOB**

Utwórz certyfikat dla punktu końcowego obiektu BLOB w magazynie osobistym.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certyfikat punktu końcowego Azure Resource Manager**

Utwórz certyfikat dla punktów końcowych Azure Resource Manager w magazynie osobistym.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certyfikat lokalnego interfejsu użytkownika sieci Web urządzenia**

Utwórz certyfikat dla lokalnego interfejsu użytkownika sieci Web urządzenia w magazynie osobistym.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Pojedynczy certyfikat wielosieci SAN dla wszystkich punktów końcowych**

Utwórz pojedynczy certyfikat dla wszystkich punktów końcowych w magazynie osobistym.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Po utworzeniu certyfikatów następnym krokiem jest przekazanie certyfikatów na urządzeniu Azure Stack EDGE Pro


## <a name="upload-certificates"></a>Przekazywanie certyfikatów 

Certyfikaty utworzone dla urządzenia domyślnie znajdują się w **magazynie osobistym** klienta. Te certyfikaty należy wyeksportować na kliencie do odpowiednich plików formatu, które można następnie przekazać do urządzenia.

1. Certyfikat główny należy wyeksportować jako format DER z `.cer` rozszerzeniem. Aby uzyskać szczegółowe instrukcje, zobacz [Eksportowanie certyfikatów w formacie der](#export-certificates-as-der-format).
2. Certyfikaty punktów końcowych muszą być eksportowane jako pliki *PFX* z kluczami prywatnymi. Aby uzyskać szczegółowe instrukcje, zobacz [Eksportowanie certyfikatów jako plik *PFX* z kluczami prywatnymi](#export-certificates-as-pfx-format-with-private-key). 
3. Certyfikaty główne i końcowe są następnie przekazywane na urządzeniu przy użyciu opcji **+ Dodaj certyfikat** na stronie certyfikaty w lokalnym interfejsie użytkownika sieci Web. 

    1. Najpierw przekaż certyfikaty główne. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **certyfikaty > + Dodaj certyfikat**.

        ![Dodaj certyfikat łańcucha podpisywania 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Następnie Przekaż certyfikaty punktów końcowych. 

        ![Dodaj certyfikat łańcucha podpisywania 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Wybierz pliki certyfikatów w formacie *PFX* i wprowadź hasło podane podczas eksportowania certyfikatu. Zastosowanie certyfikatu Azure Resource Manager może potrwać kilka minut.

        Jeśli łańcuch podpisywania nie zostanie najpierw zaktualizowany i podjęta zostanie próba przekazania certyfikatów punktu końcowego, zostanie wyświetlony komunikat o błędzie.

        ![Błąd zastosowania certyfikatu](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Wróć i przekaż certyfikat łańcucha podpisywania, a następnie Przekaż i Zastosuj certyfikaty punktów końcowych.

> [!IMPORTANT]
> W przypadku zmiany nazwy urządzenia lub domeny DNS należy utworzyć nowe certyfikaty. Certyfikaty klienta i certyfikaty urządzeń należy zaktualizować przy użyciu nowej nazwy urządzenia i domeny DNS. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importowanie certyfikatów na kliencie uzyskujących dostęp do urządzenia

Certyfikaty utworzone i przekazane do urządzenia muszą zostać zaimportowane na klienta systemu Windows (dostęp do urządzenia) do odpowiedniego magazynu certyfikatów.

1. Certyfikat główny wyeksportowany jako algorytm DER powinien zostać teraz zaimportowany do **zaufanych głównych urzędów certyfikacji** w systemie klienta. Aby uzyskać szczegółowe instrukcje, zobacz [Importowanie certyfikatów do magazynu zaufanych głównych urzędów certyfikacji](#import-certificates-as-der-format).

2. Certyfikaty punktów końcowych, które zostały wyeksportowane jako `.pfx` muszą zostać wyeksportowane jako der z `.cer` rozszerzeniem. `.cer`Następnie jest zaimportowany w **osobistym magazynie certyfikatów** w systemie. Aby uzyskać szczegółowe instrukcje, zobacz [Importowanie certyfikatów do osobistego magazynu certyfikatów](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importowanie certyfikatów w formacie DER

Aby zaimportować certyfikaty na kliencie systemu Windows, wykonaj następujące czynności:

1. Kliknij plik prawym przyciskiem myszy, a następnie wybierz pozycję **Zainstaluj certyfikat**. Ta akcja powoduje uruchomienie Kreatora importowania certyfikatów.

    ![Importuj certyfikat 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. W polu **Lokalizacja magazynu** wybierz pozycję **komputer lokalny**, a następnie wybierz pozycję **dalej**.

    ![Importuj certyfikat 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie wybierz pozycję **Przeglądaj**. 

    - Aby zaimportować do magazynu osobistego, przejdź do magazynu osobistego hosta zdalnego, a następnie wybierz przycisk **dalej**.

        ![Importuj certyfikat 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Aby zaimportować do zaufanego magazynu, przejdź do zaufanego głównego urzędu certyfikacji, a następnie wybierz przycisk **dalej**.

        ![Importuj certyfikat 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Wybierz pozycję **Zakończ**. Zostanie wyświetlony komunikat z efektem, że importowanie zakończyło się pomyślnie.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Eksportuj certyfikaty jako format PFX z kluczem prywatnym

Wykonaj następujące kroki, aby wyeksportować certyfikat SSL z kluczem prywatnym na komputerze z systemem Windows. 

> [!IMPORTANT]
> Wykonaj te kroki na tym samym komputerze, który został użyty do utworzenia certyfikatu. 

1. Uruchom *certlm. msc* , aby uruchomić Magazyn certyfikatów komputera lokalnego.

1. Kliknij dwukrotnie folder **osobiste** , a następnie pozycję **Certyfikaty**.

    ![Eksportuj certyfikat 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Kliknij prawym przyciskiem myszy certyfikat, którego kopię zapasową chcesz utworzyć, a następnie wybierz pozycję **wszystkie zadania > Eksportuj...**

    ![Eksportuj certyfikat 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Postępuj zgodnie z instrukcjami Kreatora eksportu certyfikatów, aby utworzyć kopię zapasową certyfikatu w pliku PFX.

    ![Eksportuj certyfikat 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Wybierz opcję **tak, eksportuj klucz prywatny**.

    ![Eksportowanie certyfikatu 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. **Jeśli to możliwe, wybierz opcję Dołącz wszystkie certyfikaty do ścieżki certyfikatu**, **wyeksportuj wszystkie właściwości rozszerzone** i **Włącz ochronę prywatności certyfikatów**. 

    > [!IMPORTANT]
    > NIE wybieraj **opcji Usuń klucz prywatny, jeśli eksport zakończył się pomyślnie**.

    ![Eksportuj certyfikat 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Wprowadź hasło, które będzie pamiętane. Potwierdź hasło. Hasło chroni klucz prywatny.

    ![Eksportuj certyfikat 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Wybierz, aby zapisać plik w lokalizacji zestawu.

    ![Eksportuj certyfikat 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Wybierz pozycję **Zakończ**.

    ![Eksportuj certyfikat 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Zostanie wyświetlony komunikat, że eksport zakończył się pomyślnie. Wybierz przycisk **OK**.

    ![Eksportuj certyfikat 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

Kopia zapasowa pliku PFX jest teraz zapisywana w wybranej lokalizacji i jest gotowa do przeniesienia lub zapisania do bezpiecznego przechowywania.


### <a name="export-certificates-as-der-format"></a>Eksportowanie certyfikatów w formacie DER

1. Uruchom *certlm. msc* , aby uruchomić Magazyn certyfikatów komputera lokalnego.

1. W osobistym magazynie certyfikatów wybierz certyfikat główny. Kliknij prawym przyciskiem myszy i wybierz pozycję **wszystkie zadania > Eksportuj...**

    ![Eksportowanie certyfikatu DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Zostanie otwarty Kreator certyfikatów. Wybierz format formacie **X. 509 szyfrowany binarnie algorytmem DER (CER)**. Wybierz opcję **Dalej**.

    ![Eksportowanie certyfikatu DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Przeglądaj i wybierz lokalizację, w której chcesz wyeksportować plik. cer.

    ![Eksportowanie certyfikatu DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Wybierz pozycję **Zakończ**.

    ![Eksportowanie certyfikatu DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Obsługiwane algorytmy certyfikatów

 Tylko certyfikaty Rivest – Shamir – Adleman (RSA) są obsługiwane przez urządzenie Azure Stack EDGE Pro. Jeśli używane są certyfikaty algorytmu Digital Signature Algorithm (ECDSA), zachowanie urządzenia jest nieokreślone.

 Certyfikaty zawierające klucz publiczny RSA są określane jako certyfikaty RSA. Certyfikaty, które zawierają klucz publiczny (ECC), są określane jako ECDSA (algorytm podpisywania cyfrowego krzywej eliptyczna). 


## <a name="view-certificate-expiry"></a>Wyświetl wygaśnięcie certyfikatu

Jeśli wprowadzisz własne certyfikaty, certyfikaty wygasną zwykle w ciągu 1 roku lub 6 miesięcy. Aby wyświetlić datę wygaśnięcia certyfikatu, przejdź do strony **Certyfikaty** w lokalnym interfejsie użytkownika sieci Web urządzenia. W przypadku wybrania określonego certyfikatu można wyświetlić datę wygaśnięcia certyfikatu.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia z Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-prep.md)