---
title: Konfigurowanie protokołu TLS dla usługi w chmurze | Microsoft Docs
description: Informacje na temat określania punktu końcowego HTTPS dla roli sieci Web oraz przekazywania certyfikatu TLS/SSL w celu zabezpieczenia aplikacji. W poniższych przykładach użyto Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742645"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Konfigurowanie protokołu TLS dla aplikacji na platformie Azure

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Transport Layer Security (TLS), wcześniej znana jako szyfrowanie Secure Socket Layer (SSL), to najczęściej stosowana Metoda zabezpieczania danych wysyłanych przez Internet. W ramach tego typowego zadania omówiono sposób określania punktu końcowego HTTPS dla roli sieci Web oraz przekazywania certyfikatu TLS/SSL w celu zabezpieczenia aplikacji.

> [!NOTE]
> Procedury opisane w tym zadaniu dotyczą platformy Azure Cloud Services; Aby uzyskać App Services, zobacz [ten](../app-service/configure-ssl-bindings.md)temat.
>

To zadanie używa wdrożenia produkcyjnego. Informacje o korzystaniu z wdrożenia przejściowego znajdują się na końcu tego tematu.

Przeczytaj [ten](cloud-services-how-to-create-deploy-portal.md) pierwszy, jeśli nie utworzono jeszcze usługi w chmurze.

## <a name="step-1-get-a-tlsssl-certificate"></a>Krok 1. Uzyskiwanie certyfikatu TLS/SSL
Aby skonfigurować protokół TLS dla aplikacji, należy najpierw uzyskać certyfikat TLS/SSL, który został podpisany przez urząd certyfikacji (CA), zaufaną stronę trzecią, która wystawia certyfikaty w tym celu. Jeśli jeszcze tego nie zrobiono, należy uzyskać go od firmy, która sprzedaje certyfikaty TLS/SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów TLS/SSL na platformie Azure:

* Certyfikat musi zawierać klucz publiczny.
* Należy utworzyć certyfikat do wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* Nazwa podmiotu certyfikatu musi być zgodna z domeną używaną do uzyskiwania dostępu do usługi w chmurze. Nie można uzyskać certyfikatu TLS/SSL z urzędu certyfikacji dla domeny cloudapp.net. Musisz uzyskać niestandardową nazwę domeny, która będzie używana podczas uzyskiwania dostępu do usługi. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną w celu uzyskania dostępu do aplikacji. Na przykład jeśli nazwa domeny niestandardowej to **contoso.com** , żądanie certyfikatu z urzędu certyfikacji dla **_. contoso.com_* lub **www \. contoso.com**.
* Certyfikat musi mieć co najmniej 2048-bitowe szyfrowanie.

W celach testowych można [utworzyć](cloud-services-certs-create.md) certyfikat z podpisem własnym i użyć go. Certyfikat z podpisem własnym nie jest uwierzytelniany przez urząd certyfikacji i może używać domeny cloudapp.net jako adresu URL witryny sieci Web. Na przykład następujące zadanie używa certyfikatu z podpisem własnym, w którym nazwa pospolita (CN) użyta w certyfikacie jest **sslexample.cloudapp.NET**.

Następnie należy dołączyć informacje o certyfikacie w definicji usługi i plikach konfiguracji usługi.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2. modyfikowanie definicji usługi i plików konfiguracji
Aplikacja musi być skonfigurowana do korzystania z certyfikatu i należy dodać punkt końcowy HTTPS. W związku z tym należy zaktualizować definicję usługi i pliki konfiguracji usługi.

1. W środowisku programistycznym Otwórz plik definicji usługi (CSDEF), Dodaj sekcję **Certyfikaty** w sekcji **rola** Webi podaj następujące informacje o certyfikacie (i certyfikatach pośrednich):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   Sekcja **Certyfikaty** definiuje nazwę certyfikatu, jego lokalizację oraz nazwę magazynu, w którym się znajduje.

   Uprawnienia ( `permissionLevel` atrybut) można ustawić na jedną z następujących wartości:

   | Wartość uprawnienia | Opis |
   | --- | --- |
   | limitedOrElevated |**(Ustawienie domyślne)** Wszystkie procesy ról mogą uzyskać dostęp do klucza prywatnego. |
   | pełny |Tylko podwyższone procesy mogą uzyskać dostęp do klucza prywatnego. |

2. W pliku definicji usługi Dodaj element **InputEndpoint** w sekcji **punkty końcowe** , aby włączyć protokół https:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. W pliku definicji usługi Dodaj element **Binding** w sekcji **sites** . Ten element dodaje powiązanie HTTPS w celu zamapowania punktu końcowego na lokację:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Wszystkie wymagane zmiany w pliku definicji usługi zostały ukończone; jednak nadal trzeba dodać informacje o certyfikacie do pliku konfiguracji usługi.
4. W pliku konfiguracji usługi (CSCFG), ServiceConfiguration. Cloud. cscfg Dodaj wartość **certyfikatów** z certyfikatem. Poniższy przykładowy kod zawiera szczegóły sekcji **Certyfikaty** , z wyjątkiem wartości odcisku palca.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(W tym przykładzie zastosowano algorytm **SHA1** dla algorytmu odcisku palca. Określ odpowiednią wartość dla algorytmu odcisku palca certyfikatu.

Teraz, gdy pliki definicji usługi i konfiguracji usługi zostały zaktualizowane, należy spakować wdrożenie na potrzeby przekazywania na platformę Azure. Jeśli używasz **cspack**, nie używaj flagi **/generateConfigurationFile** , ponieważ spowoduje to zastąpienie właśnie wstawionych informacji o certyfikacie.

## <a name="step-3-upload-a-certificate"></a>Krok 3. przekazywanie certyfikatu
Połącz z Azure Portal i...

1. W sekcji **wszystkie zasoby** w portalu wybierz usługę w chmurze.

    ![Publikowanie usługi w chmurze](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kliknij pozycję **Certyfikaty**.

    ![Kliknij ikonę Certyfikaty](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kliknij przycisk **Przekaż** w górnej części obszaru certyfikaty.

    ![Kliknij element menu przekazanie](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Podaj **plik**, **hasło**, a następnie kliknij przycisk **Przekaż** u dołu obszaru wprowadzania danych.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4. Nawiązywanie połączenia z wystąpieniem roli przy użyciu protokołu HTTPS
Teraz, gdy wdrożenie jest uruchomione na platformie Azure, możesz połączyć się z nim przy użyciu protokołu HTTPS.

1. Kliknij **adres URL witryny** , aby otworzyć przeglądarkę sieci Web.

   ![Kliknij adres URL witryny](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. W przeglądarce sieci Web zmodyfikuj link, aby używał **protokołu HTTPS** zamiast **protokołu HTTP**, a następnie odwiedź stronę.

   > [!NOTE]
   > Jeśli używasz certyfikatu z podpisem własnym, podczas przechodzenia do punktu końcowego HTTPS skojarzonego z certyfikatem z podpisem własnym może zostać wyświetlony komunikat o błędzie certyfikatu w przeglądarce. Użycie certyfikatu podpisanego przez zaufany urząd certyfikacji eliminuje ten problem; w międzyczasie można zignorować ten błąd. (Inna opcja polega na dodaniu certyfikatu z podpisem własnym do magazynu certyfikatów zaufanego urzędu certyfikacji użytkownika).
   >
   >

   ![Podgląd witryny](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Jeśli chcesz użyć protokołu TLS do wdrożenia przejściowego, a nie wdrożenia produkcyjnego, musisz najpierw określić adres URL używany do wdrożenia przejściowego. Po wdrożeniu usługi w chmurze adres URL środowiska tymczasowego jest określany przez **Identyfikator GUID identyfikatora wdrożenia** w tym formacie: `https://deployment-id.cloudapp.net/`  
   >
   > Utwórz certyfikat z nazwą pospolitą (CN) równą adresowi URL opartemu na identyfikatorze GUID (na przykład **328187776e774ceda8fc57609d404462.cloudapp.NET**). Użyj portalu, aby dodać certyfikat do usługi w chmurze przemieszczanej. Następnie należy dodać informacje o certyfikacie do plików CSDEF i CSCFG, ponownie spakować aplikację i zaktualizować wdrożenie przemieszczane, aby użyć nowego pakietu.
   >

## <a name="next-steps"></a>Następne kroki
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Skonfiguruj [niestandardową nazwę domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą w chmurze](cloud-services-how-to-manage-portal.md).



